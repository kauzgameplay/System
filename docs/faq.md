# FAQ

- Posso operar só com celulares ao invés de RF?
  - Sim. A interface é responsiva e pode operar como PWA com leitura de códigos via câmera.

- Preciso integrar com ERP/TMS?
  - Não. O sistema é independente. Integrações podem ser adicionadas no futuro como módulos.

- Onde configuro o banco de dados?
  - Em apps/api/.env (DATABASE_URL). Use prisma migrate para aplicar o schema.

- Como habilitar HTTPS na VPS?
  - Configure Nginx como reverse proxy e use Certbot/Let's Encrypt para os certificados.

- Como imprimir etiquetas?
  - Via servidor de impressão (ex.: CUPS) ou endpoint que envie ZPL para uma impressora de rede.
