# Design Turbo

Stack de repertório visual para o Claude parar de criar site com cara de template e começar a criar sites cinematográficos — inspirado no conceito "o problema não é o Claude, é repertório".

## O que vem no plugin

| Skill | O que faz |
|---|---|
| `frontend-design` | Direção de arte: tipografia expressiva, paleta autoral, layout assimétrico, checklist anti-template |
| `gsap-scroll` | GSAP + ScrollTrigger: parallax, sticky sections, scrub, split text, scroll horizontal, contadores |
| `threejs` | Cenas 3D: luz cinematográfica, materiais premium, câmera controlada pelo scroll, partículas |
| `parallax-landings` | Landings em camadas com profundidade real — "não parece um site, parece uma experiência" |
| `scroll-world` | Scrollytelling imersivo: a página vira um mundo que o visitante atravessa em capítulos |
| `jogos-3d` | Jogos 3D no navegador: game loop, controles, colisão, HUD e polimento |
| `site-premium` | Orquestrador `/site-premium`: conduz do briefing à entrega usando o stack inteiro |

## Instalação

**No Claude Code** (terminal):

```
/plugin marketplace add ArrecheNeto/Design-Turbo
/plugin install design-turbo@design-turbo
```

**No Claude Cowork / app desktop**: baixe o arquivo `design-turbo.plugin` (Releases) e aceite a instalação no chat, ou peça ao Claude para instalar o plugin a partir do arquivo.

## Como usar

- Peça naturalmente: "cria uma landing com parallax pra minha cafeteria" — a skill certa é acionada sozinha.
- Ou rode `/site-premium` para o fluxo completo guiado (briefing → conceito → construção → QA).

As skills se referenciam entre si: um site premium típico usa `frontend-design` para o conceito, `gsap-scroll` para o movimento e `threejs`/`parallax-landings`/`scroll-world` conforme a espinha dorsal escolhida.
