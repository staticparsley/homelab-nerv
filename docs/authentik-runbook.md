# Authentik Forward Auth Runbook

This document describes the process for protecting an application with Authentik Forward Auth in NERV.

---

## Prerequisites

- Application is already deployed and accessible.
- Application has its own Ingress resource.
- Authentik is healthy.
- Embedded Outpost is running.

---

## 1. Create an Application

Authentik → Applications → Create

- Name: `<app>`
- Slug: `<app>`

---

## 2. Create a Proxy Provider

Authentik → Providers → Create

Type:

- Proxy Provider

Mode:

- Forward Auth (single application)

External Host:

```
https://<app>.nerv.local
```

Attach the provider to the Application.

---

## 3. Add the Provider to the Embedded Outpost

⚠️ **Required**

Authentik does **not** automatically assign the provider to the Embedded Outpost.

Navigate to:

```
Applications
→ Outposts
→ authentik Embedded Outpost
```

Edit the Outpost and add the new provider/application.

If this step is skipped, nginx will return:

```
auth request unexpected status: 404
```

and users will receive a **500 Internal Server Error**.

---

## 4. Create an Outpost Ingress

Create:

```
apps/authentik/<app>-outpost-ingress.yaml
```

Example:

```yaml
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: <app>-authentik-outpost
  namespace: authentik
spec:
  ingressClassName: nginx
  rules:
    - host: <app>.nerv.local
      http:
        paths:
          - path: /outpost.goauthentik.io
            pathType: ImplementationSpecific
            backend:
              service:
                name: authentik-server
                port:
                  number: 80
```

Add the new ingress to the Authentik kustomization.

---

## 5. Protect the Application Ingress

Add the following annotations:

```yaml
nginx.ingress.kubernetes.io/auth-url: http://authentik-server.authentik.svc.cluster.local:80/outpost.goauthentik.io/auth/nginx

nginx.ingress.kubernetes.io/auth-signin: https://<app>.nerv.local/outpost.goauthentik.io/start?rd=$scheme://$http_host$escaped_request_uri

nginx.ingress.kubernetes.io/auth-response-headers: Set-Cookie,X-authentik-username,X-authentik-groups,X-authentik-entitlements,X-authentik-email,X-authentik-name,X-authentik-uid

nginx.ingress.kubernetes.io/auth-snippet: |
  proxy_set_header X-Forwarded-Host $http_host;
```

---

## 6. Deploy

```bash
git add .
git commit -m "feat(<app>): protect with Authentik forward auth"
git push
```

Wait for Argo CD to sync.

---

## 7. Verify

### Outpost ingress

```bash
kubectl get ingress -n authentik
```

Expected:

```
<app>-authentik-outpost
```

### Ping endpoint

```bash
curl -k -i https://<app>.nerv.local/outpost.goauthentik.io/ping
```

Expected:

```
HTTP/2 204
```

### Application

Open:

```
https://<app>.nerv.local
```

Expected:

- Redirect to Authentik if unauthenticated.
- Application loads if authenticated.

---

# Troubleshooting

## 500 Internal Server Error

Check nginx logs:

```bash
kubectl logs -n ingress-nginx deployment/ingress-nginx-controller --since=5m | grep <app>
```

---

## auth request unexpected status: 404

Usually indicates one of the following:

- Provider is **not assigned to the Embedded Outpost**
- Outpost ingress is missing
- Hostname mismatch between Provider and Ingress

---

## Outpost health

```bash
curl -k -i https://<app>.nerv.local/outpost.goauthentik.io/ping
```

Expected:

```
HTTP/2 204
```

If this returns 404, verify the outpost ingress exists and matches the application hostname.

---

## Notes

This pattern is reusable for any application protected by Authentik Forward Auth (Homepage, Headlamp, AdGuard Home, Forgejo, etc.).
