---
name: site-premium
description: Orquestrador do stack Design Turbo — conduz a criação completa de um site cinematográfico do zero, combinando frontend-design (direção de arte), gsap-scroll (animações), threejs (3D), parallax-landings e scroll-world conforme o projeto pede. Esta skill deve ser usada quando o usuário rodar /site-premium ou pedir "site premium", "site cinematográfico", "site completo com tudo", "landing de outro nível", "site que impressiona", "usa o stack inteiro".
---

# /site-premium — Orquestrador do Stack

Conduzir o projeto do briefing à entrega, acionando as skills do stack na ordem certa. O resultado é sempre um único arquivo HTML autossuficiente, entregue ao usuário.

## Etapa 1 — Briefing (perguntar apenas o que faltar)

Coletar: negócio/produto, objetivo da página (vender, capturar lead, portfólio), público, e se existe material (logo, cores, fotos, site atual). Se o usuário der uma URL de referência ou site atual, analisá-la primeiro.

## Etapa 2 — Conceito (skill frontend-design)

Ler a skill **frontend-design** e definir por escrito: a direção estética escolhida, o par tipográfico, a paleta com tokens e a emoção-alvo. Apresentar o conceito ao usuário em 3–4 linhas ANTES de codar. Ajustar se ele pedir.

## Etapa 3 — Escolher a espinha dorsal de movimento

Decidir qual técnica carrega a página (no máximo UMA dominante + apoios discretos):

| Projeto pede… | Espinha dorsal | Skills |
|---|---|---|
| Elegância editorial, conteúdo forte | reveals + parallax discreto | gsap-scroll |
| Profundidade e atmosfera | parallax em camadas | parallax-landings + gsap-scroll |
| Narrativa/jornada, lançamento | mundo de scroll | scroll-world + gsap-scroll |
| Produto físico/tech em destaque | 3D no hero ou câmera por scroll | threejs + gsap-scroll |
| Marca ousada querendo impacto máximo | scroll-world com cena 3D | scroll-world + threejs + gsap-scroll |

Anunciar a escolha e o porquê em 1 frase. Não empilhar tudo: uma página com sticky + horizontal + 3D + mundo vira ruído.

## Etapa 4 — Estrutura da página

Esqueleto padrão de alta conversão (adaptar, não copiar cegamente):

1. **Hero** — promessa em tipografia gigante + o momento visual mais forte da página
2. **Problema/tensão** — por que o jeito atual falha
3. **Solução/método** — a seção-espetáculo (aqui vive a espinha dorsal escolhida)
4. **Prova** — números animados, depoimentos, cases
5. **Oferta/serviços** — clara, sem ruído visual
6. **CTA final** — repetir a promessa, um botão, zero distração

## Etapa 5 — Implementação

Ler as skills escolhidas na Etapa 3 e construir seguindo as receitas delas. Regras de montagem:

- Um único `index.html` com CSS e JS inline, CDNs do cdnjs para GSAP/Three
- Tokens de design no `:root` primeiro; nenhuma cor ou fonte hardcoded fora deles
- Conteúdo real do briefing — nunca lorem ipsum; se faltar texto, escrever copy de venda plausível para o negócio
- Imagens: usar as do usuário; na falta, gradientes/formas/SVG gerados ou 3D — nunca links quebrados
- `gsap.matchMedia()` + `prefers-reduced-motion` desde o primeiro commit, não como remendo

## Etapa 6 — Controle de qualidade (obrigatório)

Rodar o checklist final da skill **frontend-design** item a item. Adicionalmente verificar:

- [ ] A seção-espetáculo funciona e tem começo/meio/fim claros
- [ ] Página legível com JS desligado (conteúdo não nasce com opacity: 0 sem fallback `noscript`/classe)
- [ ] Mobile: sem overflow horizontal, animações reduzidas, botões ≥ 44px
- [ ] Performance: imagens lazy, pixelRatio limitado, uma timeline mestre em vez de dezenas de triggers
- [ ] CTA visível em no máximo 2 momentos-chave sem rolar caçando

Corrigir o que falhar ANTES de entregar. Entregar o arquivo ao usuário e oferecer: ajustes de conceito, versão com outra espinha dorsal, ou publicação.
