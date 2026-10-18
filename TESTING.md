# Testes da versão 0.1.0

Sessões **reais** e novas do Claude Code 2.1.300 (`claude -p --plugin-dir <este repositório>`). Cada
sessão teve só as ferramentas de leitura e git, mais `Write`/`Edit` apenas na fase de aplicação do
`init`. Os projetos de teste ficaram em diretórios temporários isolados. A "aprovação do dono" nos
projetos de teste foi simulada por mensagem explícita.

| Teste | Cenário | Resultado |
|---|---|---|
| G1 | `reanchor xyz` | só a linha de uso |
| G2 | `reanchor deep` sem tema | pediu o tema e parou sem ler nada |
| G3 | `reanchor` sem REANCHOR | "não inicializado, rode o init"; nenhum pacote fabricado |
| A1 | `init` em repositório vazio | proposta mínima; perguntas ao dono; nada escrito |
| A2 | aprovação | REANCHOR honesto: sem histórico, sem decisões, lacunas declaradas |
| A3 | `reanchor` logo depois | **DEFEITO**: recusou o REANCHOR não rastreado. Corrigido no commit "fix(reanchor)…" |
| A3b | `reanchor` depois da correção | pacote gerado, REANCHOR declarado não publicado, envelhecimento UNVERIFIABLE |
| A4 | `reanchor` depois do commit | CURRENT |
| B1 | `init` em projeto com ADRs, roadmap e notas | reconheceu as fontes; não duplicou; propôs CP-001 só a partir de uma discussão documentada; achou um conflito real entre a documentação e o código |
| B2 | aprovação | `docs/REANCHOR.md` preenchido com evidência real e ponteiro no `CLAUDE.md` |
| B3 | `reanchor normal` | pacote completo, com as decisões, as razões, o planejado separado do autorizado e o conflito |
| D1 | `init` de novo | "já inicializado, nada a mudar"; hashes idênticos antes e depois |
| E1 | CP-001 reescrito com nova compreensão | o pacote mostrou só o entendimento atual |
| E2 | ADR aceito; CP-001 retirado | "nenhuma discussão aberta"; a conclusão, as razões e as alternativas apareceram nas decisões |
| F1 | commit posterior contradiz o REANCHOR; remoto avançou noutro clone; mudança não comitada | STALE; o cache separado do remoto verificado; divergência reportada; a fonte venceu o resumo; nada não comitado tratado como base |
| T1 | `reanchor trajectory` | recuperou as razões, as alternativas rejeitadas e a mudança de rota |
| C1 | projeto maduro com REANCHOR sem marcador, só leitura | tratado como legado compatível; usou a regra de envelhecimento do próprio REANCHOR |
| H | isolamento | os repositórios vizinhos ficaram idênticos antes e depois |

Limitação observada: uma sessão rotulou como VERIFIED FACT um detalhe errado (a posição de uma
linha num arquivo). As marcas de evidência reduzem, mas não eliminam, o erro do modelo.

## Instalação no escopo de usuário (2026-10-18, Claude Code 2.1.302, WSL/Linux)

`claude plugin marketplace add <este repositório> --scope user` e
`claude plugin install auttares-colab@auttares --scope user`. Depois, sessões **novas**
(`claude -p`), **sem** `--plugin-dir`:

| Teste | Onde | Resultado |
|---|---|---|
| I1 | projeto maduro, `reanchor xyz` | só a linha de uso |
| I2 | projeto temporário isolado, `reanchor deep` | pediu o tema e parou |
| I3 | o mesmo, sem REANCHOR, `reanchor` | "não inicializado, rode o init"; nenhum pacote |
| I4 | o mesmo, `init` | proposta com as fontes, um CONFLICT real (roadmap "feito" contra código vazio), perguntas; nada escrito |
| I5 | aprovação simulada | `REANCHOR.md` com marcador, `colab-watch` e CP-001 |
| I6 | CP-001 fechada (ADR aceito, entrada removida, nota de revisão que cita a CP-001 comitada) | "nenhuma discussão aberta"; a conclusão, as razões e as alternativas na seção D; a nota de revisão não ressuscitou a entrada |
| I7 | projeto maduro, `init` só leitura | REANCHOR legado reconhecido; proposta mínima; nada escrito |
| I8 | projeto maduro, `reanchor normal`, comparado com o protótipo local `/reanchor` no mesmo estado | mesma base, autorização, obrigações, checkpoint, conflitos e proveniência |
| I9 | sessão anterior à instalação, retomada (`--resume --fork-session`) | o plugin está disponível |

Os projetos de teste ficaram no diretório temporário da sessão e não são preservados.
