<template>
  <div id="app">
    <!-- title -->
    <h1 class="site-title">{{ title }}</h1>
    <span class="site-description">{{ currentDate }}</span>
    <!-- entry list -->
    <ul class="entry-list">
      <li v-for="entry in filteredEntries" :key="entry.id" class="entry-item">
        <span class="entry-daytime">{{ entry [0] }} Uhr, {{ entry[1].replaceAll("/", ".") }}</span>
        <h3 class="entry-title">{{ entry[2] }}</h3>
        <span class="entry-description">{{ entry[3] }}</span>
      </li>
    </ul>
    {{updateCurrentDate()}}
    <!-- footer -->
    <footer class="footer">
      <img class="img" src="./assets/STZH_SEB_Logo.png"  alt="Stadt Zürich Soziale Betriebe und Einrichtungen Logo">
      <img class="img" src="./assets/Opportunity.png" alt="Opportunutiy Logo">
      <img class="img" src="./assets/SAG_Logo_De.png"  alt="Stiftung Arbeitsgestaltung Logo">
    </footer>
  </div>
</template>

<script>
import axios from "axios"; //npm install --save axios

export default {
  name: 'App',
  data(){
    return {
        title: "Welcome to Opportunity",
        // date:  new Date().toISOString().slice(8,10)+"."+new Date().toISOString().slice(5,7)+"."+new Date().toISOString().slice(0,4)
        currentDate:"",
        gsheet_url:
          "https://sheets.googleapis.com/v4/spreadsheets/1qLZJwuNv3QmwGhSj1wZZbuXNOkDKN-Ha7fo0Ca_uVVU/values:batchGet?ranges=A1%3AE100&valueRenderOption=FORMATTED_VALUE&key=AIzaSyBesotaNgSaTUIhrSKjEaExdi-ksKInhoE", // redacted
        entries: [],
      };
    },
    computed: {
      // computed properties are like data properties, but with a method combined. it gets executed automatically, instead of calling a function exlicitly
      filteredEntries() {
        return [...this.entries].slice(1); // remove first of array
      }
    },
    methods: {
      getData() {
        axios.get(this.gsheet_url).then((response) => {
          this.entries = response.data.valueRanges[0].values;
          console.log(response);
        });
      },
      updateCurrentDate(){
        let today = new Date();
        const date = `${today.getDate()}.${today.getMonth()+1}.${today.getFullYear()}`
        this.currentDate = date;
      },
      refreshData(){
        this.getData();
        this.updateCurrentDate();
      },
    },
    mounted(){
      this.refreshData(); // get first initial data and then wait for the next update
      setInterval(() => {
        this.refreshData();
      },1800000) // wait 30 min for next update
    },
};
</script>

<style>
/* css styles go here */
@import url(https://fonts.googleapis.com/css2?family=Inter:wght@500;900&display=swap);
body {
 background:#e8eff4
}
#app {
 font-family:Inter,Helvetica,Arial,sans-serif;
 -webkit-font-smoothing:antialiased;
 -moz-osx-font-smoothing:grayscale;
 color:#323d4a;
 margin:60px
}
.site-title {
 font-size:62px;
 font-weight:900;
 margin:80px 0 20px 0
}
.site-description {
 font-size:62px;
 color:#9aa7b1;
 font-weight:500;
 margin:0
}
.entry-list {
 padding-left:0
}
.entry-item {
 padding:35px 40px;
 margin:40px 0;
 background:#0f05a0;
 font-size:28px;
 line-height:1.3;
 list-style:none
}
.entry-daytime {
 color:#eb5e00;
 font-weight:900
}
.entry-title {
 font-size:inherit;
 margin:0;
 font-weight:900
}
.entry-description,
.entry-title {
 color:#ffbfab
}
.footer {
 display:flex;
 justify-content:space-between;
 box-sizing:border-box;
 position:fixed;
 bottom:0;
 left:0;
 width:100%;
 padding:40px;
 background:#fff
}
.footer img {
 height:50px
}
</style>    