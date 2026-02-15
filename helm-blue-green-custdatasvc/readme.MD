# Blue Green

The blue green strategy is not supported by built-in Kubernetes Deployment but available via third-party Kubernetes controller.
This example demonstrates how to implement blue-green deployment via [Argo Rollouts](https://github.com/argoproj/argo-rollouts):

1. Install Argo Rollouts controller: https://github.com/argoproj/argo-rollouts#installation
2. Create a blue-green-custdatasvc application and sync it.

```
argocd app create --name blue-green-custdatasvc --repo https://github.com/vadivkanag/argo-apps --dest-server https://kubernetes.default.svc --dest-namespace westnat-digx --path helm-blue-green-custdatasvc && argocd app sync helm-blue-green-custdatasvc
```

Once the application is synced you can access it using `blue-green-custdatasvc` service.

3. Change image version parameter to trigger blue-green deployment process:

```
argocd app set blue-green-custdatasvc -p image.tag=0.1.1 && argocd app sync blue-green-custdatasvc
```

Now application runs `helm-custdatasvc:v0.1.0` and `helm-custdatasvc:0.1.1` images simultaneously.
The `helm-custdatasvc:0.1.1` is still considered `blue` available only via preview service `blue-green-custdatasvc-preview`.

4. Promote `helm-custdatasvc:0.1.1` to `green` by patching `Rollout` resource:

```
argocd app patch-resource blue-green-custdatasvc --kind Rollout --resource-name helm-blue-green-custdatasvc --patch '{ "status": { "verifyingPreview": false } }' --patch-type 'application/merge-patch+json'
```

This promotes `helm-custdatasvc:0.1.1` to `green` status and `Rollout` deletes old replica which runs `helm-custdatasvc:0.1.0`.
