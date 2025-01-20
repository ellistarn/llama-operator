# llama-operator
Kubernetes operator for deploying Llama applications

# Tools
* Kind (`brew install kind`)
* Kubectl (`brew install kubectl`)
* Docker

# Getting Started

Termainal 1: Create a cluster, deploy llama, and tail logs

```sh
mkdir -p .ollama
kind create cluster --config ./kind.yaml
# Hack to speed up image pull time for kind (~10gb)
docker pull alpine/ollama:latest
docker pull llamastack/distribution-ollama:latest
kind load docker-image alpine/ollama:latest --name llama-stack
kind load docker-image llamastack/distribution-ollama:latest --name llama-stack
kubectl apply -f manifest
# Slow networks may take a few minutes to pull the 6GB image
kubectl wait deployment llama-stack --for condition=Available=True --timeout=240s
kubectl logs -f -l app.kubernetes.io/name=llama-stack
```

Terminal 2: Connect to the cluster
```sh
kubectl port-forward svc/llama-stack 8080:80
```

Terminal 3: Talk to Llama Stack
```sh
llama-stack-client configure --endpoint http://localhost:8080
llama-stack-client models list
llama-stack-client inference chat-completion --message "Hello, what model are you?"
```

# Roadblocks and Solutions

1. Tinyllama isn't supported by llama-stack's ollama distribution. Why is the set of models limited and not discovered?
1. Image/model pulling happens on restart, and needs to be optimized (10gb+).
1. Ollama image is huge and slow to pull, use alipine/ollama instead
1. Can't easily mount gpu device into kind (maybe https://www.substratus.ai/blog/kind-with-gpus?)
1. On cpu, kind maxes out at 8GB, so you can't run llama llama3.2:3b-instruct-fp16, try 1b instead
