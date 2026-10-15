---
name: reanchor
description: Gera, a partir do estado REAL do repositório, um pacote de continuidade autossuficiente para retomar a colaboração numa conversa nova com o revisor de IA. Somente leitura. Modos normal, trajectory e deep <tema>.
argument-hint: "[normal | trajectory | deep <tema>]"
disable-model-invocation: true
disallowed-tools: Write, Edit, NotebookEdit
---

# Auttares Colab — reanchor

Argumentos: `$ARGUMENTS`

Leia primeiro o protocolo. Ele é a única definição das regras e esta skill não o redefine:
`${CLAUDE_PLUGIN_ROOT}/protocol/collaboration.md` e
`${CLAUDE_PLUGIN_ROOT}/protocol/continuity.md` (§1 descoberta, §2 checkpoint, §3 proveniência,
§4 pacote).

## Regras invioláveis

- **Somente leitura.** Não crie, edite, mova nem apague arquivo. Nada de commit, push, tag,
  `git fetch`, `git pull`, build, teste, instalação ou serviço. Comandos aceitáveis: ler arquivos;
  `git status|log|show|diff|rev-parse|branch|remote|ls-remote|ls-files|tag -l`; `sha256sum`;
  `date`.
- **Não atualize o checkpoint** nem nenhuma decisão. Não autorize nem comece trabalho.
- **Não invente** conversa, decisão, aceitação nem teste. A falta é declarada.
- **A fonte vence o resumo.** Um fato verificado mais novo prevalece sobre o REANCHOR, e a
  divergência é marcada CONFLICT.
- Não use memória do agente fora do repositório como fonte.
- Sem segredos, credenciais nem transcrição de conversa.

## 1. Modo

`$ARGUMENTS`, sem diferenciar maiúsculas:

- vazio ou `normal` → NORMAL;
- `trajectory` → TRAJECTORY;
- `deep <tema>` → DEEP sobre o tema;
- `deep` sem tema → pergunte o tema e PARE;
- qualquer outra coisa → responda só `Uso: /auttares-colab:reanchor [normal | trajectory | deep <tema>]` e PARE.

## 2. Descoberta e pré-requisito

Ache o REANCHOR (`continuity.md` §1.1).

- **Nenhum REANCHOR:** responda que o projeto não está inicializado e que o
  `/auttares-colab:init` vem primeiro. Diga o pouco que foi verificado (repositório, HEAD) e PARE.
  **Não fabrique um pacote.**
- **Vários REANCHORs:** CONFLICT, conforme §1.1.
- **Sem marcador:** é legado e compatível. Use-o e diga isso.
- **Só na árvore de trabalho (não comitado):** use-o normalmente e declare-o **não publicado**. Isso
  não é motivo para recusar o pacote.

## 3. Proveniência (sempre, antes de ler o resto)

Conforme `continuity.md` §3:

- `git rev-parse --show-toplevel`; `git remote -v`; `git branch --show-current`; `git rev-parse HEAD`.
- O `origin/<ramo>` em cache, com `git rev-parse` (se existir).
- `git ls-remote <remoto> refs/heads/<ramo>` para a verificação real. Se falhar, diga "remoto não
  verificado" e o motivo.
- `git status --porcelain`, dizendo se as mudanças tocam o pacote.
- `date -Is`.
- `git log -1 --format='%H %cs' -- <REANCHOR>`.
- `sha256sum <REANCHOR>` e `git diff --quiet HEAD -- <REANCHOR>` (o lido é o comitado?).
- O envelhecimento pela linha `colab-watch` → CURRENT, STALE ou UNVERIFIABLE.

Sem Git, diga isso e liste as evidências usadas. Mudança não comitada nunca é base publicada.

## 4. Leitura seletiva

- **NORMAL:**
  - o REANCHOR inteiro;
  - as fontes que ele aponta para o estado atual, o plano e o próximo passo, só as seções
    relevantes;
  - as decisões que restringem o trabalho atual;
  - o último resultado ou revisão registrado;
  - `git log --oneline -15`.
- **TRAJECTORY:** o NORMAL, mais as razões e alternativas das decisões que governam o trabalho
  atual e o próximo, as mudanças de rota do plano e `git log --oneline -40`.
- **DEEP:** localize o tema (`grep` nas fontes), leia só os documentos donos e a evidência citada,
  e diga o que leu e o que não leu.

Se o STALE afetar um fato, leia de novo a fonte atual dele. Não copie documentos inteiros.

## 5. O pacote

Produza, na resposta final, o pacote de `continuity.md` §4, seções **A a K**. Use o idioma do
REANCHOR (`colab-language`), por padrão português do Brasil.

- Marque com VERIFIED FACT, DOCUMENTED CLAIM, INFERENCE, OPEN QUESTION e CONFLICT.
- Mantenha exatos os identificadores, caminhos, hashes e status.
- Separe sempre planejado, autorizado, implementado, verificado e aceito.
- Na seção F, ponha só as entradas OPEN e WAITING_FOR_INPUT do checkpoint. Discussão fechada,
  marco e obrigação não entram ali.
- Entregue uma só versão. Termine depois da seção K. Não proponha missão nem decida prioridade.
