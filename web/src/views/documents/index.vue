<template>
  <div>
    <div class="home-card-item" v-for="(v,k) in articleStoreData.articleList.value.data" :key="k">
      <el-card class="box-card">
        <template #header>
          <div class="card-header">
            <span>{{v.title}}</span>
            <span @click="showArticle(v)" style="color: #ff0000">详情>>></span>
          </div>
        </template>
        <div>
          <div class="head-box">
            <div>{{v.introduction}}</div>
          </div>
        </div>
      </el-card>
    </div>
    <el-pagination background
                   class="mt15"
                   layout="total, sizes, prev, pager, next, jumper"
                   :page-sizes="[10, 30, 50]"
                   v-model:current-page="state.queryParams.pagination.page_num"
                   v-model:page-size="state.queryParams.pagination.page_size"
                   :total="articleStoreData.articleList.value.total"
                   @size-change="onHandleSizeChange"
                   @current-change="onHandleCurrentChange">
    </el-pagination>
  </div>
</template>

<script setup lang="ts">
import {onMounted, reactive} from "vue";
import {useApiStore} from "/@/stores/apiStore";
import {storeToRefs} from "pinia";
import {useArticleStore} from "/@/stores/user_logic/articleStore";
import {DateStrToTime} from "../../utils/formatTime";
import {useRoute, useRouter} from "vue-router";
const apiStore = useApiStore()
const apiStoreData = storeToRefs(apiStore)
const articleStore = useArticleStore()
const articleStoreData = storeToRefs(articleStore)
const router = useRouter()



//定义变量
const state = reactive({
  queryParams:{
    table_name: 'article',
    field_params_list: [
      {field: 'status', field_chinese_name: '', field_type: '', condition: '=', condition_value: "1", operator: ''},
      {field: 'type', field_chinese_name: '', field_type: '', condition: '=', condition_value: "knowledge", operator: 'AND'},
    ] as FieldParams[],
    pagination: {
      page_num: 1,
      page_size: 30,
      order_by: '',
    } as Pagination,//分页参数
  } as QueryParams,
})

//获取article列表
const getArticleList = () => {
articleStore.getArticleList(state.queryParams)
}

//
const showArticle=(v:Article)=>{
  // console.log(v)
  articleStoreData.currentArticle.value=v
  router.push({path:"/static/showArticle"})
}

// 分页改变
const onHandleSizeChange = (val: number) => {
  state.queryParams.pagination.page_size = val;
  getArticleList()
};
// 分页改变
const onHandleCurrentChange = (val: number) => {
  state.queryParams.pagination.page_num = val;
  getArticleList()
};

//加载时
onMounted(() => {
  getArticleList()
});


</script>

<style scoped>

.card-header {
  display: flex;
  justify-content: space-between;
  align-items: center;
}

.home-card-item {
  padding: 10px;
  overflow: hidden;
  background: var(--el-color-white);
  color: var(--el-text-color-primary);
}

.card-text {
  display: flex;
  justify-content: space-between;
  height: 60px
}

.card-text-left {
  margin-top: auto;
  margin-bottom: auto;
}

.card-text-right {
  margin-top: auto;
  margin-bottom: auto;
  font-size: 30px;
}

.card-header-left {
  font-size: 15px;
  color: #AC96F1;
}
</style>