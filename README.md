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
**Começar**. Cada time tem um goleiro fixo — é só informação do jogo, não
precisa cadastrar.

## Como funciona cada frase

1. **Frase** — aparece no telão com cronômetro de 10s. No Controle, toque no
   time que está respondendo (**Quem respondeu?**), depois **ACERTOU** ou
   **ERROU**.
   - Errou (1ª vez): passa a vez pro outro time, cronômetro reinicia.
   - Errou (2ª vez) ou tempo esgota sem resposta: frase queima, pula pra
     próxima automaticamente.
2. **Acertou** — telão revela o antônimo. Controle pergunta **FEZ GOL** ou
   **PERDEU / DEFENDEU**.
   - Fez gol → time fica com **3 pontos**.
   - Perdeu/defendeu → time fica com **1 ponto**.
3. **Placar** — telão mostra o placar animado por alguns segundos. Controle
   mostra **Próxima frase**.

Três níveis em ordem: **Fácil → Médio → Difícil**, com tela de transição
anunciando o próximo nível no telão (**Começar [nível]** no Controle).

**Rodada final** (1 frase, vale **5 pontos**): acertar "Novos Começos" →
"Velhos Fins" estoura em tela cheia no telão com confete — é o nome da
noite. Não tem etapa de gol nessa rodada; os 5 pontos são só por acertar.
Depois disso vai direto pro pódio.

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
- `Espaço` — avança (placar → próxima frase, ou começa o próximo nível).
- `←` / `→` — frase anterior / pular frase.
- `M` — muda/desmuda o som.
- `P` — mostra/esconde o placar.

Atalhos são ignorados enquanto o foco está num campo de texto.

## Editar as frases

Array `LEVELS` no topo do `<script>` em `index.html`, formato
`{ frase, antonimo }` por nível (`FÁCIL`, `MÉDIO`, `DIFÍCIL`), mais a
constante `FINAL_PHRASE` pra rodada final.
