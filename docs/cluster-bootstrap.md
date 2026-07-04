# Kubernetes Cluster Bootstrap

## Control Plane

- Bootstrap tool: kubeadm
- Kubernetes version: v1.34.9
- Runtime: containerd
- OS: Ubuntu Server

## Networking

- CNI: Cilium

## Verification

- `kubectl get nodes` → Ready
- `kubectl get pods -A`
- `cilium status`

## Notes

- Single-node control plane
- Future expansion will use `kubeadm join`
