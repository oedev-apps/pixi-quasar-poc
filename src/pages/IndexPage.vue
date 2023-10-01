<template>
  <q-page class="flex flex-center" :style-fn="setOffset">
    <!-- <div>
      <img src="sample.png">
    </div> -->
    <div ref="pixiContainer" class="pixi-canvas-container" style="border: 1px solid greenyellow;">
    </div>
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
let elapsed = 0.0;
let uiUpdate = 1;
document.body.style.backgroundColor = 'black';

export default defineComponent({
  name: 'IndexPage',
  data () {
    return ({
      fps: 0
    })
  },
  mounted () {
    const pixiEl = this.$refs.pixiContainer;
    // console.log(pixiEl);
    pixiEl.appendChild(app.view);
    app.stage.addChild(sprite);
    app.ticker.add((delta) => {
      elapsed += delta;
      if (elapsed >= uiUpdate) {

        uiUpdate++;
      }
      // sprite.y = 100.0 + Math.cos(elapsed/50.0) * 100.0;
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
