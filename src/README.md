# Docker

### Criar imagem
  docker build -t pccamargo/api-produto:v1 .

### Subir imagem para o repositório
  docker login
  docker push pccamargo/api-produto:v1


# K3D
Orquestrador de containers (k8s)

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

# Prometheus
Métricas e Monitoramento

TSDB - Time Series DataBase
  Adapters permite armazenar os dados em outros sistemas de armazenamento

- Retriveal: Coleta de dados
  Coleta passiva, onde o Prometheus coleta a métrica da aplicação através de um endpoint na aplicação.
  Aplicação pode expor os dados para coleta usando libs como OpenTelemetry (Jobs)
  Suporte nativos como Grafana, Docker, K8s
  Sem suporte nativo, como jenkins, mysql, podem usar exporters (doc do prometheus)
  Processo de curta duração (batches) pode-se usar o Push Gateway
  Service Discovey para métricas dinâmicas

- Viewer: Consulta de métricas
  Terminal web do Prometheus
  Grafana

- Alerts: Alarmes
  Alert Manager recebe os alertas e envia para os canais listeneres (slack,teams...)

- Metric Server: Coleta e exibe as máetricas para o Prometheus
  Como saber se tem o Metric Server em execução no custer do k8s:
  kubectl top pods|nodes

## Instalando Grafana e Prometheus no k8s

- Instalar o Helm
  Acessar a página na internet helm.sh > Introdução > Instalação

- Recriar o Cluster no K8s com Port Bind para as portas do Promethues e Grafana
  k3d cluster delete nome_cluster
  k3d cluster create nome_custer --servers 1 --agents 2 -p "8080:30000@loadbalancer" -p "8181:30001@loadbalancer"  -p "8282:30002@loadbalancer"
  
kubectl get nodes
docker container ls

:: subir o mongo e a aplicação
kubectl apply -f k8s/mongo/
kubectl apply -f k8s/api/
kubectl get pods

:: Instalar o Prometheus
- Acessar a página de reposítórios Helm https://artifacthub.io/ (repositório do chart)
- Adicionar o repositório
  helm repo add prometheus-community https://prometheus-community.github.io/helm-charts
  helm repo update
  helm repo list
- Instalar o chart
  helm show values prometheus-community/prometheus > prometheus-values.yaml
  
  helm install prometheus prometheus-community/prometheus --values Prometheus/values.yaml
  helm list
  kubectl get services
  http://localhost:8181
  http://localhost:8080/metrics
  Incluir annotations no deployment da aplicação, conforme mostrado no site do Prometheus


Vamos gerar dados para as métricas

while true; do curl http://localhost:8080/api/produto; sleep 0.5; done


:: Instalar o Grafana
helm repo add grafana https://grafana.github.io/helm-charts
helm repo update
helm repo list
helm show values grafana/grafana > grafana.yaml
helm install grafana grafana/grafana --values Grafana/values.yaml

http://localhost:8282
admin:123456

Adicionar Datasource
  Prometheus
  URL: http://prometheus-server (service promethues do k8s)

Importar dados para o Grafana
https://grafana.com/grafana/dashboards
Filtrar Datasource > Prometheus
Grafana > import from grafana.com

## References
https://kubedev.club.hotmart.com/lesson

https://www.magalix.com/blog/create-a-ci/cd-pipeline-with-kubernetes-and-jenkins
https://youtu.be/5unI7VPnASM
https://youtu.be/kfCe8TmOmxI


