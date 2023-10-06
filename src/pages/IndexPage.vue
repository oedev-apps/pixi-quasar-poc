<template>
  <q-page class="flex flex-center" :style-fn="setOffset">
    <div ref="pixiContainer" class="pixi-canvas-container" style="border: 1px solid greenyellow;"></div>
    <q-card flat class="absolute-bottom q-mb-sm q-mx-sm q-px-xs text-white" style="background-color: rgba(0, 0, 0, 0);">
      <div class="row q-px-lg">
        <q-badge>Speed</q-badge>
        <q-slider
          v-model="speed"
          :min="1"
          :max="10"
          :step="1"
          snap dark label=""
        />
      </div>
      <div class="row q-px-lg">
        <div class="col">FPS: {{ fps }}&nbsp;</div>
        <div class="col">Elapsed: {{ elapsedSec }}s</div>
      </div>
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
sprite.y = 25;
sprite.scale.x = 0.25;
sprite.scale.y = 0.25;
let elapsed = 0.0;
let uiUpdate = 1;
document.body.style.backgroundColor = 'black';

// Sprite movement states
let moveState = 0;
const move = [
  (delta, speed) => {
    if (sprite.x <= 25 && sprite.y >= yMid) {
      moveState = 1;
      move[moveState](delta, speed);
      return;
    }
    sprite.x = sprite.x > 0 ? sprite.x - ( speed * 100 * delta / yMid ) : sprite.x;
    sprite.y = sprite.y < yMid ? sprite.y + ( speed * 100 * delta / xMid ) : sprite.y;
  },
  (delta, speed) => {
    if (sprite.x >= xMid && sprite.y >= yMax - 25) {
      moveState = 2;
      move[moveState](delta, speed);
      return;
    }
    sprite.x = sprite.x < xMid ? sprite.x + ( speed * 100 * delta / yMid ) : sprite.x;
    sprite.y = sprite.y < yMax - 25 ? sprite.y + ( speed * 100 * delta / xMid ) : sprite.y;
  },
  (delta, speed) => {
    if (sprite.x >= xMax - 25 && sprite.y <= yMid) {
      moveState = 3;
      move[moveState](delta, speed);
      return;
    }
    sprite.x = sprite.x < xMax - 25 ? sprite.x + ( speed * 100 * delta / yMid ) : sprite.x;
    sprite.y = sprite.y > yMid ? sprite.y - ( speed * 100 * delta / xMid ) : sprite.y;
  },
  (delta, speed) => {
    if (sprite.x <= xMid && sprite.y <= 25) {
      moveState = 0;
      move[moveState](delta, speed);
      return;
    }
    sprite.x = sprite.x > xMid ? sprite.x - ( speed * 100 * delta / yMid ) : sprite.x;
    sprite.y = sprite.y > 0 ? sprite.y - ( speed * 100 * delta / xMid ) : sprite.y;
  }
]

export default defineComponent({
  name: 'IndexPage',
  data () {
    return ({
      fps: 0,
      elapsedSec: 0,
      speed: 5
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
      move[moveState](delta, this.speed);
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
