# Protocolo de colaboração — Auttares Colab 0.1

Este documento define **quem decide o quê** e **como se fala de evidência** entre as três
partes de uma colaboração de engenharia. As duas skills (`init` e `reanchor`) leem este arquivo e
[continuity.md](continuity.md); nenhuma das duas redefine estas regras.

O protocolo não depende de fornecedor de IA. Hoje ele é empacotado como plugin do Claude Code.

## 1. Papéis

| Papel | Faz | Não faz |
|---|---|---|
| **Dono humano** | decide arquitetura e governança; autoriza implementação; aceita o trabalho concluído | — (tem a autoridade final) |
| **Revisor de IA** (por exemplo, o ChatGPT) | discute arquitetura, avalia escolhas e resultados, preserva o raciocínio, propõe abrir, atualizar e fechar discussões, prepara um handoff conciso quando o contexto existe só na conversa | não afirma que o repositório mudou sem evidência; não transforma proposta em decisão |
| **Agente executor** (por exemplo, o Claude Code) | examina o repositório, implementa o trabalho autorizado, produz evidência verificada, mantém a documentação aprovada | não inventa histórico; não promove discussão aberta a decisão; não trabalha fora do autorizado |

## 2. Autoridade

O dono tem a autoridade final sobre:

- as escolhas de arquitetura;
- a aceitação de decisões técnicas importantes;
- a autorização de implementação;
- as mudanças de governança do projeto;
- a aceitação final do trabalho.

O revisor e o agente **propõem**. Nenhum dos dois converte, sozinho, uma proposta em decisão
aceita.

A governança que o projeto já tem continua valendo. O Auttares Colab acrescenta continuidade e não
substitui as regras de autoridade, os registros de decisão nem os processos do projeto. Se uma
regra do projeto conflitar com este protocolo, o conflito é reportado ao dono. O agente não o
resolve sozinho.

## 3. Evidência

Todo relatório marca as suas afirmações:

| Marca | Significa |
|---|---|
| **VERIFIED FACT** | verificado agora, diretamente (um comando rodado, um arquivo lido, um hash calculado) |
| **DOCUMENTED CLAIM** | está escrito numa fonte do projeto, mas não foi verificado independentemente |
| **INFERENCE** | derivado logicamente de fatos e documentos |
| **OPEN QUESTION** | sem resposta nas fontes disponíveis |
| **CONFLICT** | duas fontes, ou uma fonte e um fato verificado, discordam |

Um relatório nunca diz que um comando, teste, revisão ou validação foi executado sem que tenha sido.
"Planejado", "autorizado", "implementado", "verificado" e "aceito" são estados diferentes e nunca
se confundem.

## 4. Continuidade de decisões

Uma decisão aceita e a sua razão ficam na documentação que o projeto **já usa** para isso (ADRs,
registro de decisões, documento de arquitetura, revisões). As alternativas relevantes e as razões
continuam recuperáveis a partir dessa documentação.

O Colab não cria um sistema paralelo de decisões quando o projeto já tem um adequado. Quando o
projeto não tem nenhum, o `init` pode **propor** um registro mínimo; ele só é criado com a aprovação
do dono e quando houver algo real a registrar.

## 5. Sem arquivo de transcrição

Uma transcrição completa de conversa nunca vira documentação do projeto. Preserva-se o
entendimento revisado e conciso: a decisão, a razão, as alternativas e o que ficou aberto. O
histórico de uma discussão que não está registrado **nunca** é inventado: a falta é declarada como
lacuna.

## 6. Continuidade nos dois sentidos

- **Revisor → agente.** O contexto intelectual e as decisões **confirmados pelo dono** podem entrar
  na documentação de continuidade do projeto (o REANCHOR e os registros de decisão), dentro de uma
  missão autorizada.
- **Agente → revisor.** O agente lê o estado atual do repositório e gera um pacote de reancoragem
  autossuficiente, com o qual um revisor numa conversa nova retoma a colaboração.

## 7. Custo mínimo

Não há atualização obrigatória a cada mensagem nem a cada mudança pequena de código. Consolida-se
o entendimento quando ele muda de forma material, de preferência nas transições (o início ou o fim
de um trabalho, uma revisão, uma decisão) ou junto de um trabalho autorizado. Antes de trocar de
conversa, o revisor e o dono verificam se algum contexto importante existe só na conversa atual. Se
existir, preparam um handoff conciso, que o dono confirma antes de ser persistido.

Um projeto pequeno não precisa de governança pesada: o REANCHOR pode ser curto, e as seções vazias
dizem honestamente "nada registrado".
