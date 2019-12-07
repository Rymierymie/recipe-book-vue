<template>
  <div id="app">
    <HelloWorld msg="Recipe Book App"/>
    <RecipeList />
  </div>
</template>

<script>
import HelloWorld from './components/HelloWorld.vue'
import RecipeList from './components/RecipeList.vue'

export default {
  name: 'app',
  components: {
    HelloWorld,
    RecipeList
  }
}
</script>

<style>

</style>
