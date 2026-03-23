URL https://kagent.dev/docs/kagent/getting-started/quickstart

Helm:
helm install kagent-crds oci://ghcr.io/kagent-dev/kagent/helm/kagent-crds \
    --namespace kagent \
    --create-namespace

helm install kagent oci://ghcr.io/kagent-dev/kagent/helm/kagent \
    --namespace kagent \
    --set providers.default=openAI \
    --set providers.openAI.apiKey=$OPENAI_API_KEY

Kagent UI:
kubectl port-forward -n kagent svc/kagent-ui 8080:8080