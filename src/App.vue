<template>
  <div id="app" v-on:keydown.left="key()">

    <div class='title-section'>
      <h1 class='title'>Hexoggle</h1>
    </div>
    <div id="game" style='display: flex;'>
      
      <button class='openMenuButton' v-if='!menuOpen' v-on:click='menuOpen = true'>|||</button>

      <transition name='slideIn'>
        <aside class='level-nav' v-if='menuOpen'>
          <button class='closeMenuButton' v-on:click='menuOpen = !menuOpen'>X</button>

          <p class='level-select-title'>Level Select</p>

          <ul class='world-list level-nav-ul'>
            <li class='world' v-for='world in worlds' :key='world.id'>
              <p class='world-title' v-on:click='openWorldLevels(world)'> {{ world.name }} </p>

              <transition name='slideIn'>
              <ul class='level-list level-nav-ul' v-if='world.open'>
                <li class='level' v-for='level in world.levels' :key='level.id' v-bind:class='{ levelComplete : level.completed, levelNotComplete : !level.completed }'> 
                  <p class='level-title' v-on:click='changeLevel(level);'> {{ level.name }} </p> 
                  </li>
              </ul>
              </transition>
            </li>
          </ul>
        </aside>
      </transition>

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
              <polygon class='hexagon-svg animated' v-bind:points='hexagonData.points' v-bind:class="{ hexToggled : hexagon.toggled, hexNotToggled : !hexagon.toggled, 'pulse' : hexagon.animation == 'pulse' }" v-on:click='hexagonClicked(hexagon)' />
              <g v-if='"toggleNeighbors" in hexagon'>           
                <rect v-if='hexagon.toggleNeighbors.left' x='15' y='50' width='10px' height='100px' />
                <rect v-if='hexagon.toggleNeighbors.right' x='150' y='50' width='10px' height='100px' />
                <rect v-if='hexagon.toggleNeighbors.leftBottom' width='10px' height='100px' style='x: -120; y: 76; transform: rotate(-60deg)' />
                <rect v-if='hexagon.toggleNeighbors.rightBottom' width='10px' height='100px' style='x: 195; y: -76; transform: rotate(60deg)' />
                <rect v-if='hexagon.toggleNeighbors.leftTop' width='10px' height='100px' style='x: 55; y: -76; transform: rotate(60deg)' />
                <rect v-if='hexagon.toggleNeighbors.rightTop' width='10px' height='100px' style='x: 20; y: 76; transform: rotate(-60deg)' />
              </g>
            </svg>
          </svg>
        </svg>

        <div class='level-switcher' style='margin-left: 6px;'> 
          <button class='level-change-prev' v-on:click='prevLevel()'>Previous</button>
          <button class='level-change-reset' v-on:click='resetCurrentLevel()'>Reset</button>
          <button class='level-change-next' v-on:click='nextLevel()'>Next</button>
        </div>

        <div clss='progress-reset'>
          <button class='level-resetAllProgress' v-on:click='resetAllProgress()'>Reset All Progress</button>
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
  import level_2_3 from './levels/2-3.json';

  // testing level
  import test_level from './levels/testing.json';

export default {

  name: 'App',
  
  components: {
  },

  created: function(){
    this.changeLevel(this.worlds[0].levels[0]);
  },

  mounted: function(){
    if(localStorage.hexoggleCompletedLevels){
      var completedLevels = JSON.parse(localStorage.hexoggleCompletedLevels);

      for(var i = 0; i < completedLevels.length; i++){

        let world = completedLevels[i].world;
        let level = completedLevels[i].level;
        let completed = completedLevels[i].completed;
        let bestMoves = completedLevels[i].bestMoves;

        this.worlds[world].levels[level].completed = completed;
        this.worlds[world].levels[level].bestMoves = bestMoves;
      }

    }
  },

  methods: {

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

      if(win){
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
      this.gameState.currentMoves = 0;
      this.nextLevel();

      this.saveState();
    },

    saveState: function(){
      var saveData = [];

      for(var i = 0; i < this.worlds.length; i++){
        for(var j = 0; j < this.worlds[i].levels.length; j++){
          
          let levelData = {
            world: i,
            level: j,
            completed: this.worlds[i].levels[j].completed,
            bestMoves: this.worlds[i].levels[j].bestMoves
          }
          saveData.push(levelData);
        }
      }

      localStorage.hexoggleCompletedLevels = JSON.stringify(saveData);
    },

    changeLevel: function(level){
      this.loadLevelData(level.data);
      this.setCurrentLevel(level);
    },

    setCurrentLevel: function(level){
      this.gameState.currentLevel = level;
    },

    resetCurrentLevel: function(){
      this.resetCurrentMoves();
      this.loadLevelData(this.gameState.currentLevel.data);
    },

    resetCurrentMoves: function(){
      this.gameState.currentMoves = 0;
    },

    resetAllProgress: function(){
      localStorage.removeItem('hexoggleCompletedLevels');

      location.reload();
    },

    nextLevel: function(){
      this.incrementLevel(1);
    },

    prevLevel: function(){
      this.incrementLevel(-1);
    },

    incrementLevel: function(increment){
      var newLevel = null;

      for(var x = 0; x < this.worlds.length; x++){
        for(var y = 0; y < this.worlds[x].levels.length; y++){
          if(this.worlds[x].levels[y] == this.gameState.currentLevel){
            if(this.isValidHex(x, y + increment)){
              newLevel = this.worlds[x].levels[y + increment];
            }
          } 
        }
      }
      
      if(newLevel != null){
        this.changeLevel(newLevel);
      }
    },


    // -- Hexagon Methods --
    hexagonClicked: function(hex){
      this.gameState.currentMoves += 1;

      this.executeHexagonAction(hex);

      this.checkWinCondition();
    },

    toggleHex: function(pos){
      let hexagon = this.hexagons[pos[0]][pos[1]];

      hexagon.animation = 'pulse';
      setTimeout(function(){
        hexagon.animation = '';
      },1000);

      this.changeColor(hexagon);

      setTimeout(
        this.checkWinCondition,
        100
      );
    },

    getLeftHex: function(hex){
      var leftHexPosition = this.getArrayPosition(hex.id);
      leftHexPosition[1] -= 1;

      return this.getHexagonFromArrayPosition(leftHexPosition[0], leftHexPosition[1]);
    },
    
    getRightHex: function(hex){
      var rightHexPosition = this.getArrayPosition(hex.id);
      rightHexPosition[1] += 1;

      return this.getHexagonFromArrayPosition(rightHexPosition[0], rightHexPosition[1]);
    },

    getBottomLeftHex: function(hex){
      var bottomLeftHexPosition = this.getArrayPosition(hex.id);
      bottomLeftHexPosition[0] += 1;
      bottomLeftHexPosition[1] -= 1;

      if(bottomLeftHexPosition[0] <= 2){
        bottomLeftHexPosition[1] += 1;
      }

      return this.getHexagonFromArrayPosition(bottomLeftHexPosition[0], bottomLeftHexPosition[1]);
    },

    getBottomRightHex: function(hex){
      var bottomRightHexPosition = this.getArrayPosition(hex.id);
      bottomRightHexPosition[0] += 1;

      if(bottomRightHexPosition[0] <= 2){
        bottomRightHexPosition[1] += 1;
      }

      return this.getHexagonFromArrayPosition(bottomRightHexPosition[0], bottomRightHexPosition[1]);
    },

    getTopLeftHex: function(hex){
      var topLeftHexPosition = this.getArrayPosition(hex.id);
      topLeftHexPosition[0] -= 1;
      topLeftHexPosition[1] -= 1;

      if(topLeftHexPosition[0] >= 2){
        topLeftHexPosition[1] += 1;
      }

      return this.getHexagonFromArrayPosition(topLeftHexPosition[0], topLeftHexPosition[1]);
    },

    getTopRightHex: function(hex){
      var topRightHexPosition = this.getArrayPosition(hex.id);
      topRightHexPosition[0] -= 1;

      if(topRightHexPosition[0] >= 2){
        topRightHexPosition[1] += 1;
      }

      return this.getHexagonFromArrayPosition(topRightHexPosition[0], topRightHexPosition[1]);
    },

    toggleLeftHex: function(currentHex){
      var leftHex = this.getLeftHex(currentHex);

      this.executeHexagonAction(leftHex);
    },

    toggleRightHex: function(currentHex){
      var rightHex = this.getRightHex(currentHex);

      this.executeHexagonAction(rightHex);
    },

    toggleBottomLeftHex: function(currentHex){
      var bottomLeftHex = this.getBottomLeftHex(currentHex);

      this.executeHexagonAction(bottomLeftHex);
    },

    toggleBottomRightHex: function(currentHex){
      var bottomRightHex = this.getBottomRightHex(currentHex);

      this.executeHexagonAction(bottomRightHex);
    },

    toggleTopLeftHex: function(currentHex){
      var topLeftHex = this.getTopLeftHex(currentHex);

      this.executeHexagonAction(topLeftHex);
    },

    toggleTopRightHex: function(currentHex){
      var topRightHex = this.getTopRightHex(currentHex);

      this.executeHexagonAction(topRightHex);
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

    loadDefaultlevelData: function(){
      // Default set for every hexagon , false toggled (red) and type of normal
      for(let i = 0; i < this.hexagons.length; i++){
        for(let j = 0; j < this.hexagons[i].length; j++){
          var current_hexagon = this.hexagons[i][j];
          
          current_hexagon.toggled = false;
          if('toggleNeighbors' in current_hexagon){
            delete current_hexagon.toggleNeighbors;
          }
        } 
      }
    },

    loadLevelData: function(json){
      this.loadDefaultlevelData();

      // load each hex element
      json.forEach(item => { 
        this.loadHex(item.i, item.j, item.toggled, item.type); 

        if("toggleNeighbors" in item){
          //alert(item.toggleNeighbors);

          this.hexagons[item.i][item.j].toggleNeighbors = item.toggleNeighbors;
        }
      });
    },

    loadHex: function(i, j, toggled, type){
      this.hexagons[i][j].type = type;
      this.hexagons[i][j].toggled = toggled;
    },


    // -- Hexagon type behaviours --
    executeHexagonAction: function(hex){
      if('toggleNeighbors' in hex){
        this.toggleSelf(hex);
        
        if('left' in hex.toggleNeighbors){
          this.toggleNeighborLeft(hex);
        }
        if('right' in hex.toggleNeighbors){
          this.toggleNeighborRight(hex);
        }
        if('leftTop' in hex.toggleNeighbors){
          this.toggleNeighborTopLeft(hex);
        }
        if('rightTop' in hex.toggleNeighbors){
          this.toggleNeighborTopRight(hex);
        }
        if('leftBottom' in hex.toggleNeighbors){
          this.toggleNeighborBottomLeft(hex);
        }
        if('rightBottom' in hex.toggleNeighbors){
          this.toggleNeighborBottomRight(hex);
        }
      } else {
        this.toggleSelf(hex);
      }
    },

    toggleSelf(hex){
      this.toggleHex(this.getArrayPosition(hex.id));
    },

    toggleNeighborLeft: function(hex){

      setTimeout(
        this.toggleLeftHex,
        100,
        hex
      );
    },

    toggleNeighborRight: function(hex){

      setTimeout(
        this.toggleRightHex,
        100,
        hex
      );
    },

    toggleNeighborBottomRight: function(hex){
      setTimeout(
        this.toggleBottomRightHex,
        100,
        hex
      );
    },

    toggleNeighborBottomLeft: function(hex){
      setTimeout(
        this.toggleBottomLeftHex,
        100,
        hex
      );
    },

    toggleNeighborTopLeft: function(hex){
      setTimeout(
        this.toggleTopLeftHex,
        100,
        hex
      );
    },

    toggleNeighborTopRight: function(hex){
      setTimeout(
        this.toggleTopRightHex,
        100,
        hex
      );
    },

    testingFunction: function(hex){
      console.log(hex.toggleNeighbors);
    }
  },


  data: function() {
    return {
      gameState: {
        currentLevel: null,
        currentMoves: 0
      },

      menuOpen: true,

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
            toggled: false,
            animation: ''
          },
          {
            id: 1,
            x: '400',
            y: '0',
            type: 'normal',
            toggled: false,
            animation: ''
          },
          {
            id: 2,
            x: '600',
            y: '0',
            type: 'normal',
            toggled: false,
            animation: ''
          }
        ],
        [
          // 2nd row
          {
            id: 3,
            x: '100',
            y: '175',
            type: 'normal',
            toggled: false,
            animation: ''
          },
          {
            id: 4,
            x: '300',
            y: '175',
            type: 'normal',
            toggled: false,
            animation: ''
          },
          {
            id: 5,
            x: '500',
            y: '175',
            type: 'neighbors',
            toggled: false,
            animation: ''
          },
          {
            id: 6,
            x: '700',
            y: '175',
            type: 'normal',
            toggled: false,
            animation: ''
          }
        ],
        [
          // 3rd row
          {
            id: 7,
            x: '0',
            y: '350',
            type: 'normal',
            toggled: false,
            animation: ''
          },
          {
            id: 8,
            x: '200',
            y: '350',
            type: 'normal',
            toggled: false,
            animation: ''
          },
          {
            
            id: 9,
            x: '400',
            y: '350',
            type: 'normal',
            toggled: false,
            animation: ''
          },
          {
            
            id: 10,
            x: '600',
            y: '350',
            type: 'normal',
            toggled: false,
            animation: ''
          },
          {
            
            id: 11,
            x: '800',
            y: '350',
            type: 'normal',
            toggled: false,
            animation: ''
          }
        ],
        [
          // 4th row
          {
            id: 12,
            x: '100',
            y: '525',
            type: 'normal',
            toggled: false,
            animation: ''
          },
          {
            id: 13,
            x: '300',
            y: '525',
            type: 'normal',
            toggled: false,
            animation: ''
          },
          {
            id: 14,
            x: '500',
            y: '525',
            type: 'normal',
            toggled: false,
            animation: ''
          },
          {
            
            id: 15,
            x: '700',
            y: '525',
            type: 'normal',
            toggled: false,
            animation: ''
          }
        ],
        [
          //  5th row
          {
            id: 16,
            x: '200',
            y: '700',
            type: 'normal',
            toggled: false,
            animation: ''
          },
          {
            id: 17,
            x: '400',
            y: '700',
            type: 'normal',
            toggled: false,
            animation: ''
          },
          {
            id: 18,
            x: '600',
            y: '700',
            type: 'normal',
            toggled: false,
            animation: ''
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
            },
            {
              name: '2-3',
              data: level_2_3,
              completed: false,
              bestMoves: null
            },
          ]
        },
        {
          name: 'Testing World',
          open: true,
          levels: [
            {
              name: 'Testing Level 1',
              data: test_level,
              completed: true,
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

  /* CSS Variables */

  --app-color-bg: #474747;
  --app-color-primary-text: #FF7A00;

  --title-color-bg: #121212;
  --title-color: #FF7A00;

  --level-color-bg: #121212;
  --level-selectTitle-color: #FFFFFF;
  --level-complete-color: green;
  --level-notComplete-color: #FA4E5D;

  --world-color-bg: #3a3a3a;
  --world-color: #FFFFFF;

  --hex-toggled-color: green;
  --hex-notToggled-color: red;

  --button-color-bg: #2b2b2b;
  --button-reset-text-color: #e20b00;
}

#app {
  width: 100vw;
  height: 100vh;
}

#game{
  width: 100%;
  height: 100%;
  
  background-color: var(--app-color-bg);
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

  background-color: var(--title-color-bg);
  color:            var(--title-color);
}

/* Level Select Menu */
.level-nav {
  text-align: left;
  background-color: var(--level-color-bg);
  opacity: 0.7; 
  width: 10%;
  height: 100%;
}

.level-select-title {
  
  padding-top: 10px;
  padding-bottom: 25px;
  
  font-size: 2rem;
  text-align: center;

  color: var(--level-selectTitle-color);
}

.level-nav-ul {
  list-style-type: none;
}

.openMenuButton {
  width: 5%;
  max-width: 40px;

  border: none;

  background-color: var(--level-color-bg);
  color: var(--app-color-primary-text);
}

.closeMenuButton {
  width: 100%;
  height: 30px;

  font-size: 1.5rem;

  border: none;
  background-color: var(--level-color-bg);
  color: var(--app-color-primary-text);
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

  background-color: var(--world-color-bg);
  color: var(--world-color);
}


/* Levels */
.level-list {
  padding-left: 0px;
}

.level {
  margin: 10px 0 10px 0;
}

.levelComplete {
  background-color: var(--level-complete-color);
}

.levelNotComplete {
  background-color: var(--level-notComplete-color);
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
  fill: var(--hex-toggled-color);
}
.hexNotToggled {
  fill: var(--hex-notToggled-color);
}

.hexagon-svg {
  stroke-width:1;
}

/* Information and Game Controls */

.stat-level {
  margin: 10px 0 0 0;

  color: var(--app-color-primary-text);
}
.stat-completed {
  margin: 10px 0 0 0;

  color: var(--app-color-primary-text);
}

.stat-moves {
  margin: 30px 0 0 10px;

  color: var(--app-color-primary-text);
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

  background-color: var(--button-color-bg);
  color:            var(--app-color-primary-text);
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

  background-color: var(--button-color-bg);
  color:            var(--button-reset-text-color);
}

.level-resetAllProgress {
  margin: 5px 20px 0 20px;

  min-width: 60px;
  min-height: 40px;
  height: 5vh;
  width: 20vh;

  border: none;
  border-radius: 10px;

  font-weight: bold;
  font-size: calc(((1vw + 1vh) / 2) + 1px);

  background-color: var(--button-color-bg);
  color:            var(--button-reset-text-color);
}

/* ANIMATIONS (VUE TRANISTIONS)*/

.slideIn-enter-active, .slideIn-leave-active {
  transition: 0.5s;
}
.slideIn-enter, .slideIn-leave-to /* .slideIn-leave-active below version 2.1.8 */ {
  transform: translate3d(-100%, 0, 0);
}

</style>