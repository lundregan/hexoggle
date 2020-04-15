<template>
  <div id="app">

    <h1 class='title'>Hexoggle</h1>

    <svg viewBox='0 0 1000 1000' class='exa'>
      <!-- 1st row -->
      <svg x='200' y='0'>
        <polygon class='hexagon-svg' points="87,0 174,50 174,150 87,200 0,150 0,50 87,0" />
      </svg>
      <svg x='400' y='0'>
         <polygon class='hexagon-svg' points="87,0 174,50 174,150 87,200 0,150 0,50 87,0" />
      </svg>
      <svg x='600' y='0'>
         <polygon class='hexagon-svg' points="87,0 174,50 174,150 87,200 0,150 0,50 87,0" />
      </svg>

      <!-- 2nd row -->
      <svg x='100' y='175'>
         <polygon class='hexagon-svg' points="87,0 174,50 174,150 87,200 0,150 0,50 87,0" />
      </svg>
      <svg x='300' y='175'>
         <polygon class='hexagon-svg' points="87,0 174,50 174,150 87,200 0,150 0,50 87,0" />
      </svg>
      <svg x='500' y='175'>
         <polygon class='hexagon-svg' points="87,0 174,50 174,150 87,200 0,150 0,50 87,0" />
      </svg>
      <svg x='700' y='175'>
         <polygon class='hexagon-svg' points="87,0 174,50 174,150 87,200 0,150 0,50 87,0" />
      </svg>

      <!-- 3rd row -->
      <svg x='0' y='350'>
         <polygon class='hexagon-svg' points="87,0 174,50 174,150 87,200 0,150 0,50 87,0" />
      </svg>
      <svg x='200' y='350'>
         <polygon class='hexagon-svg' points="87,0 174,50 174,150 87,200 0,150 0,50 87,0" />
      </svg>
      <svg x='400' y='350'>
         <polygon class='hexagon-svg' points="87,0 174,50 174,150 87,200 0,150 0,50 87,0" />
      </svg>
      <svg x='600' y='350'>
         <polygon class='hexagon-svg' points="87,0 174,50 174,150 87,200 0,150 0,50 87,0" />
      </svg>
      <svg x='800' y='350'>
         <polygon class='hexagon-svg' points="87,0 174,50 174,150 87,200 0,150 0,50 87,0" />
      </svg>

      <!-- 4th row -->
      <svg x='100' y='525'>
         <polygon class='hexagon-svg' points="87,0 174,50 174,150 87,200 0,150 0,50 87,0" />
      </svg>
      <svg x='300' y='525'>
         <polygon class='hexagon-svg' points="87,0 174,50 174,150 87,200 0,150 0,50 87,0" />
      </svg>
      <svg x='500' y='525'>
         <polygon class='hexagon-svg' points="87,0 174,50 174,150 87,200 0,150 0,50 87,0" />
      </svg>
      <svg x='700' y='525'>
         <polygon class='hexagon-svg' points="87,0 174,50 174,150 87,200 0,150 0,50 87,0" />
      </svg>
      
      <!-- 5th row -->
      <svg x='200' y='700'>
        <polygon class='hexagon-svg' points="87,0 174,50 174,150 87,200 0,150 0,50 87,0" />
      </svg>
      
      <svg x='400' y='700'>
         <polygon class='hexagon-svg' points="87,0 174,50 174,150 87,200 0,150 0,50 87,0" />
      </svg>

      <svg x='600' y='700'>
         <polygon class='hexagon-svg' points="87,0 174,50 174,150 87,200 0,150 0,50 87,0" />
      </svg>

    </svg>
  </div>
</template>

<script>

export default {
  name: 'App',
  components: {
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

.exa {
  width: 40vw;
  height: 40vw;
}

.title {
  margin-top: 0px;

  padding-bottom: 10px;

  font-size: 3rem;

  background-color: #2e2e2e;
  color:            #c0c0c0;
}

.hexagon-div {
  margin-top: 56px; /* Fixes negative margin given to each row */

  padding-top: 20px;
}

.hexagon-svg {
  fill:grey;
  stroke-width:1;
}

.row {
  margin-top: -56px;
}

.row svg {
  margin-left: 10px;
  margin-top: 10px;
}
</style>
