<template>
<div class="container">
  <div class="category-container">
    <div>Categories</div>
    <hr>
    <!--event emitter defined, this way we can select a category from here, the parent component can listen for it, and then retrieve the items to pass down to the item component-->
    <div class="category" v-for="(category, index) in categories" v-bind:item="category" v-bind:index="index" v-bind:key="category?.id" @click="$emit('onCategorySelect', category?.id)">{{ category?.name }}</div>
  </div>
</div>
</template>

<script>

import axios from 'axios';

export default {
  name: 'CategoryComponent',
  data() {
    return {
      categories: [],
      error: '',
      url: 'http://localhost:5000/api/category'
    }
  },

  //grab the list of categories first, then once a category is chosen we then fetch the items for that category. This way we are only loading the information we need.
  async created() {
       axios
      .get(this.url)
      .then((response) => {
        const data = response.data;
        this.categories = data;
      })
      .catch((err) => {
        console.error(err);
        throw err;
      });
  }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
.category {
  cursor: pointer;
  font-size: 20px;
}
</style>
