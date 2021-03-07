# Docker

### Criar imagem
  docker build -t pccamargo/api-produto:v1 .

### Subir imagem para o repositório
  docker login
  docker push pccamargo/api-produto:v1


# K3D

k3d cluster create nome_cluster
opções [--servers (qtde de servers)
        --agents (qtde de nodes)
        --no-lb (sem loadbalancer)
      ]
  kubectl geet nodes
  docker container ls

k3d cluster delete nome_cluster


kubectl api-resources

:: pod
kubectl apply -f pod.yaml
kubectl get pods
kubectl get pods -l key_label=value_label
kubectl describe pod nome_do_pod
kubectl delete pod nome_do_pod
kubectl port-forward pod/nome_do_pod 8080:80

:: replicaset
kubectl apply -f repliset.yaml
kubectl get replicaset
kubectl describe replicaset nome_do_replicaset
kubectl port-forward pod/nome_do_replicaset 8080:80
kubectl delete pod nome_do_replicaset

:: deployment
kubectl apply -f deploy.yaml
kubectl get deployment
kubectl get replicaset
kubectl get pods

:: service
- tipos de services:
  - ClusterIP : expõe o serviço dentro do cluster k8s
  - NodePort : expõe o serviço para fora do custer k8s (port:30000:32767)
  - LoadBalancer : Cloud

kubectl apply -f service.yaml
kubectl get services
kubectl port-forward service/nome_do_service 8080:80

Fazendo bind de porta do k3d para ser usado no services.
Com isso vc define no service.yaml a nodePort com o valor 30000
- k3d cluster create meucluster --servers 1 --agents: 2 -p "8080:30000@loadbalancer"



kubectl get all