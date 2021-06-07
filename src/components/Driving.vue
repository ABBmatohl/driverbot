<template>
<div>
  <div class="slidecontainer">
    <input v-model="speed" type="range" min="0" max="1023" speed="0" class="slider">
        
    <div id ="speed"><h1>Speed: {{Math.round(speed/10.24)}}%</h1></div>   
    <div id="direction"><h1>Direction: {{direction}}</h1></div>
    <div id="lastDirection"><h1>Last direction: {{lastDirection}}</h1></div>
  </div>
    <div class="btn-container">
        <div></div>
        <button class="btn" id="up" @click="up()"></button>
        <div></div>
        <button class="btn" id="left" @click="left()"><i class="fas fa-arrow-left"></i> </button>
        <button class="btn" id="pause" @click="pause()"></button>
        <button class="btn" id="right" @click="right()"><i class="fas fa-arrow-right"></i></button>

        <div></div>
        <button class="btn" id="down" @click="down()"></button>
    </div>
</div>
</template>

<script>
const mqtt = require("mqtt");



export default {
 data : function() {
     return {
         speed: 0,              //The cars speed
         dir:"f",               //Driving direction
         direction:"Forward",   //Direction which is logged
         lastDirection:"-"      //LastDirection which is logged
          
     }
 },
 watch: {
     speed: function() {      //Updates the speed whenever the slider is used
         this.client.publish(this.options.username + "/" + "drive", this.dir + this.speed)
        //else if(this.speed<0){this.client.publish(this.options.username +"/" + "drive", 'b' + Math.abs(this.speed))}
        //  this.Send('drive','f'+this.speed)
        console.log(this.speed)
     }
 },
 methods:{

    Connecting(connected) {     //Connecting to the website
      this.connected = connected;
      this.$store.dispatch("Connect", connected);
      // console.log(this.connected)
    },
    right(){ this.client.publish(this.options.username + "/" + "drive", "r90")    //Driving to the right
        this.lastDirection=this.direction
        this.direction="Right"
        console.log(this.options.username + "/" + "drive", "r90")
    },

     left() { this.client.publish(this.options.username + "/" + "drive", "l90")   //Driving to the left
        this.lastDirection=this.direction
        this.direction="Left"
        console.log( this.options.username + "/" + "drive", "l90")
     },

     pause() { this.client.publish(this.options.username + "/" + "drive", "f0")   //Pausing
        this.lastDirection=this.direction
        this.direction="Forward"
        this.speed=0
        console.log(this.options.username + "/" + "drive", "f0")
     },

     up() { 
        this.client.publish(this.options.username + "/" + "drive", "f" + this.speed)    //Forward driving
        this.lastDirection=this.direction
        this.dir="f"
        this.direction="Forward"
        console.log( this.options.username + "/" + "drive", "f" + this.speed)
     },

     down() { this.client.publish(this.options.username + "/" + "drive", "b" + this.speed)  //Backwards driving
        this.lastDirection=this.direction
        this.dir="b"
        this.direction="Backward"
        console.log(this.options.username + "/" + "drive", "b" + this.speed)
     },
     

    
  },
  
  mounted()  {    //Connecting+assigning id
      var ref = this;
      if (this.connected == true) {
        return "";
      }
      let User = this.$store.getters.GetUser;
      this.clientId =
        "DriverControll" +
        Math.random()
          .toString(16)
          .substr(2, 8);
      var mqtt_url = User.adress;
      var url = "mqtt://" + mqtt_url;
      var options = {
        port: User.port,
        clientId: this.clientId,
        username: User.name,
        password: User.password
      };
      this.options = options;
      // console.log("connecting");
      this.client = mqtt.connect(url, options);
      // console.log("connected?");

      this.client
        .on("connect", function() {
          // console.log("success");
          ref.Connecting(true);
        })
        .on("error", function() {
          // console.log("error");
          ref.Connecting(false);
        })
        .on("close", function() {
          ref.Connecting(false);
          // console.log("closing");
        });
  },
  created(){      //Driving with arrow keys
      addEventListener("keydown",(event)=>{
           if (event.keyCode == '38') {                // up arrow
            console.log("up")
            this.up()
            
        }
        else if (event.keyCode == '40') {           // down arrow
            console.log("down")
            this.down()
        }
        else if (event.keyCode == '37') {       //left arrow
            console.log("left")
            this.left()
        }
        else if (event.keyCode == '39') {       // right arrow
            console.log("right")
            this.right()
        }
        else if(event.keyCode == '32'){     //spacebar
          console.log("space")
          this.pause()
        }
      })
  }


}
</script>

<style>
.btn-container {
    position: absolute;
    left: 50%;
    right: 50%;
    top: 50%;
    width: auto;
    width: 100vw;
    transform: translate(-20.5%, -50%);

    display: grid;
    grid-template-columns: repeat(3, 15%);    
    grid-template-rows: repeat(3, 35%);   
  
}
.slidecontainer {
  width: 100%; /* Width of the outside container */

}

/* The slider itself */
.slider {
  -webkit-appearance: none;  /* Override default CSS styles */
  appearance: none;
  width: 100%; /* Full-width */
  height: 25px; /* Specified height */
  background: #d3d3d3; /* Grey background */
  outline: none; /* Remove outline */
  opacity: 0.7; /* Set transparency (for mouse-over effects on hover) */
  -webkit-transition: .2s; /* 0.2 seconds transition on hover */
  transition: opacity .2s;
  
}

/* Mouse-over effects */
.slider:hover {
  opacity: 1; /* Fully shown on mouse-over */

}

/* The slider handle (use -webkit- (Chrome, Opera, Safari, Edge) and -moz- (Firefox) to override default look) */
.slider::-webkit-slider-thumb {
  -webkit-appearance: none; /* Override default look */
  appearance: none;
  width: 25px; /* Set a specific slider handle width */
  height: 25px; /* Slider handle height */
  background: #04AA6D; /* Green background */
  cursor: pointer; /* Cursor on hover */
  
  
}

.slider::-moz-range-thumb {
  width: 25px; /* Set a specific slider handle width */
  height: 25px; /* Slider handle height */
  background: #04AA6D; /* Green background */
  cursor: pointer; /* Cursor on hover */
  
}
.btn {
  /* height: 100px;
  width: 100px;  */
  height: 11vw;
  width: 11vw;
  border-radius: 50%;
  border: 2px solid black;
  background-color:gray;
  overflow:hidden;
  
}
#right{
    background-image:url("https://cdn4.iconfinder.com/data/icons/arrows-249/24/small_chevron_arrow_right-512.png");
    background-size: cover;
    background-repeat: no-repeat;
    overflow:hidden;
}
#left{
    
    background-image:url("https://cdn4.iconfinder.com/data/icons/arrows-249/24/small_chevron_arrow_left-512.png");
    background-size: cover;
    background-repeat: no-repeat;
    overflow:hidden;
}
#pause{
    background-image:url("https://cdn4.iconfinder.com/data/icons/arrows-249/24/last_page_chevron-512.png");
    background-size: cover;
    background-repeat: no-repeat;
    overflow:hidden;
} 
#up{    
    background-image: url("https://cdn4.iconfinder.com/data/icons/arrows-249/24/small_chevron_arrow_up-512.png");
    background-size: cover;
    background-repeat: no-repeat;
    overflow:hidden;
}

#down{
    background-image: url("https://cdn4.iconfinder.com/data/icons/arrows-249/24/small_chevron_arrow_down-512.png");
    background-size:cover;
    background-repeat:no-repeat;
    overflow:hidden;
}
#direction{
    margin-top:7.5%;
    color:white;
    font-family: 'Odibee Sans', cursive;
    overflow:hidden;
}
#speed{
    margin-top:7.5%;
    color: white;
    font-family: 'Odibee Sans', cursive;
    overflow:hidden;
}
#lastDirection{
    margin-top:7.5%;
    color: white;
    font-family: 'Odibee Sans', cursive;
    overflow:hidden;
    
}

</style>