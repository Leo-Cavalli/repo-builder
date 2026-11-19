# 🏗️ Repo Builder — Protocolo `@arch` & Análise Incremental para Agentes de IA (`repo-builder` v1.1.0)

[![License: MIT](https://img.shields.io/badge/License-MIT-blue.svg)](./LICENSE)
[![Version: 1.1.0](https://img.shields.io/badge/Version-1.1.0-cyan.svg)](./package.json)
[![Token Economy](https://img.shields.io/badge/Token_Savings-Up_to_90%25-emerald.svg)](#)
[![Companion Skill](https://img.shields.io/badge/Companion-repo--visualizer-purple.svg)](https://github.com/Leo-Cavalli/repo-visualizer)

**Economize até 90% de tokens de leitura, elimine a adivinhação arquitetural e ative análise incremental instantânea ao programar com IA.**  
O `repo-builder` é uma **Skill Open Source ultra-leve (~280 tokens)** para Agentes de IA (Google Antigravity, Gemini CLI, Claude Code, Cursor, Windsurf, Cline). Ele instrui a IA a anotar o código-fonte em tempo de escrita (*Write-Time*) com tags semânticas **`@arch:*`** e controle de estado incremental **`@arch:status` (`pending` / `modified` $\rightarrow$ `read`)**.

Essas marcações alimentam diretamente o **[`repo-visualizer`](https://github.com/Leo-Cavalli/repo-visualizer)** para gerar diagramas **C4 Model (C1, C2, C3, C4)**, **Diagramas Entidade-Relacionamento (DER)**, **Diagramas de Classes UML** e documentação **arc42** sem precisar reprocessar arquivos que já foram lidos!

---

## 🆕 Novidade da Versão 1.1.0: Ciclo de Vida Incremental (`@arch:status`)

Para evitar que o `repo-visualizer` reanalise todo o repositório a cada pequena mudança, o protocolo agora inclui uma máquina de estados leve (*Dirty Bit*):

| Estado (`@arch:status`) | Quem define? | O que acontece na próxima análise? |
| :--- | :--- | :--- |
| **`pending`** | `repo-builder` (ao **criar** código novo) | O `repo-visualizer` analisa o novo elemento, adiciona ao JSON e marca automaticamente como `read`. |
| **`modified`** | `repo-builder` (ao **alterar** código `read` com nova herança, import ou FK) | O `repo-visualizer` reanalisa **apenas** esse arquivo, recalcula as conexões e marca como `read`. |
| **`read`** | `repo-visualizer` (após salvar no JSON) | **Cache Hit (`0ms`)!** O arquivo é reaproveitado direto do cache (com verificação de hash SHA-256 caso um humano edite o código sem mudar a tag). |

---

## 💡 Por que usar o `repo-builder`?

1. **⚡ Economia Brutal de Tokens (*Context Compression*):**
   - Com as tags `@arch:*` de 4 linhas no topo de cada classe/função/tabela, qualquer agente entende toda a arquitetura do repositório gastando **90% menos tokens**.
2. **🚀 Análise Incremental (`pending` / `modified` $\rightarrow$ `read`):**
   - Código já analisado vira `@arch:status read` e é pulado nas próximas varreduras do `repo-visualizer`, economizando CPU em projetos médios e grandes.
3. **🎯 Precisão Cirúrgica no `repo-visualizer` (Zero Adivinhação):**
   - Define explicitamente o **Contêiner C2** (`@arch:container`), **Papel Arquitetural** (`@arch:role`), **Camada Topológica C3** (`@arch:tier`), **Sistemas Externos reais** (`@arch:external`) e **Descrições de Negócio** (`@arch:desc`).

---

## 🏷️ O Protocolo de Tags `@arch:*`

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

Ou no formato **Ultra-Compacto de 1 linha (~18 tokens)**:
```typescript
/** @arch [status=pending, role=utility, container=backend, tier=3] Calcula imposto regional sobre pedidos. */
export function calculateOrderTax(subtotal: number): number { ... }
```

*(Após rodar o `repo-visualizer`, `@arch:status pending` muda automaticamente para `@arch:status read`!)*

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

## 🤝 Ecossistema Completo (A Tríade)

Combine com as skills irmãs para governança ponta-a-ponta:
1. **[`repo-architect`](https://github.com/Leo-Cavalli/repo-architect):** Prevenção (Clean Code, SOLID, Camadas C3, Encapsulamento e Anti-Duplicação).
2. **`repo-builder`:** Indexação Write-Time (`@arch:*` + `@arch:status pending/modified`).
3. **[`repo-visualizer`](https://github.com/Leo-Cavalli/repo-visualizer):** Análise Incremental (`@arch:status read`), Diagramas C4 (C1–C4), DER, UML e arc42 em PDF.

---

## 📄 Licença

Distribuído sob a licença **MIT**. Veja o arquivo [LICENSE](./LICENSE) para mais detalhes.
