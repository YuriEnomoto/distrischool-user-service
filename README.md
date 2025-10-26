DistriSchool - User Service

Microserviço responsável pela gestão de usuários da plataforma DistriSchool — uma solução distribuída de gestão escolar.

Este serviço é o produtor de eventos central para a criação e atualização de usuários, notificando outros microsserviços via RabbitMQ.

🚀 Tecnologias

Java 17

Spring Boot 3.5.6

Spring Data JPA

Spring Security

Spring AMQP (RabbitMQ)

Flyway (Migrations)

PostgreSQL

Docker & Docker Compose

Lombok

Actuator (Healthcheck)

⚙️ Estrutura do Projeto

src/
├── main/
│   ├── java/br/com/distrischool/user_service
│   │   ├── controller/ # Endpoints REST
│   │   ├── dto/ # Objetos de requisição e resposta
│   │   ├── entity/ # Entidades JPA (User, etc)
│   │   ├── repository/ # Interfaces de acesso ao banco
│   │   ├── service/ # Regras de negócio
│   │   └── config/ # Segurança, CORS, RabbitMQ e Beans gerais
│   └── resources/
│       ├── application.yml
│       └── db/migration/ # Scripts Flyway (ex: V1__create_users_table.sql)
└── test/ # Testes unitários e de integração


🐳 Rodando com Docker

Certifique-se de ter Docker e Docker Compose instalados.

# Clonar o repositório
git clone [https://github.com/YuriEnomoto/distrischool-user-service.git](https://github.com/YuriEnomoto/distrischool-user-service.git)
cd distrischool-user-service/user-service/user-service

# Subir os containers (incluindo o RabbitMQ)
docker compose up -d --build


📦 Componentes que sobem:

user-service → aplicação Spring Boot (porta interna: 8080)

users-db → banco PostgreSQL (porta interna: 5432)

rabbitmq → Servidor RabbitMQ, essencial para a mensageria assíncrona.

🧩 Migrations (Flyway)

Os scripts SQL estão em: src/main/resources/db/migration/

O Flyway é executado automaticamente ao subir o container, criando a tabela users e o histórico em flyway_schema_history.

🔍 Endpoints Principais (Acesso via API Gateway - Porta 8088)

Método

Endpoint

Descrição

GET

/actuator/health

Verifica se o serviço está UP

POST

/api/v1/users

Cria um novo usuário

GET

/api/v1/users

Lista todos os usuários (paginado)

GET

/api/v1/users/{id}

Busca usuário por ID

PUT

/api/v1/users/{id}

Atualiza dados de um usuário

DELETE

/api/v1/users/{id}

Remove um usuário

📬 Exemplos de Requisição (Postman)

⚠️ OBS: Todas as requisições REST são feitas na porta do API Gateway (8088), que irá rotear para o user-service.

➕ Criar Usuário (POST)

Endpoint: POST http://localhost:8088/api/v1/users

{
  "name": "Yuri",
  "email": "Yuri@example.com",
  "password": "SenhaForte!",
  "role": "ADMIN"
}


🔎 Listar Usuários (GET)

Endpoint: GET http://localhost:8088/api/v1/users?page=0&size=10

✏️ Atualizar Usuário (PUT)

Endpoint: PUT http://localhost:8088/api/v1/users/1

{
  "name": "Yuri Enomoto",
  "password": "NovaSenha!"
}


🗑️ Deletar Usuário (DELETE)

Endpoint: DELETE http://localhost:8088/api/v1/users/1

✉️ Mensageria Assíncrona (RabbitMQ)

O user-service atua como Produtor de Eventos.

Evento Publicado: Após a criação de um usuário (requisição POST), o serviço publica um evento com os dados do usuário.

Exchange: users.exchange (tipo topic).

Routing Key: Depende da operação (ex: user.created).

🔐 Segurança e Criptografia

As senhas são criptografadas com BCrypt.

O campo password_hash nunca é retornado pela API.

Exemplo de Hash no Banco: $2b$10$4YVg45c1m8a5J1o0dX2nZ.2p4x1kV9hZFCt4oHq0vT9X0w1qf1U2a (Hashes válidos começam com $2a$ ou $2b$ e possuem 60 caracteres).
