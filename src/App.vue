<template>
<img class="logo" src="./assets/logo_sharey_nisim.png" alt="kolel logo" />
    <img class="logo vue" src="./assets/logo_tzahi.png" alt="tzahi logo" />
  <nav>
    <!-- logo -->
    
    <router-link to="/">בית</router-link> |
    <router-link to="/about">הוראות שימוש</router-link> 
    <!-- <router-link to="/table">טבלה</router-link> -->
  </nav>
  <router-view/>

</template>

<style>
@import url('https://fonts.googleapis.com/css2?family=Suez+One&display=swap');

body{
  font-family: 'Suez One', serif;

}

#app {
  /* font-family: Avenir, Helvetica, Arial, sans-serif; */
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
}

nav {
  padding: 30px;
}

nav a {
  font-weight: bold;
  color: #2c3e50;
}

nav a.router-link-exact-active {
  color: #42b983;
}
:root{
  
  background-image: url("./assets/background.jpg");
  background-repeat: no-repeat;
  background-size: cover;
  background-position: center;
  background-attachment: fixed;
  background-color: #d6e4f1;
  margin: 0 auto;
}
.logo {
  height: 6em;
  padding: 1.5em;
  will-change: filter;
  transition: filter 300ms;
}
.logo:hover {
  filter: drop-shadow(0 0 2em #646cffaa);
}
.logo.vue:hover {
  filter: drop-shadow(0 0 2em #42b883aa);
}



</style>
