# FAQ

- Posso operar só com celulares ao invés de RF?
  - Não. O sistema requer dispositivos com leitor de código de barras integrado ou celulares.

- Preciso integrar com ERP/TMS?
  - Não. O sistema é independente. Integrações podem ser adicionadas no futuro como módulos.

- Onde configuro o banco de dados?
  - Em apps/api/.env (DATABASE_URL). Use prisma migrate para aplicar o schema.

- Como habilitar HTTPS na VPS?
  - Configure Nginx como reverse proxy e use Certbot/Let's Encrypt para os certificados.

- Como imprimir etiquetas?
  - Gerar PDFs via o sistema e usar uma impressora compatível com o formato.
