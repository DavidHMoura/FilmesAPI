# Documentação da FilmesAPI

Este repositório contém uma API RESTful para gerenciamento de filmes, desenvolvida em .NET. A API permite realizar operações CRUD (Criar, Ler, Atualizar, Deletar) em registros de filmes, utilizando um banco de dados MySQL e o Entity Framework Core para persistência de dados. O projeto foi desenvolvido como parte de um curso da Alura.

## Tecnologias Utilizadas

*   .NET (versão 10.0)
*   ASP.NET Core
*   Entity Framework Core
*   MySQL (Banco de Dados)
*   Docker (Opcional para ambiente de banco de dados)
*   AutoMapper
*   Swagger/OpenAPI para documentação da API

## Estrutura do Projeto

```
. 
├── Config
│   └── SwaggerExtension.cs
├── Controllers
│   └── FilmeController.cs
├── Data
│   ├── Dtos
│   │   ├── CreateFilmeDto.cs
│   │   ├── ReadFilmeDto.cs
│   │   └── UpdateFilmeDto.cs
│   └── FilmeContext.cs
├── Migrations
│   ├── 20260210165153_CriandoTabelaDeFilme.Designer.cs
│   ├── 20260210165153_CriandoTabelaDeFilme.cs
│   └── FilmeContextModelSnapshot.cs
├── Models
│   └── Filme.cs
├── Profiles
│   └── FilmeProfile.cs
├── Properties
│   └── launchSettings.json
├── FilmesApi.csproj
├── FilmesApi.http
├── FilmesApi.slnx
├── Program.cs
├── WeatherForecast.cs
├── appsettings.Development.json
└── appsettings.json
```

## Modelos de Dados

### Filme

Representa um filme na base de dados.

| Propriedade | Tipo    | Descrição                                   | Restrições                                     |
| :---------- | :------ | :------------------------------------------ | :--------------------------------------------- |
| `Id`        | `int`   | Identificador único do filme                | Obrigatório                                    |
| `Titulo`    | `string`| Título do filme                             | Obrigatório                                    |
| `Genero`    | `string`| Gênero do filme                             | Obrigatório, máximo de 50 caracteres           |
| `Duracao`   | `int`   | Duração do filme em minutos                 | Obrigatório, entre 70 e 600 minutos            |

## DTOs (Data Transfer Objects)

### `CreateFilmeDto`

Utilizado para a criação de um novo filme. Contém as propriedades `Titulo`, `Genero` e `Duracao`.

### `ReadFilmeDto`

Utilizado para a leitura (retorno) de informações de um filme. Contém as propriedades `Id`, `Titulo`, `Genero` e `Duracao`.

### `UpdateFilmeDto`

Utilizado para a atualização de um filme. Contém as propriedades `Titulo`, `Genero` e `Duracao`.

## Endpoints da API

A API `FilmesAPI` expõe os seguintes endpoints para a manipulação de filmes:

### `POST /Filme`

Adiciona um novo filme ao banco de dados.

*   **Requisição:**
    *   **Método:** `POST`
    *   **URL:** `/Filme`
    *   **Corpo da Requisição:** `CreateFilmeDto` (JSON)
        ```json
        {
            "titulo": "Nome do Filme",
            "genero": "Ação",
            "duracao": 120
        }
        ```
*   **Resposta:**
    *   **Status Code:** `201 Created`
    *   **Corpo da Resposta:** `ReadFilmeDto` (JSON) do filme criado, incluindo o `Id` gerado.

### `GET /Filme`

Recupera uma lista de filmes com paginação opcional.

*   **Requisição:**
    *   **Método:** `GET`
    *   **URL:** `/Filme?skip={quantidade_a_pular}&take={quantidade_a_pegar}`
    *   **Parâmetros de Query:**
        *   `skip` (opcional): Número de filmes a serem ignorados (padrão: 0).
        *   `take` (opcional): Número máximo de filmes a serem retornados (padrão: 50).
*   **Resposta:**
    *   **Status Code:** `200 OK`
    *   **Corpo da Resposta:** `IEnumerable<ReadFilmeDto>` (JSON) contendo a lista de filmes.

### `GET /Filme/{id}`

Recupera um filme específico pelo seu ID.

*   **Requisição:**
    *   **Método:** `GET`
    *   **URL:** `/Filme/{id}`
*   **Resposta:**
    *   **Status Code:** `200 OK` se o filme for encontrado.
    *   **Status Code:** `404 Not Found` se o filme não for encontrado.
    *   **Corpo da Resposta:** `ReadFilmeDto` (JSON) do filme encontrado.

### `PUT /Filme/{id}`

Atualiza completamente um filme existente pelo seu ID.

*   **Requisição:**
    *   **Método:** `PUT`
    *   **URL:** `/Filme/{id}`
    *   **Corpo da Requisição:** `UpdateFilmeDto` (JSON)
        ```json
        {
            "titulo": "Novo Título do Filme",
            "genero": "Comédia",
            "duracao": 90
        }
        ```
*   **Resposta:**
    *   **Status Code:** `204 No Content` se a atualização for bem-sucedida.
    *   **Status Code:** `404 Not Found` se o filme não for encontrado.

### `PATCH /Filme/{id}`

Atualiza parcialmente um filme existente pelo seu ID.

*   **Requisição:**
    *   **Método:** `PATCH`
    *   **URL:** `/Filme/{id}`
    *   **Corpo da Requisição:** `JsonPatchDocument<UpdateFilmeDto>` (JSON)
        ```json
        [
            {
                "op": "replace",
                "path": "/titulo",
                "value": "Título Parcialmente Atualizado"
            }
        ]
        ```
*   **Resposta:**
    *   **Status Code:** `204 No Content` se a atualização for bem-sucedida.
    *   **Status Code:** `404 Not Found` se o filme não for encontrado.
    *   **Status Code:** `400 Bad Request` se o corpo da requisição for inválido.

### `DELETE /Filme/{id}`

Exclui um filme existente pelo seu ID.

*   **Requisição:**
    *   **Método:** `DELETE`
    *   **URL:** `/Filme/{id}`
*   **Resposta:**
    *   **Status Code:** `204 No Content` se a exclusão for bem-sucedida.
    *   **Status Code:** `404 Not Found` se o filme não for encontrado.

## Configuração do Banco de Dados

A API utiliza o **MySQL** como banco de dados. A conexão é configurada no arquivo `appsettings.json`:

```json
{
  "ConnectionStrings": {
    "FilmeConnection": "server=localhost;database=filme;user=root;password=root"
  }
}
```

### Rodando o Banco com Docker

Com o MySQL configurado exatamente para as necessidades deste projeto, execute o seguinte comando:

```bash
docker run --name bancoFilmes -e MYSQL_DATABASE=filme -e MYSQL_ROOT_PASSWORD=root -p 3306:3306 -d mysql:oraclelinux9
```

Este comando irá:
*   Criar um container chamado `bancoFilmes`.
*   Definir o nome do banco de dados como `filme`.
*   Definir a senha do usuário `root` como `root`.
*   Mapear a porta `3306` do container para a porta `3306` da sua máquina.

## Como Executar o Projeto

Para executar a `FilmesAPI` localmente, siga os passos abaixo:

1.  **Clone o repositório:**
    ```bash
    git clone https://github.com/Tomas025/FilmesAPI.git
    cd FilmesAPI
    ```
2.  **Configure o banco de dados:**
    *   Inicie o banco de dados (via Docker ou instalação local).
    *   Execute as migrações do Entity Framework Core para criar as tabelas:
        ```bash
        dotnet ef database update
        ```
3.  **Restaure as dependências e execute a aplicação:**
    ```bash
    dotnet restore
    dotnet run
    ```

A API estará disponível em `https://localhost:7023` (ou outra porta configurada no `launchSettings.json`). A documentação interativa do Swagger estará acessível em `https://localhost:7023/swagger/index.html`.

## Licença

Este projeto está licenciado sob a licença MIT.
