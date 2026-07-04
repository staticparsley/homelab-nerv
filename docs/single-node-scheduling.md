## Single-node scheduling

Removed the default control-plane taint so workloads can run on the single-node homelab cluster.

Verification:
- Created nginx Deployment
- Exposed nginx as ClusterIP Service
- Confirmed in-cluster DNS/service access with curl
