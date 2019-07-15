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
      @keyup.esc="clear"
      :placeholder="placeholder"
      :disabled="disable"
      autocomplete="off"
      :class="classInput"
    />
    <button class="autocomplete-clear-btn" @click="clear" v-if="closeButon">
      <slot name="closeButton">
        <svg
          version="1.1"
          id="Capa_1"
          xmlns="http://www.w3.org/2000/svg"
          xmlns:xlink="http://www.w3.org/1999/xlink"
          x="0px"
          y="0px"
          viewBox="0 0 469.785 469.785"
          style="enable-background:new 0 0 469.785 469.785;"
          xml:space="preserve"
          width="15px"
          heigth="15px"
        >
          <g transform="matrix(1.25 0 0 -1.25 0 45)">
            <g>
              <g>
                <path
                  style="fill:#4A5568;"
                  d="M228.294-151.753L367.489-12.558c11.116,11.105,11.116,29.116,0,40.22
				c-11.105,11.116-29.104,11.116-40.22,0L188.073-111.533L48.866,27.663c-11.093,11.116-29.116,11.116-40.22,0
				c-11.105-11.105-11.105-29.116,0-40.22l139.207-139.196L8.338-291.268c-11.116-11.116-11.116-29.116,0-40.22
				c5.552-5.564,12.834-8.34,20.116-8.34c7.27,0,14.552,2.776,20.105,8.34l139.514,139.514l139.196-139.196
				c5.564-5.552,12.834-8.34,20.116-8.34c7.27,0,14.552,2.788,20.105,8.34c11.116,11.105,11.116,29.104,0,40.22L228.294-151.753z"
                />
              </g>
            </g>
          </g>
        </svg>
      </slot>
    </button>

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

.autocomplete-clear-btn {
  position: absolute;
  right: 8px;
  top: 50%;
  transform: translateY(-50%);
  border-radius: 4px;
  padding: 4px 8px;
  outline: none;
  font-weight: 300;
  border: none;
}

.autocomplete-clear-btn:hover {
  background-color: #eceff3;
}

.autocomplete-clear-btn:active {
  background-color: #bebebe;
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
    },
    closeButon: {
      type: Boolean,
      default: true
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
      if (newValue != undefined) {
        this.search = newValue[this.propertyToDisplay];
      } else {
        this.search = "";
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
      document.removeEventListener("click", this.handleoutsideClick);
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
    },
    clear() {
      this.search = "";
      this.closeResult();
      this.$emit("input", undefined);
      this.$emit("clear");
    }
  }
};
</script>


