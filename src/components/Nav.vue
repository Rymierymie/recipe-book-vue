<template>
  <div class="hello">
    <h1>{{ msg }}</h1>
    <button @click="clear_all_local()">Clear All Local</button>
  </div>
</template>

<script>
export default {
  name: 'HelloWorld',
  props: {
    msg: String
  },
  methods: {
    clear_all_local(){
      localStorage.clear();
      alert("Local Storage Cleared!")
    }
  }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>



</style>
