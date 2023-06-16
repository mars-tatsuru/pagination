<script setup lang="ts">
import { ref, reactive, onMounted, watch, computed } from "vue";
import Pagination from "@/components/Pagination.vue";
import { createClient } from 'microcms-js-sdk';
import { useRouter, useRoute,onBeforeRouteLeave, onBeforeRouteUpdate } from "vue-router";


/******************************
 * clientIDとAPIキーを定義
 ******************************/
const client = createClient({
  serviceDomain: "oly7867sh3",
  apiKey: "3DdYSx2xto331BSkoxSSWq4OkpGqkamqXj87",
});


/******************************
 * 記事取得に必要な変数を定義
 ******************************/
const items = ref<any>([])
const totalItems = ref(0)


/******************************
 * ページネーションに必要な変数を定義
 ******************************/
const itemPerPage = ref()
const pages = ref<any>([])
const pageProvider = ""
const router = useRoute()


/******************************
 * 描画のタイミング等で発動するもの
 ******************************/
// routingする前に記事を更新するため
onBeforeRouteLeave((to, from) => {
  handleCreateBlogList(Number(to.params.pageNum))
});

// mountedの前に用意するべきもの
const currentPage = computed(() => {
  return Number(router.query.value) || 1
})

// mounted時に記事を取得する処理
onMounted(async() => {
  await fetchItems({
    offset: 0,
    limit: 5,
  })
  setPaginationProps()
})


/*******************************
 * メソッド
 ******************************/
// 記事を取得する関数
const fetchItems = async(queryParams: {
    offset: number;
    limit: number;
  }) => {
  await client.get({
      endpoint: 'blogs',
      queries: {
        offset: queryParams.offset,
        limit: queryParams.limit,
      },
    })
    .then(res => {
      items.value = res.contents
      totalItems.value = res.totalCount
      itemPerPage.value = res.limit
    })
    .catch(err => console.error(err));
}

// ページネーションのページ数を設定する関数
const setPaginationProps = () => {
  pages.value = [
    ...Array(Math.ceil(totalItems.value / itemPerPage.value)).keys(),
  ]
}

// ページネーションのページをクリックした時の処理
const handleCreateBlogList = async(pageNum: number) => {

  const queryParams: {
    offset: number;
    limit: number;
  } = {
    offset: (pageNum - 1) * itemPerPage.value,
    limit: itemPerPage.value,
  };

  await fetchItems(queryParams)
  setPaginationProps()
}



</script>

<template>
  <div class="articleWrapper">
    <h2>記事一覧</h2>
    <ul>
      <li
        v-for="item in items"
        :key="item.id"
      >
        <p>{{ item.title }}</p>
      </li>
    </ul>
    <Pagination
      v-if="items.length"
      :total="totalItems"
      :current-page="currentPage"
      :pages="pages"
      :page-provider="pageProvider"
      :per-page="itemPerPage"
    />
  </div>
</template>

<style scoped lang="scss">

.articleWrapper{
  max-width: 400px;
  margin: 0 auto;
  width: 100%;
  padding: 20px;

  h2{
    font-size: 24px;
    font-weight: bold;
    margin-bottom: 80px;
  }

  ul{
    padding-left: 0;
    margin-bottom: 0;

    li{
      list-style: none;
      margin-bottom: 20px;
      border-bottom: 1px solid #ccc;
      padding-bottom: 20px;

      p{
        font-size: 18px;
        font-weight: bold;
      }

    }

  }

}

</style>
