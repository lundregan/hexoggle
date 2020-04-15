<template>
  <div id="app">

    <div class='title-section'>
      <h1 class='title'>Hexoggle</h1>
    </div>

    <svg viewBox='0 0 1000 1000' class='svg-viewbox'>
      <!-- 1st row -->
      <svg x='200' y='0' v-on:click='changeColor(hexagons[0][0])'>
        <polygon class='hexagon-svg' points="87,0 174,50 174,150 87,200 0,150 0,50 87,0" v-bind:style="{ fill: hexagons[0][0].fill }" v-on:click='changeColor(0, 0)' />
      </svg>
      <svg x='400' y='0'>
         <polygon class='hexagon-svg' points="87,0 174,50 174,150 87,200 0,150 0,50 87,0" v-bind:style="{ fill: hexagons[0][1].fill }" v-on:click='changeColor(0, 1)' />
      </svg>
      <svg x='600' y='0'>
         <polygon class='hexagon-svg' points="87,0 174,50 174,150 87,200 0,150 0,50 87,0" v-bind:style="{ fill: hexagons[0][2].fill }" v-on:click='changeColor(0, 2)' />
      </svg>

      <!-- 2nd row -->
      <svg x='100' y='175'>
         <polygon class='hexagon-svg' points="87,0 174,50 174,150 87,200 0,150 0,50 87,0" v-bind:style="{ fill: hexagons[1][0].fill }" v-on:click='changeColor(1, 0)' />
      </svg>
      <svg x='300' y='175'>
         <polygon class='hexagon-svg' points="87,0 174,50 174,150 87,200 0,150 0,50 87,0" v-bind:style="{ fill: hexagons[1][1].fill }" v-on:click='changeColor(1, 1)' />
      </svg>
      <svg x='500' y='175'>
         <polygon class='hexagon-svg' points="87,0 174,50 174,150 87,200 0,150 0,50 87,0" v-bind:style="{ fill: hexagons[1][2].fill }" v-on:click='changeColor(1, 2)' />
      </svg>
      <svg x='700' y='175'>
         <polygon class='hexagon-svg' points="87,0 174,50 174,150 87,200 0,150 0,50 87,0" v-bind:style="{ fill: hexagons[1][3].fill }" v-on:click='changeColor(1, 3)' />
      </svg>

      <!-- 3rd row -->
      <svg x='0' y='350'>
         <polygon class='hexagon-svg' points="87,0 174,50 174,150 87,200 0,150 0,50 87,0" v-bind:style="{ fill: hexagons[2][0].fill }" v-on:click='changeColor(2, 0)' />
      </svg>
      <svg x='200' y='350'>
         <polygon class='hexagon-svg' points="87,0 174,50 174,150 87,200 0,150 0,50 87,0" v-bind:style="{ fill: hexagons[2][1].fill }" v-on:click='changeColor(2, 1)' />
      </svg>
      <svg x='400' y='350'>
         <polygon class='hexagon-svg' points="87,0 174,50 174,150 87,200 0,150 0,50 87,0" v-bind:style="{ fill: hexagons[2][2].fill }" v-on:click='changeColor(2, 2)' />
      </svg>
      <svg x='600' y='350'>
         <polygon class='hexagon-svg' points="87,0 174,50 174,150 87,200 0,150 0,50 87,0" v-bind:style="{ fill: hexagons[2][3].fill }" v-on:click='changeColor(2, 3)' />
      </svg>
      <svg x='800' y='350'>
         <polygon class='hexagon-svg' points="87,0 174,50 174,150 87,200 0,150 0,50 87,0" v-bind:style="{ fill: hexagons[2][4].fill }" v-on:click='changeColor(2, 4)' />
      </svg>

      <!-- 4th row -->
      <svg x='100' y='525'>
         <polygon class='hexagon-svg' points="87,0 174,50 174,150 87,200 0,150 0,50 87,0" v-bind:style="{ fill: hexagons[3][0].fill }" v-on:click='changeColor(3, 0)' />
      </svg>
      <svg x='300' y='525'>
         <polygon class='hexagon-svg' points="87,0 174,50 174,150 87,200 0,150 0,50 87,0" v-bind:style="{ fill: hexagons[3][1].fill }" v-on:click='changeColor(3, 1)' />
      </svg>
      <svg x='500' y='525'>
         <polygon class='hexagon-svg' points="87,0 174,50 174,150 87,200 0,150 0,50 87,0" v-bind:style="{ fill: hexagons[3][2].fill }" v-on:click='changeColor(3, 2)' />
      </svg>
      <svg x='700' y='525'>
         <polygon class='hexagon-svg' points="87,0 174,50 174,150 87,200 0,150 0,50 87,0" v-bind:style="{ fill: hexagons[3][3].fill }" v-on:click='changeColor(3, 3)' />
      </svg>

      <!-- 5th row -->
      <svg x='200' y='700'>
        <polygon class='hexagon-svg' points="87,0 174,50 174,150 87,200 0,150 0,50 87,0" v-bind:style="{ fill: hexagons[4][0].fill }" v-on:click='changeColor(4, 0)' />
      </svg>
      <svg x='400' y='700'>
         <polygon class='hexagon-svg' points="87,0 174,50 174,150 87,200 0,150 0,50 87,0" v-bind:style="{ fill: hexagons[4][1].fill }" v-on:click='changeColor(4, 1)' />
      </svg>
      <svg x='600' y='700'>
         <polygon class='hexagon-svg' points="87,0 174,50 174,150 87,200 0,150 0,50 87,0" v-bind:style="{ fill: hexagons[4][2].fill }" v-on:click='changeColor(4, 2)' />
      </svg>

    </svg>
  </div>
</template>

<script>

export default {
  name: 'App',
  
  components: {
  },

  methods: {
    changeColor: function(i, j){

      if( this.hexagons[i][j].fill == 'red'){
        this.hexagons[i][j].fill = 'green';  
      }else{
        this.hexagons[i][j].fill = 'red';
      }
      

      this.checkWinCondition();
    },

    checkWinCondition: function(){
      let win = true;
      
      for(let i =0; i < this.hexagons.length; i++){
        for(let j = 0; j < this.hexagons[i].length; j++){
          if(this.hexagons[i][j].fill == 'red'){
            win = false;
          }
        }
      }

      if(win == true){
        alert('Hexoggle');
      }
    }
  },

  data: function() {
    return {
    
    hexagons: [
      [
        {
          fill: 'green'
        },
        {
          fill: 'green'
        },
        {
          fill: 'red'
        }
      ],
      [
        {
          fill: 'purple'
        },
        {
          fill: 'red'
        },
        {
          fill: 'red'
        },
        {
          fill: 'red'
        }
      ],
      [
        {
          fill: 'purple'
        },
        {
          fill: 'red'
        },
        {
          fill: 'red'
        },
        {
          fill: 'red'
        },
        {
          fill: 'green'
        }
      ],
      [
        {
          fill: 'purple'
        },
        {
          fill: 'red'
        },
        {
          fill: 'red'
        },
        {
          fill: 'red'
        }
      ],
      [
        {
          fill: 'green'
        },
        {
          fill: 'green'
        },
        {
          fill: 'red'
        }
      ]
    ]
    
    };
  }
}
</script>

<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
}

* {
  margin: 0;
}

#app {
  width: 100vw;
  height: 100vh;
}

.title {
  margin-top: 0px;

  padding-bottom: 10px;

  font-size: 3rem;

  background-color: #2e2e2e;
  color:            #c0c0c0;
}

.svg-viewbox {
  width: 70vw;
  height: 70vh;

  position: absolute;
  top: 50%;
  left: 50%;
  -ms-transform: translate(-50%, -50%);
  transform: translate(-50%, -50%);
}


.hexagon-svg {
  fill:grey;
  stroke-width:1;
}
</style>
