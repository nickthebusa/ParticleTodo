<script setup>
import { onMounted, onUnmounted, ref } from "vue";
import { CoFullscreen } from '@kalimahapps/vue-icons';
import { CoFullscreenExit } from '@kalimahapps/vue-icons';
import { ClWindowClose } from '@kalimahapps/vue-icons';

defineProps({
  imageURL: String,
});

const effectAmount = defineModel('effectAmount', { default: 100 });
const innerEffect = defineModel('innerEffect', { default: 100 });

const emit = defineEmits(["close"]);

const sketchContainer = ref(null);
const srcImgRef = ref(null);
const loading = ref(true);

let WIDTH;
let HEIGHT;

let img;
let particles = [];

const particleSize = defineModel('particleSize', { default: 7 });
const resolutionValue = defineModel('resolutionValue', { default: 7 });

const MAX_FORCE = defineModel('MAX_FORCE', { default: 10 });
const MIN_FORCE = defineModel('MIN_FORCE', { default: 0 });

const proxyURL = "https://api.allorigins.win/raw?url=";

let p5 = null;
let p;

let fullscreenOff = ref(true);

let currentSketch = null;

const sketch = (e) => {

  p = e;

  p.preload = () => {
    const combinedURL = `${proxyURL}${srcImgRef.value.src}`;
    img = p.loadImage(combinedURL, () => {
      loading.value = false;
    });
  }
  p.setup = () => {
    WIDTH = srcImgRef.value.width;
    HEIGHT = srcImgRef.value.height;
    const canvas = p.createCanvas(WIDTH, HEIGHT).parent(sketchContainer.value).class("particleCanvas");
    canvas.elt.getContext('2d', {
      willReadFrequently: true
    });
    p.frameRate(30);
    spawnParticles();
  }
  p.draw = () => {
    //p.image(img, 0, 0, WIDTH, HEIGHT);
    p.clear();
    particles.forEach((particle) => {
      particle.update();
      particle.draw();
    })
  }
}

class Particle {
  constructor(x, y, color) {
    this.x = x;
    this.y = y;
    this.color = color;
    this.targetX = this.x;
    this.targetY = this.y;
  }
  update() {
    let mouseVector = p.createVector(p.mouseX, p.mouseY);
    let currentVector = p.createVector(this.x, this.y);
    let targetVector = p.createVector(this.targetX, this.targetY);

    // calculate vector from mouse to particle and its magnitude (distance)
    let fromMouseToParticle = p5.Vector.sub(currentVector, mouseVector);
    let distanceToMouse = fromMouseToParticle.mag();

    // calculate vector from particle to target and its magnitude
    let fromParticleToTarget = p5.Vector.sub(targetVector, currentVector);
    let distanceToTarget = fromParticleToTarget.mag();

    let totalForce = p.createVector(0, 0);

    if (distanceToMouse < effectAmount.value) {
      let repulsionForce = p.map(distanceToMouse, 0, innerEffect.value, MAX_FORCE.value, MIN_FORCE.value);
      fromMouseToParticle.setMag(repulsionForce);
      totalForce.add(fromMouseToParticle);
    }

    if (distanceToMouse > 0) {
      let attractionForce = p.map(distanceToTarget, 0, innerEffect.value, MIN_FORCE.value, MAX_FORCE.value);
      fromParticleToTarget.setMag(attractionForce);
      totalForce.add(fromParticleToTarget);
    }

    this.x += totalForce.x;
    this.y += totalForce.y;
  }
  draw() {
    p.fill(this.color);
    p.noStroke();
    p.ellipse(this.x, this.y, particleSize.value);
  }
}


function spawnParticles() {
  for (let x = 0; x < WIDTH; x += resolutionValue.value) {
    for (let y = 0; y < HEIGHT; y += resolutionValue.value) {
      const scaleFactorX = WIDTH / img.width;
      const scaleFactorY = HEIGHT / img.height;
      const color = img.get(x / scaleFactorX, y / scaleFactorY);
      particles.push(new Particle(x + particleSize.value / 2, y + particleSize.value / 2, color));
    }
  }
}
function reloadParticles() {
  if (currentSketch) {
    particles = [];
    currentSketch.remove(); // Properly clean up
    currentSketch = null; // Clear the reference
  }
  currentSketch = new p5(sketch);
}
function handleFullscreenChange() {
  if (document.fullscreenElement) {
    fullscreenOff.value = false;
  } else {
    fullscreenOff.value = true;
  }
}
function switchFullscreen(goFullscreen) {
  if (!document.fullscreenElement) {
    document.documentElement.requestFullscreen();
  } else if (document.exitFullscreen) {
    document.exitFullscreen();
  }
}
onMounted(async () => {
  p5 = (await import("p5")).default;
  currentSketch = new p5(sketch);
  document.addEventListener("fullscreenchange", handleFullscreenChange);
})

onUnmounted(() => {
  particles = [];
  if (currentSketch) {
    currentSketch.remove();
    currentSketch = null;
  }
  if (sketchContainer.value) {
    sketchContainer.value.innerHTML = '';
  }
  p5 = null;
  document.removeEventListener("fullscreenchange", handleFullscreenChange);
})

</script>


<template>
  <div class="ParticleImage">
    <div ref="sketchContainer" class="canvas-cont"></div>
    <button class="close-btn" @click="emit('close')">
      <ClWindowClose />
    </button>
    <div class="effect-controls">
      <input class="adjust-effect" type="number" v-model="effectAmount">
      <input class="adjust-inner" type="number" v-model="innerEffect">
      <div class="particle-controls">
        <span>particle-size</span>
        <input class="particle-size" type="number" v-model="particleSize">
        <span>resolution</span>
        <input class="resolution" type="number" v-model="resolutionValue">
        <span>max-force</span>
        <input class="max-force" type="number" v-model="MAX_FORCE">
        <span>min-force</span>
        <input class="min-force" type="number" v-model="MIN_FORCE">
        <button @click="reloadParticles">reload-particles</button>
      </div>
    </div>
    <button v-if="fullscreenOff" class="fullscreen-btn" @click="switchFullscreen">
      <CoFullscreen />
    </button>
    <button v-else class="exit-fullscreen-btn" @click="switchFullscreen">
      <CoFullscreenExit />
    </button>
    <div class="src-img-cont">
      <img :src="imageURL" class="src-img" alt="particleImg" ref="srcImgRef">
    </div>

    <div class="loading-cont" v-show="loading">
      <div class="loader"></div>
    </div>

  </div>
</template>

<style lang="css" scoped>
body {
  max-height: 100vh;
}

.ParticleImage {
  position: fixed;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  background-color: rgba(0, 0, 0, 0.9);
  backdrop-filter: blur(5px);
  z-index: 3;
  touch-action: none;
}

.canvas-cont {
  position: absolute;
  top: 50%;
  left: 50%;
  transform: translate(-50%, -50%);
}

.src-img-cont {
  width: 100%;
  max-width: 60rem;
}

.src-img {
  width: 100%;
  max-width: 60rem;
  height: auto;
  position: absolute;
  visibility: hidden;
}

.close-btn {
  position: fixed;
  top: 2px;
  left: 2px;
  z-index: 4;
  background: none;
  border: none;
  color: white;
  font-size: 1.9rem;
}

.effect-controls {
  margin: 2.5rem 5px 0 5px;
  display: inline-flex;
  flex-direction: column;
}

.particle-controls {
  margin: 0.5rem 0 0 0;
  border: 1px solid white;
  display: inline-flex;
  flex-direction: column;
  z-index: 4;
}

.particle-controls button,
.particle-controls span {
  background-color: rgba(0, 100, 240, 0.1);
  color: white;
  font-family: monospace;
}

.particle-controls button:hover {
  background-color: rgba(173, 255, 47, 1);
  color: black;
  font-weight: 1000;
  cursor: pointer;
}

.adjust-effect,
.adjust-inner,
.particle-size,
.resolution,
.max-force,
.min-force {
  z-index: 4;
  background: rgba(100, 120, 170, 0.2);
  color: greenyellow;
  font-size: 1.2rem;
  width: 10rem;
}


.fullscreen-btn,
.exit-fullscreen-btn {
  position: absolute;
  top: 5px;
  right: 5px;
  z-index: 4;
  background: none;
  border: none;
  color: white;
  font-size: 2.2rem;
}

.loading-cont {
  position: fixed;
  top: 50%;
  left: 50%;
  transform: translate(-50%, -50%);
  z-index: 4;
  color: white;
}

/* HTML: <div class="loader"></div> */
.loader {
  width: 65px;
  display: grid;
  --mask:
    radial-gradient(12px at left 15px top 50%, #0000 95%, #000),
    radial-gradient(12px at right 15px top 50%, #0000 95%, #000);
  -webkit-mask: var(--mask);
  mask: var(--mask);
  -webkit-mask-composite: source-in;
  mask-composite: intersect;
  animation: l1 1s infinite alternate;
}

.loader:before,
.loader:after {
  content: "";
  grid-area: 1/1;
  height: 30px;
  aspect-ratio: 1;
  background: #fff;
  border-radius: 50%;
}

.loader:after {
  margin-left: auto;
}

@keyframes l1 {
  to {
    width: 40px;
  }
}
</style>
