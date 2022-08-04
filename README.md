# golist
Deployment Repository for the golist Application.

# Health Checks

You need to update the `argocd-cm` ConfigMap as [stated here](https://argo-cd.readthedocs.io/en/stable/operator-manual/health/#argocd-app) first. 

> **Edit manually if you don't want to do the patch**

```shell
kubectl patch cm/argocd-cm -n argocd --type=merge \
-p='{"data":{"resource.customizations.health.argoproj.io_Application":"hs = {}\nhs.status = \"Progressing\"\nhs.message = \"\"\nif obj.status ~= nil then\n  if obj.status.health ~= nil then\n    hs.status = obj.status.health.status\n    if obj.status.health.message ~= nil then\n      hs.message = obj.status.health.message\n    end\n  end\nend\nreturn hs\n"}}'
```

Now you can apply your App-of-Apps

```shell
kubectl apply -f golist-app-of-apps.yaml
```

# Troubleshooting

If you're having issues, delete the pods and wait for them to come back up.

> **You shouldn't need to do this, but it's here anyway**

```shell
kubectl delete pods --all -n argocd
kubectl rollout status deploy argocd-redis -n argocd
kubectl rollout status deploy argocd-dex-server -n argocd
kubectl rollout status deploy argocd-repo-server -n argocd
kubectl rollout status deploy argocd-applicationset-controller -n argocd
kubectl rollout status deploy argocd-server -n argocd
```

