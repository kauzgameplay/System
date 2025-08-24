# WMS System

Descrição
WMS (Warehouse Management System) desenvolvido do zero para operação de armazém, com interface web para desktop e execução de tarefas operacionais em celulares (ou coletores RF). Stack JavaScript com frontend em React + Vite e backend em Nest.js, usando Prisma e banco de dados PostgreSQL. Deploy em VPS.

Links rápidos
- Documentação (docs/README.md)
- Visão Geral (docs/visao-geral.md)
- Arquitetura (docs/arquitetura.md)
- Módulos de Negócio (docs/modulos.md)
- API (docs/api/README.md)
- Dados e Modelagem (docs/dados.md)
- Operação (docs/operacao.md)
- Guia do Desenvolvedor (docs/dev/guia-dev.md)
- Deploy (docs/deploy.md)
- Segurança (docs/seguranca.md)
- FAQ (docs/faq.md)
- Contribuição (CONTRIBUTING.md)
- Changelog (CHANGELOG.md)

Principais recursos (roadmap inicial)
- Recebimento, armazenagem (put-away), inventário, separação (picking), packing e expedição
- Operação por celular/RF com leitura de códigos de barras
- RBAC (perfis e permissões)
- Auditoria de eventos
- Observabilidade (logs, métricas e health)

Arquitetura (alto nível)
- Frontend: React + Vite
- Backend: Nest.js (REST), autenticação JWT, RBAC, validações
- ORM/BD: Prisma + PostgreSQL
- Infra: VPS com Docker e Nginx (reverse proxy), TLS via Let's Encrypt
- Observabilidade: logs estruturados e health checks

Como começar
1) Pré-requisitos
- Node.js LTS (>= 20)
- Docker + Docker Compose (para banco e/ou deploy)
- PostgreSQL (local via Docker ou instalado)
- PNPM ou NPM

2) Configuração
- Copie .env.example para .env nos apps (web e api)
- Configure URL do banco e JWT_SECRET
- Rode migrations do Prisma

3) Execução (dev)
- Frontend: npm install; npm run dev
- Backend: npm install; npx prisma migrate dev; npm run start:dev

4) Testes
- Backend (Nest): npm run test, npm run test:e2e
- Frontend: npm run test (configurar)

Estrutura sugerida
- apps/
  - web/            React + Vite
  - api/            Nest.js + Prisma
- openapi/          Especificação da API (OpenAPI)
- docs/             Documentação
- scripts/          Utilitários/DevOps
- .github/          Templates e Actions

Licença
- Defina a licença (ex.: MIT)
