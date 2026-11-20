---
name: repo-builder
description: Protocolo ultra-leve de anotação arquitetural (@arch) com estado incremental (@arch:status pending/modified/read) em tempo de escrita (Write-Time) para economizar tokens e alimentar o repo-visualizer (C4 Model C1-C4, DER, UML e arc42) com 100% de precisão sem reanalisar código inalterado. Use sempre que escrever, criar ou modificar classes, interfaces, telas, componentes de UI, funções utilitárias ou tabelas de banco de dados.
---

# Repo Builder — Protocolo de Marcação Arquitetural & Incremental (`@arch`)

Esta skill instrui o Agente de IA a adicionar metadados arquiteturais ultra-leves (`@arch:*`) diretamente nos comentários nativos da linguagem (JSDoc, TSDoc, Docstrings, Prisma `///` ou SQL `--`) no momento em que cria ou edita o código (*Write-Time*).

**Objetivo:** Economizar até **90% de tokens** em leituras futuras de contexto e alimentar o **Motor Incremental do `repo-visualizer`** para construir diagramas **C4 (C1–C4)**, **DER**, **UML** e **arc42** analisando **apenas o que foi criado ou alterado**.

---

## 1. Regra de Ouro + Ciclo de Vida Incremental (`@arch:status`)

Sempre que criar ou editar uma **Tela**, **Componente UI**, **Classe**, **Interface**, **Função Exportada** ou **Tabela de Banco**, adicione (ou atualize) um bloco de 3 a 5 linhas imediatamente acima da declaração:

```typescript
/**
 * @arch:status pending
 * @arch:container <Nome do Contêiner C2> [<Tecnologia>]
 * @arch:role <screen | component | service | adapter | utility | interface | abstract | table>
 * @arch:tier <1 | 2 | 3 | 4 | 5>
 * @arch:external <Nome do Sistema Externo> [<Protocolo / API>]  (Opcional: apenas se integrar com API externa)
 * @arch:desc <Descrição de negócio concisa em 1 linha (máx. 85 caracteres)>
 */
```

> **Alternativa Ultra-Compacta (1 Linha — ~18 tokens):**  
> `/** @arch [status=pending, role=utility, container=backend, tier=3] Calcula imposto regional sobre pedidos. */`

### Máquina de Estados do `@arch:status` (Sincronização `repo-builder` ↔ `repo-visualizer`):
1. **`@arch:status pending`** → Use sempre ao **criar** um elemento novo (nova classe, tela, componente, função ou tabela).
2. **`@arch:status modified`** → **REGRA OBRIGATÓRIA AO EDITAR:** Se você modificar um elemento que já estava marcado como `@arch:status read` (ex.: adicionou nova herança `extends`/`implements`, novo `import`, nova relação `@relation`/FK, novo componente `<Widget />` ou alterou a responsabilidade), **altere a tag dele de `read` para `modified`**!
3. **`@arch:status read`** → Definido automaticamente pelo `repo-visualizer` após ingerir o código no `arch-data.json`. Elementos `read` não são reanalisados nas próximas rodadas (Cache Hit em `0ms`), a menos que o status volte para `modified`/`pending` ou o hash do arquivo mude.

---

## 2. Tabela de Papéis (`@arch:role`) e Camadas C3 (`@arch:tier`)

| `@arch:role` | `@arch:tier` | Fronteira C4 Padrão (`@arch:container`) | Quando Usar |
| :--- | :---: | :--- | :--- |
| `screen` | `1` | `Web Application (Frontend) [React / TSX]` | Páginas, rotas, telas completas ou views de entrada. |
| `component` | `2` | `Web Application (Frontend) [React / TSX]` | Componentes visuais reutilizáveis (botões, tabelas, modais, cards). |
| `service` | `3` | `Domain & Application Services (Backend) [TS / Node]` | Serviços de domínio, casos de uso, controllers e regras de negócio. |
| `adapter` | `3` | `Domain & Application Services (Backend) [TS / Node]` | Gateways, clientes HTTP, repositórios e adaptadores de infraestrutura. |
| `utility` | `3` | `Domain & Application Services (Backend) [TS / Node]` | Funções utilitárias puras, formatadores, helpers e cálculos. |
| `interface` | `4` | `Domain & Application Services (Backend) [TS / Node]` | Contratos OOP (`interface`), portas (*ports*) e tipos de protocolo. |
| `abstract` | `4` | `Domain & Application Services (Backend) [TS / Node]` | Classes base abstratas (`abstract class`) e entidades raiz. |
| `table` | `5` | `Relational Database [PostgreSQL / Prisma / SQL]` | Modelos Prisma (`model`), tabelas SQL (`CREATE TABLE`) ou entidades ORM. |

---

## 3. Exemplos Práticos por Linguagem

### A. Classes de Domínio e Adaptadores Externos (TypeScript / Java / C#)
```typescript
/**
 * @arch:status pending
 * @arch:container Domain & Application Services (Backend) [TypeScript / OOP]
 * @arch:role adapter
 * @arch:tier 3
 * @arch:external Stripe Payment Gateway [REST API / Webhooks]
 * @arch:desc Processa cobranças de cartão e valida webhooks de pagamento via Stripe.
 */
export class StripeGatewayAdapter extends BaseEntity implements IPaymentGateway {
  // ...
}
```

### B. Telas e Componentes Reutilizáveis de UI (React / Next.js / TSX)
```tsx
/**
 * @arch:status pending
 * @arch:container Web Application (Frontend) [React / TSX]
 * @arch:role screen
 * @arch:tier 1
 * @arch:desc Tela final de checkout para revisão de taxas e confirmação do pedido.
 */
export function CheckoutScreen() {
  // ...
}
```

### C. Modelos de Banco de Dados (Prisma / SQL)
```prisma
/// @arch:status pending
/// @arch:container Relational Database [PostgreSQL / Prisma]
/// @arch:role table
/// @arch:tier 5
/// @arch:desc Armazena cabeçalhos de pedidos, status de pagamento e total auditado.
model Order {
  id     String @id @default(uuid())
  status String
}
```

```sql
-- @arch:status pending
-- @arch:container Relational Database [PostgreSQL / SQL]
-- @arch:role table
-- @arch:tier 5
-- @arch:desc Registro imutável de auditoria para operações críticas do sistema.
CREATE TABLE audit_logs (
  id VARCHAR(36) PRIMARY KEY,
  action VARCHAR(100) NOT NULL
);
```

### D. Python (Classes e Funções)
```python
# @arch:status pending
# @arch:container Domain & Application Services (Backend) [Python / FastAPI]
# @arch:role service
# @arch:tier 3
# @arch:desc Orquestra validação de pedidos e persistência transacional.
class OrderApplicationService:
    pass
```

---

## 4. Fluxo Recomendado com `repo-visualizer`

1. **Ao criar código novo:** Insira o bloco `@arch:*` com `@arch:status pending`.
2. **Ao alterar código existente (`read`):** Troque `@arch:status read` para `@arch:status modified` sempre que adicionar novas conexões (herança, composição, imports, componentes UI, chaves estrangeiras ou novos métodos/colunas).
3. **Para ler um projeto existente gastando poucos tokens:** Busque primeiro por `@arch:` ou leia `.arch-visualizer/arch-data.json` em vez de abrir dezenas de arquivos completos.
4. **Para sincronizar o dashboard incrementalmente:**
   ```bash
   # Substitua pelo diretório onde a skill está instalada (~/.gemini/skills/ ou ~/.agents/skills/)
   node "<diretorio-da-skill>/repo-visualizer/scripts/analyze_repo.cjs" .
   ```
   *(O `repo-visualizer` analisará apenas os arquivos `pending`/`modified`, atualizará o JSON e marcará as tags automaticamente como `@arch:status read`!)*
