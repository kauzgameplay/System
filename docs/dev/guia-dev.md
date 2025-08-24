# Guia do Desenvolvedor

Stack
- Frontend: React + Vite (TypeScript recomendado)
- Backend: Nest.js (TypeScript), Swagger, Class-Validator
- ORM: Prisma
- Banco: PostgreSQL
- Node.js LTS (>= 20)

Estrutura sugerida
- apps/web
- apps/api
- openapi/
- docs/

Pré-requisitos
- Node.js, PNPM/NPM
- Docker (opcional local, obrigatório para deploy)
- PostgreSQL (local ou via Docker)

Configuração
- apps/api/.env
  - DATABASE_URL="postgresql://user:pass@localhost:5432/wms?schema=public"
  - JWT_SECRET="defina_um_segredo_forte"
  - PORT=3000
- apps/web/.env
  - VITE_API_BASE_URL="http://localhost:3000/v1"

Setup rápido
- Backend:
  - cd apps/api
  - npm install
  - npx prisma migrate dev
  - npm run start:dev
- Frontend:
  - cd apps/web
  - npm install
  - npm run dev

Qualidade
- Lint/format (ESLint/Prettier)
- Testes unitários e e2e (Nest: Jest; Web: Vitest/RTL)
- Coverage mínimo: definir meta (ex.: 70%)

Commits e branches
- Conventional Commits
- PRs com descrição, checklist e testes
- Branching: trunk-based simples (feature/*, fix/*)

Convenções
- DTOs validados no Nest (class-validator)
- Tratamento de erros padronizado e logs estruturados
- Autenticação JWT e RBAC por rotas
