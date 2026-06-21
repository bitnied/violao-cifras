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
- **Completas:** Stand by Me, Take on Me (simplificada, Tom G#m), Knockin' on
  Heaven's Door, Não Quero Dinheiro.
- **Só amostra (1 verso + refrão), aguardando o Tiago colar o resto:** Blackbird,
  Come Together, Somewhere Only We Know, Wonderwall, Boys Don't Cry.
- **Vazias, aguardando colar:** Whisky a Go Go, Fazendinha, Faz Parte do Meu Show,
  Resposta, Anna Júlia. (Metadados já levantados: ver histórico — tom/capo/intro.)

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
