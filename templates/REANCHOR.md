<!-- auttares-colab:reanchor protocol=0.1 -->
# REANCHOR — {{NOME DO PROJETO}}

```text
Natureza:   ponto de entrada para retomar o contexto deste projeto (Auttares Colab, protocolo 0.1).
            NÃO decide nada. Se esta página divergir de uma fonte citada, a fonte vence e a
            divergência é um defeito desta página.
Regras:     nenhuma transcrição de conversa aqui; nenhum histórico inventado; reancorar não
            autoriza implementação. Quem decide é {{DONO}}.
Idioma:     colab-language: pt-BR
```

## 1. Onde está cada coisa

| O quê | Onde |
|---|---|
| Propósito e visão | {{caminho ou "nada registrado"}} |
| Arquitetura | {{caminho ou "nada registrado"}} |
| Decisões | {{caminho do registro de decisões ou "nada registrado"}} |
| Plano / roadmap | {{caminho ou "nada registrado"}} |
| Validação e testes | {{como se valida; caminho ou comando documentado}} |
| Resultados e evidência histórica | {{caminhos; `git log`}} |
| Instruções do agente | {{CLAUDE.md / AGENTS.md ou "nenhuma"}} |

## 2. Referência operacional

```text
verificado em        {{AAAA-MM-DD}}, contra o commit {{sha curto}} ({{ramo}})
base aceita          {{a última versão, tag ou commit aceito, ou "nenhuma ainda"}}
em curso             {{o trabalho em andamento, ou "nada"}}
próximo planejado    {{o próximo passo, com a fonte, ou "nada planejado"}}
autorização          {{AUTORIZADO por <quem, onde> | NÃO autorizado | desconhecido}}
bloqueios            {{os bloqueios, ou "nenhum registrado"}}
```

`colab-watch: {{caminhos cujas mudanças tornam este instantâneo velho}}`

**Envelhecimento.** Com `R=$(git log -1 --format=%H -- <este arquivo>)`, rode
`git log --oneline "$R"..HEAD -- <caminhos da colab-watch>`. Se a saída não for vazia, ou se a
árvore tiver mudanças nesses caminhos, o instantâneo está velho e as fontes vencem. Uma saída vazia
não prova que as conversas fora do Git estão sincronizadas.

**Obrigações pendentes** (são obrigações, não discussões):

- {{obrigação}}: {{condição que a torna relevante}} ({{fonte}}), ou "nenhuma registrada"

## 3. Decisões vigentes

As decisões moram em {{registro de decisões}}. As que restringem o trabalho atual:

- {{ID}}: {{uma linha}} ({{caminho}}), ou "nenhuma decisão registrada ainda"

## 4. OPEN DISCUSSIONS — ROLLING CHECKPOINT

Só as discussões que ainda pedem raciocínio. Não é backlog, linha do tempo nem transcrição.

{{entradas CP-nnn no formato do protocolo, ou: "Nenhuma discussão aberta registrada."}}

## 5. Como manter

- **Entra** uma questão não resolvida que precisa de continuidade, com um ID `CP-nnn` estável.
- **Atualiza-se** reescrevendo a entrada quando o entendimento muda de forma material.
- **Fecha-se** quando a pergunta central se resolve: a conclusão durável vai, com a razão, para
  {{registro de decisões}}, e a entrada **sai** daqui.
- **Reabre-se** só com uma pergunta nova, citando a conclusão anterior.
- Atualiza-se nas transições: não a cada mensagem.
- O dono confirma as decisões. O revisor propõe. O agente aplica dentro de uma missão autorizada.

Protocolo completo: Auttares Colab, `protocol/continuity.md` (versão 0.1).
