# API

Padrões
- OpenAPI em openapi/openapi.yaml
- Prefixo /v1
- Autenticação: Bearer JWT
- Paginação: limit/offset
- Erros: RFC 7807 (application/problem+json) — recomendado
- Idempotência: header Idempotency-Key em operações críticas

Recursos (rascunho)
- Infra: GET /health
- Autenticação: POST /auth/login, POST /auth/refresh
- Usuários e Perfis: /usuarios, /perfis
- Cadastros: /produtos, /enderecos
- Operação:
  - Recebimentos: /recebimentos, /itens-recebimento
  - Inventários: /inventarios, /ajustes
  - Picking: /tarefas-picking
  - Movimentações: /movimentacoes
  - Expedição: /expedicoes

Webhooks/Eventos (futuro)
- eventos como recebimento.confirmado, inventario.ajustado (para auditoria/integração futura).

Como gerar a spec
- Backend Nest + Swagger: habilitar SwaggerModule e exportar JSON/YAML.
- Manter fonte de verdade na aplicação e gerar openapi/openapi.yaml nos builds.
