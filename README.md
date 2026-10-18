# Auttares Colab

> **English summary.** Auttares Colab is a small Claude Code plugin that keeps a software project's
> technical and intellectual context recoverable across AI conversations. It defines a
> collaboration protocol (Human Owner decides, AI reviewer reasons and reviews, execution agent
> implements and gathers evidence), initializes a per-project `REANCHOR.md` from real repository
> evidence, and generates a repository-grounded handoff package for a new reviewer conversation.
> Status: 0.1.0, local and experimental, not published.

O Auttares Colab é um mini-framework portátil para a colaboração de engenharia entre três partes:

- o **dono humano**, que decide, autoriza e aceita;
- o **revisor de IA** (por exemplo, o ChatGPT), que discute arquitetura, revisa resultados e
  preserva o raciocínio;
- o **agente executor** (por exemplo, o Claude Code), que examina o repositório, implementa o que
  foi autorizado e produz evidência.

## Que problema resolve

Conversas longas com uma IA perdem o fio: a conversa nova não sabe em que pé o projeto está, que
decisões valem, o que está autorizado nem o que ainda estava em discussão. Colar a transcrição
antiga é caro, e ela traz junto o que já foi superado. Uma cópia velha de um resumo é pior ainda,
porque parece atual.

O Colab resolve isso com duas peças:

1. **`REANCHOR.md` no projeto.** É um ponto de entrada curto, que **aponta** para as fontes
   autoritativas que o projeto já tem e mantém um **checkpoint intelectual rolante**: só as
   discussões ainda abertas. O que se resolve sai do checkpoint e vai para o registro de decisões.
2. **Um pacote de reancoragem gerado do repositório real.** O agente lê o estado atual (Git,
   documentos, o REANCHOR), verifica a origem (HEAD, remoto, árvore, hash do REANCHOR lido,
   envelhecimento) e produz um relatório autossuficiente para colar numa conversa nova.

**A diferença para instruções comuns de agente** (`CLAUDE.md`, `AGENTS.md`): elas dizem **como**
trabalhar. O Colab preserva **onde o projeto está e por quê**, para um revisor que **não** tem
acesso ao repositório, e com regras de evidência: o que é fato verificado, o que é só documentado,
o que é inferência, o que está aberto e o que está em conflito.

## Estrutura

```text
.claude-plugin/plugin.json      manifesto do plugin (versão 0.1.0)
.claude-plugin/marketplace.json marketplace local "auttares" (só este plugin, fonte ./)
protocol/collaboration.md       papéis, autoridade, evidência, sem transcrições
protocol/continuity.md          REANCHOR, checkpoint rolante, proveniência, pacote
templates/REANCHOR.md           o modelo que o init preenche
skills/init/SKILL.md            /auttares-colab:init
skills/reanchor/SKILL.md        /auttares-colab:reanchor
```

As duas skills leem o mesmo protocolo (`${CLAUDE_PLUGIN_ROOT}/protocol/`). Nenhuma redefine as
regras. O conhecimento de cada projeto fica **no próprio projeto**: o Colab não centraliza decisões.

## Instalação local

Sem publicação ainda. Este repositório é também um marketplace local (`.claude-plugin/marketplace.json`,
nome `auttares`), cujo único plugin é a própria raiz. Para instalar no escopo de usuário, o que vale
para todos os projetos deste usuário nesta instalação do Claude Code:

```bash
claude plugin marketplace add /caminho/para/auttares-colab --scope user
claude plugin install auttares-colab@auttares --scope user
claude plugin list        # auttares-colab@auttares, Scope: user, Status: enabled
```

O Claude Code instala uma **cópia** em `~/.claude/plugins/cache/auttares/auttares-colab/<versão>/`.
Uma mudança neste repositório só chega à instalação depois de:

```bash
claude plugin marketplace update auttares
claude plugin update auttares-colab@auttares
```

e de reiniciar a sessão (ou `/reload-plugins` numa sessão aberta).

Para uma sessão só, sem instalar: `claude --plugin-dir /caminho/para/auttares-colab`.

## Uso

### Inicializar um projeto: `/auttares-colab:init [notas]`

1. **Descoberta (só leitura).** Lê o Git, o README, os manifestos, as instruções do agente, a
   arquitetura, o roadmap, as decisões, a validação e as perguntas abertas documentadas.
2. **Proposta.** Diz o que já basta, o que falta e exatamente o que vai criar ou alterar (o
   REANCHOR, a linha `colab-watch`, as entradas do checkpoint, a linha de descoberta opcional).
   Lista as lacunas como perguntas. **Para e pede aprovação.**
3. **Aplicação** (depois de "aprovo"). Cria o `REANCHOR.md` **preenchido com evidência real**,
   pronto para o `reanchor`. Nada inventado: lacuna fica escrita como lacuna. Num projeto vazio,
   diz que ainda não há histórico nem decisões.

Rodar de novo num projeto já inicializado não recria nada: só propõe os ajustes necessários.

O init não comita. Comitar o `REANCHOR.md` é decisão do dono, e é o que permite ao `reanchor` medir
o envelhecimento.

### Reancorar: `/auttares-colab:reanchor [normal | trajectory | deep <tema>]`

- **normal** (padrão): o estado, as decisões que restringem o trabalho, o planejado versus o
  autorizado, o checkpoint aberto, as obrigações e onde continuar.
- **trajectory**: também as razões, as alternativas e as mudanças de rota.
- **deep `<tema>`**: só o tema, com a evidência dele.

É **somente leitura**: a skill bloqueia `Write` e `Edit` e não comita, não atualiza o checkpoint e
não autoriza nada. Cole a saída numa conversa nova com o revisor.

## O checkpoint rolante

Uma entrada `CP-nnn` **entra** quando uma questão intelectual precisa de continuidade. **É
reescrita** quando o entendimento muda de forma material. **Fecha** quando a pergunta central se
resolve: a conclusão vai, com a razão, para o registro de decisões do projeto. Depois **sai** do
checkpoint, e a história fica no registro de decisões e no Git. Uma entrada fechada só **reabre**
com uma pergunta nova. Não é diário, backlog nem transcrição.

## Como as decisões são preservadas

No lugar que o projeto já usa: ADRs, registro de decisões, revisões. O Colab só aponta. Se o
projeto não tiver nenhum lugar, o init pode **propor** um registro mínimo, criado só com a
aprovação do dono e quando houver uma decisão real para registrar.

## Usando com outro projeto

Nada no Colab assume uma linguagem, um layout de documentação ou uma metodologia. O init descobre o
que existe e o REANCHOR aponta para lá. Um projeto com ADRs, um com só um README e um vazio são
casos suportados.

## Limitações (0.1.0)

- Testado com o Claude Code 2.1.300 (por `--plugin-dir`) e 2.1.302 (instalado pelo marketplace
  local, escopo de usuário, no WSL/Linux). A instalação vale para esse usuário nessa instalação:
  não para o Windows nativo, outra máquina, outra conta nem outros agentes.
- O fluxo do init é conversacional, em dois passos (proposta, depois aprovação). Não há modo de
  aprovação automática.
- O envelhecimento depende do Git e da linha `colab-watch`. Ele não sabe nada de conversas fora do
  repositório.
- O pacote sai em português do Brasil por padrão (`colab-language` no REANCHOR).
- A qualidade do pacote depende das fontes do projeto: o Colab não reconstrói razões que nunca
  foram escritas.
- Ainda sem licença escolhida (ver CHANGELOG): não distribua até o dono decidir.
