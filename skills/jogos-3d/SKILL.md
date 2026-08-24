---
name: jogos-3d
description: Jogos 3D no navegador com Three.js — game loop, controles de personagem (teclado + toque), câmera de terceira pessoa, colisão simples, física básica, HUD em HTML e polimento visual de jogo. Esta skill deve ser usada quando o usuário pedir "jogo 3D", "jogo no navegador", "game com Three.js", "joguinho no site", "criar um game", "jogo pra web", "minigame", ou quiser transformar uma cena 3D em algo jogável.
---

# Jogos 3D no Navegador

Reusar o setup de renderer/luz/materiais da skill **threejs** — este arquivo cobre o que transforma cena em JOGO. Entregar sempre um único HTML autossuficiente e jogável.

## Escopo primeiro

Definir em 3 linhas antes de codar: objetivo do jogador, mecânica única, condição de vitória/derrota. Jogos que cabem bem no navegador e impressionam: coletar itens num mundo aberto pequeno, corrida infinita (endless runner), esquivar de obstáculos, plataforma simples, exploração com chave/porta. Um jogo pequeno e polido > um jogo grande e quebrado.

## Game loop com timestep correto

Nunca amarrar velocidade ao framerate:

```js
const clock = new THREE.Clock();
function loop() {
  const dt = Math.min(clock.getDelta(), 0.05);   // clamp evita saltos em aba de fundo
  update(dt);
  renderer.render(scene, camera);
  requestAnimationFrame(loop);
}
```

Estados do jogo: `menu → playing → gameover` numa variável simples; `update` só roda em `playing`.

## Input (teclado + toque juntos, sempre)

```js
const keys = {};
addEventListener("keydown", e => keys[e.code] = true);
addEventListener("keyup",   e => keys[e.code] = false);
const input = () => ({
  x: (keys.KeyD || keys.ArrowRight ? 1 : 0) - (keys.KeyA || keys.ArrowLeft ? 1 : 0),
  z: (keys.KeyS || keys.ArrowDown ? 1 : 0) - (keys.KeyW || keys.ArrowUp ? 1 : 0),
  jump: !!keys.Space
});
```

Mobile: joystick virtual — um `pointerdown` na metade esquerda define a origem; `pointermove` calcula o vetor (limitado a raio de 50px); botão de ação na direita. Nunca entregar jogo que só funciona com teclado.

## Movimento e câmera de terceira pessoa

```js
function update(dt) {
  const inp = input();
  const dir = new THREE.Vector3(inp.x, 0, inp.z).normalize();
  if (dir.lengthSq()) {
    player.position.addScaledVector(dir, SPEED * dt);
    player.rotation.y = Math.atan2(dir.x, dir.z);          // vira pra onde anda
  }
  // câmera segue com amortecimento (lerp exponencial)
  const goal = player.position.clone().add(new THREE.Vector3(0, 4.5, 7));
  camera.position.lerp(goal, 1 - Math.pow(0.001, dt));
  camera.lookAt(player.position.x, player.position.y + 1, player.position.z);
}
```

Pulo/gravidade: `vy -= 22 * dt; player.position.y += vy * dt;` com chão em `y = 0` e `vy = 8` ao pular (só se estiver no chão).

## Colisão simples (suficiente para 90% dos jogos)

Distância entre esferas para coletáveis e inimigos:

```js
const hit = (a, b, r) => a.position.distanceToSquared(b.position) < r * r;
collectibles = collectibles.filter(c => {
  if (hit(player, c, 1)) { score++; scene.remove(c); pop(); return false; }
  return true;
});
```

Paredes/obstáculos em grade: manter lista de `Box3` (`box.setFromObject(mesh)`) e testar `box.intersectsBox(playerBox)` — ao colidir, desfazer o movimento do frame no eixo que colidiu (testar x e z separadamente para deslizar na parede).

## Mundo do jogo

- Chão: `PlaneGeometry` grande com material fosco + `GridHelper` sutil, ou tiles gerados em loop (runner)
- Cenário low-poly gerado por código: árvores = cone + cilindro; pedras = `IcosahedronGeometry` com `flatShading: true`; construções = caixas compostas. Espalhar com posições aleatórias mas com seed fixa (função pseudo-random determinística) para o mundo ser reproduzível
- Paleta coerente de 4–5 cores (aplicar direção de arte da skill **frontend-design** — jogos genéricos também têm "cara de template")
- Personagem sem modelo externo: cápsula (`CapsuleGeometry`) ou boneco de caixas com leve balanço ao andar (`rotation.z = Math.sin(t*10) * 0.05`)

## Polimento que faz parecer jogo de verdade ("juice")

Priorizar nesta ordem:

1. **Sombra do personagem** (blob: círculo escuro semi-transparente no chão acompanha o player)
2. **Squash & stretch**: `scale.y` 0.8 ao aterrissar, 1.15 ao pular, sempre voltando com lerp
3. **Feedback de coleta**: item some com escala + flash, contador do HUD dá um pulso
4. **Screen shake** curto ao tomar dano: deslocar a câmera aleatoriamente ±0.1 por 150ms
5. **Partículas** de poeira/brilho (reusar Points da skill threejs, 10–20 por evento)
6. **Som**: WebAudio com osciladores gerados por código (bip de coleta, thud de dano) — sem arquivos externos, volume baixo, iniciar só após primeiro clique/tecla (política de autoplay)

## HUD e telas (sempre HTML por cima do canvas)

```html
<div id="hud"><span id="score">0</span> ✳ <span id="lives">♥♥♥</span></div>
<div id="overlay"><h1>Nome do Jogo</h1><p>WASD/setas • toque no mobile</p><button>Jogar</button></div>
```

Overlay cobre menu e game over (com placar final e botão reiniciar). Reiniciar = resetar estado, nunca `location.reload()`. Tipografia do HUD segue a direção de arte do jogo.

## Performance

- `renderer.setPixelRatio(Math.min(devicePixelRatio, 2))`; em jogo, considerar teto 1.5
- Reusar geometrias e materiais compartilhados (um material por cor, não por mesh)
- Objetos que saem do mundo: remover da cena E dos arrays de colisão
- Runner infinito: reciclar tiles (mover o do fundo para a frente) em vez de criar/destruir
- Testar mentalmente 60fps num notebook comum: < 200 draw calls, sombras só na luz principal
