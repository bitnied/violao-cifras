# Cifras

Songbook pessoal de cifras com letra, **otimizado para celular**. É um único
arquivo `index.html` (sem build, sem dependências) que o Tiago abre no navegador
do celular e usa como app ("Adicionar à Tela de Início" → ícone offline).

## O que é cada coisa

- **`index.html`** — TUDO está aqui: CSS, JS e os dados das músicas. Não há
  outros arquivos, não há build, não há servidor. Para testar, é só abrir o
  arquivo no navegador.
- As músicas ficam no array **`MUSICAS`** dentro da tag `<script>`.

## Como o app funciona (pra não quebrar nada ao editar)

- A lista é dividida em duas seções por `cat`: **`"int"`** (internacional) e
  **`"nac"`** (nacional).
- O renderizador lê o campo `cifra` linha por linha. Ele já cuida de:
  alinhar acorde sobre a sílaba, transpor tom, rolagem automática e tamanho de
  fonte. **Você só edita os dados das músicas — não precisa mexer no CSS/JS.**

## Formato de uma música

Cada música é um objeto no array `MUSICAS`:

```js
{ cat:"int", titulo:"Stand by Me", artista:"Ben E. King", tom:"A", cifra:`
#Intro ( A  /  F#m  /  D  E )

[A]When the night has come
And the [F#m]land is dark
` },
```

Regras do campo `cifra` (é uma template string com crase `` ` ``):

- **Acorde entre colchetes, imediatamente antes da sílaba** onde ele troca:
  `[G]Mama take this [D]badge off of [Am]me`
- Acordes válidos: nota (`A`–`G`), com `#`/`b`, sufixos (`m`, `7`, `maj7`,
  `sus4`, `9`, `add9`, `dim`, `m7b5`…) e baixo invertido (`G/B`, `D/F#`).
  O transpositor entende tudo isso e também o baixo depois da `/`.
- **Linha começando com `#`** vira título de seção (ex.: `#Refrão`, `#Intro`,
  `#Ponte`). Não é acorde.
- **Linha em branco** = espaço visual.
- O campo `tom` é o tom original (ex.: `"G"`). Aparece no topo e acompanha a
  transposição. Pode ficar `""` se não souber.

## Convenções

- **Idioma:** português (com o Tiago) e mantenha os títulos/artistas como já
  estão na lista.
- **Não reordene nem renomeie** as músicas existentes sem pedido — só preencha
  o campo `cifra`.
- **Não invente acordes.** Use cifras conhecidas/consagradas de cada música.
  Em caso de versões diferentes, prefira o tom mais comum para violão e
  registre o tom em `tom`. Se houver dúvida real sobre a cifra, avise em vez
  de chutar.
- **Não quebre o formato dos colchetes** — é o que mantém o alinhamento no
  celular. Cada acorde tem que estar colado na sílaba certa.
- Mantenha tudo num arquivo só. Sem adicionar dependências, frameworks ou build.

## Fluxo principal: o Tiago cola, você formata

A letra completa de músicas com direito autoral **não** deve ser reproduzida de
memória nem copiada de páginas inteiras da web. O caminho certo (e sem travas) é
**o Tiago colar** a cifra de uma fonte que ele acessa (ex.: Cifra Club) e **você
converter e inserir**. Quando ele colar uma cifra:

1. Identifique a música na lista `MUSICAS` (ou crie a entrada, com `cat` certo).
2. **Converta para o formato `[Acorde]sílaba`**: a maioria das cifras vem com a
   linha de acordes ACIMA da linha de letra; junte as duas, pondo cada acorde
   imediatamente antes da sílaba que ele cai (conte os espaços para acertar a
   coluna). Marque seções (Intro, Refrão…) com `#`. Linha em branco = espaço.
3. Preencha o `tom` (e anote capotraste numa linha `#` se houver).
4. **Não invente nem complete** versos que o Tiago não colou. Se faltar pedaço,
   deixe o que veio e avise — não preencha o resto de cabeça.
5. Mostre o resultado / peça pra ele recarregar e conferir o alinhamento.

A parte **musical** (acordes, tom, capotraste, intro, progressões, estrutura)
pode ser preenchida/ajustada livremente — o limite é só a **letra completa**.

## Estado atual das músicas
**Todas completas** (21 músicas). Internacionais (14): Stand by Me, Take on Me,
Knockin' on Heaven's Door, Blackbird, Come Together, Somewhere Only We Know,
Wonderwall, Boys Don't Cry, Sweet Dreams, See You, About a Girl, Too Much,
Don't Be Cruel, The Way You Make Me Feel. Nacionais (7): Whisky a Go Go,
Fazendinha, Faz Parte do Meu Show, Não Quero Dinheiro, Azul da Cor do Mar,
Resposta, Anna Júlia.
- *Too Much:* artista (Spandau Ballet) por dedução — confirmar com o Tiago.
- Músicas de fingerstyle/tab (Blackbird, See You, About a Girl etc.): letra no
  Cifra, tablatura no modo Solo.

## Looper (v3.0 — era "Gravador")
Estação de loop estilo pedal (Boss RC): grava a **base**, ela fica rodando em
loop, e você vai **sobrepondo camadas** (violão, voz, percussão…) ao vivo até
montar uma banda inteira. Substituiu o gravador de backing tracks antigo.
- Acessível pelo botão **🔁 Looper** na home.
- **Botão grande central** com máquina de estados (uma cor/rótulo por estado):
  `idle → recbase (Fechar loop) → playing (Sobrepor) → overdub (Fechar camada)`
  e `stopped (Continuar)`. A **primeira gravação define o tamanho do loop**.
- **Motor de áudio** (Web Audio, ScriptProcessor p/ captura + AudioBufferSourceNodes
  em loop): cada camada é um `Float32Array` do tamanho do loop, tocadas juntas e
  **alinhadas pela fase** do loop. Overdub grava numa camada nova, escrita na
  posição de fase certa (com wrap e soma) e compensada por latência.
- **Sincronia (latência):** slider 0–250 ms (`_LP.sync`, default 60 ms) puxa a
  camada gravada para trás e corrige o atraso do round-trip do navegador.
- **Transporte:** ▶/⏸ tocar-parar, ↩ desfazer camada, ↪ refazer, 🗑 limpar tudo.
- **Cortar loop (✂️):** editor **modal** (abre só ao clicar em "Cortar loop", não fica
  na tela). Mostra a **forma de onda grande** do mix, duas alças arrastáveis com região
  destacada e o resto escurecido, tempos ao vivo e **prévia** (▶ Ouvir toca só a
  seleção em loop). "Salvar corte" fatia **todas as camadas** para `[a,b]` e redefine
  `loopLen`/`loopDur` (destrutivo no buffer em memória — avisa p/ salvar a sessão).
  Funções `lpAbrirCrop`/`_lpCropDraw`/`_lpCropLayout`/`lpCropPreview`/`lpCropSave`.
  Tem uma **linha de play branca arrastável** (pega embaixo, separada das alças de
  corte que ficam em cima): arrasta e ao soltar toca o loop inteiro a partir dali
  (`_lpCropPlayFrom`) — dá pra conferir o final sem ouvir tudo. As alças e a linha de
  play têm áreas de toque em pontas opostas (topo x base) pra não colidirem.
- **Camadas:** cada uma com mini-waveform, volume, mudo e apagar (apagar a base = limpar).
- **Botão de gravar:** disco vermelho grande estilo REC de verdade (gradiente + brilho
  interno + glow). O ícone é **desenhado em CSS** (círculo/quadrado/triângulo via
  `.lp-big-ico` + classe de estado) — nada de emoji `⏺/⏹/▶`, que o iOS renderiza como
  emoji e quebrava o layout. Estados: vermelho vivo = vai gravar, vermelho escuro
  pulsando = gravando, cinza = parado.
- **Loop sem vão no reinício:** ao fechar a base, `_lpTrimHead` **apara o silêncio
  inicial** (latência do mic + reação) que causava atraso a cada volta do loop.
- **Anti-estouro:** sensibilidade padrão **3×** (era 6) e **soft-clip `Math.tanh`**
  no lugar do corte duro — arredonda picos sem distorcer feio.
- **Persistência:** sessões salvas em IndexedDB (`gravador_db` v2, store `loops`;
  cada camada vira um WAV mono). Recarregar retoma de onde parou (estado `stopped`).
- **Exportar mix:** soma as camadas (respeitando mudo/volume) de uma volta do loop
  e baixa um **WAV**.
- **Ganho/Reduzir ruído** reaproveitados do gravador (gate simples).
- Requer **HTTPS** (ou localhost) para o microfone. Ainda usa `ScriptProcessor`
  (deprecado, mas funciona em todo lugar/iOS).
- **Rota de áudio no iOS (importante):** enquanto o `getUserMedia` está aberto, o
  Safari roteia a saída para o auscultador (rota de "ligação"), baixinho. Por isso
  o mic é **aberto só durante a gravação** (`lpAcquireMic`) e **liberado assim que a
  camada fecha** (`lpReleaseMic`) — aí a reprodução do loop volta ao alto-falante
  estéreo (rota de mídia). O contexto (`lpEnsureCtx`) fica vivo o tempo todo.
  `navigator.audioSession.type` (`'play-and-record'` ao gravar, `'playback'` ao
  tocar) reforça a rota quando existe (iOS 16.4+). **Durante o overdub** o mic está
  aberto, então o loop sai pelo auscultador nesse momento — use **fones** para
  monitorar (aí tudo sai alto e sem vazamento do alto-falante para a gravação).

## Deploy (publicado)
- **Repo:** github.com/bitnied/violao-cifras (público, branch `main`, `noindex` no HTML).
- **URL ao vivo (HTTPS):** https://bitnied.github.io/violao-cifras/
- **REGRA FIXA (decisão do Tiago):** a cada ajuste no app, **atualizar todos os
  ambientes automaticamente, sem pedir** — ou seja, salvar o arquivo local (já é o
  iCloud) **e** `git add -A && git commit && git push` para o site no ar. O GitHub
  Pages reconstrói sozinho em ~1 min.
- Como é HTTPS, o **afinador (microfone) já funciona no celular** por essa URL.
- Ainda **não** é PWA: precisa de internet pra abrir. O passo PWA (offline + ícone) segue
  pendente abaixo.

## Pendências (não fazer agora — só quando o Tiago pedir)
- **Virar PWA** (decisão tomada): adicionar `manifest` + service worker para
  funcionar 100% offline em qualquer lugar e tornar os saves duráveis (localStorage
  de PWA instalado é isento da limpeza de 7 dias do iOS). Exige hospedar 1x em
  HTTPS (GitHub Pages/Netlify) — internet só no install e nas atualizações.
  **O Tiago quer terminar de adicionar as músicas ANTES de fazer isso.**

## Como testar
Abrir `index.html` no navegador (no Mac: `open index.html`, ou um servidor local
`python3 -m http.server` para testar no iPhone via Safari, mesma rede). Conferir: a
música aparece na lista certa, abre sem erro, e os acordes ficam alinhados sobre as
sílabas mesmo em tela estreita. **Ao iterar, teste pela URL no Safari** (o ícone
da tela inicial guarda cache da versão antiga).
