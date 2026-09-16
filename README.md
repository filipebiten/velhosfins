# Velhos Fins — TalkFlow 02

Dinâmica de antônimos e futebol do evento "TalkFlow 02 — Velhos Fins" (Rede
Flow). App irmão do [TalkFlow 01](https://github.com/filipebiten/talkflow):
mesma arquitetura — single-file HTML, Preact + htm via ESM (esm.sh), sem
build, sem npm — sincronização entre telas via Firebase Realtime Database.

## Deploy no GitHub Pages

1. Commit e push do `index.html` (e este README) num repositório GitHub.
2. Settings → Pages → Deploy from branch → escolher a branch/pasta.

Funciona 100% offline depois do primeiro carregamento (libs e fonte vêm de
CDN só no load inicial).

## Como abrir as duas telas

Acesse a URL publicada (ou o `index.html` local). A tela inicial tem dois
botões, cada um abre em nova aba:

- **Abrir Palco (telão)** → `?view=palco` — projetar no telão do evento.
- **Abrir Painel de Controle** → `?view=controle` — notebook/celular do
  operador.

No Palco, a primeira coisa é um toque na tela ("Toque para iniciar o Palco")
— necessário pra liberar áudio no navegador. Depois disso ele escuta o
Controle automaticamente, em qualquer aparelho (a sincronização é via
Firebase, não `BroadcastChannel` — telão e painel podem estar em navegadores
ou celulares diferentes).

Usa o mesmo projeto Firebase do TalkFlow 01 (`talkflow-dba50`), mas num path
próprio (`talkflow/velhosfins`) pra não colidir com o estado do outro app —
as regras do banco só liberam leitura/escrita dentro do prefixo `talkflow/`.

## Configurar os times

No Controle: digite o nome dos dois times (eles escolhem ao vivo) e clique
**Começar**. Isso leva pra uma tela **"Times prontos"** — só quando o
operador clicar **Começar** ali é que a primeira frase entra no ar (o telão
fica em "Preparando…" até esse clique, então dá tempo de ajustar tudo antes
de abrir o jogo pro público).

Cada time tem um goleiro fixo — é só informação do jogo, não precisa
cadastrar.

## Como funciona cada frase

Tudo é manual — nada troca de tela sozinho, sempre é o operador que decide
avançar.

1. **Frase** — aparece no telão com cronômetro de **15s**. Quando o tempo
   zera, ele só congela em 0: a frase continua na tela e o Controle continua
   deixando escolher **quem respondeu** e clicar **ACERTOU**/**ERROU**
   normalmente, sem pressa.
   - **ERROU** (1ª vez): passa a vez pro outro time, cronômetro reinicia.
   - **ERROU** (2ª vez): frase queima, pula pra próxima.
2. **Acertou** — telão revela o antônimo. Controle pergunta **FEZ GOL** ou
   **PERDEU / DEFENDEU**.
   - **FEZ GOL** → telão mostra "GOL!" pulando em loop, indefinidamente, até
     o operador clicar **Ver pontos →** no Controle. Só aí aparece o time e
     "+3 pontos".
   - **PERDEU / DEFENDEU** → telão mostra direto o time e "+1 ponto".
3. Dali, o Controle mostra **Próxima frase** (ou **Próximo nível**, na
   última frase do nível) — clique quando quiser seguir. Não existe uma tela
   de placar automática entre uma frase e outra: se quiser mostrar o placar
   geral, use o botão **Mostrar placar** a qualquer momento.

Três níveis em ordem: **Fácil → Médio → Difícil**, com tela de transição
anunciando o próximo nível no telão (**Começar [nível]** no Controle).

**Rodada final** (1 frase, vale **5 pontos**): acertar "Novos Começos" →
"Velhos Fins" estoura em tela cheia no telão com confete — é o nome da
noite. Não tem etapa de gol nessa rodada; os 5 pontos são só por acertar. A
tela fica assim até o operador clicar **Ver Pódio →** no Controle.

## Controles extras (Controle)

- **← Frase anterior** / **Pular frase →** — navega manualmente.
- **Mostrar placar** — sobrepõe o placar geral no telão a qualquer momento.
- **Corrigir nome ou pontos** (rodapé) — ajusta +/- por time em caso de erro
  do operador.
- **Som: ligado/desligado** — muda o mudo (afeta as duas telas).

## Atalhos de teclado (no Controle)

- `1` / `2` — seleciona qual time respondeu.
- `A` / `E` — acertou / errou.
- `G` / `D` — fez gol / perdeu-defendeu (na tela de revelação).
- `Espaço` — avança o passo atual (ver pontos, próxima frase, próximo
  nível, ver pódio).
- `←` / `→` — frase anterior / pular frase.
- `M` — muda/desmuda o som.
- `P` — mostra/esconde o placar.

Atalhos são ignorados enquanto o foco está num campo de texto.

## Editar as frases

Array `LEVELS` no topo do `<script>` em `index.html`, formato
`{ frase, antonimo }` por nível (`FÁCIL`, `MÉDIO`, `DIFÍCIL`), mais a
constante `FINAL_PHRASE` pra rodada final.
