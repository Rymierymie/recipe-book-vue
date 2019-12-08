<template>
    <div>
        <h1>Plan</h1>
        <div>
            <h2>Sunday</h2>
                <div>
                    <h3>Lunch</h3>
                    <select>
                        <option v-for="(item, index) in menu" v-bind:key="index">
                            {{ item }}
                        </option>
                    </select>
                    <p>
                    <button v-on:click="meal_plan.sunday.lunch.serves -= 1">-</button>
                    {{ meal_plan.sunday.lunch.serves }}
                    <button v-on:click="meal_plan.sunday.lunch.serves += 1">+</button>
                    </p>
                </div>
                <div>
                    <h3>Dinner</h3>
                    <select>
                        <option v-for="(item, index) in menu" v-bind:key="index">
                            {{ item }}
                        </option>
                    </select>
                </div>
        </div>
        <div v-for="(value, name, index) in meal_plan" v-bind:key="index">
            <h2>{{ name }}</h2>
        </div>
    </div>
</template>

<script>
/* import Recipes from '../assets/Recipes.json'; */

import { eventBus } from '../event-bus';

export default {
  data () {
   return {
      menu: [],
      meal_plan: {
        sunday: {
            lunch: {
                recipe: 'Test',
                serves: 0
            },
            dinner: {
                recipe: '',
                serves: 0
            }
        },
        monday: {
            lunch: {
                recipe: '',
                serves: 0
            },
            dinner: {
                recipe: '',
                serves: 0
            }
        },
        tuesday: {
            lunch: {
                recipe: '',
                serves: 0
            },
            dinner: {
                recipe: '',
                serves: 0
            }
        },
        wednesday: {
            lunch: {
                recipe: '',
                serves: 0
            },
            dinner: {
                recipe: '',
                serves: 0
            }
        },
        thursday: {
            lunch: {
                recipe: '',
                serves: 0
            },
            dinner: {
                recipe: '',
                serves: 0
            }
        },
        friday: {
            lunch: {
                recipe: '',
                serves: 0
            },
            dinner: {
                recipe: '',
                serves: 0
            }
        },
        saturday: {
            lunch: {
                recipe: '',
                serves: 0
            },
            dinner: {
                recipe: '',
                serves: 0
            }
        }
    }

    }   
  },
  mounted() {
      if (localStorage.menu) {
      this.menu = JSON.parse(localStorage.getItem('menu'));
      }
      
      eventBus.$on('menu_edit', new_menu => {
                this.menu = new_menu;
                });
    
  },
  watch: {
      
        },
  methods: {
      recipe_builder(){
          
  },
} 
}      


</script>


<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>

</style>