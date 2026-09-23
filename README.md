# Bagual Bank
Sistema financeiro simplificado construído em arquitetura de microsserviços, simulando operações bancárias básicas como criação de conta, depósito, saque e transferências.

No Rio Grande do Sul, bagual é um termo que serve pra descrever um cavalo xucro, selvagem, não domado. Mas quando usado pra se referir a pessoas, representa coragem, resiliência e autenticidade.

# Sobre o projeto
Tem como objetivo aplicar os conceitos e ferramentas utilizados em sistemas corporativos de médio/grande porte: comunicação entre serviços, consistência de dados distribuídos, testes automatizados e containerização.

# Microsserviços
O sistema é dividido em microsserviços independentes:
| SERVIÇO | RESPONSABILIDADE | STATUS | LINK |
|---|---|---|---|
| account      | Cadastro de contas, consulta de saldo, débito/crédito | **Implementado** | (https://github.com/laressamoraes/bagual-account)      |
| transaction  | Depósitos, saques e transferências entre contas       | **Implementado** | (https://github.com/laressamoraes/bagual-transaction)  |
| notification | Notificações assíncronas sobre transações realizadas  | **Implementado** | (https://github.com/laressamoraes/bagual-notification) |

# Arquitetura
* `transaction` chama 'account' via REST síncrono para processar débito/crédito;
* `transaction` publica eventos no Kafka ao concluir uma transação;
* `notification` consome esses eventos de forma assíncrona e persiste em um histórico de notificações.

# Tecnologias
- Java 21 + Spring Boot 3;
- Maven;
- PostgreSQL + Flyway;
- Apache Kafka;
- JUnit 5, Mockito e AssertJ;
- Docker e Docker Compose.

# Como executar
Cada microsserviço tem seu próprio `docker-compose.yml`. A ordem de subida importa, já que o Kafka está definido no 'transaction'.

```bash
# 1. account
cd bagual-account
docker compose up -d

# 2. transaction
cd ../bagual-transaction
docker compose up -d

# 3. notification
cd ../bagual-notification
docker compose up -d
```
