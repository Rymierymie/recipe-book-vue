<template>
  <div class="hello">
    <h1>{{ msg }}</h1>
    <button @click="clear_all_local()">Clear All Local</button>
  </div>
</template>

<script>
//import { eventBus } from '../event-bus';

export default {
  name: 'HelloWorld',
  props: {
    msg: String
  },
  methods: {
    clear_all_local(){
      localStorage.clear();
      /* eventBus.$emit('local_cleared', true); */
      alert("Local Storage Cleared!")
    }
  }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>



</style>
