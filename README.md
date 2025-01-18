# llama-operator
Kubernetes operator for deploying Llama applications

# Tools
* Kind (`brew install kind`)
* Kubectl (`brew install kubectl`)

# Getting Started

Termainal 1: Create a cluster, deploy llama, and tail logs
```sh
kind create cluster --name llama
kubectl apply -f manifest
# Slow networks may take a few minutes to pull the 6GB image
kubectl wait deployment llama --for condition=Available=True --timeout=240s
kubectl logs -f -l app.kubernetes.io/name=llama
```

Terminal 2: Connect to the cluster
```sh
kubectl port-forward deployment/llama 8080:11434
```

Terminal 3: Talk to Llama
```sh
curl -s http://localhost:11434/api/generate -d '{
  "model": "tinyllama",
  "prompt": "Is Kubernetes the best way to deploy Llama?",
  "stream": false
}' | jq -r ".response"
```
