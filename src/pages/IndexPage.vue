<template>
  <q-page class="flex flex-center">
    <!-- <div>
      <img src="sample.png">
    </div> -->
    <div ref="pixiContainer">
    </div>
  </q-page>
</template>

<script>
import { defineComponent } from 'vue'
import { Screen } from 'quasar';
import * as PIXI from 'pixi.js';

const app = new PIXI.Application({ width: Screen.width, height: Screen.height });
const sprite = PIXI.Sprite.from('sample.png');
let elapsed = 0.0;

export default defineComponent({
  name: 'IndexPage',
  mounted () {
    const pixiEl = this.$refs.pixiContainer;
    // console.log(pixiEl);
    pixiEl.appendChild(app.view);
    app.stage.addChild(sprite);
    app.ticker.add((delta) => {
      elapsed += delta;
      sprite.x = 100.0 + Math.cos(elapsed/50.0) * 100.0;
    });
  }
})
</script>
