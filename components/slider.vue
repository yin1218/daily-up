<template>
    <a-slider v-model:value="value1" @afterChange="onAfterChange" />
</template>
<script>
import { defineComponent, ref } from 'vue';
export default defineComponent({
  setup() {
    const value1 = ref(30);
    const value2 = ref([20, 50]);

    const onAfterChange = value => {
      console.log('afterChange: ', value);
    };

    return {
      value1,
      value2,
      onAfterChange,
    };
  },
});
</script>

<style scoped>
.code-box-demo .ant-slider {
  margin-bottom: 16px;
}
</style>