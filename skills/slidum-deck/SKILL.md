---
name: slidum-deck
description: Use when the user wants to generate an HTML presentation to play or share in Slidum (slidum.com) — a single self-contained .html deck that plays in Slidum's native mode with filmstrip, slide counter and keyboard navigation — OR wants to fix an existing deck that Slidum flagged as "Navigates on its own"/"Navega sozinha" (passthrough mode, no filmstrip or slide list). Triggers on "make a presentation for Slidum", "gerar apresentação", "slide deck HTML", "pitch deck", "apresentação para compartilhar", "converter/consertar deck para o Slidum", "sem filmstrip", "não lista os slides".
---

# Gerar apresentações prontas para o Slidum

O [Slidum](https://slidum.com) toca apresentações **HTML self-contained** (um único arquivo,
tudo inline) como slides — com navegação, filmstrip e tela cheia. Este skill gera decks que
tocam do jeito **melhor possível** no Slidum: em **modo nativo**, onde o próprio player controla
a navegação. Siga o contrato abaixo à risca — é o que separa um deck que "abre" de um que "toca".

## O contrato Slidum-ready (não-negociável)

1. **Um único arquivo `.html`, 100% self-contained.** Todo CSS em `<style>` inline. Zero
   dependências remotas: **nenhum** `<script src="http…">`, `<link href="http…">`, fonte de CDN,
   imagem `https://…` ou `mermaid`/`chart.js`/`d3`. O Slidum bloqueia recursos remotos por
   segurança (o deck roda isolado num sandbox). Precisa de imagem? Embuta como `data:` URI.
   Precisa de fonte especial? Use a stack do sistema (`system-ui`) ou `@font-face` com `data:`.
2. **Cada slide é `<section class="slide">`** — filho direto de `<body>`. Nada de `<div>`
   aninhado como slide. O parser do Slidum reconhece `body > section` e `.slide`.
3. **Slides VISÍVEIS por padrão.** No CSS, `.slide { display: flex }` (ou block/grid) — **nunca**
   esconda slides com uma classe tipo `.active`/`.current`. O Slidum alterna a visibilidade
   colocando `display:none` inline nos slides inativos. Se você já esconde por CSS, o player não
   consegue mostrar — o deck fica preto.
4. **NÃO escreva navegação própria.** Sem `addEventListener('keydown'…)`, sem setas, sem
   `scrollIntoView`. Se o deck trouxer sua própria engine de teclado, o Slidum entra em modo
   *passthrough* e você perde filmstrip, contador e o ritual de tela. Deixe o Slidum dirigir.
   (Regra de ouro: **um deck Slidum-ready não tem `<script>` de navegação — idealmente não tem
   `<script>` nenhum.**)
5. **Cada slide ocupa a tela: `height: 100vh`** (ou `100dvh`). Pense em 16:9, mas use unidades
   responsivas (`vmin`, `clamp()`) para funcionar em qualquer tela.
6. **`<title>`** preenchido — vira o nome da apresentação na biblioteca.

## Design (Caixa de Luz / palco)

- **Uma ideia por slide.** Tipografia grande, frases curtas, muito respiro. O slide é um palco,
  não um documento.
- Escala tipográfica com `clamp()` — o mesmo deck lê bem no notebook e no projetor.
- Contraste alto: fundo escuro + texto claro, ou o inverso. Um acento de cor, usado com
  parcimônia (eyebrow em mono, um número de slide no rodapé).
- Hierarquia por peso e tamanho, não por decoração.
- **Gradients da marca, prontos no template**: `.bg-faceta`, `.bg-anel`, `.bg-palco-aceso`,
  `.bg-profundidade` (escuros) e `.bg-mesa-de-luz` (claro, já troca a tinta) — aplique no
  `<section class="slide">`. Use quando o usuário quiser a cara do Slidum ou pedir "um fundo
  bonito": **um gradiente por deck**, na abertura OU no fechamento — nunca em todos os slides.
  São CSS inline (custom properties no `:root` do template): não quebram o contrato self-contained.

## Narrativa (transforme, não informe)

A melhor apresentação **muda** a audiência — o que ela acredita, sente ou faz. Estruture:
abertura que fisga → contexto/tensão → argumento (poucos pontos, cada um com um dado) →
a transformação que você propõe (termine no que muda para quem assiste, nunca em "obrigado").
Um slide = um beat da história.

## Consertar um deck existente que "Navega sozinha" (passthrough)

O cenário: o usuário já tem um `.html` (gerado sem este skill) e o Slidum mostrou **"Navega
sozinha — o player encaminha as teclas"** / **"Navigates on its own"**, sem filmstrip nem lista
de slides. Não é defeito do deck — é o Slidum detectando um **motor de navegação próprio** e,
por segurança de compatibilidade, entregando as teclas a ele em vez de injetar o controller
(dois motores brigariam). Sem o controller, não dá para endereçar "slide N" → sem filmstrip,
sem contador, sem miniaturas.

**Como o Slidum detecta** (heurística estática sobre os `<script>` inline): o deck é
classificado self-driving quando um script **escuta teclado** (`addEventListener('keydown'…)`,
`addEventListener('keyup'…)` ou `onkeydown=`) **E** referencia teclas de navegação
(`ArrowLeft/Right/Up/Down`, `PageUp/PageDown`). As duas condições juntas.

**Cirurgia para converter em nativo** (preservando todo o visual):

1. **Remova o(s) bloco(s) de navegação por teclado** — o `addEventListener('keydown'…)` e
   qualquer função que ele chame só para trocar de slide (`goTo`, `next/prev`,
   `scrollIntoView`, contadores próprios de slide).
2. **Remova a UI do motor removido**: hint "use as setas", contador de slides próprio no
   rodapé, bolinhas de progresso controladas por JS — no Slidum, o player já mostra contador
   e filmstrip; a UI órfã vira ruído congelado.
3. **Garanta slides visíveis por padrão**: se o deck escondia slides com `.active`/transform/
   scroll-container, faça cada `<section class="slide">` renderizar sozinha
   (`display:flex; height:100vh`) — regras 2, 3 e 5 do contrato acima.
4. **Confira que não sobrou** `keydown`/`keyup`/`Arrow*`/`Page(Up|Down)` em nenhum `<script>`
   (se não sobrou script nenhum, melhor ainda).
5. **Se o deck carrega recursos remotos** (o Slidum reporta "Loads a script/stylesheet from
   another site" e bloqueia no sandbox — mermaid/chart.js não desenham, fontes caem no
   fallback): *asse* as dependências — renderize diagramas UMA vez e cole o SVG resultante
   inline no lugar do bloco fonte; troque Google Fonts por `@font-face` com `data:` (ou
   `system-ui`); remova os `<link>`/`<script>` de CDN. Lógica de conteúdo que não é navegação
   (ex.: animar números quando o slide aparece) pode ficar — troque gatilhos de "slide ativo"
   por `IntersectionObserver`, que funciona no sandbox.
6. Re-importe no Slidum: o veredito deve mudar para **modo nativo**, com filmstrip e contador.

**Trade-off honesto (diga ao usuário):** sem motor próprio, o arquivo aberto direto do disco
não navega por teclado — ele é uma página com todos os slides empilhados. Quem pagina é o
Slidum (é o papel dele: o deck é o conteúdo, o player é o palco). Se o usuário PRECISA
apresentar o arquivo cru offline sem o Slidum, mantenha o motor próprio e aceite o modo
passthrough — toca do mesmo jeito, só sem filmstrip/contador. É uma escolha, não um erro.

## Como entregar

1. Gere o arquivo seguindo `template.html` (neste skill) como esqueleto — troque o conteúdo,
   mantenha a estrutura e o CSS de base.
2. Confira o **checklist** antes de entregar.
3. Diga ao usuário: salve como `.html` e suba em **slidum.com** (Nova apresentação → cole ou
   arraste) para tocar e compartilhar por link.

## Checklist antes de entregar

- [ ] Um único `.html`, sem nenhum `http://`/`https://` em `src`/`href`/`url()`/`@import`.
- [ ] Sem `<script>` de navegação (idealmente sem `<script>`). Prova rápida: buscar no arquivo
      por `keydown`, `keyup` e `Arrow` — zero ocorrências dentro de `<script>`.
- [ ] Cada slide é `<section class="slide">`, filho direto de `<body>`.
- [ ] `.slide { display: flex/block }` — visível por padrão, sem classe `.active`.
- [ ] `height: 100vh` por slide; tipografia com `clamp()`.
- [ ] `<title>` preenchido; `lang` no `<html>`.
- [ ] Abre num navegador e mostra o **primeiro** slide inteiro (os outros só aparecem no
      Slidum, que pagina) — isso é esperado.

Um deck que passa neste checklist toca no Slidum em **modo nativo**: setas/espaço navegam,
`F` tela cheia, filmstrip lateral, contador de slides — e o link compartilhado abre e toca
sem download.
