<template>
    <div>
        <h1>Plan</h1>
        <button @click="reset_planner()">Reset</button>
        <button @click="meal_plan_summary_builder()">Summary Builder</button>
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
      meal_plan_summary: Array,
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
                //this.meal_plan_summary_builder();
/*                 eventBus.$emit('meal_plan_edit', this.meal_plan); 
                console.log("huh?")
                console.log(this.meal_plan) */
                this.meal_plan_summary = this.meal_plan_summary_builder()
                //console.log("is the meal plan summary a thing??")
                console.log(this.meal_plan_summary)
                eventBus.$emit('meal_plan_summary_change', this.meal_plan_summary);
                localStorage.setItem('meal_plan', JSON.stringify(this.meal_plan)) 
            },
            deep: true,
            },
  },

  methods: {
      meal_plan_summary_builder: function(){
           let component = this;
           let meals = [];
           //console.log("hey");
           let new_meal_plan = component.meal_plan;
           //console.log(new_meal_plan);
           let keys = Object.keys(new_meal_plan)
           //console.log(keys);
           let times = ['lunch','dinner'];
           //console.log(keys)
           for (var key in keys){
               for (var time in times){                   
                   if (new_meal_plan[keys[key]][times[time]].recipe != ''){
                       let meal = new_meal_plan[keys[key]][times[time]].recipe;
                       let serves = new_meal_plan[keys[key]][times[time]].serves;
                       //console.log(meal);
                       //console.log(meals.findIndex(x => x.recipe == meal));
                       if (meals.findIndex(x => x.recipe == meal) == -1){
                           console.log("There's no " + meal + " already in the meal plan")
                           //add a push to the meals array here
                           meals.push({
                               recipe: meal,
                               serves: serves
                           })
                       } else {
                           console.log(meal + " is already in the list. Just updating the serve")
                           //update the serve for the meal in the meals array
                           let index = meals.findIndex(x => x.recipe == meal);
                           //console.log(meals[index].serves);
                           meals[index].serves = meals[index].serves + serves;
                           //console.log(meals[index].serves)
                       }
                       }
               }
           } 
           //console.log("component");
           component.meal_plan_summary = meals;
           console.log(component.meal_plan_summary);       
           return component.meal_plan_summary  
       },
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