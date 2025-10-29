# projeto-automacao-aws
Projeto de estudo de arquiteturas AWS com EC2, S3 e Lambda para fluxos de automação.

# Projeto de Automação AWS: Sem Parar e Bob's

Este repositório documenta duas arquiteturas de automação simples na AWS, demonstrando conceitos básicos de EC2, EBS, S3 e Lambda para processamento de tarefas em lote e em tempo real.

## Arquiteturas

Os diagramas abaixo ilustram os fluxos de automação para dois cenários de negócios distintos.

### 1. Automação "Sem Parar" (Processamento Batch com EC2)

Este fluxo representa uma automação baseada em servidor (EC2) que processa dados de transações. Este modelo é ideal para tarefas agendadas (batch), como consolidação de dados no final do dia ou geração de relatórios.

**Descrição do Fluxo (Diagrama):**
1.  **EC2 Instance:** Um servidor virtual (Instância EC2) está em execução, servindo como o cérebro da operação.
2.  **EBS Volume:** Um disco (Volume EBS) está anexado à instância. Ele armazena o sistema operacional, o script Python e os dados temporários.
3.  **Crontab Schedule:** Um agendador de tarefas (Crontab) dentro da própria instância EC2 é programado para disparar o script em horários específicos (ex: toda madrugada).
4.  **Python Script + Boto3:** No horário agendado, o script Python é executado. Ele usa a biblioteca Boto3 para interagir com serviços (se necessário) e contém a lógica para se comunicar com o sistema externo.
5.  **Sem Parar Automation:** O script envia os dados processados (ex: transações de pedágio) para o sistema final do Sem Parar.

<p style="font-size: 40px; color: lightblue;">
  Diagrama Sem Parar
</p>

<img width="801" height="411" alt="automacao-semparar" src="https://github.com/user-attachments/assets/694a4c69-2e93-41d1-bd43-9f2e158b2f8f" />


Este fluxo representa uma arquitetura "Serverless" (sem servidor) que processa pedidos de clientes em tempo real. Este modelo é ideal para processamento orientado a eventos, sendo altamente escalável e econômico.

**Descrição do Fluxo (Diagrama):**
1.  **S3 Bucket (bobs-order-queue):** Um cliente (ex: o aplicativo do Bob's) envia um novo pedido. Esse pedido é salvo como um novo arquivo (objeto) dentro deste "balde" (Bucket) S3, que atua como uma fila de entrada.
2.  **ObjectCreated (Event Trigger):** A chegada do novo arquivo no S3 dispara automaticamente (`Event Trigger`) uma função Lambda.
3.  **Lambda Function (ProcessOrder):** A função Lambda é executada instantaneamente. Seu código (ex: `ProcessOrder`) lê o arquivo do pedido, valida os dados e o formata.
4.  **DynamoDB (OrderDetails):** A função Lambda salva os detalhes do pedido já processados em um banco de dados DynamoDB, que é rápido e flexível.
5.  **Bob's Order Processing:** O sistema interno da cozinha do Bob's é notificado ou consulta o DynamoDB para buscar novos pedidos e iniciar o preparo.
6.  **SMS Notification:** (Opcional) A própria função Lambda também pode enviar uma notificação por SMS para o cliente, confirmando que o pedido foi recebido.

<div style="font-size: 40px; color: lightblue;">
  Diagrama Bobs
</div>


<img width="812" height="692" alt="automacao-bobs" src="https://github.com/user-attachments/assets/da638094-9058-403c-9f97-a55547101268" />

## Tecnologias Utilizadas

* **AWS EC2 (Elastic Compute Cloud):** Servidor virtual para executar a automação agendada.
* **AWS EBS (Elastic Block Store):** O "disco rígido" virtual para a instância EC2.
* **AWS S3 (Simple Storage Service):** Armazenamento de objetos usado como "fila" para receber os pedidos.
* **AWS Lambda:** Serviço de computação sem servidor para processar os pedidos em tempo real.
* **AWS DynamoDB:** Banco de dados NoSQL para armazenar os detalhes dos pedidos.
* **Crontab:** Agendador de tarefas baseado em Linux (usado dentro da EC2).
