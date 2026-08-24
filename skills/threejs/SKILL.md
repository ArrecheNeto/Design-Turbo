---
name: threejs
description: Cenas 3D no navegador com Three.js — produto em 3D girando, objetos com luz e sombra cinematográficas, câmera controlada pelo scroll, partículas, texturas e profundidade real integradas a uma landing page. Esta skill deve ser usada quando o usuário pedir "site em 3D", "Three.js", "objeto 3D no site", "produto girando", "cena 3D", "sair do 2D", "profundidade", "webgl", ou quando o conceito do site pedir um elemento tridimensional de destaque no hero.
---

# Three.js — O Site Sai do 2D

## Setup base (ES modules via CDN, sem build)

```html
<script type="importmap">
{ "imports": {
    "three": "https://cdnjs.cloudflare.com/ajax/libs/three.js/0.160.0/three.module.min.js"
} }
</script>
<script type="module">
import * as THREE from "three";

const canvas = document.querySelector("#scene");
const renderer = new THREE.WebGLRenderer({ canvas, antialias: true, alpha: true });
renderer.setPixelRatio(Math.min(devicePixelRatio, 2));
renderer.setSize(innerWidth, innerHeight);
renderer.toneMapping = THREE.ACESFilmicToneMapping;   // visual cinematográfico
renderer.toneMappingExposure = 1.1;

const scene = new THREE.Scene();
const camera = new THREE.PerspectiveCamera(38, innerWidth / innerHeight, 0.1, 100);
camera.position.set(0, 0.4, 5);

addEventListener("resize", () => {
  camera.aspect = innerWidth / innerHeight;
  camera.updateProjectionMatrix();
  renderer.setSize(innerWidth, innerHeight);
});
</script>
```

O canvas fica atrás do conteúdo: `#scene { position: fixed; inset: 0; z-index: -1; }` (ou dentro de uma seção com `position: sticky`).

## Luz cinematográfica (o que separa amador de profissional)

Nunca usar apenas AmbientLight. Setup de 3 pontos:

```js
const key = new THREE.DirectionalLight(0xfff1e0, 2.5);   // luz quente principal
key.position.set(4, 6, 3);
key.castShadow = true;
key.shadow.mapSize.set(2048, 2048);

const fill = new THREE.DirectionalLight(0x8899ff, 0.4);  // preenchimento frio
fill.position.set(-4, 2, -2);

const rim = new THREE.DirectionalLight(0xffffff, 1.2);   // recorte por trás
rim.position.set(0, 3, -5);

scene.add(key, fill, rim, new THREE.AmbientLight(0xffffff, 0.15));
renderer.shadowMap.enabled = true;
renderer.shadowMap.type = THREE.PCFSoftShadowMap;
```

Adicionar névoa para profundidade atmosférica: `scene.fog = new THREE.Fog(0x0c0a09, 6, 16);` (mesma cor do fundo da página).

## Materiais com aparência premium

- `MeshStandardMaterial` com `roughness: 0.3–0.6` e `metalness: 0.1–0.9` conforme o material
- Pedra/concreto: `roughness: 0.9, metalness: 0` + textura de ruído
- Metal escovado: `roughness: 0.35, metalness: 1`
- Vidro: `MeshPhysicalMaterial` com `transmission: 1, thickness: 0.5, roughness: 0.05`
- Para reflexos ricos sem HDRI externo: `scene.environment = new THREE.PMREMGenerator(renderer).fromScene(new THREE.Scene(), 0.04).texture` ou usar `RoomEnvironment`

## Geometria herói sem modelo externo

Quando não houver arquivo GLB, compor formas primitivas com personalidade: `IcosahedronGeometry(1, 0)` facetado, `TorusKnotGeometry`, caixas com `RoundedBoxGeometry`-like (BoxGeometry + bevel via escala), ou grupos de cubos flutuando em órbita. Rotação idle sutil:

```js
function tick(t) {
  mesh.rotation.y = t * 0.00015;
  mesh.position.y = Math.sin(t * 0.001) * 0.08;   // flutuação
  renderer.render(scene, camera);
  requestAnimationFrame(tick);
}
requestAnimationFrame(tick);
```

## Câmera controlada pelo scroll (integração com GSAP)

O padrão mais cinematográfico — a página rola e a câmera viaja pela cena:

```js
gsap.registerPlugin(ScrollTrigger);
const camTl = gsap.timeline({
  scrollTrigger: { trigger: "#journey", start: "top top", end: "bottom bottom", scrub: 1 }
});
camTl.to(camera.position, { x: 2, y: 1.2, z: 3.2, ease: "none" })
     .to(mesh.rotation, { y: Math.PI * 1.5, ease: "none" }, "<")
     .to(camera.position, { x: -1.5, y: 0.6, z: 2.4, ease: "none" });
```

Se a câmera precisa sempre olhar para o objeto, chamar `camera.lookAt(mesh.position)` dentro do loop de render.

## Mouse parallax (profundidade reativa)

```js
const target = { x: 0, y: 0 };
addEventListener("pointermove", e => {
  target.x = (e.clientX / innerWidth - 0.5) * 0.4;
  target.y = (e.clientY / innerHeight - 0.5) * 0.25;
});
// no tick:
camera.position.x += (target.x - camera.position.x) * 0.05;
camera.position.y += (-target.y + 0.4 - camera.position.y) * 0.05;
```

## Partículas atmosféricas (poeira/estrelas)

```js
const N = 800, pos = new Float32Array(N * 3);
for (let i = 0; i < N * 3; i++) pos[i] = (Math.random() - 0.5) * 14;
const pGeo = new THREE.BufferGeometry();
pGeo.setAttribute("position", new THREE.BufferAttribute(pos, 3));
const points = new THREE.Points(pGeo, new THREE.PointsMaterial({ size: 0.02, color: 0xd8c6a8, transparent: true, opacity: 0.6 }));
scene.add(points);
// no tick: points.rotation.y = t * 0.00002;
```

## Modelos GLB externos

```js
import { GLTFLoader } from "https://cdn.jsdelivr.net/npm/three@0.160.0/examples/jsm/loaders/GLTFLoader.js";
new GLTFLoader().load(url, gltf => scene.add(gltf.scene));
```

Só usar quando o usuário fornecer o modelo ou uma URL confiável.

## Performance e acessibilidade

- `setPixelRatio(Math.min(devicePixelRatio, 2))` sempre
- Pausar o loop quando o canvas sai da viewport (IntersectionObserver) e quando `document.hidden`
- No mobile: reduzir partículas pela metade, desligar sombras (`renderer.shadowMap.enabled = false`)
- `prefers-reduced-motion`: manter a cena estática (render único), sem rotação nem viagem de câmera
- Sempre prover fallback: se `WebGLRenderer` lançar erro, esconder o canvas e mostrar imagem estática

## Composição com a página

O 3D serve ao conteúdo, não o contrário. Padrões que funcionam: objeto herói atrás do título gigante (título em HTML, nunca em 3D); cena fixa de fundo com seções de texto passando por cima; produto que gira e troca de ângulo a cada seção (câmera + scroll). Tipografia e CTAs ficam SEMPRE em HTML/CSS por cima do canvas.
