# Custos Mensais de Deployment e Operação — Iron Cave

Data de referência e verificação: **2026-04-10**

## Resumo

Leitura rápida para decisão de negócio:

- Colocar a app online no mínimo: **~$15/mês**.
- Operar um MVP estável: **~$36 a $91/mês**.
- Operar um MVP com perfil mais robusto: **~$148 a $242/mês**.
- Potencial de crescimento: **~$233 a $538/mês**.

Principais fatores que fazem o custo subir:

- volume de deploys/testes (CI/CD);
- ativação de cache/filas;
- quantidade e duração de jobs automáticos.
- uso de IA.

## 1) Objetivo

Estimar custos mensais para colocar e operar a Iron Cave em ambiente online, com foco em:

- frontend;
- backend/API;
- base de dados;
- integrações opcionais (storage, email, observabilidade, IA).

Este documento é uma estimativa operacional (não contratual) para apoio a decisão de faseamento.

## 2) Escopo e premissas

- Valores apresentados em **USD**.
- Base em **list prices públicos** dos fornecedores.
- Custos reais variam com tráfego, carga, retenção de dados, equipa e consumo de IA.
- Quando existe variação regional, a referência usada é **EUA**.
- Definições rápidas:
    - **custo fixo**: valor mensal previsível;
    - **custo variável**: depende do uso mensal;
    - **limite gratuito**: utilização sem custo até um teto.
- O relatório combina:
    - `estado atual da codebase` (implementação em runtime);
    - `estado canónico da documentação` (target state já documentado).

## 3) Cobertura face à codebase e docs canónicas

| Item de custo                             | Estado de cobertura no relatório | Evidência canónica                                                                                                            | Nota                                                                                                                                          |
| ----------------------------------------- | -------------------------------- | ----------------------------------------------------------------------------------------------------------------------------- | --------------------------------------------------------------------------------------------------------------------------------------------- |
| Frontend hosting (Vercel)                 | Coberto                          | `apps/frontend/package.json`, `docs/operations/setup-run-deploy.md`                                                           | Adequado para fases iniciais e produção leve.                                                                                                 |
| Backend hosting (Render)                  | Coberto                          | `apps/api/package.json`, `docs/backend/api-reference.md` (`## 35`)                                                            | Cobertura base correta para API Node/Express.                                                                                                 |
| MongoDB Atlas                             | Coberto                          | `apps/api/package.json` (`mongoose`), `docs/backend/data-model.md`                                                            | Base alinhada com stack MongoDB.                                                                                                              |
| OpenAI API                                | Coberto (opcional)               | `docs/backend/api-reference.md` (`## 32`), `docs/shared/business-rules.md` (`12.6`)                                           | Integração existe em documentação de target state.                                                                                            |
| Email transacional                        | Coberto (opcional)               | `docs/shared/business-rules.md` (`12.6`)                                                                                      | Relatório usa Resend; docs também referem email no backend.                                                                                   |
| Storage de ficheiros/media                | Coberto (opcional)               | `docs/backend/api-reference.md` (`## 34`), `docs/shared/business-rules.md` (`12.6`)                                           | R2 está alinhado com necessidade de uploads/anexos.                                                                                           |
| Observabilidade (Sentry)                  | Coberto parcialmente             | `docs/backend/api-reference.md` (`## 36`), `docs/shared/conventions.md`                                                       | Planos base cobertos; extras por volume (eventos/logs/replays) dependem do uso e devem ser orçamentados separadamente.                        |
| Jobs/cron de backend                      | Coberto                          | `docs/backend/api-reference.md` (`## 33`), `docs/shared/business-rules.md` (`12.6`)                                           | Passa a incluir opções com preço oficial (Render Cron Jobs, Render Workflows, Upstash QStash/Workflow).                                       |
| Cache/filas (Redis/BullMQ ou equivalente) | Coberto                          | `docs/shared/conventions.md` (`15.1`/Infra), `docs/backend/data-model.md` (boas práticas cache)                               | Passa a incluir opções com preço oficial (Render Key Value, Upstash Redis).                                                                   |
| Realtime/WebSocket                        | Parcial                          | `docs/shared/conventions.md` (`WebSockets`), `apps/frontend/src/contexts/SocketContext.tsx` (socket local de desenvolvimento) | Custos de infraestrutura de realtime mantêm forte dependência de volume e ativação efetiva do backend WS.                                     |
| CI/CD e validação automática              | Coberto parcialmente             | `docs/operations/runbooks.md`                                                                                                 | Passa a incluir GitHub Actions e Render Build Pipeline. Parte de pricing de pipeline extra da Render não é pública (dashboard), ver secção 8. |

## 4) Custos por componente (USD)

Como interpretar esta secção:

- preços mensais diretos são mais previsíveis;
- preços por uso podem iniciar em `$0` e subir com adoção.

### 4.1 Frontend (Vercel)

O Vercel é onde armazenamos o que o utilizador vai ver (frontend), e tem planos:

- Hobby: **$0/mês**
- Pro: **$20/mês + uso adicional**

Numa fase inicial, o plano Hobby será suficiente.

Fonte oficial: [Vercel Pricing](https://vercel.com/pricing)

### 4.2 Backend (Render)

O Render é onde armazenamos a lógica de toda a aplicação e onde interpretamos os dados e os pedidos do frontend. Tem planos:

- Web Service Starter: **$7/mês**
- Web Service Standard: **$25/mês**
- Web Service Pro: **$85/mês**
- Workspace Professional (quando necessário): **$19/user/mês**

Numa fase inicial, o plano Starter será suficiente para o backend. O plano Standard pode ser necessário caso haja um crescimento significativo de tráfego ou necessidade de mais recursos.
Fonte oficial: [Render Pricing](https://render.com/pricing)

### 4.3 Base de dados (MongoDB Atlas)

O MongoDB Atlas é onde armazenamos os dados da aplicação (utilizadores, planos de treino, mensagens, etc.). É a base de dados da aplicação.

De forma muito simples, o frontend é o que o utilizador vê e comunica com o backend.
O backend é a lógica que processa os pedidos do frontend e interage com a base de dados, que é onde os dados são armazenados.

Por exemplo, se o utilizador quiser ver um treino, o frontend envia um pedido ao backend para obter esse treino, que por sua vez consulta a base de dados para obter os dados do treino e depois envia-os de volta ao frontend para serem exibidos.

Tem opções:

- Flex: tipicamente **~$8/mês** (cap até **$30/mês**)
- Dedicated (entrada M10): **a partir de ~$56.94/mês**

Numa fase inicial, o plano Flex será suficiente. O plano Dedicated pode ser necessário caso haja um crescimento significativo de dados, tráfego ou necessidade de performance.

Fontes oficiais: [MongoDB Atlas Flex Costs](https://www.mongodb.com/docs/atlas/billing/atlas-flex-costs/), [MongoDB Pricing](https://www.mongodb.com/pricing)

### 4.4 Storage opcional (Cloudflare R2)

O Cloudflare R2 é onde podemos armazenar ficheiros e media (imagens, vídeos, PDFs, etc.) que os utilizadores possam querer guardar ou partilhar. É opcional porque depende do volume de ficheiros e da necessidade de armazenamento externo.

- Storage (Standard): **$0.015 / GB-mês**
- Class A: **$4.50 / milhão requests**
- Class B: **$0.36 / milhão requests**
- Egress: **$0**
- Free tier mensal (Standard): **10 GB-mês + 1M Class A + 10M Class B**

Fonte oficial: [Cloudflare R2 Pricing](https://developers.cloudflare.com/r2/pricing/)

### 4.5 Email opcional (Resend)

O Resend é um serviço de email transacional que podemos usar para enviar emails automáticos aos utilizadores (confirmação de conta, lembretes, notificações, etc.). É opcional porque depende do volume de emails e da necessidade de comunicação por email.

- Free: **$0/mês** (3,000 emails/mês, limite diário 100)
- Pro: **$20/mês** (50,000 emails/mês)
- Scale: **$90/mês** (100,000 emails/mês)
- Extra emails em planos pagos: **$0.90 / 1,000**

Numa fase inicial, o plano Free pode ser suficiente para testes e primeiros utilizadores.

Fonte oficial: [Resend Pricing](https://resend.com/pricing)

### 4.6 Observabilidade opcional (Sentry)

Observabilidade é o processo de monitorizar e compreender o comportamento da aplicação quando está online, incluindo erros, desempenho e experiência do utilizador. O Sentry é uma plataforma que nos ajuda a fazer isso, e tem planos:

- Developer: **$0/mês**
- Team: **$26/mês**
- Business: **$80/mês**
- Exemplo de extra explícito na página: logs adicionais **+$0.50/GB**

Numa fase inicial, o plano Developer pode ser suficiente para monitorizar erros básicos.

Fonte oficial: [Sentry Pricing](https://sentry.io/pricing/)

### 4.7 IA opcional (OpenAI API)

Referência usada neste relatório:

- `GPT-5.4`: input **$2.50 / 1M**, cached input **$0.25 / 1M**, output **$15.00 / 1M**
- `GPT-5.4 mini`: input **$0.75 / 1M**, cached input **$0.075 / 1M**, output **$4.50 / 1M**

Exemplo mensal simplificado (`1M input + 0.5M output`):

- `GPT-5.4 mini`: **~$3**
- `GPT-5.4`: **~$10**

Nota: Isto daria cerca de 800 planos de treino realizados pela IA. Ou cerca de 4500 mensagens de chat enviadas pela IA.

Fonte oficial: [OpenAI API Pricing](https://openai.com/api/pricing)

### 4.8 CI/CD e validação automática

CI/CD (Continuous Integration/Continuous Deployment) é o processo de automatizar a construção, teste e deploy da aplicação. É importante para garantir que as alterações no código são testadas e implementadas de forma eficiente e fiável, para que novas alterações não quebrem a aplicação.

| Opção                             | Faixa USD/mês                                                                                                                                   | O que inclui                                              | Quando usar                                         | Fonte                                                                                                                                                                                               |
| --------------------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------- | --------------------------------------------------------- | --------------------------------------------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| GitHub Actions (standard runners) | **$0** dentro da quota; acima da quota varia por minuto (ex.: Linux 2-core **$0.006/min**, Windows 2-core **$0.010/min**, macOS **$0.062/min**) | Quota mensal por plano + billing por minuto fora da quota | CI/CD principal para repo GitHub                    | [GitHub Actions billing](https://docs.github.com/en/billing/concepts/product-billing/github-actions), [Actions runner pricing](https://docs.github.com/en/billing/reference/actions-runner-pricing) |
| GitHub Actions cache extra        | **$0.07/GiB/mês** (acima do incluído)                                                                                                           | Cache storage adicional                                   | Repositórios com cache pesado de dependências/build | [GitHub Actions billing](https://docs.github.com/en/billing/concepts/product-billing/github-actions)                                                                                                |
| Render Build Pipeline             | Inclui **500 min/mês** (Hobby) e **500 min/mês por membro** (Professional+); extra: **preço não encontrado** (público)                          | Build + pre-deploy pipeline no Render                     | Fluxo de deploy centrado em Render                  | [Render Build Pipeline](https://render.com/docs/build-pipeline)                                                                                                                                     |

Numa fase inicial, o uso de CI/CD pode ser mantido dentro das quotas gratuitas, mas é importante monitorizar o consumo de minutos e o tipo de runner usado para evitar custos inesperados.

### 4.9 Cache/Filas

O cache é uma camada de armazenamento temporário que ajuda a acelerar o acesso a dados frequentemente usados, enquanto as filas (queues) são usadas para gerir tarefas assíncronas e desacoplar partes da aplicação. Ambos são importantes para melhorar a performance e a escalabilidade da aplicação.

Um Render Key Value é um serviço de cache simples e rápido, ideal para armazenar dados temporários e reduzir a carga na base de dados. O Upstash Redis é uma solução de cache e filas baseada em Redis, que oferece mais funcionalidades e escalabilidade, mas pode ser mais complexa de configurar.

| Opção                     | Faixa USD/mês                                                                                     | Limites/throughput        | Quando usar                         | Fonte                                                                   |
| ------------------------- | ------------------------------------------------------------------------------------------------- | ------------------------- | ----------------------------------- | ----------------------------------------------------------------------- |
| Render Key Value Free     | **$0**                                                                                            | 25 MB RAM, 50 ligações    | Desenvolvimento/testes              | [Render Pricing](https://render.com/pricing)                            |
| Render Key Value Starter  | **$10/mês**                                                                                       | 256 MB RAM, 250 ligações  | MVP leve com cache e filas simples  | [Render Pricing](https://render.com/pricing)                            |
| Render Key Value Standard | **$32/mês**                                                                                       | 1 GB RAM, 1,000 ligações  | MVP com carga média                 | [Render Pricing](https://render.com/pricing)                            |
| Upstash Redis Free        | **$0**                                                                                            | 256 MB, 500K comandos/mês | PoC e baixo tráfego                 | [Upstash Redis Pricing](https://upstash.com/docs/redis/overall/pricing) |
| Upstash Redis PAYG        | **$0.20 / 100K requests** + storage **$0.25/GB** + bandwidth (200 GB grátis, depois **$0.03/GB**) | Modelo elástico por uso   | Carga variável sem compromisso fixo | [Upstash Redis Pricing](https://upstash.com/docs/redis/overall/pricing) |

Numa fase inicial, o uso de cache e filas pode ser mantido dentro das opções gratuitas, mas é importante monitorizar o volume de comandos e o uso de memória para evitar custos adicionais.

### 4.10 Jobs/Cron / processamento assíncrono

Jobs e cron são tarefas automáticas que a aplicação executa em segundo plano, como enviar emails, processar dados ou realizar limpezas periódicas.

| Opção            | Faixa USD/mês                                                                                                       | Modelo de cobrança                     | Quando usar                                     | Fonte                                                                         |
| ---------------- | ------------------------------------------------------------------------------------------------------------------- | -------------------------------------- | ----------------------------------------------- | ----------------------------------------------------------------------------- |
| Render Cron Jobs | Desde **$1/mês**; Starter **$0.00016/min**, Standard **$0.00058/min**, Pro **$0.00197/min**                         | Prorata por minuto                     | Tarefas agendadas simples (lembretes/limpezas)  | [Render Pricing](https://render.com/pricing)                                  |
| Render Workflows | Starter **$0.05/h**, Standard **$0.20/h**, Pro **$0.40/h** (+ concorrência extra **$0.20 por run concorrente/mês**) | Compute por hora + concorrência mensal | Processamento assíncrono mais robusto e fan-out | [Render Workflows Limits & Pricing](https://render.com/docs/workflows-limits) |
| Upstash QStash   | Free **$0** (1,000 msgs/dia), PAYG **$1/100K mensagens**, Fixed **$180/mês**                                        | Mensagens/schedules                    | Filas HTTP, retries e scheduling desacoplado    | [Upstash QStash Pricing](https://upstash.com/docs/qstash/overall/pricing)     |
| Upstash Workflow | Free **$0** (1,000 steps/dia), PAYG **$1/100K steps**, Fixed **$180/mês**                                           | Steps por workflow                     | Orquestração assíncrona serverless              | [Upstash Workflow Pricing](https://upstash.com/pricing/workflow)              |

Para já não temos jobs/cron ativos, mas é importante considerar os custos potenciais ao planear a implementação de tarefas automáticas, especialmente se houver um volume significativo de execuções ou necessidade de maior escalabilidade.

## 5) Cenários mensais por fase

Faixas abaixo incluem o total anterior + impacto incremental dos blocos novos, com perfil de uso típico (não máximo teórico).

Para cliente não técnico, a coluna principal é: **Novo total estimado (min-max)**.

| Fase                                                | Total atual          | Acréscimo CI/CD+Cache/Filas+Jobs | Novo total estimado (min-max) | Observações                                                                   |
| --------------------------------------------------- | -------------------- | -------------------------------- | ----------------------------- | ----------------------------------------------------------------------------- |
| A — Desenvolvimento/Testes lean/ Lançamento inicial | **~$15/mês**         | **+$0 a +$1**                    | **~$15 a $16/mês**            | Normalmente fica dentro de quotas grátis; cron pode introduzir mínimo mensal. |
| B — Primeiro degrau de crescimento                  | **~$35 a $72/mês**   | **+$1 a +$19**                   | **~$36 a $91/mês**            | Cenário comum: cache starter + cron básico + CI quase todo dentro da quota.   |
| C — Segundo degrau de crescimento                   | **~$127 a $152/mês** | **+$21 a +$90**                  | **~$148 a $242/mês**          | Crescimento típico ao ativar cache pago, jobs recorrentes e CI fora de quota. |
| D — Aplicação robusta                               | **~$163 a $203/mês** | **+$70 a +$335**                 | **~$233 a $538/mês**          | Fase mais sensível ao volume de pipelines, filas e compute assíncrono.        |

## 6) Fontes verificadas

### 6.1 Fontes externas oficiais

| Serviço                           | URL oficial                                                                | Data de verificação | Notas                                                 |
| --------------------------------- | -------------------------------------------------------------------------- | ------------------- | ----------------------------------------------------- |
| Vercel Pricing                    | https://vercel.com/pricing                                                 | 2026-04-10          | Hobby/Pro e uso adicional.                            |
| Render Pricing                    | https://render.com/pricing                                                 | 2026-04-10          | Web services, Key Value, Cron Jobs, Workflows.        |
| Render Build Pipeline             | https://render.com/docs/build-pipeline                                     | 2026-04-10          | Minutos incluídos e nota de pricing no dashboard.     |
| Render Workflows Limits & Pricing | https://render.com/docs/workflows-limits                                   | 2026-04-10          | Compute por hora e concorrência adicional.            |
| GitHub Actions Billing            | https://docs.github.com/en/billing/concepts/product-billing/github-actions | 2026-04-10          | Quotas, billing de minutos e cache extra.             |
| GitHub Actions Runner Pricing     | https://docs.github.com/en/billing/reference/actions-runner-pricing        | 2026-04-10          | Tarifas por minuto por tipo de runner.                |
| MongoDB Atlas Flex Costs          | https://www.mongodb.com/docs/atlas/billing/atlas-flex-costs/               | 2026-04-10          | Flex $8-$30 e cap mensal.                             |
| MongoDB Pricing                   | https://www.mongodb.com/pricing                                            | 2026-04-10          | Dedicated a partir de $0.08/h e $56.94/mês.           |
| Cloudflare R2 Pricing             | https://developers.cloudflare.com/r2/pricing/                              | 2026-04-10          | Storage, operações, egress e free tier.               |
| Resend Pricing                    | https://resend.com/pricing                                                 | 2026-04-10          | Free/Pro/Scale e overage.                             |
| Sentry Pricing                    | https://sentry.io/pricing/                                                 | 2026-04-10          | Developer/Team/Business e exemplos de extras.         |
| OpenAI API Pricing                | https://openai.com/api/pricing                                             | 2026-04-10          | GPT-5.4, GPT-5.4 mini e notas de regional processing. |
| Upstash Redis Pricing             | https://upstash.com/docs/redis/overall/pricing                             | 2026-04-10          | Free, PAYG e custos de storage/bandwidth.             |
| Upstash QStash Pricing            | https://upstash.com/docs/qstash/overall/pricing                            | 2026-04-10          | Mensagens/dia e PAYG por 100K.                        |
| Upstash Workflow Pricing          | https://upstash.com/pricing/workflow                                       | 2026-04-10          | Steps/dia e PAYG por 100K.                            |

## 7) Custos complementares (CI/CD, Cache/Filas e Jobs)

Para a stack Iron Cave, os custos complementares tendem a evoluir nesta ordem:

1. **CI/CD**: começa frequentemente a **$0** (quotas incluídas), mas cresce com minutos fora da quota e tipo de runner.
2. **Cache/Filas**: pode arrancar em **$0** (tiers free), mas normalmente entra em **$10–$32/mês** no MVP para estabilidade operacional.
3. **Jobs/assíncrono**: inicia em **$1/mês** com cron básico e pode escalar rapidamente com workflows e concorrência.

Regra prática: manter limites de orçamento por bloco desde o início e rever mensalmente consumo real antes de mudar tier.

## Changelog

- **v1.4.0 — 2026-04-10**
    - Revisão linguística para PT-PT e correções ortográficas pontuais.
    - Correção de pontuação e fecho de frase incompleta na secção de jobs/cron.
- **v1.3.0 — 2026-04-10**
    - Adicionada secção `Resumo Executivo (para cliente)` no início do documento.
    - Linguagem simplificada em pontos-chave para leitura por público não técnico, sem remover conteúdo técnico existente.
    - Incluídas notas de interpretação simples nas secções de custos e cenários.
- **v1.2.0 — 2026-04-10**
    - Validação de preços com fontes oficiais e data de verificação explícita.
    - Cobertura concluída dos blocos em falta: CI/CD, Cache/Filas e Jobs/Cron.
    - Cenários mensais atualizados com acréscimo dos três blocos e novo total por fase.
    - Adicionada secção reutilizável `Custos complementares (CI/CD, Cache/Filas e Jobs)`.
    - Registados pontos com `preço não encontrado` e pricing dependente de volume.
- **v1.1.0 — 2026-04-10**
    - Reestruturação para formato canónico de documentação (`objetivo`, `escopo`, `cobertura`, `cenários`, `referências`, `estado`, `changelog`).
    - Adicionada auditoria explícita de cobertura face à codebase e docs canónicas.
    - Identificados itens parcialmente cobertos/em falta para custo operacional completo (jobs, cache/filas, CI/CD, observabilidade alargada).
- **v1.0.0 — 2026-04-10**
    - Primeira versão do relatório de custos mensais.
