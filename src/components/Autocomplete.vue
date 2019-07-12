<template>
  <div class="autocomplete">
    <input
      id="search-input"
      type="text"
      name="search-input"
      v-model="search"
      @input="filterItems"
      @keyup.enter="onEnter"
      @keydown.down="onArrowDown"
      @keydown.up="onArrowUp"
      :placeholder="placeholder"
      :disabled="disable"
    >
    <div class="search-result" v-if="displayResult">
      <ul class="search-result-items">
        <li
          v-for="(item, index) in results"
          class="search-result-item"
          :class="{'active': index === arrowCounter}"
          :key="item.id"
          @click="select(item)"
        >{{item[propertyToDisplay]}}</li>
        <li class="search-result-item-disabled" v-if="results.length === 0">{{emptyResults}}</li>
      </ul>
    </div>
  </div>
</template>

<style scoped>
#search-input {
  width: 100%;
  margin: auto;
  box-sizing: border-box;
  border: 1px solid #bfc3c9;
  padding: 0.4rem;
}
.autocomplete {
  position: relative;
}
.search-result {
  position: absolute;
  top: 40px;
  background-color: white;
  border: 1px solid #edf2f7;
  border-radius: 4px;
  box-shadow: 1px 1px 4px #80808042;
  width: 100%;
  max-height: 300px;
  overflow: auto;
}
.search-result-items {
  padding: 0;
  margin: 0;
}
.search-result-item {
  list-style: none;
  padding: 0.8rem;
  cursor: pointer;
}

.search-result-item-disabled {
  padding: 0.8rem;
}

.search-result-item:hover {
  background-color: #4299E1;
  color: white;
}
.active {
  background-color: #BEE3F8;
}
</style>


<script>
export default {
  name: "Autocompete",
  data: function() {
    return {
      search: "",
      results: this.items,
      displayResult: false,
      arrowCounter: -1
    };
  },
  props: {
    items: {
      type: Array,
      required: true
    },
    value: undefined,
    placeholder: {
      type: String,
      default: "Search"
    },
    emptyResults: {
      type: String,
      default: "No results"
    },
    propertyToDisplay: {
      type: String,
      default: "label"
    },
    disable: {
      type: Boolean,
      default: false
    }
  },
  mounted: function() {
    this.$nextTick(function() {
      this.initialiseWithValue();
    });
  },
  watch: {
    items(newValue) {
      const item = newValue.find(item => item["id"] === this.value.id);
      if (item !== undefined) {
        this.search = item[this.propertyToDisplay];
      }
    }
  },
  methods: {
    initialiseWithValue() {
      if (
        this.value !== undefined &&
        this.value[this.propertyToDisplay] !== undefined
      ) {
        this.search = this.value[this.propertyToDisplay];
      }
    },
    filterItems() {
      this.openResult();
      this.arrowCounter = -1;
      this.results = this.items.filter(item =>
        item[this.propertyToDisplay]
          .toLowerCase()
          .includes(this.search.toLowerCase())
      );
    },
    select(value) {
      if (typeof value === "string") {
        const item = this.items.find(item =>
          item[this.propertyToDisplay]
            .toLowerCase()
            .includes(value.toLowerCase())
        );
        if (item !== undefined) this.throwSelected(item);
      } else if (typeof value === "object") {
        this.throwSelected(value);
      }
    },
    throwSelected(value) {
      this.closeResult();
      document.removeEventListener("click", this.handleoutsideClick);
      this.search = value[this.propertyToDisplay];
      this.$emit("input", value);
      this.$emit("selected", value);
    },
    openResult() {
      this.displayResult = true;
      document.addEventListener("click", this.handleoutsideClick);
    },
    closeResult() {
      this.displayResult = false;
    },
    handleoutsideClick(e) {
      if (!this.$el.contains(e.target)) {
        this.closeResult();
        document.removeEventListener("click", this.handleoutsideClick);
      }
    },
    onArrowDown() {
      if (this.arrowCounter < this.results.length - 1) {
        this.arrowCounter = this.arrowCounter + 1;
      } else {
        this.arrowCounter = 0;
      }
      this.followArrow();
    },
    onArrowUp() {
      if (this.arrowCounter > 0) {
        this.arrowCounter = this.arrowCounter - 1;
      } else {
        this.arrowCounter = this.results.length - 1;
      }
      this.followArrow();
    },
    onEnter() {
      if (this.arrowCounter !== -1) {
        this.search = this.results[this.arrowCounter];
        this.arrowCounter = -1;
        this.select(this.search);
      } else {
        this.select(this.search);
      }
    },
    followArrow() {
      const el = document.getElementsByClassName("active");
      if (el.length > 0) {
        el[0].scrollIntoView({ behavior: "smooth", block: "center" });
      }
    }
  }
};
</script>


