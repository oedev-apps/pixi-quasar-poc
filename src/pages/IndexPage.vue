<template>
  <q-page class="flex flex-center" :style-fn="setOffset">
    <div ref="pixiContainer" class="pixi-canvas-container" style="border: 1px solid greenyellow;"></div>
    <q-card flat class="absolute-bottom q-mb-sm q-mx-sm q-px-xs row text-white" style="background-color: rgba(0, 0, 0, 0);">
      <div class="col">FPS: {{ fps }}</div> <div class="col">Elapsed: {{ elapsedSec }}s</div>
    </q-card>
  </q-page>
</template>

<script>
import { defineComponent } from 'vue'
import { Screen } from 'quasar';
import * as PIXI from 'pixi.js';

const xMax = Screen.width - 2;
const yMax = Screen.height - 2;
const xMid = xMax / 2;
const yMid = yMax / 2;

const app = new PIXI.Application({ width: xMax, height: yMax });
const sprite = PIXI.Sprite.from('sample.png');
sprite.anchor.set(0.5);
sprite.x = xMid;
sprite.y = yMid;
sprite.scale.x = 0.25;
sprite.scale.y = 0.25;
let elapsed = 0.0;
let uiUpdate = 1;
document.body.style.backgroundColor = 'black';

// Sprite movement states
// let currentState = 0;
// const spriteStates = [
//   delta => {
//     // Inital movement from center
//     if (sprite.y < 5) {
//       currentState = 1;
//       spriteStates[currentState](delta);
//       return;
//     }
//     if (sprite.y < 50) {
//       sprite.angle += -delta;
//       sprite.y += -delta * 0.5;
//       return;
//     }
//     sprite.y += -delta;
//   },
//   delta => {
//     // Move to left side
//     if (sprite.angle > -180) {
//       sprite.angle += -delta * .5;
//     }
//     if (sprite.x > 25) {
//       sprite.x += -delta;
//     }
//     if (sprite.y < Screen.height / 2) {
//       sprite.y += delta;
//     }
//     if (sprite.y >= Screen.height / 2 ) {
//       currentState = 2;
//       spriteStates[currentState](delta);
//     }
//   },
//   delta => {
//     // Move to bottom
//   }
// ]

let moveState = 0;
const move = [
  delta => {
    if (sprite.x <= 25 && sprite.y >= yMid) {
      moveState = 1;
      move[moveState](delta);
      return;
    }
    sprite.x = sprite.x > 25 ? sprite.x - delta : sprite.x;
    sprite.y = sprite.y < yMid ? sprite.y + delta : sprite.y;
  },
  delta => {
    if (sprite.x >= xMid && sprite.y >= yMax - 25) {
      moveState = 2;
      move[moveState](delta);
      return;
    }
    sprite.x = sprite.x < xMid ? sprite.x + delta : sprite.x;
    sprite.y = sprite.y < yMax - 25 ? sprite.y + delta : sprite.y;
  },
  delta => {

  }
]
let rotateState = 0;
const rotate = [
  delta => {}
]

export default defineComponent({
  name: 'IndexPage',
  data () {
    return ({
      fps: 0,
      elapsedSec: 0
    })
  },
  mounted () {
    // Init Pixi canvas
    const pixiEl = this.$refs.pixiContainer;
    pixiEl.appendChild(app.view);
    app.stage.addChild(sprite);
    // Update function
    app.ticker.add((delta) => {
      // Track elapsed time
      elapsed += app.ticker.deltaMS;
      // Update Vue components
      if (elapsed / 1000 >= uiUpdate) {
        this.fps = app.ticker.FPS.toFixed(2);
        this.elapsedSec = Math.round(elapsed / 1000);
        uiUpdate++;
      }
      // Move the Sprite
      move[moveState](delta);
      rotate[rotateState](delta);
    });
  },
  methods: {
    setOffset (offset) {
      return ({ minHeight: '100vh' })
    }
  }
})
</script>

<style>
.pixi-canvas-container {
  position: absolute;
  top: 50vh;
  left: 50vw;
  height: 100vh;
  width: 100vw;
  margin-top: -50vh;
  margin-left: -50vw;
}
</style>
