LAN request path:

browser

→ /etc/hosts or DNS

→ 192.168.1.240 MetalLB IP

→ ingress-nginx LoadBalancer Service

→ Ingress rule by Host header

→ app Service

→ Pod
