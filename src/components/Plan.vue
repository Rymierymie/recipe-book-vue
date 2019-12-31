<template>
    <div class="componentDiv">
        <div id="menuDiv" v-show="this.menu.length !== 0">
            <h4>Menu
                <!-- <button @click="edit_menu()" class="customButton" id="editMenuButton">Edit menu</button> -->
                <img src="../assets/icons/edit.png" class="icon-big" id="editMenuButton" @click="edit_menu()"/>
                <!-- <button @click="clear_menu()" class="customButton display-toggle" id="clearMenuButton">Clear menu</button> -->
                <img src="../assets/icons/trash.png" @click="clear_menu()" class="icon-big display-toggle" id="clearMenuButton" />
            </h4>    
                <ul id="menuList"> 
                    <li v-for="(item, index) in menu" v-bind:key="index" class="menuListItem">
                        {{ item }}
                        <img src="../assets/icons/cancel.png" class="icon display-toggle menuDeleteIcon" id="menuDeleteIcon" @click="remove_from_menu(item)" />
                    </li>
                </ul>
                <div class="divider">
                </div>
        </div>

        <h4 id="planHeadline">Plan
        <img src="../assets/icons/reset.png" @click="reset_planner()" id="resetPlanIcon" class="icon-big"/>
        </h4>
        <div v-for="(value, name, index) in meal_plan" v-bind:key="index">
            <h2 id="dayHeadline">{{ name }}
            <button @click="show_day(name, 'Lunch')" id="showLunchButton" class="addMealButton customButton">Add lunch</button> 
            <button @click="show_day(name, 'Dinner')" id="showDinnerButton" class="addMealButton customButton">Add dinner</button>    
            </h2> 
            <div v-show="value.lunch.recipe !=''">
                <p class="menuTimeHeadline">Lunch</p> 
                <p class="menuMealItem">{{ value.lunch.recipe }}</p>
                <p v-show="value.lunch.serves != 0" class="menuServesAmount"><em>{{ value.lunch.serves }} serve<span v-show="value.lunch.serves > 1">s</span></em></p>
            </div>
            <div v-show="value.dinner.recipe !=''">
                <p class="menuTimeHeadline">Dinner</p> 
                <p class="menuMealItem">{{ value.dinner.recipe }}</p>
                <p v-show="value.dinner.serves != 0" class="menuServesAmount"><em>{{ value.dinner.serves }} serve<span v-show="value.dinner.serves > 1">s</span></em></p>
            </div>
            <div class="display-toggle selectorCard" :id="`${name}LunchSelectors`">
            <span>Lunch &nbsp;</span>
            <select v-model="value.lunch.recipe" v-on:change="mealPlanChange">
                <option value="" selected></option>
                <option v-for="(item, index) in menu" v-bind:key="index">
                    {{ item }}
                </option>
            </select>
            <p class="servesSetters" v-show="value.lunch.recipe != ''">
            <button class="plusMinusButton" @click="value.lunch.serves -= 1" v-on:click="mealPlanChange">-</button>
            {{ value.lunch.serves }}
            <button class="plusMinusButton" @click="value.lunch.serves += 1" v-on:click="mealPlanChange">+</button>
            </p>
            </div>
            <div class="display-toggle selectorCard" :id="`${name}DinnerSelectors`">
            <span>Dinner &nbsp;</span>
            <select v-model="value.dinner.recipe" v-on:change="mealPlanChange">
                <option value="" selected></option>
                <option v-for="(item, index) in menu" v-bind:key="index">
                    {{ item }}
                </option>
            </select>
            <p class="servesSetters" v-show="value.dinner.recipe != ''">
            <button class="plusMinusButton" @click="value.dinner.serves -= 1" v-on:click="mealPlanChange">-</button>
            {{ value.dinner.serves }}
            <button class="plusMinusButton" @click="value.dinner.serves += 1" v-on:click="mealPlanChange">+</button>
            </p>
            </div>
            <div class="divider dayDivider">
            </div>
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
                    if (this.meal_plan[meal].lunch.serves <= 0){
                        this.meal_plan[meal].lunch.serves = 0;

                    if (this.meal_plan[meal].lunch.recipe == ''){
                        this.meal_plan[meal].lunch.serves = 0;
                    }    

                    if (this.meal_plan[meal].dinner.recipe == ''){
                        this.meal_plan[meal].dinner.serves = 0;
                    }    

                        // may need to add this back: this.meal_plan[meal].dinner.recipe == '' || 
                    }
                    if (this.meal_plan[meal].dinner.serves <= 0){
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
        remove_from_menu(recipe){
            let mealsPlan = this.meal_plan;   
            for (let meal in mealsPlan) {
                console.log(mealsPlan[meal].lunch.recipe)
                    if (mealsPlan[meal].lunch.recipe == recipe){
                        mealsPlan[meal].lunch.serves = 0;
                        }

                    if (mealsPlan[meal].dinner.recipe == recipe){
                        mealsPlan[meal].dinner.serves = 0;
                    }    
            }
        console.log(mealsPlan);   
          let index = this.menu.findIndex(x => x == recipe);
          this.menu.splice(index, 1); 
          localStorage.setItem('menu', JSON.stringify(this.menu)) 
          eventBus.$emit('menu_edit', this.menu); 
        this.meal_plan = mealsPlan;
        this.meal_plan_summary_builder()
        eventBus.$emit('meal_plan_summary_change', this.meal_plan_summary);
              
          },
            clear_menu: function(){
                let list = document.getElementById("menuList");
                list.innerHTML = '';
                this.menu = [];
                localStorage.removeItem('menu');
                this.reset_planner();

            },
            edit_menu: function(){
                let elems = document.getElementsByClassName("menuDeleteIcon");
                console.log(elems);
                Array.from(elems).forEach(function (element) { 
                    console.log(element) 
                    element.classList.toggle("display-toggle");
                }); 
                document.getElementById("clearMenuButton").classList.toggle("display-toggle");
            },
            show_day: function(dayName, dayTime){
                console.log(dayName + dayTime);
                let id = dayName + dayTime + 'Selectors';
                let elem = document.getElementById(id);
                console.log(elem);
                elem.classList.toggle("display-toggle");
                /* this.meal_plan[dayName][dayTime].serves = 1
                this.meal_plan[dayName][dayTime].recipe = ; */
                let buttonId = 'show' + dayTime + 'Button'
                let button = document.getElementById(buttonId)
                console.log(button)
                //need to decide what to do with this button here. Do I want the add lunch/dinner button to disappear or change the text?

            },

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

ul {
    list-style-type:none;
    padding: 0px 0px;
    margin: 0px 0px;

}

#dayHeadline {
    margin-bottom: 0px;
}

.display-toggle {
    display: none;
}

.addMealButton {
    margin: 5px 5px;
}

#menuDeleteIcon {
    position: relative;
    left: 5px;
}

#editMenuButton {
    margin-right: 20px;
    position: relative;
    float: right;
    /* bottom: 5px; */
}

#menuDiv h4 {
    /* padding-top: 10px;
    padding-bottom: 20px; */
    margin-bottom: 10px;
    margin-top: 15px;
}

.menuListItem {
    padding: 5px 0px 5px 0px;
}

#menuList {
    padding-bottom: 15px;
}

.menuTimeHeadline {
    font-size: 0.8rem;
    margin: 0px 0px;
}

.menuMealItem {
    margin: 0px 0px;
}

.menuServesAmount {
    margin: 0px 0px 10px 0px;
}

.selectorCard {
    margin: 10px 0px;
    border: 1px solid rgba(0, 0, 0,0.1);
    padding: 5px 5px;
    border-radius: 5px;
}

.servesSetters {
    margin: 0px 0px;
}

.plusMinusButton {
    border-radius: 50px;
    height: 20px;
    width: 20px;
}

#planHeadline {
    margin-top: 15px;
    margin-bottom: 10px;
}

#resetPlanIcon {
    position: relative;
    float: right;
    margin-right: 20px;
}

#clearMenuButton {
    position: relative;
    float: right;
    margin-right: 20px;
}

.dayDivider {
    margin-top: 15px;
}


</style>