# Dados e Modelagem

Modelo (alto nível)
- Usuário, Perfil (RBAC)
- Produto, Unidade de Medida (UOM), Lotes/Validade (opcional)
- Endereço (rua/nivel/posição, tipo: picking/estoque)
- LPN/SSCC (identificação logística) — opcional inicial
- Saldos por endereço e/ou LPN
- Recebimento (cabeçalho/itens), Inventário (contagens/ajustes)
- Tarefas operacionais (put-away, picking, movimentações)
- Eventos de auditoria

Prisma e migrações
- Gerenciar schema.prisma no backend
- Migrations versionadas (npx prisma migrate dev / deploy)
- Seeds para dados mestres (UOMs, perfis)

Exemplo de entidades (rastreio)
| Entidade | Chaves | Observações |
|---------|--------|-------------|
| usuario | id, email | hash de senha, perfil_id, ativo |
| perfil  | id, nome | permissões agregadas (RBAC) |
| produto | id, sku  | descrição, uom, dimensões |
| endereco| id, codigo| tipo, capacidade |
| saldo   | id, produto_id, endereco_id | quantidade, lote, validade |
| recebimento | id, status | fornecedor opcional, timestamps |
| rec_item | id, recebimento_id, produto_id | quantidade, uom |
| inventario | id, status | tipo (cíclico/geral) |
| inv_contagem | id, inventario_id, endereco_id | qtd contada, operador |
| ajuste  | id, motivo | delta, audit |

Eventos de domínio
- Registrar em tabela de eventos (append-only) para auditoria e troubleshooting.

Índices recomendados
- sku em produto
- codigo em endereco
- chaves compostas em saldo (produto_id, endereco_id)
