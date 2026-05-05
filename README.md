# Student Registry

O Student Registry e um sistema simples para gerir o registo de estudantes. A aplicacao permite autenticar o utilizador e trabalhar com informacoes de estudantes guardadas numa base de dados MySQL.

O projeto e composto por um frontend em Vue, um backend em PHP e uma base de dados MySQL, executados localmente com Docker Compose.

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

- `db`: base de dados MySQL 8.0
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
| MySQL    | Apenas na rede Docker   |

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
