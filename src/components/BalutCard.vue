<template>
<div class="col-md-6 col-lg-6">
    <div class="modal fade" id="exampleModal" tabindex="-1" role="dialog" aria-labelledby="exampleModalLabel" aria-hidden="true">
  <div class="modal-dialog" role="document">
    <div class="modal-content">
      <div class="modal-header">
        <h5 class="modal-title" id="exampleModalLabel">Insert value</h5>
        <button type="button" class="close" v-on:click="closeModal" aria-label="Close">
          <span aria-hidden="true">&times;</span>
        </button>
      </div>
      <div class="modal-body">
        <input id="inputValue" type="number">
      </div>
      <div class="modal-footer">
        <button type="button" class="btn btn-secondary" v-on:click="closeModal">Close</button>
        <button type="button" v-on:click="addScoreToRow" class="btn btn-primary">Save</button>
      </div>
    </div>
  </div>
</div>
     <div class="table-responsive-md">
                <table class="table table-bordered">
                    <thead>
                        <tr>
                            <th id="player" colspan="5">You</th>
                            <th id="playerScore">Score</th>
                            <th id="playerPoints">Points</th>
                        </tr>
                    </thead>
                    <tbody>
                        <tr>
                            <td id="fours" v-on:click="openModal">Fours</td>    
                            <td  v-for="(value, name) in categories.fours" :key="name" v-bind:id="name" class="cell-width">{{value}}</td>
                            <td id="fours-score" class="cell-width js-row-score"></td>
                            <td id="fours-points" class="cell-width js-row-points"></td>
                        </tr>
                        <tr>
                            <td id="fives" v-on:click="openModal">Fives</td>
                            <td  v-for="(value, name) in categories.fives" :key="name" v-bind:id="name" class="cell-width">{{value}}</td>
                            <td id="fives-score" class="cell-width js-row-score"></td>
                            <td id="fives-points" class="cell-width js-row-points"></td>
                        </tr>
                        <tr>
                            <td id="sixes" v-on:click="openModal">Sixes</td>
                            <td  v-for="(value, name) in categories.sixes" :key="name" v-bind:id="name" class="cell-width">{{value}}</td>
                            <td id="sixes-score" class="cell-width js-row-score"></td>
                            <td id="sixes-points" class="cell-width js-row-points"></td>
                        </tr>
                        <tr>
                            <td id="straight" v-on:click="openModal">Straight</td>
                            <td  v-for="(value, name) in categories.straight" :key="name" v-bind:id="name" class="cell-width">{{value}}</td>
                            <td id="straight-score" class="cell-width js-row-score"></td>
                            <td id="straight-points" class="cell-width js-row-points"></td>
                        </tr>
                        <tr>
                            <td id="fullhouse" v-on:click="openModal">Full House</td>
                            <td  v-for="(value, name) in categories.fullhouse" :key="name" v-bind:id="name" class="cell-width">{{value}}</td>
                            <td id="fullhouse-score" class="cell-width js-row-score"></td>
                            <td id="fullhouse-points" class="cell-width js-row-points"></td>
                        </tr>
                        <tr>
                            <td id="choice" v-on:click="openModal">Choice</td>
                            <td  v-for="(value, name) in categories.choice" :key="name" v-bind:id="name" class="cell-width">{{value}}</td>
                            <td id="choice-score" class="cell-width js-row-score"></td>
                            <td id="choice-points" class="cell-width js-row-points"></td>
                        </tr>
                        <tr>
                            <td id="balut" v-on:click="openModal">Balut</td>
                            <td  v-for="(value, name) in categories.balut" :key="name" v-bind:id="name" class="cell-width">{{value}}</td>
                            <td id="balut-score" class="cell-width js-row-score"></td>
                            <td id="balut-points" class="cell-width js-row-points"></td>
                        </tr>
                        <tr>
                            <td class="scorebg1" colspan="5">Total Score</td>
                            <td id="total-score" class="scorebg1"></td>
                            <td id="total-points" class="scorebg1"></td>
                        </tr>
                        <tr>
                            <td class="scorebg1" colspan="5">Total Points</td>
                            <td id="final-points" class="scorebg1" colspan="2"></td>
                        </tr>
                    </tbody>
                </table>
        </div>
     <button id="strikeOutButton" class="btn btn-secondary" v-on:click="toggleStrikeOut">Enable Strikeout</button>
</div>
</template>

<script>
const STORAGE_KEY = 'categories-values';

export default {
  name: 'BalutCard',
  data: function(){
      return{
          categories:{
                fours:{
                    "fours-1":"",
                    "fours-2":"",
                    "fours-3":"",
                    "fours-4":""
                    },                       
                fives:{
                    "fives-1":"",
                    "fives-2":"",
                    "fives-3":"",
                    "fives-4":"",
                },
                sixes:{
                    "sixes-1":"",
                    "sixes-2":"",
                    "sixes-3":"",
                    "sixes-4":"",
                },
                straight:{
                    "straight-1":"",
                    "straight-2":"",
                    "straight-3":"",
                    "straight-4":"",
                },
                fullhouse:{
                    "fullhouse-1":"",
                    "fullhouse-2":"",
                    "fullhouse-3":"",
                    "fullhouse-4":"",
                },
                choice:{
                    "choice-1":"",
                    "choice-2":"",
                    "choice-3":"",
                    "choice-4":"",
                },
                balut:{
                    "balut-1":"",
                    "balut-2":"",
                    "balut-3":"",
                    "balut-4":"",
                }
          },
          modal:{
              categoryName: "",
              categoryPlace:"",
              strikeOut:"off"
          },
          categoriesScore:{
              fours:0,
              fives:0,
              sixes:0,
              straight:0,
              fullhouse:0,
              choice:0,
              balut:0
          }
        
      }      
  },
  created (){      
      if (localStorage.getItem(STORAGE_KEY) !== null) {
          this.categories = JSON.parse(localStorage.getItem(STORAGE_KEY))       
    }
  },
  mounted (){
    this.calculateLocalStorageRowScore();
  },
//   watch: {
//     // whenever question changes, this function will run
//     categories:{
//         handler: function (newQuestion, oldQuestion) {
//             console.log(newQuestion);
//             console.log(oldQuestion);
//             },
//         deep: true
//         }     
//     },
  methods: {
        openModal: function(event){
            let rowName = event.target.id;            
            this.addCategoryToModal(rowName);

            if(this.modal.strikeOut === "on"){
                let category = this.$data.categories[this.modal.categoryName];
                category[this.modal.categoryPlace] = "/";

                //strikeout button and setting
                this.modal.strikeOut = "off";                
                let strikeOutButton = document.getElementById('strikeOutButton');
                strikeOutButton.classList.remove('btn-warning');
                strikeOutButton.classList.add('btn-secondary');
                strikeOutButton.innerHTML = "Enable Strike-out";

                localStorage.setItem(STORAGE_KEY, JSON.stringify(this.categories));           
                
                return;
            }

                let body = document.getElementsByTagName('body');
                body[0].classList.add('modal-open');
                
                let newDiv = document.createElement('div');
                newDiv.classList.add('modal-backdrop', 'fade','show');
                body[0].appendChild(newDiv);

                let modalPopup = document.getElementById('exampleModal');
                modalPopup.classList.add('show');
                modalPopup.style.display = 'block';
                modalPopup.removeAttribute('aria-hidden');
                
        },
        closeModal: function(){
                let modalBackground = document.getElementsByClassName('modal-backdrop');
                modalBackground[0].parentNode.removeChild(modalBackground[0]);

                let body = document.getElementsByTagName('body');
                body[0].classList.remove('modal-open');

                let modalPopup = document.getElementById('exampleModal');
                modalPopup.classList.remove('show');
                modalPopup.style.display = 'none';
                modalPopup.setAttribute('aria-hidden', 'true');

                //Clean up category tags and input value
                this.modal.categoryName = "";
                this.modal.categoryPlace = "";
                document.getElementById('inputValue').value = "";
        },
        addCategoryToModal: function (rowName) {
                //find id and get values from the data
                var data = this.$data.categories[rowName];
                console.log(data);
                this.modal.categoryName = rowName;

                //loop through to figure out where to add the input value
                var cellName = Object.keys(data).find(key => data[key] === "");
                console.log(cellName);
                this.modal.categoryPlace = cellName;
        },
        addScoreToRow: function(){
            let inputValue = document.getElementById('inputValue').value;

            if(inputValue !== ""){
                console.log(inputValue);
                let category = this.$data.categories[this.modal.categoryName];
                category[this.modal.categoryPlace] = inputValue;

                this.calculateRowScore(category);

                this.closeModal();     
                localStorage.setItem(STORAGE_KEY, JSON.stringify(this.categories));           
            }
            else{
                console.log("Missing value");
            }
        },
        toggleStrikeOut: function(event){
            if(this.modal.strikeOut === "off"){
                this.modal.strikeOut = "on";
                let target = event.target;
                target.classList.remove('btn-secondary');
                target.classList.add('btn-warning');
                target.innerHTML = "Disable Strike-out"
            }
            else{
                this.modal.strikeOut = "off";                
                let target = event.target;
                target.classList.remove('btn-warning');
                target.classList.add('btn-secondary');
                target.innerHTML = "Enable Strike-out"                
            }
        },
        calculateRowScore: function(category){
            let rowScore = 0;
            for (const [key, value] of Object.entries(category)) {
                console.log(`${key}: ${value}`);
                let intValue = parseInt(value);
                //Check if value is equal to self, if true it's not NaN and we add it to the total
                //http://adripofjavascript.com/blog/drips/the-problem-with-testing-for-nan-in-javascript.html
                if(intValue === intValue){
                    rowScore = rowScore + intValue;
                }                
                console.log(rowScore);
            };
            this.categoriesScore[this.modal.categoryName] = rowScore;
            let divName = this.modal.categoryName + "-score";
            console.log(divName);
            document.getElementById(divName).innerHTML = rowScore;
            
            this.calculateTotalScore();
        },
        clearLocalStorage: function(){
            localStorage.removeItem(STORAGE_KEY);
        },
        calculateLocalStorageRowScore: function(){
            //Calcucate the row score from the local storage data
            let categories = this.categories;
            Object.keys(categories).forEach(key => {
                this.modal.categoryName = key;
                this.calculateRowScore(categories[key]);
                console.log(key);        // the name of the current key.
            });           
        },
        calculateTotalScore: function(){
            let totalScore = 0;
            let allScores = document.querySelectorAll('.js-row-score');
            for (var i = 0; i < allScores.length; i++) {
                totalScore = totalScore + parseInt(allScores[i].innerHTML);
            }        
            document.getElementById("total-score").innerHTML = totalScore;
        },
        toggleAddFunctionalityOnRow: function(){

        },
        calculateRowPoints: function(){

        },
        calculateTotalPoints: function(){

        }
      },
     
  
  
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped lang="scss">
.cell-width{
  min-width: 50px;
}
</style>
