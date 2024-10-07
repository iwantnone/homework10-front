<template>
  <div style="display: flex;justify-content: center;margin-top: 150px;">
    <el-form :model="form" label-width="auto" style="max-width: 600px">
      <el-form-item label="商品图片">
        <el-upload ref="upload" class="upload-demo" :action="uploadUrl" :show-file-list="false" :limit="1"
          :on-success="handleSuccess" :on-error="handleError" :auto-upload="false">
          <template #trigger>
            <el-button type="primary">选择文件</el-button>
          </template>
          <el-button class="ml-3" type="success" @click="submitUpload">
            上传至服务器
          </el-button>
        </el-upload>
      </el-form-item>
      <el-form-item label="商品名称">
        <el-input v-model="form.merchandiseName" />
      </el-form-item>
      <el-form-item label="商品价格">
        <el-input v-model.number="form.merchandisePrice" type="number" />
      </el-form-item>
      <el-form-item label="商品库存">
        <el-input v-model.number="form.merchandiseInventories" type="number" />
      </el-form-item>
      <el-form-item label="商品备注">
        <el-input v-model="form.remark" type="textarea" />
      </el-form-item>
      <el-form-item>
        <el-button type="primary" @click="onSubmit">创建</el-button>
        <el-button @click="onCancel">取消</el-button>
      </el-form-item>
    </el-form>
  </div>
</template>


<script>
import axios from 'axios';
import { ElMessage } from 'element-plus';

export default {
  data() {
    return {
      upload: null,
      uploadUrl: '/api/merchandise/image', // 设置上传接口地址
      form: {
        merchandiseName: '',
        merchandisePrice: 0,
        merchandiseInventories: 0,
        merchandiseImageUrl: '',
        remark: ''
      }
    };
  },
  methods: {
    onSubmit() {
      // 提交表单数据到后端
      axios.post('/api/merchandise', this.form)
        .then(response => {
          if (response.data.code === 200) {
            ElMessage.success('商品创建成功！');
            // 清空表单或跳转到其他页面
            this.resetForm();
          } else {
            ElMessage.error(response.data.msg);
          }
        })
        .catch(error => {
          ElMessage.error('商品创建失败！');
          console.error(error);
        });
    },
    onCancel() {
      // 清空表单或执行其他取消操作
      this.resetForm();
    },
    resetForm() {
      this.form.merchandiseName = '';
      this.form.merchandisePrice = 0;
      this.form.merchandiseInventories = 0;
      this.form.merchandiseImageUrl = '';
      this.form.remark = '';
      this.upload.clearFiles(); // 清空上传的文件
    },
    submitUpload() {
      this.upload.submit();
    },
    handleSuccess(response, file, fileList) {
      if (response.code === 200) {
        ElMessage.success('图片上传成功！');
        this.form.merchandiseImageUrl = response.data; // 将上传成功的图片URL保存到表单数据中
      } else {
        ElMessage.error(response.msg);
      }
    },
    handleError(err, file, fileList) {
      ElMessage.error('图片上传失败！');
      console.error(err);
    }
  },
  mounted() {
    this.upload = this.$refs.upload;
  }
};
</script>
