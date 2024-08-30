<template>
  <div class="home">
    <BaseForm></BaseForm>
  </div>
</template>

<script lang="ts">
import { defineComponent } from "vue";
import BaseForm from "@/components/base/BaseForm.vue";

export default defineComponent({
  name: "HomeView",
  components: {
    BaseForm,
  },
});
</script>
<style lang="less" scoped>
.home {
  display: flex;
  align-items: center;
  justify-content: center;
  height: 100%;
}
</style>
