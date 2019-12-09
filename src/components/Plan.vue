<template>
    <div>
        <h1>Plan</h1>
        <button @click="reset_planner()">Reset</button>
        <div v-for="(value, name, index) in meal_plan" v-bind:key="index">
            <h2>{{ name }}</h2>
            <h3>Lunch</h3>
            <select v-model="value.lunch.recipe">
                <option selected></option>
                <option v-for="(item, index) in menu" v-bind:key="index">
                    {{ item }}
                </option>
            </select>
            <p v-if="value.lunch.recipe != ''">
            <button @click="value.lunch.serves -= 1">-</button>
            {{ value.lunch.serves }}
            <button @click="value.lunch.serves += 1">+</button>
            </p>
            <h3>Dinner</h3>
            <select v-model="value.dinner.recipe">
                <option selected></option>
                <option v-for="(item, index) in menu" v-bind:key="index">
                    {{ item }}
                </option>
            </select>
            <p v-if="value.dinner.recipe != ''">
            <button @click="value.dinner.serves -= 1">-</button>
            {{ value.dinner.serves }}
            <button @click="value.dinner.serves += 1">+</button>
            </p>
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
                recipe: '',
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
      if (localStorage.meal_plan) {
          this.meal_plan = JSON.parse(localStorage.getItem('meal_plan'));
          }

      if (localStorage.menu) {
      this.menu = JSON.parse(localStorage.getItem('menu'));
      }
      
      eventBus.$on('menu_edit', new_menu => {
                this.menu = new_menu;
                });
    
  },
  watch: { 
        'meal_plan': { 
            handler: function() {
                for (var meal in this.meal_plan) {
                    if (this.meal_plan[meal].lunch.recipe == '' || this.meal_plan[meal].lunch.serves <= 0){
                        this.meal_plan[meal].lunch.serves = 0;
                    }
                    if (this.meal_plan[meal].dinner.recipe == '' || this.meal_plan[meal].dinner.serves <= 0){
                        this.meal_plan[meal].dinner.serves = 0;
                    }
                }
                eventBus.$emit('meal_plan_edit', this.meal_plan); 
                localStorage.setItem('meal_plan', JSON.stringify(this.meal_plan)) 
            },
            deep: true,
            },
  },

  methods: {
      reset_planner(){
          this.meal_plan = {
        sunday: {
            lunch: {
                recipe: '',
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

} 
}      


</script>


<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>

</style>