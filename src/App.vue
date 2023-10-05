<template>
  <div class="main">
    <NavigationView 
    v-on:add-city="toggleModal" 
    v-on:edit-city="toggleEdit" 
    :addCityActive="addCityActive" 
    :isDay="isDay" 
    :isNight="isNight"/>
    <ModalView v-if="modalOpen" v-on:close-modal="toggleModal" :APIkey="APIkey" />
    <router-view 
    :isDay="isDay" 
    :isNight="isNight"
    v-bind:cities="cities" 
    v-bind:edit="edit" 
    :APIkey="APIkey" 
    v-on:is-day="dayTime" 
    v-on:is-night="nightTime"
    v-on:resetDays="resetDays"/>
   
  </div>
</template>

<script>
import axios from "axios";
import db from "./firebase/firebaseinit";
import NavigationView from "./components/Navigation.vue";
import ModalView from "./components/Modal.vue";
export default {
  name:"App",
  components:{
    NavigationView,
    ModalView,
  },
  data(){
    return{
      isDay: "null",
      isNight: "null",
      APIkey: "",
      cities: [],
      modalOpen: null,
      edit: null,
      addCityActive: null,
    };
  },
  created(){
    this.getCityWeather();
    this.checkRoute();
  },
  methods:{
    getCityWeather(){
      let firebaseDB = db.collection('cities');

      firebaseDB.onSnapshot(snap => {
        snap.docChanges().forEach(async (doc) => {
          if (doc.type === "added" && !doc.doc.Nd) {
            try{
              const response = await axios.get(
                `https://api.openweathermap.org/data/2.5/weather?q=${doc.doc.data().city}&units=metric&APPID=${
                  this.APIkey
                }`
              );
              const data = response.data;
              firebaseDB
                  .doc(doc.doc.id)
                  .update({
                    currentWeather: data,
                })
              .then(() => {
                  this.cities.push(doc.doc.data());
             });
            } catch (err) {
              console.log(err);
            }
          } else if (doc.type === "added" && doc.doc.Nd) {
            this.cities.push(doc.doc.data());
          } else if (doc.type == 'removed') {
            this.cities = this.cities.filter(city => city.city !== doc.doc.data().city);
          }
        });
      });
    },
    toggleModal() {
      this.modalOpen = !this.modalOpen;
    },
    toggleEdit(){
      this.edit = !this.edit;
    },
    checkRoute() {
      if (this.$route.name === "AddCity") {
        this.addCityActive = true
      } else {
        this.addCityActive = false;
      }
    },
    dayTime () {
      this.isDay = !this.isDay
    },
    nightTime(){
      this.isNight = !this.isNight;
    },
    resetDays(){
      this.isDay = false;
      this.isNight = false;
    },
  },
  watch: {
    $route() {
      this.checkRoute();
    },
  },
};
</script>

<style lang="scss">
* {
  margin: 0;
  padding: 0;
  box-sizing: border-box;
  font-family: "Quicksand", sans-serif;
}

.main{
  max-width: 1024px;
  margin: 0 auto;
  height: 100vh;

  .container {
    padding: 0 20px;
  }
}

</style>
