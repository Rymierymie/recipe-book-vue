<template>
    <div>
        
    </div>
</template>

<script>
import Recipes from '../assets/Recipes.json';

export default {
  data () {
  let recipe_list = [];
  let ingredients_list = [];
  for (var recipe in Recipes){
      recipe_list.push(Recipes[recipe].recipe)
      for (var ingredient in Recipes[recipe].ingredients){
          let name = Recipes[recipe].ingredients[ingredient].name
          if (ingredients_list.findIndex(x => x == name) == -1){
              ingredients_list.push(name);
          } else {
              console.log("Already in the list!")
              }
      }
     
      }
   ingredients_list.sort()
  

   return {
      recipe_list: recipe_list,
      ingredients_list: ingredients_list
    }   
  },
  methods: {
      recipe_builder(){
          
  }
} 
}      


</script>


<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>

</style>