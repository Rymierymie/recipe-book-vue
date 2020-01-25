<template>
    <div class="componentDiv">
        <div id="menuDiv" v-show="this.menu.length !== 0">
            <h3 class="pl10">Menu
                <!-- <button @click="edit_menu()" class="c-button" id="editMenuButton">Edit menu</button> -->
                <img src="../assets/icons/edit.png" class="icon-big" id="editMenuButton" @click="edit_menu()"/>
                <!-- <button @click="clear_menu()" class="c-button display-toggle" id="clearMenuButton">Clear menu</button> -->
                <img src="../assets/icons/trash.png" @click="clear_menu()" class="icon-big display-toggle" id="clearMenuButton" />
            </h3>    
                <div class="card">
                <ul id="menuList"> 
                    <li v-for="(item, index) in menu" v-bind:key="index" class="menuListItem">
                        {{ item }}
                        <img src="../assets/icons/cancel.png" class="icon display-toggle menuDeleteIcon" id="menuDeleteIcon" @click="remove_from_menu(item)" />
                    </li>
                </ul>
                
                </div>
        </div>

        <h3 class="pl10">Plan
        <img :src="reset_plan_icon_dark" @mouseover="reset_plan_hover = true" @mouseleave="reset_plan_hover = false" @click="reset_planner()" id="resetPlanIcon" class="icon-big"/>
        </h3>
        <div class="card" v-for="(value, name, index) in meal_plan" v-bind:key="index">
            <h2 id="dayHeadline">{{ name.charAt(0).toUpperCase() + name.slice(1) }}
            <button @click="show_day(name, 'Dinner')" id="showDinnerButton" class="addMealButton c-button pl10 pr10">Plan dinner</button> 
            <button @click="show_day(name, 'Lunch')" id="showLunchButton" class="addMealButton c-button pl10 pr10">Plan lunch</button>   
            </h2> 
            <div class="display-toggle" :id="`${name}LunchSelectors`">
            <span>Lunch &nbsp; <img src="../assets/icons/cancel.png" @click="show_day(name, 'Lunch')" class="icon closeSelectorCard" /> </span>
            <select class="mealSelecter" v-model="value.lunch.recipe" v-on:change="mealPlanChange">
                <option value="" selected></option>
                <option v-for="(item, index) in menu" v-bind:key="index">
                    {{ item }}
                </option>
            </select>
            <p class="servesSetters" v-show="value.lunch.recipe != ''">
            <button class="plusMinusButton" @click="value.lunch.serves -= 1" v-on:click="mealPlanChange"><img class="icon" src="../assets/icons/minus.png" /></button>
            <span class="selectorServes">{{ value.lunch.serves }}</span>
            <button class="plusMinusButton" @click="value.lunch.serves += 1" v-on:click="mealPlanChange"><img class="icon" src="../assets/icons/plus.png" /></button>
            </p>
            </div>
            <div class="display-toggle" :id="`${name}DinnerSelectors`">
            <span>Dinner &nbsp; <img src="../assets/icons/cancel.png" @click="show_day(name, 'Dinner')" class="icon closeSelectorCard" /> </span>
            <select class="mealSelecter" v-model="value.dinner.recipe" v-on:change="mealPlanChange">
                <option value="" selected></option>
                <option v-for="(item, index) in menu" v-bind:key="index">
                    {{ item }}
                </option>
            </select>
            <p class="servesSetters" v-show="value.dinner.recipe != ''">
            <button class="plusMinusButton" @click="value.dinner.serves -= 1" v-on:click="mealPlanChange"><img class="icon" src="../assets/icons/minus.png" /></button>
            <span class="selectorServes">{{ value.dinner.serves }}</span>
            <button class="plusMinusButton" @click="value.dinner.serves += 1" v-on:click="mealPlanChange"><img class="icon" src="../assets/icons/plus.png" /></button>
            </p>
            </div>
            <div v-show="value.lunch.recipe !=''">
                <p class="menuTimeHeadline">Lunch</p> 
                <p class="menuMealItem">{{ value.lunch.recipe }}</p>
                <p class="menuServesAmount">{{ value.lunch.serves }} serve<span v-show="value.lunch.serves !== 1">s</span></p>
                <button class="c-button" v-on:click="removePlannedMeal(name, 'lunch')" >Remove</button>
            </div>
            <div v-show="value.dinner.recipe !=''">
                <p class="menuTimeHeadline">Dinner</p> 
                <p class="menuMealItem">{{ value.dinner.recipe }}</p>
                <p class="menuServesAmount">{{ value.dinner.serves }} serve<span v-show="value.dinner.serves !== 1">s</span></p>
                <button class="c-button" v-on:click="removePlannedMeal(name, 'dinner')" >Remove</button>
            </div>
        </div>
        <div style="padding-bottom: 100px;"></div>
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
      reset_plan_icon: require('../assets/icons/reset.png'),
      reset_plan_icon_dark: require('../assets/icons/reset-dark.png'),
      reset_plan_hover: false,
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
      removePlannedMeal: function(day, meal){
          //let that = this;
          this.meal_plan[day][meal].serves = 0;
          this.meal_plan[day][meal].recipe = '';
          this.mealPlanChange()
          console.log(day);
          console.log('meal');
          console.log(meal);
          let capitalisedMeal = meal.charAt(0).toUpperCase() + meal.slice(1);
          console.log(capitalisedMeal);
          let selector = day + capitalisedMeal + "Selectors";
          console.log(selector);
          let elem = document.getElementById(selector);
          console.log("elem.classList");
          let classes = elem.classList;
/*           for (let c in classes){
              console.log(classes[c])
          } */
          classes = Array.from(classes);
          let index = classes.indexOf('display-toggle');
          console.log(index);
          if (index === -1){
              console.log("the element doesn't have the class 'display-toggle'");
              elem.classList.toggle("display-toggle");
          } else {
              console.log("the element already has the class 'display-toggle");
          }

          /* Array.from(classes).forEach(function (element) { 
                    console.log(element) 
                    if (element === 'display-toggle'){
                        console.log("display toggle is in this class list already, we don't want to toggle it!")
                    } else {
                        console.log("calling show_day func to toggle display")
                        elem.classList.toggle("display-toggle");
                    }
                    //element.classList.toggle("display-toggle");
                });  */
            //next pull the class list from this elem to see if display-toggle is present

          //Need to pull the display-toggle into this function from the @click on the element 
          //add in here an IF that checks for whether the element has a class of display-toggle
          //IF it doesn't then it needs one added to hide it
          //IF it does, then we don't want to add one (as the display-toggle is currently doing, becasue that will show the element incorrectly!) 

      },

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
                /* let buttonId = 'show' + dayTime + 'Button'
                let button = document.getElementById(buttonId)
                console.log(button) */
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
    margin-top: 0px;

}

.display-toggle {
    display: none;
}

.addMealButton {
    margin: 5px 5px;
    background-color: #ffffff;
    position: relative;
    top: 0px;
    float: right;

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
    font-weight: 200;
}

.menuListItem {
    padding: 5px 0px 5px 0px;
}

#menuList {
    /* padding-bottom: 15px; */
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

.mealSelecter {
    margin: 15px 0px;
    padding: 0px 10px;
}


.servesSetters {
    margin: 0px 0px 8px 0px;
    text-align: center;
}

.selectorServes {
    margin: 0px 10px;
}

.closeSelectorCard {
    position: relative;
    float: right;
    top: 5px;
    right: 5px;
}

.plusMinusButton {
    border-radius: 50px;
    height: 30px;
    width: 30px;
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



</style>