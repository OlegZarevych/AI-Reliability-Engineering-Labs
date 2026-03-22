# Install AgentGateway
URL - https://agentgateway.dev/docs/standalone/latest/quickstart/llm/
Step 1 - CRDs
helm upgrade -i agentgateway-crds oci://cr.agentgateway.dev/charts/agentgateway-crds \
--create-namespace --namespace agentgateway-system \
--version v1.0.0 \
--set controller.image.pullPolicy=Always

Step 2 - AgentGateway
helm upgrade -i agentgateway oci://cr.agentgateway.dev/charts/agentgateway \
  --namespace agentgateway-system \
  --version v1.0.0 \
  --set controller.image.pullPolicy=Always \
  --set controller.extraEnv.KGW_ENABLE_GATEWAY_API_EXPERIMENTAL_FEATURES=true


# Port-Forwarding
kubectl port-forward deployment/agentgateway-proxy -n agentgateway-system 8080:80


# Test connection
export INGRESS_GW_ADDRESS=$(kubectl get svc -n agentgateway-system agentgateway-proxy -o jsonpath="{.status.loadBalancer.ingress[0]['hostname','ip']}")
echo $INGRESS_GW_ADDRESS

curl "http://localhost:8070/openai" -H content-type:application/json  -d '{
   "model": "",
   "messages": [
     {
       "role": "system",
       "content": "You are a poetic assistant, skilled in explaining complex programming concepts with creative flair."
     },
     {
       "role": "user",
       "content": "Compose a poem that explains the concept of recursion in programming."
     }
   ]
 }' | jq




--- KGateway not working
# Install KGateway via Helm
URL - https://kgateway.dev/docs/envoy/latest/quickstart/

Step1 - Install CRD via Helm
helm upgrade -i kgateway-crds oci://cr.kgateway.dev/kgateway-dev/charts/kgateway-crds \
--create-namespace --namespace kgateway-system \
--version v2.3.0-main \
--set controller.image.pullPolicy=Always

Step2 - Install Kgateway via Helm
helm upgrade -i kgateway oci://cr.kgateway.dev/kgateway-dev/charts/kgateway \
--namespace kgateway-system \
--version v2.3.0-main \
--set controller.image.pullPolicy=Always \
--set gateway.aiExtension.enabled=true \
--set controller.extraEnv.KGW_ENABLE_GATEWAY_API_EXPERIMENTAL_FEATURES=true

Step3 - Enable AI extension
helm upgrade -i -n kgateway-system kgateway oci://cr.kgateway.dev/kgateway-dev/charts/kgateway \
     --set gateway.aiExtension.enabled=true \
     --version v32.3.0-main


