<template>
  <div class="currencyConverter">
    <div>Currency converter</div>
    <el-form label-position="left" label-width="130px">
      <el-form-item label="`Currency converter">
        <el-date-picker v-model="date" type="date" format="dd-MMM-yyyy">
        </el-date-picker>
      </el-form-item>
    </el-form>
  </div>
</template>
<script lang="ts">
import { Component, Vue } from "vue-property-decorator";

@Component
export default class HelloWorld extends Vue {
  date: string = "";
}
</script>
<style scoped></style>
