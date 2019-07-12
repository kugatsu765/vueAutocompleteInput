<template>
  <div id="app">
    <p>J'ai envie de manger un/une {{ fruit ? fruit : '??'}}</p>
    <Autocomplete
      v-model="fruit"
      :items="fruits"
      placeholder="Fruits"
      empty-results="Aucun fruit ne correspond à votre recherche"
      property-to-display="name"
      @selected="logSmt"
    ></Autocomplete>
    <p>Je suis en dessous</p>
  </div>
</template>

<script>
import Autocomplete from "./components/Autocomplete";

export default {
  name: "App",
  components: {
    Autocomplete
  },
  data: function() {
    return {
      fruits: [
        { id: 1, name: "Banana" },
        { id: 2, name: "Orange" },
        { id: 3, name: "Pêche" },
        { id: 4, name: "Abricot" },
        { id: 5, name: "Figue" },
        { id: 6, name: "Raisin" },
        { id: 7, name: "Mangue" },
        { id: 8, name: "Pasteck" },
        { id: 9, name: "Melon" },
        { id: 10, name: "Kiwi" }
      ],
      fruit: undefined
    };
  },
  methods: {
    logSmt(value) {
      console.log({ value: JSON.stringify(value) });
    }
  }
};
</script>

<style>
#app {
  font-family: "Avenir", Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  color: #2c3e50;
}
</style>
