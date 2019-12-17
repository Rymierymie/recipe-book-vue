<template>
    <div>
        <h1>Plan</h1>
        <button @click="reset_planner()">Reset</button>
        <div v-for="(value, name, index) in meal_plan" v-bind:key="index">
            <h2>{{ name }}</h2>
            <h3>Lunch</h3>
            <select v-model="value.lunch.recipe" v-on:change="mealPlanChange">
                <option value="" selected></option>
                <option v-for="(item, index) in menu" v-bind:key="index">
                    {{ item }}
                </option>
            </select>
            <p v-if="value.lunch.recipe != ''">
            <button @click="value.lunch.serves -= 1" v-on:click="mealPlanChange">-</button>
            {{ value.lunch.serves }}
            <button @click="value.lunch.serves += 1" v-on:click="mealPlanChange">+</button>
            </p>
            <h3>Dinner</h3>
            <select v-model="value.dinner.recipe" v-on:change="mealPlanChange">
                <option value="" selected></option>
                <option v-for="(item, index) in menu" v-bind:key="index">
                    {{ item }}
                </option>
            </select>
            <p v-if="value.dinner.recipe != ''">
            <button @click="value.dinner.serves -= 1" v-on:click="mealPlanChange">-</button>
            {{ value.dinner.serves }}
            <button @click="value.dinner.serves += 1" v-on:click="mealPlanChange">+</button>
            </p>
        </div>
    </div>
</template>

<script>

import { eventBus } from '../event-bus';

export default {
  data () {
      //console.log("start of data");

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
      console.log("mounted")
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
            //immediate: false,
            handler: function() {
                console.log("watch kicked in!");
                for (var meal in this.meal_plan) {
                    if (this.meal_plan[meal].lunch.recipe == '' || this.meal_plan[meal].lunch.serves <= 0){
                        this.meal_plan[meal].lunch.serves = 0;
                    }
                    if (this.meal_plan[meal].dinner.recipe == '' || this.meal_plan[meal].dinner.serves <= 0){
                        this.meal_plan[meal].dinner.serves = 0;
                    }
                }
            localStorage.setItem('meal_plan', JSON.stringify(this.meal_plan))
            },
            deep: true,
            },
  },

  computed: {

  },

  methods: {
            mealPlanChange: function(){
                console.log("mealPlanChange")
                this.meal_plan_summary_builder()
                eventBus.$emit('meal_plan_summary_change', this.meal_plan_summary);
                localStorage.setItem('meal_plan', JSON.stringify(this.meal_plan)) 
            },
            meal_plan_summary_builder: function(){
                let component = this;
                let meals = [];
                let new_meal_plan = component.meal_plan;
                let keys = Object.keys(new_meal_plan)
                let times = ['lunch','dinner'];
                for (var key in keys){
                    for (var time in times){                   
                        if (new_meal_plan[keys[key]][times[time]].recipe != ''){
                            let meal = new_meal_plan[keys[key]][times[time]].recipe;
                            let serves = new_meal_plan[keys[key]][times[time]].serves;
                            if (meals.findIndex(x => x.recipe == meal) == -1){
                                console.log("There's no " + meal + " already in the meal plan")
                                meals.push({
                                    recipe: meal,
                                    serves: serves
                                })
                            } else {
                                console.log(meal + " is already in the list. Just updating the serve")
                                let index = meals.findIndex(x => x.recipe == meal);
                                meals[index].serves = meals[index].serves + serves;
                            }
                            }
                    }
                }      
           component.meal_plan_summary = meals;      
           //return component.meal_plan_summary  
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
    this.meal_plan_summary_builder()
    eventBus.$emit('meal_plan_summary_change', this.meal_plan_summary);
      }

} 
}      


</script>


<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>

</style>