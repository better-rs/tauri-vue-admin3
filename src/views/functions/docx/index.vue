<script setup lang="ts">
  import type { UploadRawFile } from 'element-plus';
  import { ElUpload } from 'element-plus';
  import { ref } from 'vue';
  import { fileToArrayBuffer } from '@jsxiaosi/utils';
  import PreviewDocx from '@/components/PreviewDocx/index.vue';

  const fileSrc = ref<string | ArrayBuffer>(
    `https://supercutexiaosi.top/resource/develop_docx.docx`,
  );

  const beforeUpload = async (rawFile: UploadRawFile) => {
    fileSrc.value = await fileToArrayBuffer(rawFile);
    return false;
  };
</script>

<template>
  <div>
    <ElUpload :limit="1" accept=".docx,.doc" :before-upload="beforeUpload" action="">
      <ElButton style="margin-bottom: 12px">点击上传</ElButton>
    </ElUpload>

    <PreviewDocx :src="fileSrc" />
  </div>
</template>

<style lang="scss" scoped></style>
