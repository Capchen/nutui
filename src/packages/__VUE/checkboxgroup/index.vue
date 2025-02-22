<script lang="ts">
import { h, watch, provide, computed, ComponentInternalInstance, reactive, ComponentPublicInstance } from 'vue';
import { createComponent } from '@/packages/utils/create';
import { useExpose } from '@/packages/utils/useExpose/index';
const { create, componentName } = createComponent('checkbox-group');

export default create({
  props: {
    modelValue: {
      type: Array,
      default: () => []
    },
    disabled: {
      type: Boolean,
      default: false
    },
    max: {
      type: Number,
      default: 0
    }
  },
  emits: ['change', 'update:modelValue'],
  setup(props, { slots, emit }) {
    const state = reactive({
      children: [] as ComponentPublicInstance[]
    });

    const relation = (child: ComponentInternalInstance, cancel: boolean = false) => {
      if (child.proxy) {
        if (!cancel) {
          state.children.push(child.proxy);
        } else {
          let key_1 = state.children.indexOf(child.proxy);
          if (key_1 > -1) {
            state.children.splice(key_1, 1);
          }
          let key_2 = props.modelValue.indexOf((child.proxy as any)?.label);
          if (key_2 > -1) {
            const value = props.modelValue.filter((_, index) => index !== key_2);
            emit('update:modelValue', value);
          }
        }
      }
    };

    const updateValue = (value: string[]) => {
      emit('update:modelValue', value);
      emit('change', value);
    };

    const toggleAll = (checked: boolean) => {
      let values: string[] = [];
      if (!!checked) {
        state.children.forEach((item: any) => {
          if (!item?.disabled) {
            values.push(item?.label);
          }
        });
      }
      emit('update:modelValue', values);
    };

    const toggleReverse = () => {
      let values = props.modelValue.slice();
      state.children.forEach((item: any) => {
        let findIndex = values.findIndex((value: any) => value === item.label);
        if (findIndex > -1) {
          values.splice(findIndex, 1);
        } else {
          if (!item?.disabled) {
            values.push(item?.label);
          }
        }
      });
      emit('update:modelValue', values);
    };

    provide('parent', {
      value: computed(() => props.modelValue),
      disabled: computed(() => props.disabled),
      max: computed(() => props.max),
      updateValue,
      relation
    });

    watch(
      () => props.modelValue,
      (value) => {
        emit('change', value);
      }
    );

    useExpose({ toggleAll, toggleReverse });

    return () => {
      return h(
        'view',
        {
          class: componentName
        },
        slots.default?.()
      );
    };
  }
});
</script>
