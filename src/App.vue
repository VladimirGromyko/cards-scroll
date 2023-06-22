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
    /**
     * getItems - this async function is used to receive data from the server and upload it to the application
     * to display a list of users.
     * @returns {Promise<number|string>} data from the server as a true result or record of
     * error in console
     */
    async getItems () {
      try{
        this.loading = true;
        const res = await fetch('https://randomuser.me/api/?results=50', {
        })
        const data = await res.json()
        return data.results
      } catch (e) {
        console.log('Server error')
        console.log(e)
      } finally {
        this.loading = false
      }
    },

    /**
     * loadMore - this async function is used to load additional items into the list.
     * It loads new elements only when necessary and adds them to the existing elements.
     *
     * Variable `items` (array of objects) - is the main data store. Data retrieved from the temporary store tempItems
     * is written to it each time the scroll ribbon with cards is scrolled down to the end.
     * The variable `tempItems` (array of objects) is a temporary storage for the data received from the server.
     * When data is fetched from the tempItems temporary storage to the main storage (items),
     * the tempItems temporary storage is cleared. As soon as the temporary storage is completely cleared,
     * a new request is made to the server for the next piece of data.
     * @returns {Promise<void>}
     */
    async loadMore() {
      const completeItems = (tempItems) => {
        const newItems = tempItems.splice(0, portion)
        this.items = [...this.items, ...newItems];
      }
      if (!this.items.length || !this.tempItems.length) {
        this.tempItems = await this.getItems()
        this.tempItems && completeItems(this.tempItems)
      } else if (this.tempItems.length) completeItems(this.tempItems)
    },

    /**
     * handleScroll - the function monitors the scrolling of the container.
     * And if the user has scrolled to the bottom of the list, it calls the loadMore() function.
     * This allows additional items to be loaded when the end of the list is reached.
     */
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
  overflow-y: hidden;
  border: 1px solid #ccc;
  background: linear-gradient(180deg, rgba(70, 115, 243, 0.04), rgba(50, 99, 243, 0.4))
}
.container:hover {
  overflow-y: scroll;
}
.item {
  border: 1px solid #ccc;
  border-radius: 4px;
  max-width: 445px;
  margin: 10px 10px 10px 25px;
  background: white;
}
</style>
