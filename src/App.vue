<template>
  <div id="app" class="p-8">
    <h1 class="text-3xl font-medium mb-12">Vue autocomplete demonstation</h1>
    <h2 class="text-xl font-medium">🚀 Basic use case</h2>

    <div class="flex flex-row">
      <div class="flex-1 p-3">
        <Autocomplete v-model="fruit" :items="fruits" property-to-display="name"></Autocomplete>
        <div class="mt-3 p-2 bg-gray-300 shadow text-center">
          <div v-text="basicHtml"></div>
          <!-- <img src="../assets/doc/basic.png" width="500" class="mx-auto"> -->
        </div>
      </div>
      <div class="flex-1 p-3 bg-gray-800 p-3 m-3 rounded text-white">{{ fruit }}</div>
    </div>

    <h2 class="text-xl font-medium">💄 Custom item row</h2>

    <div class="flex flex-row">
      <div class="flex-1 p-3">
        <Autocomplete v-model="article" :items="articles" property-to-display="title">
          <template v-slot:listItem="slotProps">
            <p class="font-medium mb-2">{{slotProps.item.title}}</p>
            <p class="text-gray-500">{{slotProps.item.body}}</p>
          </template>
        </Autocomplete>
        <div class="mt-3 p-2 bg-gray-300 shadow">
          <div v-text="customHtml"></div>
        </div>
      </div>
      <div class="flex-1 p-3 bg-gray-800 p-3 m-3 rounded text-white">{{ article}}</div>
    </div>

    <h2 class="text-xl font-medium">🎉 Full</h2>

    <div class="flex flex-row">
      <div class="flex-1 p-3">
        <Autocomplete
          v-model="article"
          :items="articles"
          placeholder="Article"
          empty-results="Nothing to see"
          property-to-display="title"
        ></Autocomplete>

        <div class="mt-3 p-2 bg-gray-300 shadow">
          <div v-text="fullHtml"></div>
        </div>
      </div>
      <div class="flex-1 p-3 bg-gray-800 p-3 m-3 rounded text-white">{{ article}}</div>
    </div>

    <h2 class="text-xl font-medium">🎃 Halloween</h2>

    <div class="flex flex-row">
      <div class="flex-1 p-3">
        <Autocomplete
          v-model="article"
          :items="articles"
          placeholder="Article"
          empty-results="Nothing to see"
          property-to-display="title"
          class-input="my-custom-input"
          class-item-active="my-custom-item-active"
          class-item="my-custom-item"
        ></Autocomplete>
        <div class="mt-3 p-2 bg-gray-300 shadow">
          <div v-text="halloHtml"></div>
        </div>
      </div>
      <div class="flex-1 p-3 bg-gray-800 p-3 m-3 rounded text-white">{{ article}}</div>
    </div>
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
      fruit: undefined,
      articles: [],
      article: undefined,
      basicHtml: `<Autocomplete v-model='fruit' :items='fruits' property-to-display='name'>
        </Autocomplete>`,
      customHtml: `<Autocomplete v-model="article" :items="articles" property-to-display="title">
          <template v-slot:listItem="slotProps">
            <p class="font-medium mb-2">{{slotProps.item.title}}</p>
            <p class="text-gray-500">{{slotProps.item.body}}</p>
          </template>
        </Autocomplete>`,
      fullHtml: `        <Autocomplete
          v-model="article"
          :items="articles"
          placeholder="Article"
          empty-results="Nothing to see"
          property-to-display="title"
        ></Autocomplete>`,
      halloHtml: `        <Autocomplete
          v-model="article"
          :items="articles"
          placeholder="Article"
          empty-results="Nothing to see"
          property-to-display="title"
          class-input="my-custom-input"
          class-item-active="my-custom-item-active"
          class-item="my-custom-item"
        ></Autocomplete>`
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
  background-color: #ed8936;
  border: 1px solid #edf2f7;
  border-radius: 4px;
}

.my-custom-item {
  padding: 0.8rem;
  background-color: #718096;
  color: white;
}
.my-custom-item:hover {
  background-color: #f6ad55;
  color: white;
}

.my-custom-item-active {
  background-color: #fbd38d;
}
</style>
