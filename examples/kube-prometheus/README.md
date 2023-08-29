# Kube Prometheus Example

These commands deploy the kube-prometheus stack with modifications to
kube-state-metrics that add the custom metrics for gateway-api.
It also creates the example dashboards in Grafana.

First, apply the various monitoring CustomResourceDefinitions, giving them time
to reconcile fully:

```bash
kustomize build . | docker run --rm -i ryane/kfilt -i kind=CustomResourceDefinition | kubectl apply --server-side -f -
```

Then apply the full set of monitoring stack resources:

```bash
kustomize build . | docker run --rm -i ryane/kfilt -x kind=CustomResourceDefinition | kubectl apply -f -
```

To access the dashboards in Grafana, use the port-forward command:

```bash
kubectl wait --timeout=5m -n monitoring deployment/grafana --for=condition=Available
kubectl -n monitoring port-forward service/grafana 3000:3000 &
```

Then navigate to http://localhost:3000
The Gateway API State dashboards will be available in the 'Default' folder.

To access the prometheus UI, use this port-forward command:

```bash
kubectl -n monitoring port-forward service/prometheus-k8s 9090:9090 &
```

Then navigate to http://localhost:9090
You should see some example Gateway API alerts in the 'Alerts' tab.