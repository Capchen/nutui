<template>
  <div class="nut-progress">
    <div
      class="nut-progress-outer"
      :class="[showText && !textInside ? 'nut-progress-outer-part' : '', size ? 'nut-progress-' + size : '']"
      :style="{ height: height }"
    >
      <div :class="['nut-progress-inner', status == 'active' ? 'nut-active' : '']" :style="bgStyle">
        <div
          class="nut-progress-text nut-progress-insidetext"
          ref="insideText"
          :style="{
            lineHeight: height,
            left: `${percentage}%`,
            transform: `translate(-${+percentage}%,-50%)`,
            background: textBackground || strokeColor
          }"
          v-if="showText && textInside && !slotDefault"
        >
          <span :style="textStyle">{{ percentage }}{{ isShowPercentage ? '%' : '' }}</span>
        </div>
        <div
          ref="insideText"
          :style="{
            position: `absolute`,
            top: `50%`,
            left: `${percentage}%`,
            transform: `translate(-${+percentage}%,-50%)`
          }"
          v-if="showText && textInside && slotDefault"
        >
          <slot></slot>
        </div>
      </div>
    </div>
    <div class="nut-progress-text" :style="{ lineHeight: height }" v-if="showText && !textInside">
      <template v-if="status == 'text' || status == 'active'">
        <span :style="textStyle">{{ percentage }}{{ isShowPercentage ? '%' : '' }} </span>
      </template>
      <template v-else-if="status == 'icon'">
        <slot name="icon-name">
          <Checked width="15px" height="15px" color="#439422"></Checked>
        </slot>
      </template>
    </div>
  </div>
</template>

<script lang="ts">
import { computed, onMounted, useSlots, ref, watch } from 'vue';
import { createComponent } from '@/packages/utils/create';
import Taro, { eventCenter, getCurrentInstance } from '@tarojs/taro';
import { Checked } from '@nutui/icons-vue-taro';
const { create } = createComponent('progress');
export default create({
  components: { Checked },
  props: {
    percentage: {
      type: [Number, String],
      default: 0,
      required: true
    },
    size: {
      type: String,
      default: 'base'
    },
    status: {
      type: String,
      default: 'text'
    },
    strokeWidth: {
      type: [Number, String],
      default: ''
    },
    textInside: {
      type: Boolean,
      default: false
    },
    showText: {
      type: Boolean,
      default: true
    },
    strokeColor: {
      type: String,
      default: ''
    },
    textColor: {
      type: String,
      default: ''
    },
    textBackground: {
      type: String,
      default: ''
    },
    iconColor: {
      type: String,
      default: '#439422'
    },
    isShowPercentage: {
      type: Boolean,
      default: true
    }
  },
  setup(props, { emit }) {
    const slotDefault = !!useSlots().default;
    const height = ref(props.strokeWidth + 'px');
    const insideText = ref();
    const percentage = computed(() => {
      return props.percentage >= 100 ? 100 : props.percentage;
    });
    const bgStyle = computed(() => {
      return {
        width: percentage.value + '%',
        background: props.strokeColor || ''
      };
    });
    const textStyle = computed(() => {
      return {
        color: props.textColor || ''
      };
    });

    onMounted(() => {
      eventCenter.once((getCurrentInstance() as any).router.onReady, () => {});
    });
    return {
      height,
      percentage,
      bgStyle,
      textStyle,
      insideText,
      slotDefault
    };
  }
});
</script>
