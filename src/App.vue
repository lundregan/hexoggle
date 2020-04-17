<template>
  <div id="app">

    <div class='title-section'>
      <h1 class='title'>Hexoggle</h1>
    </div>
    <div id="game">

      <aside class='level-nav'>
        <p class='level-select-title'>Level Select</p>

        <ul class='world-list level-nav-ul'>
          <li class='world' v-for='world in worlds' :key='world.id'>
            <p class='world-title' v-on:click='openWorldLevels(world)'> {{world.name}} </p>

            <ul class='level-list level-nav-ul' v-if='world.open'>
              <li v-for='level in world.levels' :key='level.id'> {{ level.name }} </li>
            </ul>
          </li>
        </ul>
      </aside>

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

    openWorldLevels: function(world){
      world.open = !world.open;
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
      ],

      worlds: [
        {
          name: 'World 1',
          open: false,
          levels: [
            {
              name: '1-0'
            },
            {
              name: '1-1'
            },
            {
              name: '1-1'
            }

          ]
        },{
          name: 'World 2',
          open: false,
          levels: [
            {
              name: '2-1'
            },
            {
              name: '2-2'
            }
          ]
        }
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

.level-nav {
  text-align: left;
  background-color: darkgrey;
  width: 10%;
  height: 100%;
}

.level-select-title {
  
  padding-top: 10px;
  padding-bottom: 25px;
  
  font-size: 2rem;
  text-align: center;
  
  background-color: #3a3a3a;
  color: #FFFFFF;
}

.level-nav-ul {
  list-style-type: none;
}

.world-list {
  padding-left: 0px;
}

.world {
  padding: 10px 0 10px 0;
  
  margin-left: none;

  border-top: 2px solid black;

  text-align: center;
  font-size: 1.5rem;

  background-color: #3a3a3a;
  color: #ffffff;
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
