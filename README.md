# Criando um sistema de orçamento, utilizando CQRS, Quarkus, Kafka e deploy EKS

### Objetivo: Projeto desenvolvido para aplicação de conhecimentos no Bootcamp Banco Inter da Digital Innovation One:

## Stack utilizada
  * Banco de dados PostgreSQL
  * Elastic Search
  * Kubernetes
  * Kafka Server
  * Quarkus
  * Docker

## Sobre Segregação de responsabilidade de consulta de comando (Command Query Responsibility Segregation - CQRS)

De acordo com [Martin Folwer] (https://martinfowler.com/bliki/CQRS.html)
> Em seu cerne está a noção de que você pode usar um modelo diferente para atualizar informações do modelo que você usa para ler informações.
> Para algumas situações, essa separação pode ser valiosa, mas é necessário atenção, pois para a maioria dos sistemas, CQRS adiciona complexidade arriscada.

## A aplicação

Simula um cenário de conta bancária em que um usuário final adiciona uma transação de receita ou despesa e é processado em uma fonte de eventos ascíncrona e arquitetura CQRS para recalcular o saldo da conta bancária do usuário. O usuário também pode solicitar o saldo de sua conta.

## Implantar os serviços externos

```
docker-compose up -d --build
```

Ele implantará quatro contêineres docker em seu ambiente com MongoDB, PostgreSQL, Kafka e Zookepper (exigido por Kafka)

Depois de implantar o Kafka, você precisará [criar o tópico no cluster Kafka](https://kafka.apache.org/quickstart).

## Testando o aplicativo

#### Executando uma solicitação CURL para criar uma transação de receita
```
curl -X POST -H "Content-Type: application/json" -d @ income-transaction.json http://localhost:8080/transactions
```
#### Executando uma solicitação CURL para criar uma transação de despesas
```
curl -X POST -H "Content-Type: application/json" -d @ expens-transaction.json http://localhost:8080/transactions
```
#### Executando solicitação CURL para buscar o equilíbrio
```
curl http://localhost:8081/balance\?accountId\=wesley|json_pp
```
#### Executando [K6's] (https://k6.io) teste de desempenho simples
````
k6 run --vus 10 --duration 60s performance-tests/income.js
k6 run --vus 10 --duration 60s performance-tests/expens.js
````
