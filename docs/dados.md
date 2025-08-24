# Dados e Modelagem

Modelo (alto nível)
- Usuário, Perfil (RBAC)
- Produto, Unidade de Medida (UOM), Lotes/Validade (opcional)
- Endereço (rua/nivel/predio, tipo: picking/estoque)
- LPN/SSCC (identificação logística) — opcional inicial
- Saldos por endereço e/ou LPN
- Recebimento (cabeçalho/itens), Inventário (contagens/ajustes)
- Tarefas operacionais (put-away, picking, movimentações)
- Eventos de auditoria
- Relatórios (inventário, movimentações, desempenho)
- Dados Logisticos (Produto, Endereço picking, quantidade maxima por endereço, quantidade minima por endereço, Lastro Palete, Camada Palete, Total Palete.)

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

## Cadastro de produto — campos solicitados

Abaixo está o mapeamento direto dos campos que você listou para um modelo de produto (nomes empresariais à esquerda) e a sugestão de atributo técnico / tipo à direita, com validações e observações.

- it-codigo (codigo do produto) -> sku / item_code: string (único, não nulo). Sugestão: regex para permitir letras, números, `-` e `_` (ex: `^[A-Z0-9\-_.]+$`, case-insensitive). Usado como referência comercial.
- desc-item (descrição do produto) -> name / description: name: string curta, description: text opcional para detalhes.
- un -> uom / unidade: FK para tabela `uom` (armazenar `uomId` e possivelmente `uomCode` para consultas rápidas). Validação: existir UOM ativa.
- peso-bruto -> gross_weight (decimal, kg): >= 0. Armazenar com precisão (ex: Decimal(10,3)). Opcionalmente ter `net_weight` se necessário.
- comprim -> length (decimal, cm ou m): >= 0. Padronizar unidade (cm ou m) no sistema e documentar.
- altura -> height (decimal): >= 0.
- largura -> width (decimal): >= 0.
- fm-cod-com (Familia do Produto) -> family_code / product_family_id: string ou FK para `product_family`. Útil para agrupamentos comerciais e regras.
- DUN (Código de barras de uma Unidade) -> dun_gtin / unit_gtin: string (GTIN format — validar check digit). Indexar (índice simples) para lookup por código de barras.
- EAN (Código de barras da caixa) -> ean_gtin / pack_gtin: string (GTIN/EAN para a embalagem externa). Indexar e validar check digit.

Observações e recomendações práticas
- Normalizar UOM e Family em tabelas separadas com FK para manter integridade e traduções.
- Validar GTIN/DUN/EAN com algoritmo de check digit (EAN-13, GTIN-14, etc.) e armazenar somente dígitos (sem espaços/traços).
- Armazenar dimensões e peso em campos numéricos de precisão adequada (usar Decimal no banco/prisma).
- Incluir índices: `UNIQUE(sku)`, índices em `dun_gtin` e `ean_gtin`.
- Para pesquisa por nome/descritivo, considerar GIN/pg_trgm em `name`/`description`.

Exemplo rápido (fragmento para `schema.prisma`):

```prisma
model Product {
	id           String   @id @default(uuid())
	sku          String   @unique
	name         String
	description  String?
	uomId        String
	Uom          Uom      @relation(fields: [uomId], references: [id])
	grossWeight  Decimal? @db.Decimal(10,3)
	length       Decimal? @db.Decimal(10,3)
	height       Decimal? @db.Decimal(10,3)
	width        Decimal? @db.Decimal(10,3)
	familyId     String?  // opcional FK para product family
	familyCode   String?  
	dunGtin      String?  @db.VarChar(32)
	eanGtin      String?  @db.VarChar(32)
	isActive     Boolean  @default(true)
	createdAt    DateTime @default(now())
	updatedAt    DateTime @updatedAt

	@@index([dunGtin])
	@@index([eanGtin])
}
```

Mapping DTO (exemplo TypeScript)

```ts
export interface CreateProductDTO {
	sku: string; // it-codigo
	name: string; // desc-item (curto)
	description?: string; // desc-item (longo)
	uomId: string; // un
	grossWeight?: number; // peso-bruto (kg)
	length?: number; // comprim
	height?: number; // altura
	width?: number; // largura
	familyCode?: string; // fm-cod-com
	dunGtin?: string; // DUN
	eanGtin?: string; // EAN
}
```

Se quiser, eu posso gerar a migration do Prisma completa (arquivo .sql / prisma schema) e um seed com exemplos usando exatamente esses campos. Diga se prefere `cm` ou `m` como unidade para dimensões e se o peso deve ser `kg`.
Unidades escolhidas para documentação: dimensões = cm, peso = kg.

### Dicionário de dados (produto)

| Campo (origem) | Nome técnico | Tipo | Obrigatório | Validação / Formato | Observações |
|---|---:|---:|---:|---|---|
| it-codigo | sku | string | Sim | Regex: `^[A-Z0-9\-\._]+$` (case-insensitive) | Código comercial único (referência)
| desc-item (curto) | name | string | Sim | max 255 chars | Short label para UI/listagens
| desc-item (longo) | description | text | Não | — | Descrição detalhada
| un | uomId | uuid (FK) | Sim | deve existir em `uom` | Unidade de medida (ex: EA, KG, M)
| peso-bruto | grossWeight | decimal (kg) | Não | >= 0 | Armazenar em kg (Decimal(10,3))
| comprim | length | decimal (cm) | Não | >= 0 | Unidade padronizada: cm
| altura | height | decimal (cm) | Não | >= 0 | Unidade padronizada: cm
| largura | width | decimal (cm) | Não | >= 0 | Unidade padronizada: cm
| fm-cod-com | familyCode / product_family_id | string / uuid (FK) | Não | — | Código ou FK para família do produto
| DUN | dunGtin | string (digits) | Não | GTIN check-digit (validação EAN/GTIN) | Armazenar apenas dígitos
| EAN | eanGtin | string (digits) | Não | GTIN check-digit (validação EAN/GTIN) | Armazenar apenas dígitos (caixa/pack)

### Validações e regras

- SKU (it-codigo): aplicar regex `^[A-Z0-9\-\._]+$` e normalizar para uppercase ao salvar. Sem espaços.
- GTIN/EAN/DUN: remover tudo que não for dígito antes de validar. Validar o dígito verificador (ex: EAN-13 / GTIN-14 algoritmo: soma das posições, multiplicador 1/3, calcular módulo 10). Rejeitar se inválido.
- Dimensões e peso: aceitar apenas valores numéricos >= 0; armazenar dimensões em cm e peso em kg. Preferir Decimal no banco para evitar erros de ponto flutuante.
- UOM: referenciar `uom` por `uomId`; no cadastro validar existência e que a UOM esteja ativa/permitida.

### Exemplos de payload (CreateProductDTO)

```json
{
	"sku": "ABC-100",
	"name": "Produto Exemplo A",
	"description": "Descrição longa do produto",
	"uomId": "uom-ea",
	"grossWeight": 0.500,   // kg
	"length": 10.0,         // cm
	"height": 5.0,          // cm
	"width": 2.5,           // cm
	"familyCode": "FAM-001",
	"dunGtin": "0123456789012",
	"eanGtin": "00123456789012"
}
```

### Notas de UI / Form

- SKU: campo texto com máscara simples (uppercasing automático), placeholder `EX: ABC-100`.
- GTIN/EAN/DUN: campo numérico com máscara que aceita 8/12/13/14 dígitos; mostrar validação inline do check-digit.
- Dimensões: inputs numéricos com unidade `cm` visível ao lado; permitir valores decimais (2-3 casas).
- Peso: input numérico com unidade `kg` visível; validar >= 0.

### Observações finais

Mantemos UOM e Family como tabelas normalizadas para facilitar traduções e regras comerciais. Para buscas por nome/descrição, avaliar GIN + pg_trgm no Postgres.
