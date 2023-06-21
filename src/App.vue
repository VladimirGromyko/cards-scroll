<template>
  <div
      ref="scrollContainer"
      class="container"
      @wheel="handleScroll"
  >
    <div
        v-for="item in items"
        :key="item.id.value"
        class="item"
    >
      <Card :item="item"/>
    </div>
    <div v-if="loading">
      <Loader />
    </div>
  </div>
</template>

<script>
import Card from "./components/Сard.vue";
import Loader from "./components/Loader.vue";

const portion = 10
export default {
  components: {Card, Loader},
  data() {
    return {
      items: [],
      tempItems: [],
      loading: false,
      page: 1
    };
  },
  mounted() {
    this.loadMore();
  },
  methods: {
    async getItems () {
      try{
        // API call
        this.loading = true;
        const res = await fetch('https://randomuser.me/api/?results=50', {
        })
        debugger
        const data = await res.json()
        return data.results
      } catch (e) {
        console.log(e)
      } finally {
        this.loading = false
      }
    },
    async loadMore() {
      if (!this.items.length || !this.tempItems.length) {
        this.tempItems = await this.getItems()
      }
      if (this.tempItems.length) {
        const newItems = this.tempItems.splice(0, portion)
        this.items = [...this.items, ...newItems];
      }
    },
    handleScroll() {
      const container = this.$refs.scrollContainer;
      if (container.scrollTop + container.clientHeight >= container.scrollHeight) {
        this.loadMore();
      }
    }
  }
};
</script>

<style>
.container {
  height: 600px;
  min-width: 500px;
  overflow-y: scroll;
  border: 1px solid #ccc;
  background: white;
}
.item {
  border: 1px solid #ccc;
  border-radius: 4px;
  margin: 10px;
}
</style>
