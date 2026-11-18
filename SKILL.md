---
name: repo-builder
description: Protocolo ultra-leve de anotação arquitetural (@arch) em tempo de escrita (Write-Time) para economizar tokens e alimentar o repo-visualizer (C4 Model C1-C4, DER, UML e arc42) com 100% de precisão sem depender de adivinhação por regex. Use sempre que escrever, criar ou modificar classes, interfaces, telas, componentes de UI, funções utilitárias ou tabelas de banco de dados.
---

# Repo Builder — Protocolo de Marcação Arquitetural (`@arch`)

Esta skill instrui o Agente de IA a adicionar metadados arquiteturais ultra-leves (`@arch:*`) diretamente nos comentários nativos da linguagem (JSDoc, TSDoc, Docstrings, Prisma `///` ou SQL `--`) no momento em que cria ou edita o código (*Write-Time*).

**Objetivo:** Economizar até **90% de tokens** em leituras futuras de contexto e fornecer metadados exatos para o **`repo-visualizer`** construir diagramas **C4 (C1, C2, C3, C4)**, **DER**, **UML** e documentação **arc42** com zero ambiguidade.

---

## 1. Regra de Ouro (Economia de Tokens)

Sempre que criar ou editar uma **Tela**, **Componente UI**, **Classe**, **Interface**, **Função Exportada** ou **Tabela de Banco**, adicione um bloco de 3 a 5 linhas imediatamente acima da declaração usando as tags `@arch:*`:

```typescript
/**
 * @arch:container <Nome do Contêiner C2> [<Tecnologia>]
 * @arch:role <screen | component | service | adapter | utility | interface | abstract | table>
 * @arch:tier <1 | 2 | 3 | 4 | 5>
 * @arch:external <Nome do Sistema Externo> [<Protocolo / API>]  (Opcional: apenas se integrar com API externa)
 * @arch:desc <Descrição de negócio concisa em 1 linha (máx. 85 caracteres)>
 */
```

> **Alternativa Ultra-Compacta (1 Linha — ~15 tokens):**  
> Para funções pequenas ou economia extrema de tokens, use o formato de linha única:  
> `/** @arch [role=utility, container=backend, tier=3] Calcula imposto regional sobre pedidos. */`

---

## 2. Tabela de Papéis (`@arch:role`) e Camadas C3 (`@arch:tier`)

Use sempre os valores padronizados abaixo para que o `repo-visualizer` posicione cada componente na fronteira e camada C4 correta:

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
# @arch:container Domain & Application Services (Backend) [Python / FastAPI]
# @arch:role service
# @arch:tier 3
# @arch:desc Orquestra validação de pedidos e persistência transacional.
class OrderApplicationService:
    pass
```

---

## 4. Fluxo Recomendado com `repo-visualizer`

1. **Ao escrever código novo:** Insira o bloco `@arch:*` acima de cada elemento criado.
2. **Para ler um projeto existente gastando poucos tokens:** Busque primeiro por `@arch:` (via `grep` / `ripgrep`) ou leia `.arch-visualizer/arch-data.json` em vez de abrir dezenas de arquivos completos.
3. **Para atualizar o dashboard visual:** Caso o `repo-visualizer` esteja instalado, rode:
   ```bash
   node ~/.agents/skills/repo-visualizer/scripts/analyze_repo.cjs .
   ```
