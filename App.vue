<template>
  <div id="app">
     <div class="wrapper clearfix">
       <!--Props : parent to child-->
       <players 
       v-bind:isWinner = 'isWinner'
       v-bind:scorePlayer = 'scorePlayer'
       v-bind:activePlayer = 'activePlayer'
       v-bind:currentScore = 'currentScore'
       />

        <controls 
            v-bind:isPlaying = 'isPlaying'
            v-bind:finalScore = 'finalScore'
            v-on:handleChangeFinalScore = 'handleChangeFinalScore'
            v-on:handleNewGame = "handleNewGame"  
            v-on:handleRollDice = "handleRollDice"
            v-on:handleHoldScore = "handleHoldScore"
        />
        
        <dices 
          v-bind:dices = 'dices'
        />

        <popup-rule 
          v-on:handleConfirm = 'handleConfirm'
          v-bind:isOpenPopup = 'isOpenPopup'
        />
      </div>
  </div>
</template>

<script>
import Players from './components/Players.vue';
import Controls from './components/Controls.vue';
import Dices from './components/Dices.vue';
import PopupRule from './components/PopupRule.vue'
export default {
  name: 'app',
  data () {
    return {
      isPlaying: false,
      isOpenPopup: false,
      activePlayer : 0, // xac dinh xem ai la nguoi dang choi
      scorePlayer: [0,0],
      currentScore: 0,
      dices: [2,5],
      finalScore: 10
    }
  },
  components: {
    Players,
    Controls,
    Dices,
    PopupRule
  }, 
  computed : {
    isWinner () {
      let {scorePlayer,finalScore} = this;
      if (scorePlayer[0] >= finalScore || scorePlayer[1] >= finalScore) {
        // dung cuoc choi
        this.isPlaying = false;
        return true;
      } else { 
        return false;
      }
    }
  },
  methods: {
    handleChangeFinalScore(e){
      var number = parseInt(e.target.value)
      if (isNaN(number) ) {
        this.finalScore = ''
      } else {
        this.finalScore = number;
      }
     // console.log(parseInt(e.target.value))
    },
    handleHoldScore () {
      if (this.isPlaying == true) {
        //console.log('handleHoldScore App.vue')
        //activePlayer = 0 => nguoi choi 1
        //activePlayer = 1 => nguoi choi 2
        //scorePlayer = array
        //scorePlayer[0] = scorePlayer[activePlayer]        truy xuat vao diem cua player dang choi
        //scorePlayer[1] = scorePlayer[activePlayer]
        // cach 1: this.scorePlayer[this.activePlayer] = this.scorePlayer[this.activePlayer] + this.currentScore
        // cach 1: su dung qua nhieu tu khoa this ==> should use destructering in es6 to replace as a solution 
        let {scorePlayer, activePlayer, currentScore} = this;
        let scoreOld = scorePlayer[activePlayer];
        //cach 1: su dung clone data: (spread data) 
        //let cloneScorePlayer = [...scorePlayer];
        //cloneScorePlayer[activePlayer] = scoreOld + currentScore;
        //this.scorePlayer[activePlayer] = scoreOld + currentScore;
       // this.scorePlayer = cloneScorePlayer; // thay doi dia chi bo nho cua mang scorePLayer de co the phan ung data moi ra ngoai giao dien
        //console.log(cloneScorePlayer);
        //cach 2: su dung ham set
         this.$set(this.scorePlayer, activePlayer, scoreOld + currentScore)
        // doi luot choi
        if(!this.isWinner) {
        this.nextPlayer();
        }
      } else {
        alert ('please click button new score before clicking button hold score')
      }
    },
    nextPlayer () {
      //activePlayer = 1 ==> 0, 0==>1
      this.activePlayer = this.activePlayer === 0 ? 1:0;
      this.currentScore = 0;
    },
    handleRollDice () {
      console.log('handleRollDice App.vue')
      if (this.isPlaying) {
        // xoay xuc sac
        // Math.random(): 0 ==>1
        var dice1 = Math.floor(Math.random() * 6)  + 1;
        var dice2 = Math.floor(Math.random() *6) + 1;
        console.log(dice1,dice2)
        this.dices= [dice1, dice2];
        if (dice1 ==1 ||dice2 ==1) {
          // doi luot choi
          let activePlayer = this.activePlayer;
          setTimeout (function(){
            alert (`nguoi choi so ${activePlayer + 1} da quay trung o so 1`);
          },10) 
          
          this.nextPlayer() ;
          // reset currentScore
        } else {
          //cong don diem vao current score
          this.currentScore = this.currentScore + dice1 + dice2;
        }
      } else {
        // xay dung mot component tuong tu popup de show dua vao trang thai an hien 
        alert ('vui long click button new game to play roll dice');
      }
    },
    handleConfirm () {
      console.log('handleConfirm App.vue')
      this.isPlaying = true;
      this.isOpenPopup = false;
      this.activePlayer = 0;
      this.scorePlayer = [0,0];
      this.currentScore = 0;
      this.dices = [1,1];
    },
    handleNewGame () {
      console.log('handleNewGame App.vue')
      // hien thi popup show luat choi, thay doi data => su dung event up 
      this.isOpenPopup = true;
    }
  }
}
</script>

<style>
  /**********************************************
*** GENERAL
**********************************************/

* {
    margin: 0;
    padding: 0;
    box-sizing: border-box;
    
}

.clearfix::after {
    content: "";
    display: table;
    clear: both;
}

body {
    background-image: linear-gradient(rgba(62, 20, 20, 0.4), rgba(62, 20, 20, 0.4)), url('/public/assets/back.jpg');
    background-size: cover;
    background-position: center;
    font-family: 'Lato';
    font-weight: 300;
    position: relative;
    height: 100vh;
    color: #555;
}

.wrapper {
    width: 1000px;
    position: absolute;
    top: 50%;
    left: 50%;
    transform: translate(-50%, -50%);
    background-color: #fff;
    box-shadow: 0px 10px 50px rgba(0, 0, 0, 0.3);
    overflow: hidden;
}




/**********************************************
*** PLAYERS
**********************************************/

/**********************************************
*** Control
**********************************************/

/*===========================
**** dice *****
=======*/

</style>
