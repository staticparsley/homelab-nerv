# Kubernetes Host Prerequisites

## Completed

- Disabled swap
- Configured swap to remain disabled after reboot
- Loaded kernel modules:
  - overlay
  - br_netfilter
- Configured sysctl:
  - net.ipv4.ip_forward=1
  - net.bridge.bridge-nf-call-iptables=1
  - net.bridge.bridge-nf-call-ip6tables=1

## Verification

- `swapon --show` returns no output
- `lsmod` shows overlay and br_netfilter
- `sysctl --system` applied successfully
- `containerd` service is active
