<template>
  <div id="app">
    <div class="header">
      <h2>Infinity Scroll</h2>
      <h2>Random User</h2>
    </div>
    <div id="user" class="list">
      <div class="list__background" v-show="loading">&nbsp;</div>
      <transition name="fade">
        <div class="loading" v-show="loading">
          <img src="./assets/loading.gif" alt="" class="loading--img">
          <h2 class="loading--text">Loading ...</h2>
        </div>
      </transition>
      <div class="user" v-for="(userData, index) in user" :key="index">
        <div class="user__left">
          <img :src="userData.picture.large" class="user--img">
        </div>
        <div class="user__right">
          <div class="user--name">
            {{ userData.name.first }} {{ userData.name.last }}
          </div>
          <div class="user--date">
            Registration date: {{ userData.registered.date | moment("DD MMMM YYYY") }}
          </div>
        </div>
      </div>
    </div>
    <div class="footer" v-if="isFooter">
      <p>{{ info }}</p>
    </div>
  </div>
</template>

<script>
import axios from 'axios';

export default {
  name: 'app',
  data () {
    return {
      user: [],
      trigger: 300,
      loading: false,
      isFooter: false,
      info: '',
    }
  },
  methods: {
    getUser() {
      this.loading = true; // display loading
      setTimeout(()=> {
        for (var i=0; i <5; i++) { //loop 5 data
          axios.get(`https://randomuser.me/api/?results=5`) //pull data from API
            .then(response => {
              this.user.push(response.data.results[0]); // push data to array
            })
            .catch(error => {
              this.info = "No Internet Connection";
              this.isFooter = true;
            }); // send error message
        }
        setTimeout(() => {
          this.loading = false; //close loading
        },3000)
      }, 2000);
    },
  },
  mounted() {
    this.getUser(); //initialize load data
    const eluser = document.querySelector('#user');
    //function to detect scrolled to bottom
    eluser.addEventListener('scroll', e => {
      let bottomOfWindow = (Math.ceil(eluser.scrollTop + eluser.clientHeight)) >= (eluser.scrollHeight);
      if(bottomOfWindow && this.user.length <=15) {
        this.getUser(); // load 5 user when scroll to bottom
      }
      if (this.user.length >=20) {
        this.info = `No more than ${this.user.length}  user to display`;
        this.isFooter = true; // display alert when data exceeds the limit 20
      }
    });
  },
}
</script>

<style lang="scss">
#app {
  position: relative;
  font-family: 'Open Sans', sans-serif;
  .header{
    display: flex;
    justify-content: space-between;
    background: #ff7701;
    margin-bottom: 20px;
    padding: 0 10px;
    border-radius: 5px;
    color: white;
  }
  .list {
    overflow: auto;
    height: 70vh;
    border: 2px solid #dce4ec;
    border-radius: 5px;
    padding: 10px;

    &__background {
      width: 100%;
      height: 100%;
      position: fixed;
      right: 0;
      top: 0;
      background: red;
      opacity: 0;
    }

    .loading {
      text-align: center;
      position: absolute;
      display: flex;
      align-items: center;
      top: 50%;
      left: 50%;
      transform: translate(-50%, -50%);
      color: #fff;
      z-index: 9999;

      &--img {
        height: 100px;
      }

      &--text {
        color: black;
      }
    }

    .user {
      display: flex;
      align-items: center;
      background: #4C73BE;
      max-height: 120px;
      margin: 10px 0;
      padding: 10px;

      &__left {
        flex: 3;
      }

      &__right {
        flex: 1;
      }

      &--img {
        height: 120px;
        margin-top: 5px;
      }

      &--name, &--date {
        margin: 10px;
        color: white;
      }
    }
  }

  .footer {
    margin-top: 20px;
    display: flex;
    background: #d33434;
    color: white;
    justify-content: center;
    font-size: 25px;
  }
}

.fade-enter-active, .fade-leave-active {
  transition: opacity .5s
}
.fade-enter, .fade-leave-to {
  opacity: 0
}
</style>
