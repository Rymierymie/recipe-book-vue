<template>
  <div id="app">
    <Nav msg="Recipe Book App"/>
    <div>
      <button @click="display('search-component')">Show Search</button>
      <Search id="search-component" class="display-toggle" />
    </div>

    
  </div>
</template>

<script>
import Nav from './components/Nav.vue'
import Search from './components/Search.vue'

export default {
  name: 'app',
  components: {
    Nav,
    Search
  },
  methods: {
    display(id) {
      let element = document.getElementById(id);
      element.classList.toggle("display-toggle");
    }
  }
}
</script>

<style>

.display-toggle {
  display: none;
}

</style>
