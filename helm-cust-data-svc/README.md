# digx customer data service helm chart

## To install the helm chart
```
helm install custdatasvc . -n <your-namespace>
```

## To upgrade the helm chart
```
helm upgrade custdatasvc . -n <your-namespace>
```

## To describe all the resource created by this helm chart
```
helm get all custdatasvc -n <your-namespace>

or

kubectl get all --all-namespaces -l='app.kubernetes.io/managed-by=Helm,app.kubernetes.io/instance=<your-namespace>'
```