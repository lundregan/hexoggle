<template>
  <div id="app">

    <h1>Hexaoggle</h1>

    <div class="hexagon-div">
      <div class="row">
        <svg height="200" width="174">
          <polygon class='hexagon-svg' points="87,0 174,50 174,150 87,200 0,150 0,50 87,0" />
        </svg>
        <svg height="200" width="174">
          <polygon class='hexagon-svg' points="87,0 174,50 174,150 87,200 0,150 0,50 87,0" />
        </svg>
        <svg height="200" width="174">
          <polygon class='hexagon-svg' points="87,0 174,50 174,150 87,200 0,150 0,50 87,0" />
        </svg>
      </div>
      <div class="row">
        <svg height="200" width="174">
          <polygon class='hexagon-svg' points="87,0 174,50 174,150 87,200 0,150 0,50 87,0" />
        </svg>
        <svg height="200" width="174">
          <polygon class='hexagon-svg' points="87,0 174,50 174,150 87,200 0,150 0,50 87,0" />
        </svg>
        <svg height="200" width="174">
          <polygon class='hexagon-svg' points="87,0 174,50 174,150 87,200 0,150 0,50 87,0" />
        </svg>
        <svg height="200" width="174">
          <polygon class='hexagon-svg' points="87,0 174,50 174,150 87,200 0,150 0,50 87,0" />
        </svg>
      </div>
      <div class="row">
        <svg height="200" width="174">
          <polygon class='hexagon-svg' points="87,0 174,50 174,150 87,200 0,150 0,50 87,0" />
        </svg>
        <svg height="200" width="174">
          <polygon class='hexagon-svg' points="87,0 174,50 174,150 87,200 0,150 0,50 87,0" />
        </svg>
        <svg height="200" width="174">
          <polygon class='hexagon-svg' points="87,0 174,50 174,150 87,200 0,150 0,50 87,0" />
        </svg>
        <svg height="200" width="174">
          <polygon class='hexagon-svg' points="87,0 174,50 174,150 87,200 0,150 0,50 87,0" />
        </svg>
        <svg height="200" width="174">
          <polygon class='hexagon-svg' points="87,0 174,50 174,150 87,200 0,150 0,50 87,0" />
        </svg>
      </div>
      <div class="row">
        <svg height="200" width="174">
          <polygon class='hexagon-svg' points="87,0 174,50 174,150 87,200 0,150 0,50 87,0" />
        </svg>
        <svg height="200" width="174">
          <polygon class='hexagon-svg' points="87,0 174,50 174,150 87,200 0,150 0,50 87,0" />
        </svg>
        <svg height="200" width="174">
          <polygon class='hexagon-svg' points="87,0 174,50 174,150 87,200 0,150 0,50 87,0" />
        </svg>
        <svg height="200" width="174">
          <polygon class='hexagon-svg' points="87,0 174,50 174,150 87,200 0,150 0,50 87,0" />
        </svg>
      </div>
      <div class="row">
        <svg height="200" width="174">
          <polygon class='hexagon-svg' points="87,0 174,50 174,150 87,200 0,150 0,50 87,0" />
        </svg>
        <svg height="200" width="174">
          <polygon class='hexagon-svg' points="87,0 174,50 174,150 87,200 0,150 0,50 87,0" />
        </svg>
        <svg height="200" width="174">
          <polygon class='hexagon-svg' points="87,0 174,50 174,150 87,200 0,150 0,50 87,0" />
        </svg>
      </div>
    </div>
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
  margin-top: 60px;
}

.hexagon-div {
  margin-top: 56px; /* Fixes negative margin given to each row */
}

.hexagon-svg {
  fill:grey;
  stroke-width:1;
  border: 1px solid black;
}

.row {
  margin-top: -56px;
}

.row svg {
  margin-left: 10px;
  margin-top: 10px;
}
</style>
