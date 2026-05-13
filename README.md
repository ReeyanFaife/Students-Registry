# Student Registry

O Student Registry e um sistema simples para gerir o registo de estudantes. A aplicacao permite autenticar o utilizador e trabalhar com informacoes de estudantes guardadas numa base de dados PostgreSQL.

O projeto e composto por um frontend em Vue, um backend em PHP e uma base de dados PostgreSQL, executados localmente com Docker Compose e pela web atraves do link https://students-registry-frontend.onrender.com/.

## Relatório do sistema

- Funcionalidade principal: registrar, listar, editar e remover estudantes.
- Login e registro: o usuario pode autenticar-se e criar uma conta com email e senha.
- API REST simples: endpoints para CRUD de estudantes e autenticação de usuarios.
- Relatorio de estudantes: gera um download CSV com os registros atuais.
- Persistencia: dados salvos em PostgreSQL, inicializados automaticamente por `database/init.sql`.

## Tecnologias usadas

- Frontend:
  - `Vue 3` para a interface reativa.
  - `Vite` como bundler de desenvolvimento e build.
  - `Axios` para chamadas HTTP ao backend.
- Backend:
  - `PHP 8.2` com Apache para servir a API.
  - `PDO` para conecção com PostgreSQL.
- Base de dados:
  - `PostgreSQL 16` para armazenar estudantes e usuarios.
- Infraestrutura:
  - `Docker` e `Docker Compose` para orquestrar frontend, backend e banco de dados.

## Pre-requisitos

Antes de iniciar, confirme que tem instalado:

- Docker
- Docker Compose

Para verificar:
```bash
docker --version
docker compose version
```
## Como executar localmente

Na raiz do projeto, execute:

```bash
docker compose up --build
```
Este comando cria e inicia os seguintes servicos:

- `db`: base de dados PostgreSQL 16
- `backend`: API PHP exposta em `http://localhost:8000`
- `frontend`: aplicacao Vue exposta em `http://localhost:3000`

Depois de os containers iniciarem, abra no navegador:

```text
http://localhost:3000
```

## Executar em segundo plano

Se quiser deixar os containers a correr em background:

```bash
docker compose up -d --build
```

## Ver logs

Para ver os logs de todos os servicos:

```bash
docker compose logs -f
```

Para ver logs de um servico especifico:

```bash
docker compose logs -f frontend
docker compose logs -f backend
docker compose logs -f db
```

## Parar o projeto

Para parar os containers:

```bash
docker compose down
```

## Reiniciar do zero

A base de dados usa o volume Docker `dados-bd`, por isso os dados persistem mesmo depois de executar `docker compose down`.

Se quiser apagar os dados e recriar a base de dados a partir de `database/init.sql`, execute:

```bash
docker compose down -v
docker compose up --build
```

## Portas usadas

| Servico  | URL local               |
|----------|-------------------------|
| Frontend | http://localhost:3000   |
| Backend  | http://localhost:8000   |
| PostgreSQL | Apenas na rede Docker |

## Estrutura principal

```text
.
|-- backend/
|   |-- Dockerfile
|   |-- db.php
|   `-- index.php
|-- database/
|   `-- init.sql
|-- frontend/
|   |-- Dockerfile
|   |-- index.html
|   |-- nginx.conf
|   |-- package.json
|   |-- vite.config.js
|   `-- src/
|       |-- App.vue
|       |-- Login.vue
|       `-- main.js
|-- README.md
`-- docker-compose.yml
```
