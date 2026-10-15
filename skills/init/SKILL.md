---
name: init
description: Prepara um projeto para a colaboração Auttares Colab (dono, revisor de IA, agente executor). Descobre o que o projeto já tem, propõe o mínimo e, só com a aprovação explícita do dono, cria um REANCHOR.md preenchido com evidência real, pronto para o /auttares-colab:reanchor.
argument-hint: "[notas do dono sobre o projeto, opcional]"
disable-model-invocation: true
---

# Auttares Colab — init

Notas do dono passadas na chamada: `$ARGUMENTS`. Se houver notas, são informação do dono. Use-as
marcadas como "informado pelo dono na inicialização" e nunca como fato verificado do repositório.

Antes de qualquer coisa, leia o protocolo. Ele é a única definição das regras e esta skill não o
redefine:

- `${CLAUDE_PLUGIN_ROOT}/protocol/collaboration.md`
- `${CLAUDE_PLUGIN_ROOT}/protocol/continuity.md`
- o modelo `${CLAUDE_PLUGIN_ROOT}/templates/REANCHOR.md`

Fale com o dono em português do Brasil.

## Regras

- **Nada é escrito antes da aprovação explícita do dono** a uma proposta concreta. As fases 1 e 2
  só leem. Um "ok" vago para outra coisa não é aprovação desta proposta.
- Nada de commit, push, tag, instalação, build, teste caro nem mudança fora do que foi aprovado.
- Não invente: nenhum histórico, decisão, razão ou discussão que não esteja nas fontes. O que falta
  vira lacuna declarada e pergunta ao dono.
- Não imponha a estrutura de outro projeto. Reaproveite a documentação que já existe, sem
  duplicá-la. Não crie um sistema paralelo de decisões quando houver um adequado.
- A governança existente do projeto vence. Um conflito com o protocolo é reportado ao dono, não
  resolvido.

## Fase 1 — descoberta (só leitura)

Na raiz do repositório, quando houver Git: `git rev-parse --show-toplevel`, `git status --porcelain`,
`git log --oneline -20`, `git branch --show-current`, `git remote -v`, `git tag -l`, `git ls-files`.
Sem Git, liste os arquivos fora de diretórios de build e dependências.

Procure e leia **seletivamente**:

1. Um REANCHOR já existente (descoberta de `continuity.md` §1.1). Com marcador, o projeto **já está
   inicializado**: vá para a seção "Projeto já inicializado". Sem marcador, é um REANCHOR legado:
   proponha adotá-lo ou ajustá-lo, nunca substituí-lo em silêncio.
2. O nome, o propósito e as tecnologias: README, manifestos (`Cargo.toml`, `package.json`,
   `pyproject.toml`, `go.mod`, …).
3. As instruções do agente (`CLAUDE.md`, `AGENTS.md`, `.cursorrules`, …).
4. A arquitetura e a estrutura da documentação.
5. O plano e o progresso: roadmap, status, changelog, milestones, issues locais.
6. As decisões aceitas e onde moram (ADRs, registro de decisões, revisões), com status e razões.
7. A validação: como se testa, CI, as convenções.
8. As perguntas em aberto **documentadas** (notas, ADRs propostos, TODOs de desenho).
9. Os mecanismos de contexto que já existam.
10. Os conflitos potenciais entre a governança existente e o protocolo.

## Fase 2 — proposta (e PARE)

Responda ao dono com:

- **O que encontrei**, com caminhos e marcas de evidência.
- **O que já basta**, para ser reaproveitado.
- **O que falta** para a continuidade.
- **A proposta mínima, exata**:
  - o caminho do REANCHOR e por quê;
  - um resumo do que cada seção vai conter, vindo de quais fontes;
  - a linha `colab-watch`;
  - as entradas CP-nnn propostas (só discussões abertas documentadas);
  - a linha de descoberta opcional em `CLAUDE.md` ou `AGENTS.md`;
  - um registro de decisões, só se não existir nenhum **e** houver uma decisão real a registrar.
- **As lacunas**: o que não está no repositório. Faça as perguntas ao dono (por exemplo, quem
  autoriza, qual é a base aceita, o que está autorizado agora), sem presumir a resposta.

Termine pedindo a aprovação explícita (por exemplo: "Responda *aprovo* para aplicar exatamente esta
proposta, ou diga o que mudar") e **PARE**. Não escreva nada nesta resposta.

## Fase 3 — aplicação (só depois da aprovação, numa resposta seguinte do dono)

1. Aplique **exatamente** o aprovado, mais as respostas que o dono der às lacunas.
2. Preencha o REANCHOR a partir do modelo, com **evidência real**:
   - o que o projeto faz; o estado atual; onde está cada fonte;
   - as decisões vigentes, com as razões documentadas;
   - o próximo passo e a sua autorização;
   - as obrigações; as discussões abertas documentadas;
   - as referências para recuperar mais contexto.
   Use as marcas de evidência onde importa. Uma lacuna fica escrita como lacuna. Projeto vazio:
   escreva honestamente que ainda não há implementação, histórico nem decisão.
3. Não deixe nenhum `{{…}}` no arquivo.
4. Verifique sem rodar nada caro:
   - o marcador na primeira linha;
   - as seis partes;
   - todo caminho citado existe;
   - a linha `colab-watch` aponta para caminhos que existem;
   - nenhum placeholder sobrou.
5. Relate:
   - os arquivos criados e alterados;
   - o que ficou como lacuna;
   - que o REANCHOR **não foi comitado**: até o commit, um pacote do `reanchor` o mostra como não
     publicado e o envelhecimento fica UNVERIFIABLE. O commit é decisão do dono;
   - o próximo passo: `/auttares-colab:reanchor` para gerar o primeiro pacote.

## Projeto já inicializado (idempotência)

Não reinicialize nem recrie nada. Leia o REANCHOR existente e compare-o com o estado atual:
envelhecimento, caminhos quebrados, fontes novas. Proponha **só** os ajustes necessários, ou diga
que nada é necessário. Nunca apague nem reescreva entradas do checkpoint, decisões ou mudanças não
relacionadas. Também aqui só se escreve com aprovação.
