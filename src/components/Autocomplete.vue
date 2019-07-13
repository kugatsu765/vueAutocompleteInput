<template>
  <div class="autocomplete" :class="classComponent">
    <input
      id="search-input"
      type="text"
      name="search-input"
      class="autocomplete-input"
      v-model="search"
      @input="filterItems"
      @keyup.enter="onEnter"
      @keydown.down="onArrowDown"
      @keydown.up="onArrowUp"
      @focus="filterItems"
      :placeholder="placeholder"
      :disabled="disable"
      autocomplete="off"
      :class="classInput"
    >
    <div class="autocomplete-list-wrapper" :class="classWrapper" v-if="displayResult">
      <ul class="autocomplete-list" :class="classList">
        <li
          v-for="(item, index) in results"
          class="autocomplete-list-item"
          :class="rowClass(index)"
          :key="item.id"
          @click="select(item)"
        >
          <slot name="listItem" v-bind:item="item">{{item[propertyToDisplay]}}</slot>
        </li>
        <li class="search-result-item-disabled" v-if="results.length === 0">{{emptyResults}}</li>
      </ul>
    </div>
  </div>
</template>

<style scoped>
.autocomplete {
  position: relative;
}

.autocomplete-input {
  width: 100%;
  padding: 0;
  margin: 0;
  box-sizing: border-box;
}

.autocomplete-input-custom {
  color: #2d3748;
  box-shadow: 1px 1px 4px #80808042;
  padding: 0.7rem;
  background-color: white;
  border: 1px solid #edf2f7;
  border-radius: 4px;
}

.autocomplete-list-wrapper {
  position: absolute;
  width: 100%;
  z-index: 500;
}

.autocomplete-list-wrapper-custom {
  transform: translateY(0.75rem);
}

.autocomplete-list {
  padding: 0;
  margin: 0;
  overflow: auto;
}

.autocomplete-list-custom {
  background-color: white;
  border: 1px solid #edf2f7;
  border-radius: 4px;
  box-shadow: 1px 1px 4px #80808042;
  max-height: 300px;
}

.autocomplete-list-item {
  list-style: none;
  cursor: pointer;
}

.autocomplete-list-item-custom {
  padding: 0.8rem;
}

.autocomplete-list-item-custom:hover {
  background-color: #4299e1;
  color: white;
}

.autocomplete-item-active-custom {
  background-color: #bee3f8;
}

.search-result-item-disabled {
  padding: 0.8rem;
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
    value: {
      type: Object,
      required: false
    },
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
    },
    classComponent: {
      type: String,
      default: ""
    },
    classInput: {
      type: String,
      default: "autocomplete-input-custom"
    },
    classWrapper: {
      type: String,
      default: "autocomplete-list-wrapper-custom"
    },
    classList: {
      type: String,
      default: "autocomplete-list-custom"
    },
    classItem: {
      type: String,
      default: "autocomplete-list-item-custom"
    },
    classItemActive: {
      type: String,
      default: "autocomplete-item-active-custom"
    }
  },
  mounted: function() {
    this.$nextTick(function() {
      this.initialiseWithValue();
    });
  },
  watch: {
    items(newValue) {
      this.results = newValue;
      if (typeof this.value === "undefined") return;
      const item = newValue.find(item => item["id"] === this.value.id);
      if (item !== undefined) {
        this.search = item[this.propertyToDisplay];
      }
    },
    value(newValue) {
      this.search = newValue[this.propertyToDisplay];
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

      if (this.results.length === 0) {
        this.$emit("noResult");
      }
      if (this.search === "") {
        this.$emit("clear");
      }
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
        this.onEnter();
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
      const el = document.getElementsByClassName(this.classItemActive);
      if (el.length > 0) {
        el[0].scrollIntoView({ behavior: "smooth", block: "center" });
      }
    },
    rowClass(index) {
      let custom = this.classItem;
      if (index == this.arrowCounter) custom += " " + this.classItemActive;
      return custom;
    }
  }
};
</script>


