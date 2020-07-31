<template>
<div class="col-md-6 col-lg-6">
    <div class="modal fade" id="exampleModal" tabindex="-1" role="dialog" aria-labelledby="exampleModalLabel" aria-hidden="true">
  <div class="modal-dialog" role="document">
    <div class="modal-content">
      <div class="modal-header bg-color--primary text-light">
        <h5 class="modal-title" id="exampleModalLabel">Insert value in {{modal.categoryName}}</h5>
        <button type="button" class="close" v-on:click="closeModal" aria-label="Close">
          <span aria-hidden="true">&times;</span>
        </button>
      </div>
      <div class="modal-body d-flex justify-content-between">
        <input id="inputValue" type="number">
        <button id="strikeOutButton" class="btn btn-warning" v-on:click="addStrikeOutToRow">Strikeout</button>
      </div>
      <div class="modal-footer d-flex justify-content-start">
        <button type="button" class="btn btn-secondary" v-on:click="closeModal">Cancel</button>
        <button type="button" v-on:click="addScoreToRow" class="btn btn-primary">Save</button>
      </div>
    </div>
  </div>
</div>
     <div class="table-responsive-md">
                <table class="table table-bordered">
                    <thead>
                        <tr>
                            <th class="bg-color--primary text-light font-weight-normal" id="player" colspan="5">Category</th>
                            <th class="bg-color--primary text-light font-weight-normal" id="playerScore">Score</th>
                            <th class="bg-color--primary text-light font-weight-normal" id="playerPoints">Points</th>
                        </tr>
                    </thead>
                    <tbody>
                        <tr v-for="(value, parent_name) in categories" v-bind:key="parent_name">
                            <td class="font-weight-bold" v-bind:id="parent_name">{{parent_name}}</td>    
                            <td v-for="(value, name) in categories[parent_name]" 
                                :key="name" 
                                v-on:click="addScoreToCell" 
                                v-bind:id="name"
                                class="cell-width" 
                                v-bind:data-row="parent_name">{{value}}</td>
                            <td v-bind:id="parent_name + '-score'" class="cell-width bg-light js-row-score">0</td>
                            <td v-bind:id="parent_name + '-points'" class="cell-width bg-light js-row-points">0</td>
                        </tr>                                              
                        <tr class="bg-color--secondary-fade">
                            <td class="font-weight-bold" colspan="5">Total Score</td>
                            <td id="total-score" ></td>
                            <td id="total-points" ></td>
                        </tr>
                        <tr class="bg-color--secondary font-weight-bold">
                            <td colspan="6">Total Points</td>
                            <td id="final-points"></td>
                        </tr>
                    </tbody>
                </table>
        </div>
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
          },
          categoriesScore:{
              fours:0,
              fives:0,
              sixes:0,
              straight:0,
              fullhouse:0,
              choice:0,
              balut:0
          },
          categoryPoints:{
              fours:0,
              fives:0,
              sixes:0,
              straight:0,
              fullhouse:0,
              choice:0,
              balut:0,
              totalScore:0
          }   
      }      
  },
  created (){      
      if (localStorage.getItem(STORAGE_KEY) !== null) {
          this.categories = JSON.parse(localStorage.getItem(STORAGE_KEY))       
    }
  },
  mounted (){
    this.calculateLocalStorageRowScoreAndPoints();
    this.calculateTotalPoints();
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
        openModal: function(){
                let body = document.getElementsByTagName('body');
                body[0].classList.add('modal-open');
                
                let newDiv = document.createElement('div');
                newDiv.classList.add('modal-backdrop', 'fade','show');
                body[0].appendChild(newDiv);

                let modalPopup = document.getElementById('exampleModal');
                modalPopup.classList.add('show');
                modalPopup.style.display = 'block';
                modalPopup.removeAttribute('aria-hidden');

                document.getElementById('inputValue').focus();
                
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
                let inputValue = document.getElementById('inputValue');
                inputValue.value = "";
                inputValue.type = "number"
        },
        addScoreToRow: function(){
                let inputValue = document.getElementById('inputValue').value;
                let rowName = this.modal.categoryName;
                
                let category = this.$data.categories[rowName];
                category[this.modal.categoryPlace] = inputValue;

                this.calculateRowScore(category);
                this.calculateRowPoints(rowName);
                this.calculateTotalPoints();
                localStorage.setItem(STORAGE_KEY, JSON.stringify(this.categories));  
                         
                this.closeModal();     

        },
        addStrikeOutToRow: function(){
            let categoryName = this.modal.categoryName
            let category = this.$data.categories[categoryName];
            category[this.modal.categoryPlace] = "/";

            localStorage.setItem(STORAGE_KEY, JSON.stringify(this.categories));
            this.calculateRowScore(category);
            this.calculateRowPoints(categoryName);
            this.calculateTotalPoints();            
            this.closeModal();                          
        },
        calculateRowScore: function(category){
            let rowScore = 0;
            for (const [key, value] of Object.entries(category)) {
                let intValue = parseInt(value);
                //Check if value is equal to self, if true it's not NaN and we add it to the total
                //http://adripofjavascript.com/blog/drips/the-problem-with-testing-for-nan-in-javascript.html
                if(intValue === intValue){
                    rowScore = rowScore + intValue;
                }                
            };
            this.categoriesScore[this.modal.categoryName] = rowScore;
            let divName = this.modal.categoryName + "-score";
            document.getElementById(divName).innerHTML = rowScore;
            
            this.calculateTotalScore();
        },
        clearLocalStorage: function(){
            localStorage.removeItem(STORAGE_KEY);
        },
        calculateLocalStorageRowScoreAndPoints: function(){
            //Calculate the row score from the local storage data
            let categories = this.categories;
            Object.keys(categories).forEach(key => {
                this.modal.categoryName = key;
                this.calculateRowScore(categories[key]);
                this.calculateRowPoints(key);
            });           
        },
        calculateTotalScore: function(){
            let totalScore = 0;
            //Should use the values from this.categoryScore instead of fetching them from the html
            let allScores = document.querySelectorAll('.js-row-score');
            for (var i = 0; i < allScores.length; i++) {
                totalScore = totalScore + parseInt(allScores[i].innerHTML);
            }        
            document.getElementById("total-score").innerHTML = totalScore;
        },
        calculateRowPoints: function(rowName){
            let rowNameWithScore = rowName + '-score';
            let rowNameWithPoints = rowName + '-points';
            let minRowScore = 0;
            let strikeOutCheck;

            switch(rowName){
                case 'fours':
                    minRowScore = 52;
                    this.addRowPointsToHtml(rowNameWithScore, rowNameWithPoints, minRowScore, rowName);
                    break;  
                case 'fives':
                    minRowScore = 65;
                    this.addRowPointsToHtml(rowNameWithScore, rowNameWithPoints, minRowScore, rowName);
                    break;
                case 'sixes':
                    minRowScore = 78;
                    this.addRowPointsToHtml(rowNameWithScore, rowNameWithPoints, minRowScore, rowName);
                    break;
                case 'straight':
                    if(typeof Object.values(this.categories[rowName]).find(value => value === "") == 'undefined'){
                        strikeOutCheck = Object.values(this.categories[rowName]).find(value => value === "/");
                        if(typeof strikeOutCheck == 'undefined'){
                            document.getElementById(rowNameWithPoints).innerHTML = 4; 
                            this.categoryPoints[rowName] = 4;
                        }
                        else{
                            document.getElementById(rowNameWithPoints).innerHTML = 0;
                            this.categoryPoints[rowName] = 0;                         
                        } 
                    }                    
                    else{
                        document.getElementById(rowNameWithPoints).innerHTML = 0;
                        this.categoryPoints[rowName] = 0;                         
                    }                 
                    break;
                case 'fullhouse':
                    if(typeof Object.values(this.categories[rowName]).find(value => value === "") == 'undefined'){
                        strikeOutCheck = Object.values(this.categories[rowName]).find(value => value === "/");
                        if(typeof strikeOutCheck == 'undefined'){
                            document.getElementById(rowNameWithPoints).innerHTML = 3; 
                            this.categoryPoints[rowName] = 3;
                        }
                        else{
                            document.getElementById(rowNameWithPoints).innerHTML = 0;  
                            this.categoryPoints[rowName] = 0;                       
                        } 
                    }                    
                    else{
                        document.getElementById(rowNameWithPoints).innerHTML = 0;
                        this.categoryPoints[rowName] = 0;                         
                    }                 
                    break;
                case 'choice':
                    minRowScore = 100;
                    this.addRowPointsToHtml(rowNameWithScore, rowNameWithPoints, minRowScore, rowName);
                    break;
                case 'balut':
                    let balutTotalPoints = 0;
                    const balut = this.categories.balut;
                        Object.values(balut).forEach(value => {
                            let valueAsInt = parseInt(value);
                             //Check if value is equal to self, if true it's not NaN and we add it to the total
                            //http://adripofjavascript.com/blog/drips/the-problem-with-testing-for-nan-in-javascript.html
                            if(valueAsInt === valueAsInt){
                                balutTotalPoints = balutTotalPoints + 2;
                                //console.log('balut total points = ' + balutTotalPoints)
                            }
                        });
                    document.getElementById(rowNameWithPoints).innerHTML = balutTotalPoints; 
                    this.categoryPoints[rowName] = balutTotalPoints;                                               
                    break;   
            }
        },
        addRowPointsToHtml: function(rowNameWithScore, rowNameWithPoints, minRowScore, rowName){
             let rowScore = document.getElementById(rowNameWithScore).innerHTML;
                    //console.log('RowScore: ' + rowScore);
                    if(parseInt(rowScore) >= minRowScore){
                        document.getElementById(rowNameWithPoints).innerHTML = 2; 
                        this.categoryPoints[rowName] = 2;
                    }
                    else{
                        document.getElementById(rowNameWithPoints).innerHTML = 0;
                    };                    
        },
        calculateTotalPoints: function(){
            this.calculatePointsForTotalScore();
            let totalPoints = 0;
            const allPoints = this.categoryPoints;
            Object.values(allPoints).forEach(value => {
                totalPoints = totalPoints + value;
            });   
            document.getElementById('final-points').innerHTML = totalPoints;
        },
        calculatePointsForTotalScore: function(){
            const totalScore = parseInt(document.getElementById('total-score').innerHTML);

            switch(true){
                case (totalScore < 300): 
                    this.categoryPoints.totalScore = -2;
                    document.getElementById('total-points').innerHTML = -2;
                    break;
                case (totalScore >= 300 && totalScore < 350): 
                    this.categoryPoints.totalScore = -1;
                    document.getElementById('total-points').innerHTML = -1;
                    break;
                case (totalScore >= 350 && totalScore < 400): 
                    this.categoryPoints.totalScore = 0;
                    document.getElementById('total-points').innerHTML = 0;
                    break;
                case (totalScore >= 400 && totalScore < 450): 
                    this.categoryPoints.totalScore = 1;
                    document.getElementById('total-points').innerHTML = 1;
                    break;
                case (totalScore >= 450 && totalScore < 500): 
                    this.categoryPoints.totalScore = 2;
                    document.getElementById('total-points').innerHTML = 2;
                    break;
                case (totalScore >= 500 && totalScore < 550): 
                    this.categoryPoints.totalScore = 3;
                    document.getElementById('total-points').innerHTML = 3;
                    break;
                case (totalScore >= 550 && totalScore < 600): 
                    this.categoryPoints.totalScore = 4;
                    document.getElementById('total-points').innerHTML = 4;
                    break;
                case (totalScore >= 600 && totalScore < 650): 
                    this.categoryPoints.totalScore = 5;
                    document.getElementById('total-points').innerHTML = 5;
                    break;
                case (totalScore >= 650 && totalScore < 812): 
                    this.categoryPoints.totalScore = 6;
                    document.getElementById('total-points').innerHTML = 6;
                    break;                    
            }
        },
        addScoreToCell: function(event){
            let target = event.target;
            let rowName = target.getAttribute('data-row');
            let cellName = target.id;
            let inputValue = document.getElementById('inputValue');
            let cellValue = document.getElementById(cellName).innerHTML;

            if(cellValue === '/'){
                inputValue.type = 'text';
                inputValue.value = '/';
            }
            else{
                inputValue.value = cellValue;
            }

            // Highlight cell and add a small delay when opening modal. Transition for the modal is broken. Should be Bootstrap Fade
            //document.getElementById(cellName).classList.add('bg-lightblue');

            this.openModal();

            this.modal.categoryName = rowName;
            this.modal.categoryPlace = cellName;
        }
      },
     
  
  
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped lang="scss">
$primary: #2F8EBD;
$secondary:#28C9A8;
$secondary-fade: #79e7d0;

.cell-width{
    min-width: 50px;
}
td::first-letter{
    text-transform: capitalize;
}
.bg-lightblue{
    background:lightblue;
}
.bg-color--primary{
    background: $primary;
}
.bg-color--secondary{
    background: $secondary;
}
.bg-color--secondary-fade{
    background: $secondary-fade; 
}
</style>
