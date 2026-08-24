---
name: parallax-landings
description: Landing pages com parallax em camadas — profundidade real com planos que se movem em velocidades diferentes, imagens que sangram, elementos flutuantes e composição "não parece um site, parece uma experiência". Esta skill deve ser usada quando o usuário pedir "landing com parallax", "página com profundidade", "site em camadas", "landing imersiva", "página que parece experiência", "hero com parallax", ou quando /site-premium escolher parallax como conceito central.
---

# Parallax Landings — Página Como Experiência

Parallax bem feito é COMPOSIÇÃO EM CAMADAS, não um efeito jogado numa imagem. Pensar cada seção como um palco com profundidade: fundo, meio, frente.

## O modelo mental de 3 planos

Toda seção parallax tem:

- **Fundo (plano distante)**: move-se DEVAGAR (yPercent 6–10). Texturas, paisagens, cor
- **Meio (conteúdo)**: velocidade normal do scroll. Títulos, texto, cards
- **Frente (plano próximo)**: move-se RÁPIDO (yPercent 15–25) e pode ir na direção oposta. Elementos decorativos, produto, partículas, molduras

Quanto maior a diferença de velocidade entre planos, maior a sensação de profundidade. Elementos da frente podem ter `filter: blur(1px)` sutil para simular foco de câmera.

## Estrutura base (com GSAP — requer setup da skill gsap-scroll)

```html
<section class="stage">
  <div class="layer layer--back"><img src="bg.jpg" alt=""></div>
  <div class="layer layer--mid">
    <h2>Design que <em>move</em>.</h2>
  </div>
  <div class="layer layer--front"><div class="floating-card">…</div></div>
</section>
```

```css
.stage { position: relative; min-height: 120vh; overflow: clip; display: grid; place-items: center; }
.layer { position: absolute; inset: 0; display: grid; place-items: center; }
.layer--mid { position: relative; z-index: 2; }
.layer--back img { width: 100%; height: 115%; object-fit: cover; }
```

```js
gsap.utils.toArray(".stage").forEach(stage => {
  const back = stage.querySelector(".layer--back");
  const front = stage.querySelector(".layer--front");
  const st = { trigger: stage, start: "top bottom", end: "bottom top", scrub: true };
  if (back)  gsap.fromTo(back,  { yPercent: -8 },  { yPercent: 8,  ease: "none", scrollTrigger: st });
  if (front) gsap.fromTo(front, { yPercent: 18 }, { yPercent: -18, ease: "none", scrollTrigger: st });
});
```

## Receitas de composição

### Hero em camadas (a primeira impressão)

Título gigante entre duas camadas de imagem: montanha/objeto na frente do texto (imagem PNG recortada com z-index maior) e cenário atrás. O texto fica "dentro" da paisagem. Ao rolar, a camada da frente sobe mais rápido e revela o título por inteiro.

### Imagem-janela (depth window)

Wrapper com `clip-path: inset(0 round 24px)` ou moldura; a imagem interna é 130% da altura e viaja com scrub. Parece que a página tem buracos para outro mundo:

```css
.window { overflow: clip; border-radius: 24px; height: 70vh; }
.window img { height: 130%; width: 100%; object-fit: cover; }
```

### Elementos flutuantes dessincronizados

Cards, produtos ou ornamentos espalhados, cada um com velocidade e delay próprios — nunca todos na mesma velocidade:

```js
gsap.utils.toArray("[data-float]").forEach((el, i) => {
  gsap.fromTo(el, { y: 40 + i * 25 }, { y: -(40 + i * 25), ease: "none",
    scrollTrigger: { trigger: el.closest("section"), start: "top bottom", end: "bottom top", scrub: 1 + i * 0.3 } });
});
```

### Texto que atravessa a imagem

Título com `mix-blend-mode: difference` (ou `exclusion`) passando por cima de imagens em velocidade diferente — o texto reage visualmente ao que passa por trás.

### Zoom-through (mergulho)

Seção pinada onde uma imagem-janela cresce até ocupar a tela inteira, como se a câmera mergulhasse nela:

```js
gsap.fromTo(".dive img", { scale: 1 }, { scale: 1.6, ease: "none",
  scrollTrigger: { trigger: ".dive", start: "top top", end: "+=150%", pin: true, scrub: 1 } });
gsap.fromTo(".dive .window", { width: "60vw", height: "60vh" }, { width: "100vw", height: "100vh", borderRadius: 0, ease: "none",
  scrollTrigger: { trigger: ".dive", start: "top top", end: "+=150%", scrub: 1 } });
```

## Direção de arte específica de parallax

- Paletas atmosféricas funcionam melhor: terrosos + dourado sobre escuro, creme + acento quente. Ver conceitos da skill **frontend-design**
- Imagens com profundidade real (paisagens, objetos com perspectiva) rendem mais que ilustrações flat
- Overlay de gradiente nas imagens de fundo para garantir contraste do texto: `linear-gradient(180deg, transparent, rgb(0 0 0 / 0.55))`
- Grão de filme por cima de tudo dá acabamento cinematográfico: overlay `position: fixed` com SVG noise (`feTurbulence`) em `opacity: 0.05; pointer-events: none; mix-blend-mode: overlay`
- Máximo 4–5 seções parallax por página; intercalar com seções estáticas de respiro

## Performance e mobile

- Nunca usar `background-attachment: fixed` (quebra no iOS) — sempre transform via GSAP
- Imagens: `loading="lazy"` fora do hero, tamanhos responsivos, WebP
- No mobile reduzir amplitudes pela metade ou desligar o plano frontal (`gsap.matchMedia`)
- `prefers-reduced-motion`: todas as camadas estáticas, composição continua bonita parada — testar esse estado
