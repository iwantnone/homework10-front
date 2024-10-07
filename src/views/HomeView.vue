<script setup>
import { ElCard, ElMessage } from 'element-plus';
import { onMounted, ref } from 'vue';
import { useRouter } from 'vue-router';

const name = ref('');
const router = useRouter();
const page = ref(null);
const tableData = ref([]);
const currentPage = ref(1);
const pageSize = ref(10);


const fetchData = async () => {
  try {
    const res = await fetch(`/api/merchandise?pageNo=${currentPage.value}&pageSize=${pageSize.value}&merchandiseName=${name.value}`);
    if (!res.ok) {
      throw new Error(`HTTP error! Status: ${res.status}`);
    }
    const data = await res.json();
    if (data.code == 200) {
      page.value = data.data; // 将整个 data 对象赋值给 page
      tableData.value = data.data.element; // 从 data.element 获取商品列表
    } else {
      ElMessage({ type: 'error', message: data.msg });
    }
  } catch (error) {
    ElMessage({ type: 'error', message: error.message });
  }
};



onMounted(fetchData);

const search = () => {
  currentPage.value = 1; // Reset to the first page on search
  fetchData();
};

const handlePagination = (pageNo) => {
  currentPage.value = pageNo;
  fetchData();
};

function add() {
  router.push("/add");
}
</script>

<template>
  <el-card>
    <div style="display: flex; gap: 10px;">
      <el-input v-model="name" style="width: 240px" placeholder="请输入商品名称" />
      <el-button type="primary" @click="search">查询</el-button>
      <el-button type="warning" @click="add">新增</el-button>
    </div>
  </el-card>
  <el-table :data="tableData" border style="width: 100%; margin-top: 15px;">
    <el-table-column prop="merchandiseName" label="商品名称" width="380" />
    <el-table-column prop="merchandiseImageUrl" label="商品图片地址" />

    <el-table-column prop="merchandisePrice" label="商品价格" width="180" />
    <el-table-column prop="merchandiseInventories" label="商品库存" width="180" />
  </el-table>
  <div style="width: 100%; display: flex; justify-content: end;">
    <el-pagination layout="prev, pager, next" :total="page ? page.total : 0" :current-page="currentPage"
      :page-size="pageSize" @current-change="handlePagination" />
  </div>
</template>
