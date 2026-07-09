# disposable-domains-br

Lista curada de **domínios de e-mail descartáveis / temporários** (temp-mail, throwaway, disposable) para bloqueio em formulários de captação e aberta à comunidade.

> **Objetivo:** impedir que e-mails de serviços temporários (temp-mail.org, AdGuard Temp Mail, Mailinator, etc.) passem por gates de cadastro, protegendo o custo-por-lead de campanhas de tráfego pago.

## Estrutura

| Arquivo | Papel |
|---|---|
| `disposable_domains.txt` | **Blocklist** — um domínio por linha, minúsculo, ordenado, sem duplicatas. |
| `allowlist.txt` | **Allowlist de proteção** — domínios legítimos que **nunca** devem ser bloqueados, mesmo que apareçam por engano em alguma fonte. Subtraído por último. |

## Como consumir

Fonte bruta (raw), sem autenticação:

```
https://raw.githubusercontent.com/dsalgadox/disposable-domains-br/main/disposable_domains.txt
https://raw.githubusercontent.com/dsalgadox/disposable-domains-br/main/allowlist.txt
```

**Fórmula de aplicação recomendada:**

```
descartáveis = (blocklist ∪ fontes_vivas ∪ blocklist_manual) − allowlist
```

**Padrão de performance:** carregue a lista **uma vez** na subida da aplicação para um `Set` em memória e faça a consulta por requisição em **O(1)**. Não abra/varra o arquivo a cada validação (I/O de disco por requisição derruba a performance sob carga).

```ts
// Exemplo (Node/TS) — carga única, consulta O(1)
const DESCARTAVEIS: ReadonlySet<string> = new Set(
  listaBruta.split("\n").map((d) => d.trim().toLowerCase()).filter(Boolean),
);
export const ehDescartavel = (dominio: string) => DESCARTAVEIS.has(dominio.toLowerCase());
```

## Manutenção

- **Adicionar um domínio:** edite `disposable_domains.txt`, mantendo **minúsculo**, **um por linha** e a ordem alfabética.
- **Proteger um domínio legítimo:** adicione-o ao `allowlist.txt`.
- **Cadência:** atualização mensal planejada via **GitHub Action (`cron`)**, que baixa fontes vivas, mescla a lista manual, subtrai o allowlist e commita automaticamente.

## Aviso honesto

Nenhuma lista de domínios descartáveis é 100%. Provedores de temp-mail criam e reciclam domínios continuamente; endereços recém-nascidos podem escapar até serem reportados. Esta lista bloqueia a grande maioria (~90–95%); para garantia adicional, combine com verificação de canal (ex.: confirmação de número ativo no WhatsApp).

## Licença

[CC0 1.0 Universal](LICENSE) — domínio público. Use livremente.
