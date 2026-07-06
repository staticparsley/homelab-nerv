# homelab-nerv

GitOps-managed Kubernetes homelab running on Ubuntu.

## Platform

- kubeadm
- containerd
- Cilium
- Argo CD
- ingress-nginx
- MetalLB
- cert-manager

## Applications

- whoami
- Homepage
- Grafana
- (coming soon) Photo of the Day

## Workflow

Cursor
↓
Git Commit
↓
GitHub
↓
Argo CD
↓
Kubernetes

Internet
│
▼
Verizon Router
│
▼
MetalLB
│
▼
Ingress
│
┌────┴───────────┐
│ │
Homepage Grafana
AdGuard Argo CD
