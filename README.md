# TechFleet - Desafio Kubernetes: Ambiente de Produção Simulado

## Introdução

Este repositório documenta a configuração de um ambiente de produção simulado para a aplicação web **app-portal** utilizando Kubernetes local (Minikube). O objetivo é validar a alta disponibilidade, escalabilidade e resiliência da aplicação antes de movê-la para um ambiente de nuvem.

## Estrutura do Repositório

```
├── images            # Pasta com prints de telas de comandos e URL da aplicação 
├── deployment.yaml    # Deployment com 3 réplicas + ConfigMap
├── index.html         # página html simples para comprovar a funcionalidade da aplicação
├── service.yaml      # Service NodePort com configurações no localhost
└── README.md         # Documentação do projeto

```

## Passo a Passo e Comandos Executados

**1. Preparação do Ambiente**

```bash
# Iniciar cluster local
minikube start

# Criar o namespace
kubectl create namespace producao
```
![image](/images/01-k8s.png)

![image](/images/02-k8s.png)

# O resultado deve listar o namespace "producao"

kubectl get namespaces

![image](/images/03-k8s.png)

**2. Customização da Aplicação com ConfigMap**

```bash
# Criar arquivo index.html local
echo '<!DOCTYPE html><html><head><title>Bem-vindo a TechFleet!</title></head><body><h1>App-Portal da TechFleet rodando em Kubernetes!</h1><p>Este ambiente de producao simulado foi configurado com sucesso.</p></body></html>' > index.html

# Criar ConfigMap a partir do arquivo
kubectl create configmap app-portal-config --from-file=index.html -n producao
```

![image](/images/04-k8s.png)

**3. Deploy da Aplicação**

```bash
# Aplicar os manifestos (arquivos na raiz do repositório)
kubectl apply -f ./deployment.yaml
kubectl apply -f ./service.yaml
```

![image](/images/05-k8s.png)

![image](/images/06-k8s.png)


**4. Verificação**

```bash
# Verificar todos os recursos criados
kubectl get all -n producao

# Obter a URL de acesso
minikube service app-portal-service -n producao --url

# Testar o acesso via terminal
curl http://127.0.0.1:34115/
```
![image](/images/07-k8s.png)

![image](/images/08-k8s.png)

No navegador colocar a URL http://127.0.0.1:34115/

![image](/images/09-k8s.png)

![image](/images/10-k8s.png)


## Evidências de Funcionamento

**1. Recursos no Cluster (Pods, Service, etc.)**

![image](/images/07-k8s.png)

**2. Acesso à Página Customizada**

![image](/images/11-k8s.png)

Curl http://192.168.49.2:30080 NodePort, porta externa 30080

![image](/images/12-k8s.png)

NodePort, porta http://localhost:30080 no navegador

![image](/images/24-k8s.png)

## Testes Realizados

### Teste de Escalabilidade

Para testar a escalabilidade, o Deployment foi escalado de 3 para 5 réplicas utilizando o comando:

kubectl get pods -n producao -> Três pods em operação

![image](/images/21-k8s.png)

```bash
kubectl scale deployment app-portal --replicas=5 -n producao
```
**Evidência 5 pods rodando:**

![image](/images/22-k8s.png)


### Teste de Resiliência

Para testar a resiliência, TODOS os pods serão apagados manualmente para simular uma falha.

```bash
# Comando usado para deletar todos os pod
kubectl delete pods -l app=app-portal -n producao
```
![image](/images/25-k8s.png)


O ReplicaSet do Kubernetes detectou a ausência do Pod e iniciou todos os 5 pods imediatamente para manter o estado desejado de 5 réplicas, demonstrando a capacidade de auto-recuperação do sistema.

**Evidência:**

saída do `kubectl get pods -n producao -w` mostrando o status dos pods em "Terminating" e os novos sendo criados

![image](/images/26-k8s.png)
![image](/images/27-k8s.png)
