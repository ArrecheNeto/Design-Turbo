---
name: scroll-world
description: Scrollytelling imersivo — a página vira um mundo que o visitante ATRAVESSA ao rolar: narrativa em capítulos, cenário que evolui, câmera que viaja, transições de ambiente (dia/noite, cores, cenas) sincronizadas ao scroll. Esta skill deve ser usada quando o usuário pedir "scroll world", "scrollytelling", "site imersivo", "página que conta uma história", "você não rola uma landing, atravessa um mundo", "experiência de scroll", "site narrativo", "storytelling no site".
---

# Scroll World — Você Não Rola Uma Página, Atravessa Um Mundo

Scrollytelling é NARRATIVA, não efeito. Antes de codar, escrever o roteiro: 4–6 capítulos, cada um com uma frase-chave e uma mudança visual de estado. O scroll é a linha do tempo do filme; o visitante controla o play.

## Roteiro obrigatório (fazer primeiro)

Definir em tabela antes de qualquer código:

| Capítulo | Mensagem | Estado visual | Progresso |
|---|---|---|---|
| 1. Abertura | promessa/gancho | cena inicial, cor A | 0–15% |
| 2. Tensão | o problema | escurece, zoom in | 15–35% |
| 3. Virada | a solução aparece | cor de acento entra | 35–60% |
| 4. Prova | números/resultados | cena aberta, luz | 60–80% |
| 5. Chegada | CTA | cena final calma | 80–100% |

## Arquitetura técnica (requer setup GSAP da skill gsap-scroll)

Um mundo de scroll é UMA timeline mestre com scrub, presa a um contêiner longo:

```html
<div id="world">              <!-- height: 500vh — a "estrada" -->
  <div class="viewport">      <!-- position: sticky; top: 0; height: 100vh; overflow: clip -->
    <div class="scene">…camadas do cenário…</div>
    <div class="chapters">…textos dos capítulos…</div>
  </div>
</div>
```

```js
const master = gsap.timeline({
  scrollTrigger: { trigger: "#world", start: "top top", end: "bottom bottom", scrub: 1 }
});
```

Posicionar cada mudança na timeline com labels e posições absolutas (0–1 do total via durações proporcionais). Todos os capítulos entram na MESMA timeline — nunca ScrollTriggers independentes competindo dentro do mundo.

## Técnicas de mundo

### Cenário em camadas que viaja

Camadas de paisagem (PNG/SVG recortados ou blocos com gradientes) que se movem em velocidades diferentes E mudam de escala — não só vertical: combinar `y`, `scale` e `x` para sensação de avançar PARA DENTRO:

```js
master.to(".bg-far",  { scale: 1.15, y: "-4%",  ease: "none" }, 0)
      .to(".bg-mid",  { scale: 1.35, y: "-12%", ease: "none" }, 0)
      .to(".bg-near", { scale: 1.9,  y: "-30%", ease: "none" }, 0);
```

### Mudança de ambiente (a alma do mundo)

O cenário muda de humor por capítulo — animar CSS custom properties ou crossfade de camadas:

```js
master.to(":root", { "--sky": "#1a1035", "--light": "#7a5cff", duration: 0.2 }, 0.35)
      .to(".sun", { yPercent: 120, opacity: 0, duration: 0.15 }, 0.32)
      .from(".stars", { opacity: 0, duration: 0.2 }, 0.38);
```

Tudo que usa `var(--sky)` acompanha a transição automaticamente.

### Estrada/caminho que guia o olho

Um elemento contínuo (linha SVG, estrada, rio) conecta os capítulos. Desenhar o caminho com stroke-dashoffset:

```js
const path = document.querySelector("#trail");
const len = path.getTotalLength();
path.style.strokeDasharray = len;
path.style.strokeDashoffset = len;
master.to(path, { strokeDashoffset: 0, ease: "none" }, 0);
```

### Capítulos de texto

Cada bloco de texto entra, respira e sai dentro da sua janela de progresso:

```js
const chapters = gsap.utils.toArray(".chapter");
const slot = 1 / chapters.length;
chapters.forEach((ch, i) => {
  master.fromTo(ch, { autoAlpha: 0, y: 50 }, { autoAlpha: 1, y: 0, duration: slot * 0.3 }, i * slot + slot * 0.1)
        .to(ch, { autoAlpha: 0, y: -50, duration: slot * 0.3 }, i * slot + slot * 0.65);
});
```

### Mundo 3D real

Quando o conceito pedir, usar a skill **threejs**: a timeline mestre move `camera.position` por waypoints da cena (dolly pelo mundo). O padrão câmera-por-scroll está documentado lá. Regra: HTML para todo texto, 3D só para o cenário.

## Direção de arte

- O mundo precisa de IDENTIDADE: escolher um universo (montanhas ao amanhecer, cidade à noite, fundo do mar, espaço, interior de um estúdio) e mantê-lo coerente do primeiro ao último capítulo
- Transições de cor entre capítulos: mudar `--bg`, `--ink`, `--accent` juntos, nunca uma cor só
- Grão de filme + vinheta sutil (`box-shadow: inset 0 0 30vmin rgb(0 0 0 / .35)`) seguram a atmosfera
- Áudio nunca em autoplay; se houver, botão discreto de ativar
- Indicador de progresso da jornada (barra fina ou marcadores de capítulo) — o visitante precisa sentir que avança

## Performance e acessibilidade

- 400–600vh de mundo é o teto; mais que isso cansa
- Uma única timeline scrubada é mais performática que dezenas de triggers
- `will-change: transform` apenas nas camadas grandes do cenário
- Mobile: reduzir para 300vh, cortar camadas intermediárias, manter a narrativa
- `prefers-reduced-motion`: transformar o mundo em capítulos estáticos empilhados (seções normais com o mesmo conteúdo) — a história continua legível sem o filme
- Conteúdo dos capítulos em HTML semântico (h2, p) — screen readers leem a narrativa na ordem
