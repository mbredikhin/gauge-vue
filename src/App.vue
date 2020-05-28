<template>
  <div id="app">
    <Gauge
      :value="parseInt(value)"
      :min="parseInt(min)"
      :max="parseInt(max)"
      :label="label"
      :units="units"
    />

    <label for="value">
      Value
      <input type="range" name="value" v-model="value" />
    </label>
    <br />
    <label for="min">
      Min
      <input type="range" name="min" v-model="min" />
    </label>
    <br />
    <label for="max">
      Max
      <input type="range" name="max" v-model="max" />
    </label>
    <br />
    <label for="label">
      Label
      <input type="text" name="label" v-model="label" />
    </label>
    <br />
    <label for="units">
      Units
      <input type="text" name="units" v-model="units" />
    </label>
  </div>
</template>

<script>
import Gauge from './components/Gauge.vue';

export default {
  name: 'App',
  components: {
    Gauge,
  },
  data() {
    return {
      value: 35,
      min: 0,
      max: 100,
      label: 'Label',
      units: 'Units',
    };
  },
};
</script>

<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  display: flex;
  flex-flow: column nowrap;
  align-items: center;
}
</style>
