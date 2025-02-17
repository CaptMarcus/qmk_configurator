<template>
  <div>
    <div ref="console">
      <controllerTop />
      <statusPanel />
      <controllerBottom />
    </div>
    <div class="hint">
      <a target="_blank" href="https://github.com/qmk/qmk_toolbox/releases">{{
        $t('message.downloadToolbox.label')
      }}</a>
    </div>
    <div class="split-content">
      <div class="left-side">
        <layerControl />
      </div>
      <div class="right-side">
        <p>
          <label class="keymap--label" :title="$t('message.ColorwayTip.title')">
            {{ $t('message.keymap.label') }}:
            <font-awesome-icon
              v-if="continuousInput"
              icon="keyboard"
              fixed-width
            />
          </label>
          &nbsp;
          <!-- maintain spacing for paragraph -->
          <select
            class="keymap--keyset"
            id="colorway-select"
            v-model="curIndex"
          >
            <option
              class="option"
              v-for="(name, index) in displayColorways"
              :key="index"
              :value="index"
              >{{ name }}</option
            >
          </select>
        </p>
        <visualKeymap :profile="false" />
      </div>
    </div>
  </div>
</template>

<script>
import capitalize from 'lodash/capitalize';
import { mapMutations as _mapMutations, createNamespacedHelpers } from 'vuex';
const { mapState, mapGetters, mapMutations } = createNamespacedHelpers(
  'keymap'
);
import ControllerTop from '@/components/ControllerTop';
import StatusPanel from '@/components/StatusPanel';
import ControllerBottom from '@/components/ControllerBottom';
import VisualKeymap from '@/components/VisualKeymap';
import LayerControl from '@/components/LayerControl';
import * as jquery from '@/jquery';

export default {
  name: 'Main',
  props: {},
  components: {
    ControllerTop,
    StatusPanel,
    ControllerBottom,
    VisualKeymap,
    LayerControl
  },
  computed: {
    ...mapState(['continuousInput']),
    ...mapGetters(['colorwayIndex', 'colorways']),
    curIndex: {
      get() {
        return this.colorwayIndex;
      },
      set(value) {
        this.nextColorway(value);
      }
    },
    displayColorways() {
      return this.colorways.map(keyset => {
        return keyset
          .replace(/-/g, ' ')
          .split(' ')
          .map(word => capitalize(word))
          .join(' ')
          .replace(/Gmk/, 'GMK')
          .replace(/^Sa/, 'SA')
          .replace(/^Dsa/, 'DSA')
          .replace(/Wob/, 'WOB')
          .replace(/Ta/, 'TA');
      });
    },
    redditPost() {
      return 'https://www.reddit.com/r/MechanicalKeyboards/comments/aio97b/qmk_configurator_updates_beta_need_your_input/';
    }
  },
  methods: {
    ...mapMutations(['nextColorway']),
    ..._mapMutations('app', ['resetListener'])
  },
  mounted() {
    jquery.init();
  },
  beforeDestroy() {
    this.resetListener();
  }
};
</script>
<style>
.hint {
  display: grid;
  justify-content: end;
}
#colorway-select {
  font-family: 'Noto Sans', 'Helvetica Neue', Helvetica, Arial, sans-serif;
}
.beta-feedback {
  position: fixed;
  right: 10px;
  top: 30px;
}
.beta-button {
  height: 30px;
  font-size: 15px;
  border-radius: 9px;
  cursor: pointer;
}
.keymap--label {
  float: left;
}
.keymap--keyset {
  float: right;
}
</style>
