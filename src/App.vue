<template>
  <div id="app" v-on:keydown.left="key()">

    <div class='title-section'>
      <h1 class='title'>Hexoggle</h1>
    </div>
    <div id="game" style='display: flex;'>

      <aside class='level-nav'>
        <p class='level-select-title'>Level Select</p>

        <ul class='world-list level-nav-ul'>
          <li class='world' v-for='world in worlds' :key='world.id'>
            <p class='world-title' v-on:click='openWorldLevels(world)'> {{ world.name }} </p>

            <ul class='level-list level-nav-ul' v-if='world.open'>
              <li class='level' v-for='level in world.levels' :key='level.id' v-bind:class='{ levelComplete : level.completed, levelNotComplete : !level.completed }'> 
                <p class='level-title' v-on:click='changeLevel(level);'> {{ level.name }} </p> 
                </li>
            </ul>
          </li>
        </ul>
      </aside>

      <!-- Game Container -->
      <div class='game-container'>
        <div style='text-align: center;'>
          <span class="left"><p class='stat-level'> Level: {{ this.gameState.currentLevel.name }} </p></span>
          <p class='stat-completed' v-if='this.gameState.currentLevel.completed'> Personal Best - Moves: {{this.gameState.currentLevel.bestMoves}}</p>
          <span class="right"><p class='stat-moves'> Moves Made: {{ this.gameState.currentMoves }} </p></span>
        </div>
        
        <svg viewBox='0 0 1000 1000' class='svg-viewbox'>
          <svg v-for='row in hexagons' :key='row.id' class='svg-row'>
            <svg v-for='hexagon in row' :key='hexagon.id' v-bind:x='hexagon.x' v-bind:y='hexagon.y'>
              <polygon class='hexagon-svg' v-bind:points='hexagonData.points' v-bind:class="{ hexToggled : hexagon.toggled, hexNotToggled : !hexagon.toggled }" v-on:click='hexagonClicked(hexagon)' />
              <circle v-if='hexagon.type == "neighbors"' cx="86" cy="100" r="70" stroke="gray" fill="black" stroke-width="5" v-on:click='hexagonClicked(hexagon)' />
              <rect v-if='hexagon.type == "singleNeighbor"' width='20%' height='30%' />
            </svg>
          </svg>
        </svg>

        <div class='level-switcher' style='margin-left: 6px;'> 
          <button class='level-change-prev' v-on:click='prevLevel()'>Previous</button>
          <button class='level-change-reset' v-on:click='resetCurrentLevel()'>Reset</button>
          <button class='level-change-next' v-on:click='nextLevel()'>Next</button>
        </div>

      </div>
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
    this.changeLevel(this.worlds[0].levels[0]);
  },

  mounted: function(){
    if (localStorage.worlds) {
      this.worlds = JSON.parse(localStorage.worlds);
    }
  },

  methods: {
    // -- Key Binding --
    key: function(event) {
      alert('key ' + event.key + ' (' + event.keyCode + ')');
    },

    // -- Game Methods --
    checkWinCondition: function(){
      let win = true;
      
      for(let i =0; i < this.hexagons.length; i++){
        for(let j = 0; j < this.hexagons[i].length; j++){
          if(this.hexagons[i][j].toggled == false){
            win = false;
          }
        }
      }

      if(win == true){
        this.winCurrentLevel();
      }
    },

    winCurrentLevel: function(){
      this.gameState.currentLevel.completed = true;
        
      if(this.gameState.currentLevel.bestMoves == null){
        this.gameState.currentLevel.bestMoves = this.gameState.currentMoves;
      }else{
        if(this.gameState.currentMoves < this.gameState.currentLevel.bestMoves){
          this.gameState.currentLevel.bestMoves = this.gameState.currentMoves;
        }
      }
        
      alert('Level Complete');
      this.nextLevel();

      this.saveState();
    },

    saveState: function(){
      localStorage.worlds = JSON.stringify(this.worlds);
    },

    changeLevel: function(level){
      this.loadLevelData(level.data);
      this.setCurrentLevel(level);
    },

    setCurrentLevel: function(level){
      this.gameState.currentLevel = level;
    },

    resetCurrentLevel: function(){
      this.gameState.currentMoves = 0;

      this.loadLevelData(this.gameState.currentLevel.data);
    },

    resetCurrentMoves: function(){
      this.gameState.currentMoves = 0;
    },

    resetAllProgress: function(){
      localStorage.removeItem('worlds');
    },

    nextLevel: function(){
      let nextLevel = null;
      
      for(let x = 0; x < this.worlds.length; x++){
        for(let y = 0; y < this.worlds[x].levels.length - 1; y++){
          if(this.worlds[x].levels[y] == this.gameState.currentLevel){
            nextLevel = this.worlds[x].levels[y + 1];
          }
        }
      }

      if(nextLevel != null){
        this.changeLevel(nextLevel);
      }

      this.resetCurrentMoves();
    },

    prevLevel: function(){
      let prevLevel = null;

      for(let x = 0; x < this.worlds.length; x++){
        for(let y = 1; y < this.worlds[x].levels.length; y++){
          if(this.worlds[x].levels[y] == this.gameState.currentLevel){
            prevLevel = this.worlds[x].levels[y - 1];
          }
        }
      }

      if(prevLevel != null){
        this.changeLevel(prevLevel);
      }

      this.resetCurrentMoves();
    },


    // -- Hexagon Methods --
    hexagonClicked: function(hex){
      this.gameState.currentMoves += 1;

      this.executeHexagonAction(hex);

      this.checkWinCondition();
    },

    toggleHex: function(pos){
      let hexagon = this.hexagons[pos[0]][pos[1]];
      this.changeColor(hexagon);    
    },

    changeColor: function(hex){
      hex.toggled = !hex.toggled;
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

    getHexagonFromArrayPosition: function(i, j){
      return this.hexagons[i][j];
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
      // Default set for every hexagon , false toggled (red) and type of normal
      for(let i = 0; i < this.hexagons.length; i++){
        for(let j = 0; j < this.hexagons[i].length; j++){
          this.hexagons[i][j].type = 'normal';
          this.hexagons[i][j].toggled = false;
        } 
      }

      // load each hex element
      json.forEach(item => { 
        this.loadHex(item.i, item.j, item.toggled, item.type); 
      });
    },

    loadHex: function(i, j, toggled, type){
      this.hexagons[i][j].type = type;
      this.hexagons[i][j].toggled = toggled;
    },



    // -- Hexagon type behaviours --
    executeHexagonAction: function(hex){

      if(hex.type == 'normal'){
        this.changeColor(hex);
      }

      if(hex.type == 'neighbors'){
        this.toggleNeighbors(hex.id);
      }
    },
    
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

      let center = this.getArrayPosition(id);

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
      if(this.isValidHex(center[0], center[1])){
        this.toggleHex([center[0], center[1]]);
      }
    },
  },


  data: function() {
    return {
      test: 'NA',

      gameState: {
        currentLevel: null,
        currentMoves: 0
      },

      hexagonData: {
        points: '87,0 174,50 174,150 87,200 0,150 0,50 87,0'
      },
      
      hexagons: [
        [
          // 1st row
          {
            id: 0,
            x: '200',
            y: '0',
            type: 'normal',
            toggled: false
          },
          {
            id: 1,
            x: '400',
            y: '0',
            type: 'normal',
            toggled: false
          },
          {
            id: 2,
            x: '600',
            y: '0',
            type: 'normal',
            toggled: false
          }
        ],
        [
          // 2nd row
          {
            id: 3,
            x: '100',
            y: '175',
            type: 'normal',
            toggled: false
          },
          {
            id: 4,
            x: '300',
            y: '175',
            type: 'normal',
            toggled: false
          },
          {
            id: 5,
            x: '500',
            y: '175',
            type: 'neighbors',
            toggled: false
          },
          {
            id: 6,
            x: '700',
            y: '175',
            type: 'normal',
            toggled: false
          }
        ],
        [
          // 3rd row
          {
            id: 7,
            x: '0',
            y: '350',
            type: 'normal',
            toggled: false
          },
          {
            id: 8,
            x: '200',
            y: '350',
            type: 'normal',
            toggled: false
          },
          {
            
            id: 9,
            x: '400',
            y: '350',
            type: 'normal',
            toggled: false
          },
          {
            
            id: 10,
            x: '600',
            y: '350',
            type: 'normal',
            toggled: false
          },
          {
            
            id: 11,
            x: '800',
            y: '350',
            type: 'normal',
            toggled: false
          }
        ],
        [
          // 4th row
          {
            id: 12,
            x: '100',
            y: '525',
            type: 'normal',
            toggled: false
          },
          {
            id: 13,
            x: '300',
            y: '525',
            type: 'normal',
            toggled: false
          },
          {
            id: 14,
            x: '500',
            y: '525',
            type: 'normal',
            toggled: false
          },
          {
            
            id: 15,
            x: '700',
            y: '525',
            type: 'normal',
            toggled: false
          }
        ],
        [
          //  5th row
          {
            id: 16,
            x: '200',
            y: '700',
            type: 'normal',
            toggled: false
          },
          {
            id: 17,
            x: '400',
            y: '700',
            type: 'normal',
            toggled: false
          },
          {
            id: 18,
            x: '600',
            y: '700',
            type: 'normal',
            toggled: false
          }
        ]
      ],

      worlds: [
        {
          name: 'World 1',
          open: true,
          levels: [
            {
              name: '1-0',
              data: level_1_0,
              completed: false,
              bestMoves: null
            },
            {
              name: '1-1',
              data: level_1_1,
              completed: false,
              bestMoves: null
            },
            {
              name: '1-2',
              data: level_1_2,
              completed: false,
              bestMoves: null
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
              completed: false,
              bestMoves: null
            },
            {
              name: '2-2',
              data: level_2_2,
              completed: false,
              bestMoves: null
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
  padding: 0 0 0 10px;

  padding-bottom: 10px;

  font-size: 3rem;
  font-family: Roboto;
  font-style: normal;
  font-weight: bold;
  text-align: left;

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

/* Game */
.game-container{
  width: 100%;
}

/* SVG */
.svg-viewbox {
  margin: 20px 0 0 0;

  width: 70vw;
  height: 70vh;
}

/* Hexagons */
.hexToggled {
  fill: green;
}
.hexNotToggled {
  fill: red;
}

.hexagon-svg {
  stroke-width:1;
}

/* Information and Game Controls */

.stat-level {
  margin: 10px 0 0 0;

  color: #FF7A00;
}
.stat-completed {
  margin: 10px 0 0 0;

  color:#FF7A00;
}

.stat-moves {
  margin: 30px 0 0 10px;

  color: #FF7A00;
}

/* Level Switcher & Reset*/

.level-switcher {
  margin-top: 20px;
}

.level-change-prev, 
.level-change-next{
  margin: 0 20px 0 20px;

  min-width: 60px;
  min-height: 40px;
  height: 5vh;
  width: 5vw;

  border: none;
  border-radius: 10px;

  font-weight: bold;
  font-size: calc((1vw + 1vh) / 2);

  background-color: #2b2b2b;
  color:            #FF7A00;
}

.level-change-reset{
  margin: 0 20px 0 20px;

  min-width: 60px;
  min-height: 40px;
  height: 5vh;
  width: 5vh;

  border: none;
  border-radius: 10px;

  font-weight: bold;
  font-size: calc(((1vw + 1vh) / 2) + 1px);

  background-color: #2b2b2b;
  color:            #e20b00;
}

</style>