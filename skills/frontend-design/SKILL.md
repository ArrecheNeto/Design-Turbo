---
name: frontend-design
description: Direção de arte para sites e landing pages — elimina a "cara de template" (cards iguais, fontes óbvias, layout sem vida) e aplica identidade visual autoral com tipografia expressiva, grid quebrado com intenção, paleta com personalidade e hierarquia dramática. Esta skill deve ser usada SEMPRE que o usuário pedir para criar ou redesenhar um site, landing page, página de vendas, portfólio ou qualquer interface web — mesmo que ele não mencione design. Acione com "cria um site", "landing page", "página bonita", "site premium", "design que converte", "site sem cara de template", "direção de arte".
---

# Frontend Design — Direção de Arte

Aplicar estas regras ANTES de escrever a primeira linha de HTML. O objetivo é que nenhum site saia parecendo template.

## O diagnóstico do site genérico

Um site tem "cara de template" quando apresenta 3 ou mais destes sintomas. NUNCA entregar um site com eles:

1. Hero centralizado com título + subtítulo + botão, tudo no eixo central
2. Três cards iguais lado a lado com ícone em cima
3. Fonte Inter/Roboto/Open Sans para tudo
4. Paleta azul-corporativo (#3B82F6 sobre branco) ou gradiente roxo/rosa clichê
5. Seções empilhadas com o mesmo padding e o mesmo fundo
6. Imagens de banco genéricas (pessoas sorrindo apontando para laptop)
7. Border-radius médio (8–12px) em tudo, sombras suaves iguais em tudo

## Processo obrigatório: conceito antes de código

Antes de codar, definir por escrito (1 parágrafo) o CONCEITO do site: qual emoção ele provoca e qual referência estética segue. Escolher UMA direção e comprometer-se com ela até o fim. Direções que funcionam:

- **Editorial/Revista**: tipografia serifada gigante, muito branco, grid assimétrico, números de seção expostos (01, 02…), regras finas de 1px
- **Brutalist/Studio**: preto e off-white, sans grotesca condensada em caps, bordas duras sem radius, hover invertido
- **Cinematográfico/Atmosférico**: fundo escuro com textura, imagens full-bleed com overlay, dourados/terrosos, luz e sombra, muito espaço negativo
- **Retrô-print**: papel creme (#F5F0E6), 1 cor de acento saturada (laranja, vermelho), tipografia display condensada, asteriscos e ornamentos tipográficos
- **Tech-luxo**: dark mode profundo (#0A0A0B, nunca #000 puro), acentos metálicos, glassmorphism sutil, microanimações precisas

## Tipografia (a alavanca nº 1)

- Sempre 2 famílias: uma DISPLAY com personalidade para títulos + uma neutra para texto. Pares testados (Google Fonts): Fraunces + Inter; Playfair Display + Söhne-like (Inter); Space Grotesk + IBM Plex Sans; Bricolage Grotesque + Instrument Sans; Clash-like (Archivo Expanded) + Satoshi-like (Inter)
- Título do hero: 12–18vw ou clamp(3rem, 10vw, 9rem). Grande de verdade. `line-height: 0.95; letter-spacing: -0.03em`
- Contraste dramático de escala: se o título tem 9rem, o texto de apoio tem 1rem. Nada de escadinha (h1 4rem, h2 3rem, h3 2rem)
- Usar itálico, peso variável, palavras destacadas em cor de acento ou serifa no meio de uma sans (ex.: "Design que <em>move</em>")
- Caps + letter-spacing largo (0.2em) apenas para labels/eyebrows pequenos

## Cor

- Definir tokens CSS custom properties no `:root`. Máximo: 1 fundo, 1 superfície, 1 texto, 1 acento, 1 acento secundário opcional
- O acento aparece em POUCOS lugares (5–10% da tela) — é isso que o torna forte
- Nunca cinza puro: aquecer ou esfriar (ex.: texto #1C1917 em vez de #333; fundo escuro #0C0A09 em vez de #111)
- Testar contraste: texto corrido mínimo 4.5:1

## Layout

- Grid de 12 colunas mas QUEBRADO com intenção: elementos que atravessam colunas, imagens que sangram até a borda, textos deslocados do eixo
- Sobreposição: título por cima da imagem, número de seção gigante semi-transparente atrás do conteúdo
- Espaço negativo generoso: seções com `padding-block: clamp(6rem, 15vh, 12rem)`
- Variar o ritmo entre seções: cheia → vazia → duas colunas → full-bleed. Nunca duas seções com a mesma estrutura em sequência
- Detalhes de acabamento: regras de 1px, marcadores tipográficos (✳, →, ●), números de seção, labels verticais em `writing-mode: vertical-rl`

## Movimento (mínimo obrigatório)

Mesmo sem a skill gsap-scroll, todo site deve ter:

- Reveal on scroll (IntersectionObserver + classe `.visible` com transform/opacity, `transition: 0.8s cubic-bezier(0.16, 1, 0.3, 1)`)
- Hover states com intenção em TODOS os elementos interativos (deslocamento, inversão de cor, sublinhado animado)
- `prefers-reduced-motion` respeitado sempre

Para animações complexas (pin, scrub, parallax, timeline), acionar a skill **gsap-scroll**. Para 3D, acionar **threejs**.

## Checklist final antes de entregar

Verificar item a item; se falhar em algum, corrigir antes de entregar:

- [ ] Zero sintomas da lista de site genérico
- [ ] Tipografia display no hero com pelo menos 6rem em desktop
- [ ] Uma paleta autoral com tokens no :root
- [ ] Pelo menos 2 seções com layout assimétrico ou sobreposição
- [ ] Reveal on scroll funcionando
- [ ] Responsivo (testar mentalmente 375px, 768px, 1440px)
- [ ] `prefers-reduced-motion` tratado
