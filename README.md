# Bagual Bank
Sistema financeiro simplificado construído em arquitetura de microsserviços, simulando operações bancárias básicas como criação de conta, depósito, saque e transferências.

No Rio Grande do Sul, bagual é um termo que serve pra descrever um cavalo xucro, selvagem, não domado. Mas quando usado pra se referir a pessoas, representa coragem, resiliência e autenticidade.

## Sobre o projeto
Tem como objetivo aplicar os conceitos e ferramentas utilizados em sistemas corporativos de médio/grande porte: comunicação entre serviços, consistência de dados distribuídos, testes automatizados e containerização.

## Microsserviços
O sistema é dividido em microsserviços independentes:
| SERVIÇO | RESPONSABILIDADE | STATUS |
|---|---|---|
| [auth](https://github.com/laressamoraes/bagual-auth)                 | Autenticação e autorização do Bagual Bank             | **Implementado** |
| [account](https://github.com/laressamoraes/bagual-account)      | Cadastro de contas, consulta de saldo, débito/crédito      | **Implementado** | 
| [transaction](https://github.com/laressamoraes/bagual-transaction)  | Depósitos, saques e transferências entre contas        | **Implementado** | 
| [notification](https://github.com/laressamoraes/bagual-notification) | Notificações assíncronas sobre transações realizadas  | **Implementado** | 

## Arquitetura
* `transaction` chama `account` via REST síncrono para processar débito/crédito;
* `transaction` publica eventos no Kafka ao concluir uma transação;
* `notification` consome esses eventos de forma assíncrona e persiste em um histórico de notificações.

## Tecnologias
**Microsserviços:**
- Java 21 + Spring Boot 3;
- Maven;
- PostgreSQL + Flyway;
- Apache Kafka;
- JUnit 5, Mockito e AssertJ.

**Autenticação:**
- Keycloak (OAuth2/JWT);
- PostgreSQL.

**Infraestrutura:**
- Docker e Docker Compose.

## Como executar
Cada microsserviço tem seu próprio `docker-compose.yml`. A ordem de subida importa, já que o Kafka está definido no `transaction`.

**1. Subir o `auth` (Keycloak):**
```bash
cd bagual-auth
docker compose up -d
```

**2. Configurar o Keycloak:**

Siga a seção "Configuração necessária" do README do [auth](https://github.com/laressamoraes/bagual-auth/blob/main/README.md). Ao final você terá o Client Secret do `bagual-client`.

**3. Subir o `account`:**
```bash
cd ../bagual-account
docker compose up --build -d
```

**4. Subir o `transaction`:**

Antes, crie o arquivo `.env` na raiz do projeto com o Client Secret (use o `.env.example` como modelo):

```bash
cd ../bagual-transaction
docker compose up --build -d
```

**5. Subir o `notification`:**
```bash
cd ../bagual-notification
docker compose up --build -d
```

## Como testar a API
Todos os endpoints exigem um token JWT emitido pelo Keycloak. O passo a passo para obter o token está no README do [auth](https://github.com/laressamoraes/bagual-auth#como-obter-um-token).

Com o token em mãos, use-o como Bearer Token nas chamadas, por exemplo:

* `POST http://localhost:8081/accounts` cria uma conta;
* `POST http://localhost:8082/transactions` cria uma transação;
* `GET http://localhost:8083/notifications` lista as notificações geradas.

O token expira em cerca de 5 minutos. Se receber `401`, basta gerar outro.
