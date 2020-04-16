<template>
  <div id="app">

    <div class='title-section'>
      <h1 class='title'>Hexoggle</h1>
    </div>
    <div id="game">

      <!-- SVG Viewbox -->
      <svg viewBox='0 0 1000 1000' class='svg-viewbox'>
        

        <svg v-for='row in hexagons' :key='row.id' class='svg-row'>
          <svg v-for='hexagon in row' :key='hexagon.id' v-bind:x='hexagon.x' v-bind:y='hexagon.y'>
            <polygon class='hexagon-svg' v-bind:points='hexagonData.points' v-bind:style="{ fill: hexagon.fill }" v-on:click='hexagonClicked(hexagon)'/>
          </svg>
        </svg>
      </svg>
    </div>
    
  </div>
</template>

<script>

export default {
  name: 'App',
  
  components: {
  },

  methods: {
    hexagonClicked: function(hex){
      if(hex.type == 'normal'){
        this.changeColor(hex);
      }
    },

    changeColor: function(hex){
      
      if( hex.fill == 'red'){
        hex.fill = 'green';  
      }else{
        hex.fill = 'red';
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

    hexagonData: {
      points: '87,0 174,50 174,150 87,200 0,150 0,50 87,0'
    },
    
    hexagons: [
      [
        // 1st row
        {
          fill: 'red',
          x: '200',
          y: '0',
          type: 'normal'
        },
        {
          fill: 'red',
          x: '400',
          y: '0',
          type: 'normal'
        },
        {
          fill: 'red',
          x: '600',
          y: '0',
          type: 'normal'
        }
      ],
      [
        // 2nd row
        {
          fill: 'red',
          x: '100',
          y: '175',
          type: 'normal'
        },
        {
          fill: 'red',
          x: '300',
          y: '175',
          type: 'normal'
        },
        {
          fill: 'red',
          x: '500',
          y: '175',
          type: 'normal'
        },
        {
          fill: 'red',
          x: '700',
          y: '175',
          type: 'normal'
        }
      ],
      [
        // 3rd row
        {
          fill: 'red',
          x: '0',
          y: '350',
          type: 'normal'
        },
        {
          fill: 'red',
          x: '200',
          y: '350',
          type: 'normal'
        },
        {
          fill: 'red',
          x: '400',
          y: '350',
          type: 'normal'
        },
        {
          fill: 'red',
          x: '600',
          y: '350',
          type: 'normal'
        },
        {
          fill: 'red',
          x: '800',
          y: '350',
          type: 'normal'
        }
      ],
      [
        // 4th row
        {
          fill: 'red',
          x: '100',
          y: '525',
          type: 'normal'
        },
        {
          fill: 'red',
          x: '300',
          y: '525',
          type: 'normal'
        },
        {
          fill: 'red',
          x: '500',
          y: '525',
          type: 'normal'
        },
        {
          fill: 'red',
          x: '700',
          y: '525',
          type: 'normal'
        }
      ],
      [
        //  5th row
        {
          fill: 'red',
          x: '200',
          y: '700',
          type: 'normal'
        },
        {
          fill: 'red',
          x: '400',
          y: '700',
          type: 'normal'
        },
        {
          fill: 'red',
          x: '600',
          y: '700',
          type: 'normal'
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

#game{
  width: 100%;
  height: 100%;
  
  background-color: #474747;
}

.title {
  margin-top: 0px;

  padding-bottom: 10px;

  font-size: 3rem;

  background-color: #2e2e2e;
  color:            #dfdfdf;
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
