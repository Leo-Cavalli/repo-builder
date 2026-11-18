# 🏗️ Repo Builder — Protocolo `@arch` para Agentes de IA (`repo-builder`)

[![License: MIT](https://img.shields.io/badge/License-MIT-blue.svg)](./LICENSE)
[![Version: 1.0.0](https://img.shields.io/badge/Version-1.0.0-cyan.svg)](./package.json)
[![Token Economy](https://img.shields.io/badge/Token_Savings-Up_to_90%25-emerald.svg)](#)
[![Companion Skill](https://img.shields.io/badge/Companion-repo--visualizer-purple.svg)](https://github.com/Leo-Cavalli/repo-visualizer)

**Economize até 90% de tokens de leitura e elimine a adivinhação arquitetural ao programar com IA.**  
O `repo-builder` é uma **Skill Open Source ultra-leve (~250 tokens)** para Agentes de IA (Google Antigravity, Gemini CLI, Claude Code, Cursor, Windsurf, Cline). Ele instrui a IA a anotar o código-fonte em tempo de escrita (*Write-Time*) com tags semânticas **`@arch:*`** compatíveis com JSDoc/TSDoc/Docstrings/Prisma/SQL.

Essas marcações alimentam diretamente o **[`repo-visualizer`](https://github.com/Leo-Cavalli/repo-visualizer)** para gerar diagramas **C4 Model (C1, C2, C3, C4)**, **Diagramas Entidade-Relacionamento (DER)**, **Diagramas de Classes UML** e documentação **arc42** com **100% de precisão e zero custo extra de processamento**.

---

## 💡 Por que usar o `repo-builder`?

1. **⚡ Economia Brutal de Tokens (*Context Compression*):**
   - Sem marcações, a IA precisa ler arquivos inteiros de 400+ linhas em cada nova sessão só para descobrir o papel arquitetural de cada módulo.
   - Com as tags `@arch:*` de 3 a 4 linhas no topo de cada classe/função/tabela, qualquer agente (ou um simples `grep "@arch:"`) entende toda a arquitetura do repositório gastando **90% menos tokens**.
2. **🎯 Precisão Cirúrgica no `repo-visualizer` (Zero Adivinhação):**
   - Define explicitamente a qual **Contêiner C2** (`@arch:container`), **Papel Arquitetural** (`@arch:role`) e **Camada Topológica C3** (`@arch:tier`) o elemento pertence.
   - Declara **Sistemas Externos reais** (`@arch:external Stripe Payment Gateway [REST API]`) diretamente no código, desenhando-os automaticamente nos diagramas **C4 Nível 1 e Nível 2**.
   - Substitui descrições genéricas por **descrições reais de negócio** (`@arch:desc`) dentro das caixas dos diagramas C4 e nas tabelas do **arc42**.
3. **🧹 Código 100% Limpo e Nativo na IDE:**
   - Usa comentários padrão da linguagem (`/** ... */`, `///`, `--`, `#`), que o próprio VS Code e IntelliJ exibem nativamente no *hover* do mouse.

---

## 🏷️ O Protocolo de Tags `@arch:*`

Quando o `repo-builder` está ativo, a IA anota automaticamente os componentes criados:

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

Ou no formato **Ultra-Compacto de 1 linha (~15 tokens)**:
```typescript
/** @arch [role=utility, container=backend, tier=3] Calcula imposto regional sobre pedidos. */
export function calculateOrderTax(subtotal: number): number { ... }
```

### Tabela de Papéis (`@arch:role`) e Camadas C4 (`@arch:tier`)

| `@arch:role` | `@arch:tier` | Fronteira C4 Padrão (`@arch:container`) | Descrição |
| :--- | :---: | :--- | :--- |
| `screen` | `1` | `Web Application (Frontend)` | Telas, páginas e rotas de entrada UI. |
| `component` | `2` | `Web Application (Frontend)` | Componentes visuais reutilizáveis (`<Button />`, `<DataTable />`). |
| `service` | `3` | `Domain & Application Services (Backend)` | Serviços de domínio, casos de uso e regras de negócio. |
| `adapter` | `3` | `Domain & Application Services (Backend)` | Gateways externos, clientes de API e repositórios. |
| `utility` | `3` | `Domain & Application Services (Backend)` | Funções utilitárias exportadas e helpers. |
| `interface` | `4` | `Domain & Application Services (Backend)` | Interfaces OOP e contratos de domínio. |
| `abstract` | `4` | `Domain & Application Services (Backend)` | Classes base abstratas (`abstract class`). |
| `table` | `5` | `Relational Database` | Tabelas SQL e modelos ORM (Prisma, Drizzle, TypeORM). |

---

## 🚀 Como Instalar na sua IA

### Opção 1: Via `npx skills` (Recomendado)
```bash
npx skills add Leo-Cavalli/repo-builder
```

### Opção 2: Via `git clone` direto na pasta de Skills da sua IA

- **Google Antigravity / Cursor / Windsurf / Agentes padrão `.agents`:**
  ```bash
  git clone https://github.com/Leo-Cavalli/repo-builder.git ~/.agents/skills/repo-builder
  ```

- **Gemini CLI:**
  ```bash
  git clone https://github.com/Leo-Cavalli/repo-builder.git ~/.gemini/skills/repo-builder
  ```

---

## 🤝 Ecossistema Recomendado

Para aproveitar todo o poder visual das marcações geradas pelo `repo-builder`, instale também o **[`repo-visualizer`](https://github.com/Leo-Cavalli/repo-visualizer)**:

1. A IA cria/edita o código usando as tags leves do **`repo-builder`**.
2. O **`repo-visualizer`** lê as tags `@arch:*` instantaneamente e gera o dashboard interativo com Diagramas C4 (C1-C4), DER, UML e documentação arc42 em PDF!

---

## 📄 Licença

Distribuído sob a licença **MIT**. Veja o arquivo [LICENSE](./LICENSE) para mais detalhes.
