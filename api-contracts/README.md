# api-contracts

Contratos OpenAPI 3.1 dos serviços do finance-tracker. **A API é definida
aqui antes de existir código de servidor** — este diretório é a fonte da
verdade; controllers, DTOs e o client TypeScript são gerados a partir dele.

```
api-contracts/
├── auth-service.yaml
├── accounts-service.yaml       # contas + categorias
├── transactions-service.yaml   # comandos, consultas e relatórios
└── shared/
    ├── errors.yaml             # RFC 7807 Problem Details
    └── pagination.yaml         # parâmetros e schema de paginação
```

## Uso

```bash
npm install

# Lint de estilo/consistência (spectral:oas + regras do projeto em .spectral.yaml)
npm run lint

# Visualizar um contrato em Swagger/Redoc localmente
npm run docs

# Gerar interfaces de controller + DTOs Java a partir do contrato (fonte da verdade)
npm run codegen:java:accounts

# Gerar client TypeScript para o frontend
npm run codegen:ts:client
```

## Convenções

- **Prefixo de versão:** todo path começa com `/api/v1`.
- **Erros:** todo 4xx/5xx retorna `application/problem+json` no formato de
  `shared/errors.yaml#/components/schemas/ProblemDetails` (RFC 7807).
- **Paginação:** listas retornam `{ content, page, size, totalElements, totalPages }`,
  controlado pelos parâmetros `page`/`size`/`sort` de `shared/pagination.yaml`.
- **Dinheiro:** todo valor monetário é `type: string, format: decimal`
  (mapeia para `BigDecimal` no Java / `NUMERIC(19,2)` no Postgres) — nunca
  `number` de ponto flutuante.
- **Datas:** `data` de negócio usa `format: date` (LocalDate, ISO-8601);
  timestamps de auditoria (`criadoEm`/`atualizadoEm`) usam `format: date-time`
  (Instant, UTC).
- **Idempotência:** `POST /transacoes` exige o header `Idempotency-Key`.
- **Autenticação:** todos os serviços exceto `auth-service` exigem
  `Authorization: Bearer <accessToken>` (JWT emitido pelo `auth-service`).