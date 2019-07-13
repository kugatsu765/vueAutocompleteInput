<template>
  <div id="app">
    <p>J'ai envie de manger un/une {{ article ? article : '??'}}</p>
    <Autocomplete
      v-model="article"
      :items="articles"
      placeholder="Article"
      empty-results="Aucun article ne correspond à votre recherche"
      property-to-display="title"
      @selected="logSmt"
      @clear="clearField"
      class-input="my-custom-input"
      class-item-active="my-custom-item-active"
      class-item="my-custom-item"
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
      articles: [],
      article: {
        userId: 1,
        id: 7,
        title: "magnam facilis autem",
        body:
          "dolore placeat quibusdam ea quo vitae\nmagni quis enim qui quis quo nemo aut saepe\nquidem repellat excepturi ut quia\nsunt ut sequi eos ea sed quas"
      }
    };
  },
  mounted() {
    this.$nextTick(() => {
      fetch("https://jsonplaceholder.typicode.com/posts")
        .then(response => response.json())
        .then(json => {
          this.articles = json;
        });
    });
  },
  methods: {
    logSmt(value) {
      console.log({ value: JSON.stringify(value) });
    },
    clearField() {
      console.log("clear");
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

.my-custom-input {
  color: white;
  box-shadow: 1px 1px 4px #80808042;
  padding: 0.7rem !important;
  background-color: #2d3748;
  border: 1px solid #edf2f7;
  border-radius: 4px;
}

.my-custom-item {
  padding: 0.8rem;
  background-color: #4299e1;
  color: white;
}
.my-custom-item:hover {
  background-color: #fc8181;
  color: white;
}

.my-custom-item-active {
  background-color: #f8bef3;
}
</style>
