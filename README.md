# ✂️ EncurtaUrl - encurtador de URLs simples desenvolvido em Spring Boot

<div align="center">

   ![Java](https://img.shields.io/badge/Java-ED8B00?logo=openjdk&logoColor=white)
   ![Spring Boot](https://img.shields.io/badge/Spring%20Boot-6DB33F?logo=spring&logoColor=white)
   ![PostgreSQL](https://img.shields.io/badge/PostgreSQL-316192?logo=postgresql&logoColor=white)
   ![JWT](https://img.shields.io/badge/JWT-000000?logo=JSON%20web%20tokens&logoColor=white)
   ![Docker](https://img.shields.io/badge/Docker-257bd6?logo=docker&logoColor=white)
   
   ![Imagem demonstrando o funcionamento da API, informando uma URL alvo e recebendo a URL encurtada](https://i.imgur.com/0rSjpce.png)

URLs muito longas podem ser difíceis de compartilhar e pouco práticas em algumas situações. O EncurtaURL resolve esse problema ao armazenar o link original e gerar uma versão curta, facilitando o compartilhamento e o uso.
   
</div>

## 📦 Pré-requisitos
#### Manual (sem Docker)
- 🌿 **Git** *(opcional)* [(Download)](https://git-scm.com/install/)
- ☕ **Java JDK 17+** [(Download)](https://openjdk.org/)
- 📦 **Maven** [(Download)](https://maven.apache.org/download.cgi)
- 🐘 **PostgreSQL 16+** [(Download)](https://www.postgresql.org/download/)

#### O caminho fácil (com Docker) *(recomendado)*
- 🌿 **Git** *(opcional)* [(Download)](https://git-scm.com/install/)
- 🐋 **Docker e Docker Compose** [(Download)](https://www.docker.com/get-started/)

## ⚙️ Configuração
### Manual (sem Docker)
1. Instale o Java JDK 17+, Maven e o PostgreSQL 16+
2. Configure o banco de dados
3. Clone o repositório (pelo website ou com o comando `git clone https://github.com/thiishy/EncurtaUrl.git` caso tenha instalado o Git)
4. Na raiz do repositório, crie um arquivo **.env** seguindo o template que está no arquivo **.env.example**:
   - **DB_NAME**: Nome do banco de dados
   - **DB_USERNAME**: Nome do seu usuário no banco de dados
   - **DB_PASSWORD**: Senha do seu usuário no banco de dados
   - **DB_URL**: URL da conexão JDBC com o banco de dados *(ex: jdbc:postgresql://localhost/db_sistema)*
   - **ENCURTAURL_BASE_URL**: A URL base do seu servidor. Deve estar no formato correto, essa vai ser a URL apresentada pela API junto com o código curto após o encurtamento. *(ex: https://google.com, http://127.0.0.1:8080)*
   - **JWT_SECRET**: Segredo JWT de 256 bits, **NÃO VAZE** e rotacione sempre que puder. *(ex: LzR7ceG3a1BhjQLzxvmxo2GqSuoSr7d3rAw1XGkgss9NJTL7i68btNlMGo6F6jyO)*
5. Rode o comando `mvn clean install` para fazer a build do projeto
6. Para inicializar a aplicação, rode `mvn spring-boot:run` e a aplicação irá inicializar

> [!NOTE]
> Se você decidir fazer a build do projeto com o banco de dados desligado, use o parâmetro `-DskipTests` no comando para evitar erros de build.

### O caminho fácil (com Docker)
1. Instale o Docker e o Docker Compose
2. Clone o repositório (pelo website ou com o comando `git clone https://github.com/thiishy/EncurtaUrl.git` caso tenha instalado o Git)
3. Na raiz do repositório, crie um arquivo **.env** seguindo o template que está no arquivo **.env.example**. Note que o banco de dados será **configurado automaticamente** pelo Docker Compose com os dados que você escolher aqui:
   - **DB_NAME**: Nome do banco de dados
   - **DB_USERNAME**: Nome do seu usuário no banco de dados
   - **DB_PASSWORD**: Senha do seu usuário no banco de dados
   - **DB_URL**: URL da conexão JDBC com o banco de dados. Não use `localhost`, use `db` para referenciar o banco de dados. *(ex: jdbc:postgresql://db/db_sistema)*
   - **ENCURTAURL_BASE_URL**: A URL base do seu servidor. Deve estar no formato correto, essa vai ser a URL apresentada pela API junto com o código curto após o encurtamento. *(ex: https://google.com, http://127.0.0.1:8080)*
   - **JWT_SECRET**: Segredo JWT de 256 bits, **NÃO VAZE** e rotacione sempre que puder. *(ex: LzR7ceG3a1BhjQLzxvmxo2GqSuoSr7d3rAw1XGkgss9NJTL7i68btNlMGo6F6jyO)*
4. Ainda na raiz do repositório, rode o comando `docker-compose up --build` e aguarde
5. Após a build, a aplicação irá inicializar automaticamente. Você pode parar os serviços com `docker-compose stop` ou apagar os containers com `docker-compose down` (**não apaga** os volumes) ou `docker-compose down -v` (**apaga** os volumes)

## 🛠️ Funcionalidades
- Login e cadastro de usuários
- Encurtar URLs
- Listar URLs encurtadas
- Apagar URLs encurtadas

## 📍 Lista de endpoints da API

🔒 significa que o endpoint requer **autorização** por meio de um token JWT válido no cabeçalho da requisição. *(ex: Authorization: Bearer eyJhbGciOiJIUzI1NiIs...)*

<details>
   <summary>🔑 <b>Auth</b></summary>

### 1. Login

*   **Método:** `POST`
*   **Caminho:** `/auth/login`
*   **Descrição:** Endpoint de autenticação de usuários.
*   **Exemplo de requisição:**

       ```json
            {
                "username": "teste@teste.com",
                "password": "testando"
            }
    
*   **Resposta:**
    *   `200 OK` *(sucesso)*
    *   `401 Unauthorized` *(falha no login)*
 
### 2. Registro

*   **Método:** `POST`
*   **Caminho:** `/auth/register`
*   **Descrição:** Endpoint de registro de usuários.
*   **Exemplo de requisição:**

       ```json
            {
                 "name": "Conta Teste",
                 "username": "teste@teste.com",
                 "password": "testando",
                 "confirmPassword": "testando"
            }
    
*   **Respostas:**
    *   `200 OK` *(sucesso)*
    *   `422 Unprocessable Entity` *(dados inválidos)*
   
</details>

<details>
   <summary>🌐 <b>URLs</b></summary>

### 1. Listar todas as URLs do usuário [🔒]

*   **Método:** `GET`
*   **Caminho:** `/me/urls?page=0 (opcional, padrão: 0)`
*   **Descrição:** Lista todas as URLs registradas/encurtadas divididas em páginas com 10 registros cada por padrão.
*   **Respostas:**
    *   `200 OK` *(sucesso)*
    *   `401 Unauthorized` *(não autorizado)*

### 2. Excluir uma URL encurtada [🔒]

*   **Método:** `DELETE`
*   **Caminho:** `/me/urls/delete/{uuid}`
*   **Descrição:** Recebe o UUID da URL encurtada e realiza a exclusão caso ela exista.
*   **Respostas:**
    *   `204 No Content` *(sucesso)*
    *   `404 Not Found` *(URL não encontrada ou pertence a outro usuário)*
    *   `401 Unauthorized` *(não autorizado)*

### 3. Registrar/encurtar uma URL [🔒]

*   **Método:** `POST`
*   **Caminho:** `/me/shorten`
*   **Descrição:** Recebe a URL alvo (enviada no corpo da requisição em formato JSON) e a encurta.
*   **Exemplo de requisição:**

       ```json
            {
                "targetUrl": "https://google.com"
            }
    
*   **Respostas:**
    *   `201 Created` *(sucesso)*
    *   `400 Bad Request` *(URL inválida)*
    *   `401 Unauthorized` *(não autorizado)*

### 4. Redirecionamento

*   **Método:** `GET`
*   **Caminho:** `/u/{shortCode}`
*   **Respostas:**
    *   `302 Found` *(sucesso)*
    *   `404 Not Found` *(URL não existe)*
    *   `410 Gone` *(URL marcada como inativa)*

</details>

<br>

<img src="https://i.imgur.com/3uzTDPX.png" width="600px" alt="Imagem demonstrando a API documentada com Swagger UI">

> [!TIP]
> Para testar e saber mais detalhes, acesse o **Swagger UI** em http://127.0.0.1:8080/swagger-ui/index.html *(substitua localhost pelo seu domínio, caso esteja hospedado)*

## 🧩 Dependências
- Spring Web
- Spring Security
- Spring Data JPA
- PostgreSQL Driver
- Lombok
- SpringDoc OpenAPI Starter WebMVC UI
- Flyway
- Auth0 JWT
- Bouncy Castle Provider
- Apache Commons Validator

Projeto inicializado com Maven - confira o **pom.xml** para mais detalhes.

## 📋 Próximos passos (fora do escopo do MVP)

Este projeto foi finalizado como um MVP para fins de estudo e portfólio.

Os itens abaixo representam possíveis evoluções pensando em um cenário de produção real.

- **Caching**: melhorar a performance em alto volume de requisições
- **Rate limiting**: proteção contra abuso
- **Observabilidade**: logs e métricas

---

Feito com ♥ por [thiishy](https://github.com/thiishy)

Projeto desenvolvido para fins de estudo.
