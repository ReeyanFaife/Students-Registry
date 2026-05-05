# 🍽️ RestaurantOS — Sistema Completo de Gestão

Sistema moderno de gestão de restaurante com dashboard administrativo, autenticação JWT, gestão de pedidos e interface visual premium.

## 🚀 Início Rápido

### Pré-requisitos
- [Docker](https://www.docker.com/get-started) instalado e em execução
- [Docker Compose](https://docs.docker.com/compose/install/) v2+

### Iniciar o sistema

```bash
# Clonar / entrar na pasta
cd restaurant-system

# Iniciar todos os serviços
docker compose up --build

# Aguardar ~30-60 segundos para tudo inicializar
# Aceder em: http://localhost:5173
```

### Credenciais padrão (seed automático)
| Campo    | Valor                   |
|----------|-------------------------|
| Email    | admin@restaurant.com    |
| Password | admin123                |

> O primeiro utilizador registado torna-se automaticamente **admin**.

---

## 🏗️ Arquitetura

```
restaurant-system/
├── frontend/          # React + Vite + Tailwind CSS
│   ├── src/
│   │   ├── pages/     # Dashboard, Pratos, Mesas, Pedidos, etc.
│   │   ├── components/ # Layout, Sidebar, Navbar, Modal, Skeleton
│   │   ├── contexts/  # Auth, Toast, Theme
│   │   └── lib/       # Axios instance
│   └── Dockerfile
├── backend/           # Node.js + Express + Prisma
│   ├── src/
│   │   ├── routes/    # auth, dashboard, pratos, mesas, pedidos
│   │   └── middleware/ # JWT auth
│   ├── prisma/
│   │   ├── schema.prisma
│   │   └── migrations/
│   └── Dockerfile
├── docker-compose.yml
└── README.md
```

## 🌐 URLs e Portas

| Serviço   | URL                       |
|-----------|---------------------------|
| Frontend  | http://localhost:5173      |
| Backend   | http://localhost:3000      |
| PostgreSQL| localhost:5432             |

## 📋 Stack Tecnológica

### Frontend
- **React 18** + **Vite** — bundler ultrarrápido
- **Tailwind CSS** — utility-first styling
- **React Router v6** — navegação SPA
- **Axios** — cliente HTTP com interceptors JWT
- **Chart.js** + **react-chartjs-2** — gráficos do dashboard
- **Lucide React** — ícones profissionais
- **Fontes**: Syne (display) + DM Sans (body) + JetBrains Mono

### Backend
- **Node.js** + **Express.js** — API REST
- **Prisma ORM** — acesso à base de dados com type safety
- **JWT** + **bcryptjs** — autenticação segura
- **Multer** — upload de imagens de pratos
- **CORS** configurado para o frontend

### Base de Dados
- **PostgreSQL 15** — base de dados principal
- Migrações automáticas no arranque
- Volume Docker persistente

## 🔧 Comandos Úteis

```bash
# Ver logs de todos os serviços
docker compose logs -f

# Ver logs apenas do backend
docker compose logs -f backend

# Executar seed manualmente (dados de exemplo)
docker compose exec backend node src/seed.js

# Parar todos os serviços
docker compose down

# Parar e remover volumes (reset total)
docker compose down -v

# Reconstruir apenas o frontend
docker compose up --build frontend

# Abrir Prisma Studio (gestor visual da BD)
docker compose exec backend npx prisma studio
```

## 📡 API Endpoints

### Autenticação
```
POST /auth/register    — Registar utilizador
POST /auth/login       — Login, retorna JWT
GET  /auth/me          — Perfil do utilizador atual
```

### Dashboard
```
GET /dashboard/stats   — Estatísticas gerais + gráficos
```

### Pratos
```
GET    /pratos              — Listar pratos (com filtros: search, categoria, disponivel)
GET    /pratos/:id          — Detalhe do prato
POST   /pratos              — Criar prato (multipart/form-data com imagem)
PUT    /pratos/:id          — Editar prato
DELETE /pratos/:id          — Remover prato
GET    /pratos/meta/categorias — Listar categorias existentes
```

### Mesas
```
GET    /mesas       — Listar mesas (com pedido ativo incluído)
GET    /mesas/:id   — Detalhe da mesa
POST   /mesas       — Criar mesa
PUT    /mesas/:id   — Atualizar estado/número
DELETE /mesas/:id   — Remover mesa
```

### Pedidos
```
GET    /pedidos        — Listar pedidos (filtros: status, mesaId, page, limit)
GET    /pedidos/:id    — Detalhe do pedido com itens
POST   /pedidos        — Criar pedido (atualiza mesa para "ocupada")
PUT    /pedidos/:id    — Atualizar status / adicionar itens
DELETE /pedidos/:id    — Remover pedido
```

## 🗃️ Modelo de Dados

```sql
users          — id, name, email, password, role, created_at
pratos         — id, nome, preco, categoria, imagem, disponivel, descricao, created_at
mesas          — id, numero, status
pedidos        — id, mesa_id, total, status, created_at
pedido_itens   — id, pedido_id, prato_id, quantidade, preco
```

## ✨ Funcionalidades

- 🔐 **Autenticação JWT** com sessão persistente
- 📊 **Dashboard** com 4 KPIs + 4 gráficos em tempo real
- 🍽️ **Gestão de Pratos** — CRUD completo com upload de imagem, toggle disponível
- 🪑 **Gestão de Mesas** — grid visual com estado em tempo real
- 🛒 **Interface POS** — novo pedido com carrinho interativo
- 📦 **Gestão de Pedidos** — fluxo: Aberto → Preparando → Entregue
- 📈 **Relatórios** — faturamento, volume, top pratos
- 🌓 **Dark/Light Mode** com persistência
- 📱 **Totalmente responsivo** — mobile first
- 🔔 **Toast notifications** para feedback visual
- ⏳ **Loading skeletons** para UX suave

## 🔒 Segurança

- Passwords com hash bcrypt (salt 12)
- JWT com expiração configurável (padrão: 7 dias)
- Middleware de autenticação em todas as rotas privadas
- CORS configurado apenas para o frontend
- Variáveis de ambiente via `.env`

---

Desenvolvido com ❤️ — RestaurantOS v1.0
