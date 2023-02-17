<template lang="pug">
.wrapper
  img(v-bind:src="info.data.data.img_src" v-if="seen" draggable="false").image
  img(src="@/assets/loader.gif" v-else draggable="false").image
  .buttons_area
    button(@click="get_word" v-show="success") Конечно!
    button(@click="showModal" v-show="success") Я уже устал!
  div(v-show="success") Вы выиграли! Повторить?
  draggable( v-model="letters" @end="check").field
    .letter(v-for="el in letters") {{el}}
  .modal-backdrop(v-show="isModalVisible")
    .modal
        .btn-close(type="button" @click="closeModal") х
        table(style="width: 500px; height: 300px; border: 1px solid black")
          th(@click="sortByIndex") Номер 
            img(width="20px" v-show="sorted_index=='inc'" src="@/assets/inc.png")
            img(width="20px" v-show="sorted_index=='dec'" src="@/assets/dec.png")
          th(@click="sortByName") Слово
            img(width="20px" v-show="sorted_name=='inc'" src="@/assets/inc.png")
            img(width="20px" v-show="sorted_name=='dec'" src="@/assets/dec.png")
          th(@click="sortByTime") Время
            img(width="20px" v-show="sorted_time=='inc'" src="@/assets/inc.png")
            img(width="20px" v-show="sorted_time=='dec'" src="@/assets/dec.png")
          tr(v-for="(elem,index) in words" )
            th {{ elem.num }}
            th {{ elem.name }}
            th {{ elem.minutes }} минут  {{ elem.seconds }} секунд
</template>

<script>
  import draggable from "vuedraggable";
  import axios from 'axios';
  export default {
    components: {
      draggable,
    },
    data() {
      return {
        arr: "робототехника",
        enabled: true,
        letters: [],
        minutes: [],
        miliseconds: [],
        seconds: [],
        words: [],
        dragging: false,
        info: null,
        seen: false,
        isModalVisible: false,
        success: false,
        start: null,
        num: 1,
        sorted_index: 'dec',
        sorted_name: 'dec',
        sorted_time: 'dec',
      };
    },
    mounted(){
      this.get_word()
    },
    methods:{
      sortByName(){
        if(this.sorted_name == 'inc'){
        this.words.sort((a,b) => b.name.localeCompare(a.name));
        this.sorted_name = 'dec';
        }else if(this.sorted_name == 'dec'){
        this.words.sort((a,b) => a.name.localeCompare(b.name))
        this.sorted_name = 'inc';
        }
      },
      sortByIndex(){
        if(this.sorted_index == 'inc'){
        this.words.sort((a,b) => b.num - a.num);
        this.sorted_index = 'dec';
        }else if(this.sorted_index == 'dec'){
        this.words.sort((a,b) => a.num - b.num);
        this.sorted_index = 'inc';
        }
      },
      sortByTime(){
        if(this.sorted_time == 'inc'){
        this.words.sort((a,b) => b.miliseconds - a.miliseconds);
        this.sorted_time = 'dec';
        }else if(this.sorted_time == 'dec'){
        this.words.sort((a,b) => a.miliseconds - b.miliseconds);
        this.sorted_time = 'inc';
        }
      },
      showModal(){
        this.isModalVisible = true;
      },
      closeModal(){
        this.isModalVisible = false;
      },
      check(){
        let word = this.letters.join('');
        if(word == this.arr){
          this.words.push({num: this.num++, name: this.arr, minutes: Math.floor(((new Date() - this.start)/1000/60%60)), seconds: Math.floor(((new Date() - this.start)/1000%60)), miliseconds: new Date() - this.start});
          this.success = true;
          console.log("success");
        }
      },
      get_word(){
        this.seen = false;
        this.success = false;
        axios
          .get('https://apidir.pfdo.ru/v1/directory-program-activities/' + (Math.floor(Math.random() * (1368 -  2)) + 2))
          .then(response => (this.info = response));
        setTimeout(() => {
        if(this.info.data.result_code == "FLSCS" && this.words.includes(this.info.data.data.name.toLowerCase()) == false && this.info.data.data.status == 10){
          this.seen = true;
          this.arr = this.info.data.data.name.toLowerCase();
          this.letters = this.arr.split('').sort(() => Math.random() - 0.5);
          this.start = new Date();
          console.log(this.arr);
        }else{
          this.get_word();
        }
        }, 300);
      },
    },
  }
  </script>
<style scoped lang="sass">
.wrapper
  display: flex
  flex-direction: column
  align-items: center
.field
  width: 70vw
  min-height: 10vh
  display: flex
  justify-content: space-around
  text-align: center
  align-items: flex-end
.letter
  width: 50px
  height: 50px
  background: #D1F3EA
  color: #7B7796
  font-size: 3rem
  display: flex
  align-items: center
  justify-content: center
  text-transform: uppercase
  border: 1px solid #6B6693
.elem
  width:250px
  height: 250px
  margin: auto
  position: relative
  background: #000
  transition: .5s
.buttons
  margin-top: 35px
.image
  width: 300px
  height: 300px
  margin-bottom: 50px

.buttons_area
  width: 50%
  display: flex
  justify-content: space-around
  margin-bottom: 100px
.modal-backdrop
  position: fixed
  top: 0
  bottom: 0
  left: 0
  right: 0
  background-color: rgba(0, 0, 0, 0.3)
  display: flex
  justify-content: center
  align-items: center
.modal
  background: #FFFFFF
  box-shadow: 2px 2px 20px 1px
  overflow-x: auto
  display: flex
  flex-direction: column
.btn-close
  width: 20px
  position: relative
  top: 0
  right: 0
  border: none
  font-size: 20px
  padding: 10px
  cursor: pointer
  font-weight: bold
  color: #4AAE9B
  background: transparent
</style>
