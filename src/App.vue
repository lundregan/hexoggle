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
              <li class='level' v-for='level in world.levels' :key='level.id' v-bind:class='{ levelComplete : level.completed, levelNotComplete : !level.completed }'> 
                <p class='level-title' v-on:click='changeLevel(level);'> {{ level.name }} </p> 
                </li>
            </ul>
          </li>
        </ul>
      </aside>

      <!-- SVG Viewbox -->
      <svg viewBox='0 0 1000 1000' class='svg-viewbox'>
        <svg v-for='row in hexagons' :key='row.id' class='svg-row'>
          <svg v-for='hexagon in row' :key='hexagon.id' v-bind:x='hexagon.x' v-bind:y='hexagon.y'>
            <polygon class='hexagon-svg' v-bind:points='hexagonData.points' v-bind:style="{ fill: hexagon.fill }" v-on:click='hexagonClicked(hexagon)'/>
            <circle v-if='hexagon.type == "neighbors"' cx="25" cy="75" r="20" stroke="red" fill="black" stroke-width="5"/>
          </svg>
        </svg>
      </svg>
    </div>
    
  </div>
</template>

<script>
  import level_1_0 from './levels/1-0.json';
  import level_1_1 from './levels/1-1.json';
  import level_1_2 from './levels/1-2.json';
  import level_2_1 from './levels/2-1.json';
  import level_2_2 from './levels/2-2.json';

export default {

  name: 'App',
  
  components: {
  },

  created: function(){
    this.gameState.currentLevel = this.worlds[0].levels[0]
  },

  methods: {

    // -- Game Methods --
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
        this.gameState.currentLevel.completed = true;
      }
    },

    changeLevel: function(level){
      this.loadLevelData(level.data);
      this.setCurrentLevel(level);
    },

    setCurrentLevel: function(level){
      this.gameState.currentLevel = level;
    },


    // -- Hexagon Methods --
    hexagonClicked: function(hex){
      this.changeColor(hex);

      if(hex.type == 'neighbors'){
        this.toggleNeighbors(hex.id);
      }

      this.checkWinCondition();
    },

    toggleHex: function(pos){
      let hexagon = this.hexagons[pos[0]][pos[1]];
      this.changeColor(hexagon);    
    },

    changeColor: function(hex){
      
      if( hex.fill == 'red'){
        hex.fill = 'green';  
      }else{
        hex.fill = 'red';
      }
    },

    getArrayPosition: function(id){
      let arrayPosition = [0, 0]
      
      for(let i = 0; i < this.hexagons.length; i++){
        for(let j = 0; j < this.hexagons[i].length; j++){
          if(this.hexagons[i][j].id == id){
            arrayPosition[0] = i;
            arrayPosition[1] = j;
          }
        }
      }
      
      return arrayPosition;
    },

    isValidHex: function(x, y){
      if(x < 0 || x > 4){
        return false;
      }
      if(y < 0 || y > 4){
        return false;
      }
      
      if(x == 0 && y > 2){
        return false;
      }
      if(x == 1 && y > 3){
        return false;
      }
      if(x == 3 && y > 3){
        return false;
      }
      if(x == 4 && y > 2){
        return false;
      }

      return true;
    },



    // -- Loading worlds, levels, and hexs --
    openWorldLevels: function(world){
      world.open = !world.open;
    },

    loadLevelData: function(json){
      // Default set for every hexagon , purple = no hex data
      for(let i = 0; i < this.hexagons.length; i++){
        for(let j = 0; j < this.hexagons[i].length; j++){
          this.hexagons[i][j].type = 'noHexData';
          this.hexagons[i][j].fill = 'purple';
        } 
      }

      // load each hex element
      json.forEach(item => { 
        this.loadHex(item.i, item.j, item.fill, item.type); 
      });
    },

    loadHex: function(i, j, fill, type){
      console.log(i + ' ' + j + ' ' + fill + ' ' + type);

      this.hexagons[i][j].fill = fill;
      this.hexagons[i][j].type = type;
    },



    // -- Hexagon type behaviours --
    toggleNeighbors: function(id){

      let upRight = this.getArrayPosition(id);
      upRight[0] -= 1;
      if(upRight[0] >= 2){
        upRight[1] += 1;
      }

      let upLeft = this.getArrayPosition(id);
      upLeft[0] -= 1;
      upLeft[1] -= 1;
      if(upLeft[0] >= 2){
        upLeft[1] += 1;
      }

      let left = this.getArrayPosition(id);
      left[1] -= 1;

      let right = this.getArrayPosition(id);
      right[1] += 1;

      let downRight = this.getArrayPosition(id);
      downRight[0] += 1;
      if(downRight[0] <= 2){
        downRight[1] += 1;
      }
      
      let downLeft = this.getArrayPosition(id);
      downLeft[0] += 1;
      downLeft[1] -= 1;
      if(downLeft[0] <= 2){
        downLeft[1] += 1;
      }

      // Check if hexs are valid, if so toggle them
      if(this.isValidHex(upRight[0], upRight[1])){
        this.toggleHex([upRight[0], upRight[1]]);
      }
      if(this.isValidHex(upLeft[0], upLeft[1])){
        this.toggleHex([upLeft[0], upLeft[1]]);
      }
      if(this.isValidHex(left[0], left[1])){
        this.toggleHex([left[0], left[1]]);
      }
      if(this.isValidHex(right[0], right[1])){
        this.toggleHex([right[0], right[1]]);
      }
      if(this.isValidHex(downRight[0], downRight[1])){
        this.toggleHex([downRight[0], downRight[1]]);
      }
      if(this.isValidHex(downLeft[0], downLeft[1])){
        this.toggleHex([downLeft[0], downLeft[1]]);
      }
    },
  },


  data: function() {
    return {
      gameState: {
        currentLevel: null
      },

      hexagonData: {
        points: '87,0 174,50 174,150 87,200 0,150 0,50 87,0'
      },
      
      hexagons: [
        [
          // 1st row
          {
            id: 0,
            fill: 'red',
            x: '200',
            y: '0',
            type: 'normal'
          },
          {
            id: 1,
            fill: 'red',
            x: '400',
            y: '0',
            type: 'normal'
          },
          {
            id: 2,
            fill: 'red',
            x: '600',
            y: '0',
            type: 'normal'
          }
        ],
        [
          // 2nd row
          {
            id: 3,
            fill: 'red',
            x: '100',
            y: '175',
            type: 'normal'
          },
          {
            id: 4,
            fill: 'red',
            x: '300',
            y: '175',
            type: 'normal'
          },
          {
            id: 5,
            fill: 'red',
            x: '500',
            y: '175',
            type: 'neighbors'
          },
          {
            id: 6,
            fill: 'red',
            x: '700',
            y: '175',
            type: 'normal'
          }
        ],
        [
          // 3rd row
          {
            id: 7,
            fill: 'red',
            x: '0',
            y: '350',
            type: 'normal'
          },
          {
            id: 8,
            fill: 'red',
            x: '200',
            y: '350',
            type: 'normal'
          },
          {
            
            id: 9,
            fill: 'red',
            x: '400',
            y: '350',
            type: 'normal'
          },
          {
            
            id: 10,
            fill: 'red',
            x: '600',
            y: '350',
            type: 'normal'
          },
          {
            
            id: 11,
            fill: 'red',
            x: '800',
            y: '350',
            type: 'normal'
          }
        ],
        [
          // 4th row
          {
            id: 12,
            fill: 'red',
            x: '100',
            y: '525',
            type: 'normal'
          },
          {
            id: 13,
            fill: 'red',
            x: '300',
            y: '525',
            type: 'normal'
          },
          {
            id: 14,
            fill: 'red',
            x: '500',
            y: '525',
            type: 'normal'
          },
          {
            
            id: 15,
            fill: 'red',
            x: '700',
            y: '525',
            type: 'normal'
          }
        ],
        [
          //  5th row
          {
            id: 16,
            fill: 'red',
            x: '200',
            y: '700',
            type: 'normal'
          },
          {
            id: 17,
            fill: 'red',
            x: '400',
            y: '700',
            type: 'normal'
          },
          {
            id: 18,
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
              name: '1-0',
              data: level_1_0,
              completed: true
            },
            {
              name: '1-1',
              data: level_1_1,
              completed: false
            },
            {
              name: '1-2',
              data: level_1_2,
              completed: false
            }

          ]
        },
        {
          name: 'World 2',
          open: false,
          levels: [
            {
              name: '2-1',
              data: level_2_1,
              completed: false
            },
            {
              name: '2-2',
              data: level_2_2,
              completed: false
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

/* Title header */
.title {
  margin-top: 0px;

  padding-bottom: 10px;

  font-size: 3rem;
  font-family: Roboto;
  font-style: normal;
  font-weight: bold;

  background-color: #121212;
  color:            #FF7A00;
}

/* Level Select Menu */
.level-nav {
  text-align: left;
  background-color: #121212;
  opacity: 0.7; 
  width: 10%;
  height: 100%;
}

.level-select-title {
  
  padding-top: 10px;
  padding-bottom: 25px;
  
  font-size: 2rem;
  text-align: center;

  color: #FFFFFF;
}

.level-nav-ul {
  list-style-type: none;
}

/* Worlds */
.world-list {
  padding-left: 0px;
}

.world {
  margin: 10px 0 10px 0;
  padding: 10px 0 10px 0;
  
  margin-left: none;


  text-align: center;
  font-size: 1.5rem;

  background-color: #3a3a3a;
  color: #ffffff;
}


/* Levels */
.level-list {
  padding-left: 0px;
}

.level {
  margin: 10px 0 10px 0;
}

.levelComplete {
  background-color: green;
}

.levelNotComplete {
  background-color: #FA4E5D;
}


/* SVG */
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
