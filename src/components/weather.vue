<template>
  <div class="widget">
    <div class="flex-container">
      <div class="top-icon">
        <img alt="sun" src="~@/assets/sun.svg">
      </div>
      <div class="temp-data" v-if="weatherObj[0]">
        <div class="temp-top">
          <div class="visibility">{{weatherObj[0]['visibility']}}</div>
          <div class="max">{{weatherObj[0]['celsius_min']}}</div>
          <div class="separator"></div>
          <div class="min">{{weatherObj[0]['celsius_max']}}</div>
        </div>
        <div class="celsius">{{weatherObj[0]['celsius']}}</div>
      </div>
      <div class="location-data" v-if="weatherObj[0]">
        <div class="top">{{weatherObj[0]['city']}}</div>
        <div class="bottom">{{dayWeek}}</div>
        <div class="bottom">{{weatherObj[0]['date']}}</div>
      </div>  
    </div>
    <div class="scrollmenu">
      <div v-for="(item, index) in weatherObj" v-bind:key="item.index">
        <div class="box">
          <div class="item-time">{{weatherObj[index]['hour']}}</div>
          <div class="item-icon"><img alt="sun" src="~@/assets/sun.svg"></div>
          <div class="item-temp">{{weatherObj[index]['celsius']}}</div>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  name: "Weather",
  data () {
    return {
      weatherObj: {},
      dayWeek: ""
    }
  },
  methods: {
    dayConverter (UNIX_timestamp) {
      var a = new Date(UNIX_timestamp * 1000);
      var months = ["January","February", "March","April","May","June","July","August","September","October","November","December"];
      var month = months[a.getMonth()];
      var date = a.getDate();
      var day = date + '. ' + month
      return day;
    },
    cleanData (data) {
      var d = new Date();
      var weekday = new Array(7);
      weekday[0] = "Sunday";
      weekday[1] = "Monday";
      weekday[2] = "Tuesday";
      weekday[3] = "Wednesday";
      weekday[4] = "Thursday";
      weekday[5] = "Friday";
      weekday[6] = "Saturday";
      this.dayWeek = weekday[d.getDay()]
      var celsius;
      var celsiusMax;
      var celsiusMin;
      var city;
      var date;
      var hour;
      var lastItem = data.list.slice(1).slice(-9);
      var visibility;
      var fetchedWeather = {};
      for (var i = 0; i < lastItem.length; i++) {
        city = data["city"]["name"];
        date = this.dayConverter(lastItem[i]["dt"]);
        hour = this.hourConverter(lastItem[i]["dt"]);
        visibility = lastItem[i]["weather"][0]["main"];
        celsius =  Math.round(lastItem[i]["main"]["temp"] -273.15);
        celsiusMax = Math.round(lastItem[i]["main"]["temp_max"] -273.15);
        celsiusMin = Math.round(lastItem[i]["main"]["temp_min"] -273.15);
        fetchedWeather[i] = {
                            "city": city,
                            "date": date,
                            "hour": hour,
                            "celsius" : celsius,
                            "celsius_max": celsiusMax,
                            "celsius_min": celsiusMin,
                            "visibility" : visibility
                            };
      }
      this.weatherObj = fetchedWeather;
    },
    getItems () {
      const url = "https://cors-anywhere.herokuapp.com" + 
                  "/http://samples.openweathermap.org/data/2.5/forecast?id=524901&appid=b6907d289e10d714a6e88b30761fae22";
      fetch(url)
        .then(response => response.json())
        .then(data => {
          this.cleanData(data)
        });    
    },
    hourConverter(UNIX_timestamp){
      var a = new Date(UNIX_timestamp * 1000);
      var hour = a.getHours() < 10 ? '0' + a.getHours() : a.getHours();
      var min = a.getMinutes() < 10 ? '0' + a.getMinutes() : a.getMinutes();
      var time = hour + ':' + min
      return time;
    }   
  },
  beforeMount: function(){
    this.getItems()
  }
}
</script>

<style scoped>
@import url(https://fonts.googleapis.com/css?family=Roboto:400,700&display=swap);

@media only screen and (max-width: 360px) {
  div.flex-container > .temp-data {
    margin: 10px 5px 0 0;
  }
  div.flex-container > .location-data {
    margin: 10px 0 0 5px;
  }
  div.flex-container > .top-icon {
    margin: 0 0 10px 0
  }
}

.bottom {
color:#FFF;
text-align:left;
font-size:1.15em;
font-weight:700;
}

.box > .item-time {
color:#8385a1;
margin-top:10px;
}

.box > .item-time,
.box > .item-temp,
.box > .item-icon {
display:block;
}

.celsius {
font-size:2.3em;
font-weight:700;
}

.celsius::after,
.item-temp::after,
.max::after,
.min::after {
content:"\00b0";
}

.item-temp {
font-weight:700;
font-size:1.4em;
}

.flex-container {
display:flex;
flex-wrap:nowrap;
background-color:#262a59;
justify-content:center;
padding:20px 0 0;
}

.flex-container > div {
background-color:#262a59;
width:150px;
text-align:center;
line-height:30px;
margin:10px;
}

.max, .min {
float:right;
}

.scrollmenu {
background-color:#262a59;
overflow:auto;
white-space:nowrap;
-webkit-overflow-scrolling:touch;
height:300px;
}

.scrollmenu div {
display:inline-block;
color:#FFF;
text-align:center;
text-decoration:none;
padding:14px;
}

.scrollmenu > div:nth-child(1) {
background:#51557A;
}

.separator::after {
content:"/";
float:right;
padding:0 5%;
}

.temp-data {
color:#fff;
}

.temp-top {
font-size:.8em;
font-weight:400;
color:#A8AABD;
margin:0 0 45px;
}

.top {
font-size:.8em;
color:#8386a0;
text-align:left;
margin:0 0 7px;
}

.visibility {
float:left;
}

.widget {
font-family:Roboto;
}

</style>
