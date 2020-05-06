<template>
  <div id="app" class="h-full min-h-screen">
    <div class="text-center">
      <button class="text-center bg-teal-500 px-6 mr-3 py-2 rounded my-4 hover:bg-orange-300 hover:text-white inline-block" @click="azantime(true)">Click</button>
      <button class="text-center bg-teal-500 px-6 py-2 rounded my-4 hover:bg-orange-300 hover:text-white inline-block" @click="azantime(false)">Today</button>
      <p class="text-white font-bold">{{error}}</p>
      <hr>
      <div class="w-1/2 sm:w-1/4 p-4 text-center mx-auto flex justify-between flex-col">
        <h2 class="text-white">City</h2><input required type="text" class="w-full" placeholder="city" v-model="city">
        <br>
        <h2 class="text-white">Country</h2><input required type="text" placeholder="country" v-model="country">
        <br>
        <!-- month: <input required type="text" placeholder="month" v-model="month"> -->
         <div class="text-white">Month</div>
        <select v-model="month">
          <option value="">Select month</option>
          <option :value="number" v-for="(number,index) in no" :key="index" >{{number}}</option>
        </select>
        <br>
        <div class="text-white">Year</div>
         
        <select v-model="year">
          <option value="">Select Year</option>
          <option :value="number" v-for="(number,index) in yr" :key="index" >{{number}}</option>
        </select>
      </div>
      <br>
      <p v-if="loading">Loading----</p>
      <div class="flex flex-wrap p-4 justify-center" v-if="!loading">
        <div class="bg-white w-full sm:w-1/4 p-4 m-2"  v-for="(result, index) in today" :key=index>
        <div class="m-2">
          <div>Date:{{result.date.readable}}</div>
        <div>Hizri Date: {{result.date.hijri.date}}</div>
        <div>Azan time</div>
        <div class="flex flex-wrap">
          <div class="w/5 flex-grow border">
          <h2>fajr</h2>
          <p>{{result.timings.Fajr}}</p>
        </div>
          <div class="w/5 flex-grow border">
            <h2>Zuhr</h2>
            <p>{{result.timings.Dhuhr}}</p>
          </div>
          <div class="w/5 flex-grow border">
            <h2>Asr</h2>
            <p>{{result.timings.Asr}}</p>
          </div>
          <div class="w/5 flex-grow border">
            <h2>Maghrib</h2>
            <p>{{result.timings.Maghrib}}</p>
          </div>
          <div class="w/5 flex-grow border">
            <h2>Esha</h2>
            <p>{{result.timings.Isha}}</p>
          </div>
        </div>
        </div>
      </div>
      
      
    </div>
    </div>
  </div>
</template>

<script>
import axios from "axios";
import './assets/css/main.css'


  let val = []
  for (let i = 1; i <= 12; i++) {
    if(i<10){
      val.push(String(i).padStart(2, '0'))
    }else{
      val.push(String(i))
    }
  }

  let yr =[]
  let min = 2020
  let max = 2050
  for(let x=min; x<=max; x++){
    yr.push(String(x))
  }
  
export default {
  name: 'App',
  data:function(){
    return{
      yr:yr,
      no:val,
      date:null,
      today:'',
      country:'',
      city:'',
      month:'',
      year:'',
      error:'',
      test:'',
      show:false,
      loading:false
    }
  },
  methods:{
    azantime(cause){
      var that = this
      this.loading = true
      axios.get(`https://api.aladhan.com/v1/calendarByCity?city=${that.city}&country=${that.country}&method=2&month=${that.month}&year=${that.year}`)
      .then(function(res){
        that.loading = false
        that.date = res.data.data
        if(cause){
          that.today = res.data.data
          that.show = true
        }else{
          that.show = true
          that.dateBuilder()
          that.today = that.datefilter
        }
        // console.log(res.data.data[0].date.readable);
        // console.log(cause);
        // if(cause){
        //   console.log(cause);
        //   that.date = res.data.data
        //   that.today = res.data.data
        //   that.show = true
        //   that.dateBuilder()
        // }else{
        //   console.log(cause);
        //   //that.date = res.data.data
        //   that.date = this.datefilter
        // }
        
      })
      .catch(()=>{
        this.error =  'Please fill all the fields'
      })
    },
    dateBuilder(){
        let d = new Date();
        let months = ["January", "February", "March", "April", "May", "June", "July", "August", "September", "October", "November", "December"];
        //let days = ["Sunday", "Monday", "Tuesday", "Wednesday", "Thursday", "Friday", "Saturday"];
        //let day = days[d.getDay()];
        //let date = d.getDate();
        let md = (d.getDate() < 10 ? '0' : '') + d.getDate();
        let month = months[d.getMonth()];
        let year = d.getFullYear();
        this.test = `${md} ${month} ${year}`
        this.year = year;
      },
      todaytime(){
        this.dateBuilder()
        this.date = this.datefilter
      }
  },
  computed:{
    datefilter:function(){
      var that = this
      return this.date.filter(function(result){
        return result.date.readable == that.test
      })
      
    },
    ret(){
      return this.azantime
    }
    
  },
  created(){
    
  }
}
</script>

<style>
#app {
  background-image: url(./assets/mos.jpg);
  background-position: bottom;
  background-size: cover;

  
}
</style>
