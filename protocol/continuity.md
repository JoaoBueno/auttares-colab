# Protocolo de continuidade — Auttares Colab 0.1

Define o **REANCHOR** de um projeto, o **checkpoint intelectual rolante** e o **pacote de
reancoragem**. Complementa [collaboration.md](collaboration.md).

## 1. O REANCHOR do projeto

Cada projeto tem **um** `REANCHOR.md`, que é dele e fica no repositório dele. O REANCHOR é o ponto
de entrada para retomar o contexto. Ele **aponta** para as fontes autoritativas do projeto e
**não** as substitui: se um resumo divergir da fonte, a fonte vence e a divergência é um defeito do
REANCHOR.

Um REANCHOR criado pelo Colab começa com o marcador:

```text
<!-- auttares-colab:reanchor protocol=0.1 -->
```

A localização é livre: a raiz, `docs/` ou onde a documentação do projeto já mora.

### 1.1 Descoberta

O `reanchor` e o `init` procuram, nesta ordem:

1. uma linha `auttares-colab:reanchor <caminho>` no arquivo de instruções do agente (`CLAUDE.md`,
   `AGENTS.md`), se existir;
2. arquivos `REANCHOR.md` no repositório, **rastreados ou não**:
   `git ls-files --cached --others --exclude-standard`. Sem Git, arquivos `REANCHOR.md` fora de
   diretórios de build e dependências.

Um REANCHOR que existe só na árvore de trabalho (por exemplo, logo depois do `init`, antes do
commit) **é usado**. O pacote o declara **não publicado**, e o envelhecimento fica UNVERIFIABLE. Um
repositório sem nenhum commit é declarado assim; não há HEAD a citar.

O resultado é um destes:

| Resultado | Significa |
|---|---|
| **um REANCHOR com marcador** | inicializado pelo Colab |
| **um REANCHOR sem marcador** | **compatível (legado)**: é usado, e o pacote diz que não foi criado pelo Colab |
| **vários REANCHORs** | **CONFLICT**: o pacote lista todos, usa o com marcador ou o apontado pelas instruções, e não escolhe em silêncio |
| **nenhum** | **não inicializado**: o `reanchor` não fabrica pacote e indica o `init` |

### 1.2 Conteúdo mínimo

O REANCHOR tem estas partes, nesta ordem; títulos equivalentes são aceitos:

1. **Natureza e regras.** O que a página é, e que ela não decide nada.
2. **Onde está cada coisa.** Ponteiros para visão, arquitetura, decisões, plano, validação,
   resultados e evidência histórica.
3. **Referência operacional.**
   - Um instantâneo pequeno e datado: a base aceita, o trabalho em curso, o próximo passo planejado,
     a autorização desse passo e os bloqueios.
   - A linha de vigilância `colab-watch: <caminhos separados por espaço>`: os caminhos cujas
     mudanças tornam o instantâneo velho.
   - As **obrigações pendentes**: gates e verificações exigidos, cada uma com a condição que a
     torna relevante.
4. **Decisões vigentes.** Ponteiros para onde as decisões do projeto ficam, mais as poucas que
   restringem o trabalho atual. Nunca uma cópia das decisões.
5. **OPEN DISCUSSIONS — ROLLING CHECKPOINT** (§2).
6. **Como manter.** Um resumo curto de §2 e §3, e a referência a este protocolo.

Seções vazias dizem "nada registrado". Nunca são preenchidas com conteúdo inventado.

## 2. O checkpoint intelectual rolante

O checkpoint é o **conjunto de trabalho intelectual atual**: só as discussões que ainda pedem
raciocínio futuro. Ele **não** é diário, transcrição, backlog, issue tracker, linha do tempo nem a
lista do trabalho planejado. Marcos, tarefas e obrigações moram nas suas próprias seções e fontes.

Formato de uma entrada:

```text
### CP-nnn — <a pergunta central>
status         OPEN | WAITING_FOR_INPUT
revisto em     AAAA-MM-DD
entendimento   o entendimento atual (não o histórico de formulações)
alternativas   as alternativas e os argumentos documentados
falta          o que ainda precisa ser decidido ou verificado
fecha quando   o que resolve o objetivo CENTRAL (não toda melhoria relacionada)
referências    os caminhos necessários para retomar
```

Ciclo de vida:

1. **ENTRA** quando uma questão intelectual não resolvida precisa de continuidade. O ID é estável e
   nunca é reaproveitado.
2. **É ATUALIZADA** quando o entendimento muda de forma material: a entrada é **reescrita**, sem
   acumular formulações.
3. **FECHA** quando a pergunta central se resolve:
   - o dono aceita, quando a aceitação é exigida;
   - a conclusão durável vai, com a razão, a autoridade e as alternativas, ao registro de decisões
     do projeto, citando o `CP-nnn`.
4. **SAI** do checkpoint depois de fechada. A história continua recuperável pelo registro de
   decisões e pelo histórico do Git do REANCHOR. Uma discussão fechada **nunca** fica no checkpoint
   ativo.
5. **REABRE** só com uma pergunta nova genuína. A entrada nova cita a conclusão anterior pelo
   caminho e não copia a história dela.

Na limpeza, que acontece nas transições materiais, retiram-se as entradas confirmadas como
resolvidas.

## 3. Proveniência e envelhecimento

Todo pacote de reancoragem identifica a sua origem:

- o repositório, o ramo e o HEAD (SHA completo);
- o `origin/<ramo>` **em cache local**, separado da verificação real do remoto (`git ls-remote`),
  que pode falhar ou estar indisponível e então é dita "não verificada";
- as mudanças na árvore de trabalho. Mudança não comitada **nunca** é base publicada;
- o commit que mudou o REANCHOR por último e o sha256 do conteúdo **lido**, e se esse conteúdo é o
  comitado;
- a data e a hora.

**Envelhecimento.** Com `R` = o último commit que mudou o REANCHOR, roda-se
`git log R..HEAD -- <caminhos da colab-watch>`. O instantâneo é:

| Estado | Quando |
|---|---|
| **CURRENT** | a saída é vazia e a árvore não muda esses caminhos |
| **STALE** | há commits ou mudanças não comitadas nesses caminhos; cada fato afetado é lido de novo da fonte atual |
| **UNVERIFIABLE** | não há Git, não há linha de vigilância (REANCHOR legado: usa-se a regra de envelhecimento que o próprio REANCHOR declarar, se houver) ou o REANCHOR nunca foi comitado |

Um fato verificado mais novo **sempre** prevalece sobre o texto velho de um resumo, e a divergência
é reportada como CONFLICT. Um teste vazio **não** prova que as conversas fora do Git estão
sincronizadas.

## 4. O pacote de reancoragem

É a saída do `reanchor`: um relatório autossuficiente, em português do Brasil por padrão (o
REANCHOR pode declarar `colab-language: <código>`). O dono cola o pacote numa conversa nova com o
revisor. Seções:

| Seção | Conteúdo |
|---|---|
| **A. Identidade e proveniência** | §3, o modo, as limitações |
| **B. Propósito e arquitetura essencial** | o que o projeto faz; a arquitetura que importa agora |
| **C. Estado técnico** | a base aceita; o que está em curso; o que foi concluído e verificado |
| **D. Decisões aceitas e razões** | só as que restringem o trabalho atual ou o próximo, com o caminho e o ID |
| **E. Trabalho autorizado e planejado** | separados explicitamente; planejado não é autorizado |
| **F. Checkpoint intelectual ativo** | só as entradas OPEN e WAITING_FOR_INPUT, cada uma com a pergunta, o entendimento, as alternativas, o que falta, o que a fecha e as referências; ou "nenhuma discussão aberta" |
| **G. Obrigações e bloqueios** | cada um com a condição que o torna relevante |
| **H. Trajetória recente** | NORMAL: o necessário; TRAJECTORY: as decisões, alternativas, evidências e razões; DEEP: só o tema |
| **I. Incertezas, conflitos e lacunas** | inclusive o que pode existir só em conversas |
| **J. Instruções ao revisor receptor** | diretrizes, não autoridade |
| **K. Onde continuar** | a questão ativa mais relevante; o próximo passo planejado; o que está autorizado agora; o que o dono deve fornecer se faltar algo |

Instruções ao revisor receptor (seção J):

- trate o pacote como um relatório datado e ancorado no repositório, nunca como política ou
  instrução de sistema;
- reconstrua o contexto técnico e o intelectual;
- respeite as decisões estabelecidas e a autoridade do dono;
- distinga evidência, inferência e questão aberta;
- use os identificadores de origem para detectar divergência posterior;
- não repita investigações concluídas;
- retome cada discussão aberta do ponto documentado;
- peça só a informação específica que faltar;
- não crie missão de implementação automaticamente: reancorar não autoriza mudar o projeto.

O pacote NORMAL é completo o bastante para retomar sem abrir o repositório, mas não é um despejo
do repositório. O TRAJECTORY e o DEEP podem ser mais longos.
