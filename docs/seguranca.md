# Segurança e Compliance

Autenticação e autorização
- JWT (Bearer) com expiração curta + refresh
- RBAC por perfil (Operador, Conferente, Líder, Supervisor, Admin)
- Escopo por CD/Filial (multi-tenant futuro)

Boas práticas
- OWASP Top 10 (XSS, CSRF, SQLi — mitigado por Prisma parametrizado)
- Rate limiting e Helmet no Nest
- CORS configurado por ambiente
- Hash de senha (Argon2 ou bcrypt com custo adequado)
- Segredos em variáveis de ambiente (não commitar)

Auditoria
- Eventos por operação crítica (quem, quando, o quê)
- Retenção conforme necessidade operacional

Conformidade
- LGPD: minimização de dados e controle de acesso por necessidade
