<template>
  <div class="nav">
    <!-- getItems passed down on event emitter so we can run the function within the category component -->
  <CategoryComponent :selectedCategoryId="selectedCategoryId" @onCategorySelect="getItems"/>
  </div>
  <div class="body">
    <!--actual contents of item passed down to the item component when event emitter fires getItems-->
    <ItemComponent :selectedCategoryName="selectedCategoryName" :items="items" />
  </div>
</template>

<script>
import CategoryComponent from './components/CategoryComponent.vue';
import ItemComponent from './components/ItemComponent.vue';
import axios from 'axios';
export default {
  name: 'App',
  components: {
    CategoryComponent,
    ItemComponent
  },
  data() {
      return {
        items: [],
        selectedCategoryId: null,
        selectedCategoryName: null,
        error: '',
        url: 'http://localhost:5000/api/item',
      }
  },
  methods: {
    getItems(category) {
      this.selectedCategoryId = category.id;
      this.selectedCategoryName = category.name;
        axios
        .get(`${this.url}/${category.id}`)
        .then((response) => {
          const data = response.data;
          this.items = data;
        })
        .catch((err) => {
          console.error(err);
          this.error = err.message
          throw err;
        });
    }
  }
}
</script>

<style>
.nav {
  width: 10%;
  float: left;
  background: rgb(251, 247, 238);
  box-shadow: 0px 4px 4px rgba(0, 0, 0, 0.25);
  height: 800px;
  padding-top: 20px;
  font-size: 30px;
}
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin: 0;
  padding: 0;
}

li {
  list-style-type: none;
}

button {
          padding: 5px;
        border-radius: 5px;
        border: none;
        background: rgb(251, 247, 238);
        cursor: pointer;
}
</style>
