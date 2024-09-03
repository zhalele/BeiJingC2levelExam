<template>
  <el-config-provider namespace="ep">
    <!-- <BaseHeader /> -->
    <div class="flex main-container">
      <!-- <BaseSide /> -->
      <div w="full" py="4">
        <HelloWorld msg="" />
      </div>
    </div>
  </el-config-provider>
</template>

<style>
#app {
  margin: 0px 15px 5px 15px;
  color: var(--ep-text-color-primary);
}

html {
  background-color: #fafbff;
  -webkit-tap-highlight-color: rgba(0, 0, 0, 0);
}

.main-container {
  height: calc(100vh - var(--ep-menu-item-height) - 3px);
}

::v-deep .el-checkbox__label {
  vertical-align: text-top;
  white-space: normal;
  word-break: break-all;
  width: 250px;
}
</style>
