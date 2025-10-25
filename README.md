# projeto-automacao-aws
Projeto de estudo de arquiteturas AWS com EC2, S3 e Lambda para fluxos de automação.

# Projeto de Automação AWS: Sem Parar e Bob's

Este repositório documenta duas arquiteturas de automação simples na AWS, demonstrando conceitos básicos de EC2, EBS, S3 e Lambda para processamento de tarefas em lote e em tempo real.

## Arquiteturas

Os diagramas abaixo ilustram os fluxos de automação para dois cenários de negócios distintos.

### 1. Automação "Sem Parar" (Processamento Batch com EC2)

Este fluxo representa uma automação baseada em servidor (EC2) que processa dados de transações de pedágio. Este modelo é ideal para tarefas agendadas (batch), como consolidação de dados no final do dia ou geração de relatórios.

**Fluxo da Automação:**
1.  **Cliente (Veículo):** O cliente passa pela cancela de pedágio.
2.  **Sensor/Portagem:** O sistema de portagem registra a passagem e envia os dados da transação.
3.  **EC2 Instance:** Um servidor virtual recebe e armazena esses dados em seu disco (Volume EBS).
4.  **Crontab (Agendador):** Dentro da EC2, um agendador (Crontab do Linux) executa um script Python em um horário definido (ex: toda madrugada).
5.  **Script Python:** O script processa todas as transações acumuladas, calcula os débitos e se comunica com o sistema central do Sem Parar para atualizar as contas dos clientes.

![Diagrama da Automação Sem Parar](./images/automacao-semparar.png)

### 2. Automação "Bob's" (Processamento de Eventos com S3/Lambda)

Este fluxo representa uma arquitetura "Serverless" (sem servidor) que processa pedidos de clientes em tempo real. Este modelo é ideal para processamento orientado a eventos, sendo altamente escalável e econômico.

**Fluxo da Automação:**
1.  **Cliente (App Bob's):** O cliente faz um pedido através do aplicativo.
2.  **API Gateway:** O aplicativo envia o pedido (ex: um arquivo JSON) para um "portão de entrada" da AWS.
3.  **S3 Bucket (FilaDePedidos):** O API Gateway salva o arquivo do pedido em um S3 Bucket, que atua como uma fila de entrada.
4.  **Event Trigger (S3 -> Lambda):** O S3 automaticamente detecta a chegada do novo arquivo (`ObjectCreated`) e dispara uma função Lambda.
5.  **Lambda Function (ProcessarPedido):** A função Lambda (código que roda sob demanda) lê o arquivo do pedido, valida os dados e o formata.
6.  **DynamoDB (Banco de Dados):** A Lambda insere os detalhes do pedido em um banco de dados NoSQL para registro.
7.  **Sistema Bob's:** O sistema interno do restaurante é notificado (ou consulta o DynamoDB) e o pedido é enviado para a fila de preparo na cozinha.

![Diagrama da Automação Bob's](./images/automacao-bobs.png)

## Tecnologias Utilizadas

* **AWS EC2 (Elastic Compute Cloud):** Servidor virtual para executar a automação agendada.
* **AWS EBS (Elastic Block Store):** O "disco rígido" virtual para a instância EC2.
* **AWS S3 (Simple Storage Service):** Armazenamento de objetos usado como "fila" para receber os pedidos.
* **AWS Lambda:** Serviço de computação sem servidor para processar os pedidos em tempo real.
* **AWS API Gateway:** Ponto de entrada (API) para receber os pedidos do aplicativo.
* **AWS DynamoDB:** Banco de dados NoSQL para armazenar os detalhes dos pedidos.
* **Crontab:** Agendador de tarefas baseado em Linux (usado dentro da EC2).
