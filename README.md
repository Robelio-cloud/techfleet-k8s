# TechFleet - Desafio Kubernetes: Ambiente de Produção Simulado

## Introdução

Este repositório documenta a configuração de um ambiente de produção simulado para a aplicação web **app-portal** utilizando Kubernetes local (Minikube). O objetivo é validar a alta disponibilidade, escalabilidade e resiliência da aplicação antes de movê-la para um ambiente de nuvem.

## Estrutura do Repositório

-   `/k8s/deployment.yaml`: Manifesto do Deployment que gerencia os Pods da aplicação.
-   `/k8s/service.yaml`: Manifesto do Service que expõe a aplicação para acesso externo.
-   `README.md`: Este documento com todo o passo a passo e evidências.

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

kubectl get namespaces
# O resultado deve listar o namespace "producao"

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
# Aplicar os manifestos do diretório k8s/
kubectl apply -f k8s/deployment.yaml
kubectl apply -f k8s/service.yaml
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

*(Insira aqui um print da saída do comando `kubectl get all -n producao`)*

**2. Acesso à Página Customizada**

*(Insira aqui um print da saída do comando `curl` ou do navegador mostrando a mensagem personalizada)*

## Testes Realizados

### Teste de Escalabilidade

Para testar a escalabilidade, o Deployment foi escalado de 3 para 5 réplicas utilizando o comando:

```bash
kubectl scale deployment app-portal --replicas=5 -n producao
```

**Evidência:**

*(Insira aqui um print da saída do `kubectl get pods -n producao` mostrando os 5 pods rodando)*

### Teste de Resiliência

Para testar a resiliência, um dos 5 Pods foi deletado manualmente para simular uma falha.

```bash
# Comando usado para deletar um pod (substituir com o nome real)
kubectl delete pod app-portal-xxxxxxxxxx-yyyyy -n producao
```

O ReplicaSet do Kubernetes detectou a ausência do Pod e iniciou um novo container imediatamente para manter o estado desejado de 5 réplicas, demonstrando a capacidade de auto-recuperação do sistema.

**Evidência:**

*(Insira aqui um print da saída do `kubectl get pods -n producao -w` mostrando o pod antigo em "Terminating" e o novo sendo criado)*