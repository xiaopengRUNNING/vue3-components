<script setup>
import { reactive } from 'vue';
import TableSearchWrapper from './components/TableSearchWrapper.vue';

const form = reactive({
  field1: '',
  field2: '',
  field3: '',
  field4: '',
  field5: '',
  field6: ''
});
</script>

<template>
  <TableSearchWrapper :form="form" style="margin: 48px;">
    <template v-slot:item1>
      <a-form-item field="field1" label="筛选项1">
        <a-input v-model="form.field1" />
      </a-form-item>
    </template>
    <template v-slot:item2>
      <a-form-item field="field2" label="筛选项2">
        <a-input v-model="form.field2" />
      </a-form-item>
    </template>
    <template v-slot:item3>
      <a-form-item field="field3" label="筛选项3">
        <a-input v-model="form.field3" />
      </a-form-item>
    </template>
    <template v-slot:item4>
      <a-form-item field="field4" label="筛选项4">
        <a-input v-model="form.field4" />
      </a-form-item>
    </template>
    <template v-slot:item5>
      <a-form-item field="field5" label="筛选项5">
        <a-input v-model="form.field5" />
      </a-form-item>
    </template>
    <template v-slot:item6>
      <a-form-item field="field6" label="筛选项6">
        <a-input v-model="form.field6" />
      </a-form-item>
    </template>
  </TableSearchWrapper>
</template>

<style scoped>
.logo {
  height: 6em;
  padding: 1.5em;
  will-change: filter;
  transition: filter 300ms;
}
.logo:hover {
  filter: drop-shadow(0 0 2em #646cffaa);
}
.logo.vue:hover {
  filter: drop-shadow(0 0 2em #42b883aa);
}
</style>
