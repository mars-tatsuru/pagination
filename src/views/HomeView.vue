<script setup lang="ts">
import { ref, reactive, onMounted, watch } from "vue";
import Pagination from "@/components/Pagination.vue";
import { createClient } from 'microcms-js-sdk';

const client = createClient({
  serviceDomain: "oly7867sh3",
  apiKey: "3DdYSx2xto331BSkoxSSWq4OkpGqkamqXj87",
});


// ページネーションに必要なデータ
const items = ref([])
const totalItems = ref(0)
const itemsPerPage = ref(20)
const pages = ref([])
const pageProvider = "/"
const currentPage = ref(1)


const fetchItems = async() => {
  await client.get({
      endpoint: 'blogs',
      queries: { limit: 20},
    })
    .then(res => {
      console.log(res)
      items.value = res.contents
      totalItems.value = res.totalCount
    })
    .catch(err => console.error(err));
}

// const setPagiantionProps = () => {
//   totalItems.value = items.value.length
//   pages.value = [
//     ...Array(Math.ceil(totalItems.value / itemsPerPage.value)).keys(),
//   ]
//   console.log(pages.value)
// }

onMounted(async() => {
  await fetchItems()
  // setPagiantionProps()
})





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
      :per-page="itemsPerPage"
      :current-page="currentPage"
      :pages="pages"
      :page-provider="pageProvider"
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
