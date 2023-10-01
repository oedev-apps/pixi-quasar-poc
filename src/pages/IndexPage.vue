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

const app = new PIXI.Application({ width: Screen.width - 2, height: Screen.height - 2 });
const sprite = PIXI.Sprite.from('sample.png');
sprite.anchor.set(0.5);
sprite.x = app.screen.width / 2;
sprite.y = app.screen.height / 2;
sprite.scale.x = 0.25;
sprite.scale.y = 0.25;
let elapsed = 0.0;
let uiUpdate = 1;
document.body.style.backgroundColor = 'black';

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
      sprite.y = Math.max(sprite.y - 1, 0);
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
