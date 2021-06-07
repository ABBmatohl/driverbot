<template>

  <div id="body">
    
    <driving> </driving>
   
  </div>

</template>

<script>
import Driving from "./components/Driving";


export default {
  components: {
    
    Driving,
 
  }
}
</script>

<style>
#body {
  background-image:url("https://c4.wallpaperflare.com/wallpaper/414/753/190/sport-dangerous-sport-f1-formula-one-wallpaper-preview.jpg") ;
  height: 100vh;
  width:100vw;
  background-size: cover;
  background-position: center;
  overflow:hidden!important;
  position:relative;
  overflow-x:hidden;
  overflow-y:hidden;
  margin:0px;
}
html{
  overflow:hidden!important;
   height:100%;
   width:100%;
   position:relative;
   margin:0px;
}

</style>
