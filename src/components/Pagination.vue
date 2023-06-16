<!-- eslint-disable vue/multi-word-component-names -->
<script setup lang="ts">
import { ref, reactive, onMounted, watch, computed } from "vue";

const props = defineProps({
  totalItems: {
    type: Number,
    required: true,
  },
  itemPerPage: {
    type: Number,
    required: true,
  },
  pageProvider: {
    type: String,
    required: true,
  },
  currentPage: {
    type: Number,
    required: true,
  },
  pages: {
    type: Array,
    required: true,
    of: Number,
  },
});

const prevPage = computed(() => {
  if (props.currentPage === 2) {
    return props.pageProvider
  } else {
    return `${props.pageProvider}/${props.currentPage - 1}`
  }
});

const nextPage = computed(() => {
    return `${props.pageProvider}/${props.currentPage + 1}`
});


const shouldShowPage = (page: number) => {
  let currentPageToShow
  if (props.currentPage === 1) {
    currentPageToShow = 3
    return props.currentPage + currentPageToShow >= page
  }
  if (props.currentPage === 2) {
    currentPageToShow = 2
    return props.currentPage + currentPageToShow >= page
  }
  if (props.currentPage === props.pages.length - 1) {
    currentPageToShow = 4
    return props.currentPage - currentPageToShow <= page
  }
  if (props.currentPage === props.pages.length) {
    currentPageToShow = 5
    return props.currentPage - currentPageToShow <= page
  }

  const currentPageToShowMin = 3
  const currentPageToShowMax = 1
  return props.currentPage - currentPageToShowMin <= page && page <= props.currentPage + currentPageToShowMax
}

</script>

<template>
  <nav class="nav">
    <ul class="list">
      <li class="item prev">
        <router-link
          :to="prevPage"
          :disabled="currentPage === 1"
          class="circle"
        >
          ←
        </router-link>
      </li>
      <li
        v-for="(page, i) in (pages as number[])"
        v-show="shouldShowPage(page)"
        :key="i"
        class="item"
      >
        <router-link
          :to="page + 1 === 1 ? pageProvider : `${pageProvider}/${page + 1}`"
        >
          {{ page + 1 }}
        </router-link>
      </li>
      <li class="item next">
        <router-link
          :to="nextPage"
          :disabled="currentPage === pages.length"
          class="circle"
        >
          →
        </router-link>
      </li>
    </ul>
  </nav>
</template>

<style lang="scss" scoped>
.nav {
    max-width: 690px;
    margin: 30px auto;
    display: flex;
    justify-content: space-evenly;
    align-items: center;
    height: 55px;
    width: 100%;
    padding: 0 10px;
}

.list {
  display: flex;
  align-items: center;
  width: 100%;
  padding-left: 0;
  margin-bottom: 0;
  justify-content: space-between;
}

.item {
  list-style: none;
  width: 30px;
  height: 30px;

  a {
    color: #585858;
    text-decoration: none;
    width: 100%;
    display: flex;
    height: 100%;
    align-items: center;
    justify-content: center;

    &[disabled='true'] {
      pointer-events: none;
      opacity: 0.3;
    }

    &[aria-current='page'] {
      border: 1px solid #585858;
      border-radius: 50%;
    }

  }

  &.prev {
    a {
      &[aria-current='page'] {
        color: #585858;
        background: inherit;
      }
    }
  }
}
// @media (min-width: $breakPoint) {
//   .nav {
//     max-width: 370px;
//     position: initial;
//     transform: translate(0px, -100%);
//     margin: auto;
//     .list {
//       .item {
//         a {
//           border-radius: 0px;
//           border: 1px solid #EDEDED;
//         }
//       }
//     }
//   }
// }
</style>
