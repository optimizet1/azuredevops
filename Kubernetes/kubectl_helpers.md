# To check if a Kubernetes node image (especially in AKS or another cloud) already has the NVIDIA GPU driver installed, follow these steps:

# Check for NVIDIA driver:
nvidia-smi

# If the driver is installed and working, you'll see output like:

+-----------------------------------------------------------------------------+
| NVIDIA-SMI 470.129.06    Driver Version: 470.129.06    CUDA Version: 11.4  |
+-----------------------------------------------------------------------------+
# If it says command not found or fails, the driver isn’t installed.


#########


Option 2: Check from Inside a GPU Pod

If you already have a GPU pod running, you can exec into it and run nvidia-smi:

kubectl exec -it <gpu-pod-name> -- nvidia-smi

This also confirms whether the device plugin and driver are working inside the container runtime.
Option 3: Check Node Info via Kubernetes

kubectl describe node <node-name> | grep -i "gpu"

This shows if the GPU is allocatable (driver + plugin loaded):

Allocatable:
  nvidia.com/gpu:  1

If that’s missing, likely:

    The driver isn’t installed

    The NVIDIA device plugin isn't running

    The node isn't a GPU-enabled VM



#!/bin/bash
echo "Checking all nodes for GPU info..."

kubectl get nodes -o json | jq -r '
  .items[] |
  {
    name: .metadata.name,
    has_gpu: (.status.capacity."nvidia.com/gpu" // "No"),
    skip_driver_label: (.metadata.labels["skip-gpu-driver-install"] // "false"),
    agentpool: (.metadata.labels["agentpool"] // "N/A")
  }
'

###################################

Check which node the pod was scheduled to:
kubectl get pod gpu-pod -o wide



Then verify GPU allocation on that node:
kubectl describe node <node-name> | grep -i "nvidia.com/gpu"


##########################

**1. Confirm Nodes Have GPUs**
Check if your nodes have GPU capacity:

kubectl describe nodes | grep -A5 "Capacity"

You should see something like:
nvidia.com/gpu: 1

If not, your nodes don’t have GPUs. Add a GPU node pool (see step 6).



2. Check GPU Allocations

See if other pods are already using the GPUs:

kubectl top pod --all-namespaces

Or check what’s currently running that uses GPUs:

kubectl get pods -A -o json | jq '.items[] | select(.spec.containers[].resources.limits."nvidia.com/gpu") | {name: .metadata.name, namespace: .metadata.namespace}'

3. Check for NVIDIA Device Plugin

Make sure it's running:

kubectl get pods -n kube-system | grep nvidia

Should see something like:

nvidia-device-plugin-daemonset-xxxxx   Running

4. Check Pod Node Affinity and Tolerations

If your pods have node affinity or taints, they might be unschedulable even if GPU exists.

Make sure your pod has:

tolerations:
  - key: "sku"
    operator: "Equal"
    value: "gpu"
    effect: "NoSchedule"

nodeSelector:
  agentpool: gpunp  # or whatever your GPU pool is

5. Restart or Reschedule Pods

Sometimes terminating and recreating pods can help the scheduler re-evaluate:

kubectl delete pod <pod-name>

6. Scale Up or Add GPU Node Pool in AKS

If your nodes are just out of GPUs, you need more!

Add a GPU-enabled node pool:

az aks nodepool add \
  --resource-group <rg> \
  --cluster-name <aks-name> \
  --name gpunp \
  --node-count 1 \
  --node-vm-size Standard_NC6 \
  --aks-custom-headers UseGPUDedicatedVHD=true \
  --node-taints sku=gpu:NoSchedule

7. (Optional) Preemption and Priority

If you want to preempt lower-priority GPU workloads, use priorityClassName in your Pod spec and set it higher than others.


TL;DR – Fixing the Insufficient nvidia.com/gpu Error:

✅ Check that nodes have GPUs
✅ Ensure GPUs aren’t already fully used
✅ Verify NVIDIA plugin is running
✅ Match taints/affinity
✅ Scale up GPU node pool if needed