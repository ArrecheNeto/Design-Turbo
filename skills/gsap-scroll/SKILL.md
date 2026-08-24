---
name: gsap-scroll
description: Animações de scroll profissionais com GSAP + ScrollTrigger — parallax, sticky sections, pin, scrub, reveals encadeados, contadores animados, texto que se monta letra a letra e transições entre seções. Esta skill deve ser usada quando o usuário pedir "animação de scroll", "site animado", "parallax", "seção que gruda", "sticky section", "GSAP", "scroll suave", "efeito ao rolar", "site que se move", ou quando um site criado com frontend-design precisar de movimento além de reveals simples.
---

# GSAP + Scroll — Animação por Rolagem

Usar GSAP 3 com ScrollTrigger via CDN. Este é o setup base de todo site animado:

```html
<script src="https://cdnjs.cloudflare.com/ajax/libs/gsap/3.12.5/gsap.min.js"></script>
<script src="https://cdnjs.cloudflare.com/ajax/libs/gsap/3.12.5/ScrollTrigger.min.js"></script>
<script>
  gsap.registerPlugin(ScrollTrigger);
</script>
```

## Regras de ouro

1. **Scrub para narrativa, trigger para entrada.** Animações ligadas ao progresso do scroll usam `scrub: 1` (o 1 dá inércia suave). Entradas de elementos usam `toggleActions: "play none none reverse"`.
2. **Animar apenas `transform` e `opacity`.** Nunca animar width/height/top/left (causa reflow). Usar `will-change: transform` com moderação.
3. **Easing padrão**: `power3.out` para entradas, `power2.inOut` para movimentos contínuos, `expo.out` para elementos dramáticos.
4. **Sempre** encapsular tudo em `gsap.matchMedia()` para desligar ou simplificar animações no mobile e respeitar `prefers-reduced-motion`:

```js
const mm = gsap.matchMedia();
mm.add("(prefers-reduced-motion: no-preference) and (min-width: 768px)", () => {
  // animações completas aqui
});
```

## Receitas prontas

### Reveal padrão (entrada de seção)

```js
gsap.utils.toArray("[data-reveal]").forEach(el => {
  gsap.from(el, {
    y: 60, opacity: 0, duration: 1, ease: "power3.out",
    scrollTrigger: { trigger: el, start: "top 80%" }
  });
});
```

Para grupos, usar `stagger: 0.08`.

### Parallax de imagem

A imagem vive num wrapper com `overflow: hidden` e é 20–30% maior que ele:

```js
gsap.utils.toArray(".parallax-img").forEach(img => {
  gsap.fromTo(img, { yPercent: -12 }, {
    yPercent: 12, ease: "none",
    scrollTrigger: { trigger: img.parentElement, start: "top bottom", end: "bottom top", scrub: true }
  });
});
```

Camadas com velocidades diferentes (`yPercent` maior = camada mais próxima) criam profundidade real.

### Sticky section com etapas (pin + timeline)

A seção fica presa enquanto o conteúdo troca — o coração do storytelling:

```js
const tl = gsap.timeline({
  scrollTrigger: {
    trigger: ".sticky-section", start: "top top",
    end: "+=300%",           // 3 telas de scroll presas
    pin: true, scrub: 1
  }
});
tl.to(".step-1", { opacity: 0, y: -40 })
  .from(".step-2", { opacity: 0, y: 40 }, "<0.2")
  .to(".step-2", { opacity: 0, y: -40 }, "+=0.5")
  .from(".step-3", { opacity: 0, y: 40 }, "<0.2");
```

### Título que se monta (split text manual, sem plugin pago)

```js
document.querySelectorAll("[data-split]").forEach(el => {
  el.innerHTML = el.textContent.replace(/\S/g, "<span class='ch'>$&</span>");
  gsap.from(el.querySelectorAll(".ch"), {
    yPercent: 110, opacity: 0, stagger: 0.02, duration: 0.8, ease: "expo.out",
    scrollTrigger: { trigger: el, start: "top 85%" }
  });
});
```

CSS necessário: `.ch { display: inline-block; } [data-split] { overflow: hidden; }` (preservar espaços com `white-space: pre-wrap`).

### Scroll horizontal dentro da página

```js
const track = document.querySelector(".h-track");
gsap.to(track, {
  x: () => -(track.scrollWidth - window.innerWidth),
  ease: "none",
  scrollTrigger: { trigger: ".h-section", start: "top top", end: () => "+=" + track.scrollWidth, pin: true, scrub: 1, invalidateOnRefresh: true }
});
```

### Contador animado

```js
gsap.utils.toArray("[data-count]").forEach(el => {
  const target = +el.dataset.count;
  gsap.fromTo(el, { textContent: 0 }, {
    textContent: target, duration: 2, ease: "power1.out", snap: { textContent: 1 },
    scrollTrigger: { trigger: el, start: "top 85%" }
  });
});
```

### Barra de progresso / hero que encolhe

```js
gsap.to(".progress", { scaleX: 1, ease: "none",
  scrollTrigger: { trigger: document.body, start: "top top", end: "bottom bottom", scrub: 0.3 } });

gsap.to(".hero-content", { scale: 0.92, opacity: 0.3, ease: "none",
  scrollTrigger: { trigger: ".hero", start: "top top", end: "bottom top", scrub: true } });
```

## Composição de uma página completa

Ordem de intensidade recomendada (não usar tudo em toda seção):

1. Hero: split text + hero que encolhe ao sair
2. Seção 2: reveals com stagger
3. Seção central (a mais importante): sticky com etapas OU scroll horizontal — apenas UMA sticky por página
4. Provas/números: contadores
5. Imagens ao longo da página: parallax discreto
6. Footer: reveal simples

## Armadilhas

- Chamar `ScrollTrigger.refresh()` após carregar fontes/imagens que mudam o layout (`window.addEventListener("load", () => ScrollTrigger.refresh())`)
- Pin dentro de elemento com `transform` ou `overflow: hidden` quebra — pinar sempre elementos de nível de seção
- `end` de sticky sections em unidades relativas (`"+=200%"`), nunca px fixos
- No mobile, reduzir: sem pin longo, parallax mais sutil ou desligado
