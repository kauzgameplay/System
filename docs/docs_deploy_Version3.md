# Deploy e Observabilidade

Ambiente: VPS
- SO recomendado: Ubuntu LTS
- Componentes: Docker, Docker Compose, Nginx (reverse proxy), Certbot (Let's Encrypt)
- Containers: api, web (build estático servido pelo Nginx), postgres (ou banco gerenciado)

Passos (alto nível)
1) Preparar VPS
- Instalar Docker e Docker Compose
- Abrir portas 80/443
- Criar usuário de deploy e diretório do projeto

2) Banco de dados
- Usar container Postgres com volume
- Ajustar DATABASE_URL no .env da API
- Executar migrations (prisma migrate deploy)

3) Build e containers
- Backend: imagem Node alpine, expor 3000
- Frontend: build Vite e servir estático via Nginx
- Nginx: reverse proxy para /api e servir / (web)

4) TLS
- Configurar Certbot para o domínio
- Renovação automática

5) Observabilidade
- Health checks: /v1/health
- Logs: redirecionar stdout/stderr; coletar com filebeat/Vector (opcional)
- Alertas (uptime monitor)

Exemplo (esboço docker-compose.yml)
```yaml
version: "3.9"
services:
  postgres:
    image: postgres:16
    environment:
      POSTGRES_USER: wms
      POSTGRES_PASSWORD: wms
      POSTGRES_DB: wms
    volumes:
      - pgdata:/var/lib/postgresql/data
    ports:
      - "5432:5432"

  api:
    build: ./apps/api
    env_file:
      - ./apps/api/.env
    depends_on:
      - postgres
    ports:
      - "3000:3000"

  web:
    build:
      context: ./apps/web
      dockerfile: Dockerfile
    ports:
      - "8080:80"
    depends_on:
      - api

volumes:
  pgdata: {}
```

Procedimentos
- Releases versionadas (CHANGELOG)
- Backup BD: dump diário e teste de restauração
- RTO/RPO: defina metas e valide em exercícios
