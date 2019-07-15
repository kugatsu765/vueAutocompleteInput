# Autocomplete Vue JS 🚀

The **Autocomplete vue** component allow you to create select input with autocomplete drop down. You can select with arraow / enter / click or just fill the field.

Demo here => [Code Sandbox](https://codesandbox.io/s/kugatsuvueautocompleteinput-rsxtj)

## How to use it

Install with npm.

```console
npm i @kugatsu/vueautocompleteinput --save
```

Import with ES6

```javascript
import VueAutocomplete from "@kugatsu/vueautocompleteinput";

components: {
   VueAutocomplete
},
data: function() {
    return {
        articles: [{id: 1, label: "first items"}],
        article: undefined // or {id: 1, label: "first items"} to initialise
    };
},
```

```javascript
<vue-autocomplete :items="articles" v-model="article" property-to-display="label"></vue-autocomplete>
```

## Parameters

| Name              | Type      |                      Default value |
| ----------------- | --------- | ---------------------------------: |
| items             | String    |                           required |
| v-model           | Object    |                           required |
| placeholder       | String    |                             Search |
| emptyResults      | No result |                         No results |
| propertyToDisplay | String    |                              label |
| disable           | Boolean   |                              false |
| closeButon        | Boolean   |                               true |
| classComponent    | String    |                                 "" |
| classInput        | String    |        "autocomplete-input-custom" |
| classWrapper      | String    | "autocomplete-list-wrapper-custom" |
| classList         | String    |         "autocomplete-list-custom" |
| classItem         | String    |    "autocomplete-list-item-custom" |
| classItemActive   | String    |  "autocomplete-item-active-custom" |

## Event throw

| Event    |                   When |
| -------- | ---------------------: |
| selected |      value is selected |
| clear    |         input is empty |
| noResult | filter return no value |

## Slot

1. Item row
   You can huse slot to fully customize item row.

Sample 💩:

```javascript
<template v-slot:listItem="slotProps">
    <h4 style="margin: 0;">{{slotProps.item.title}}</h4>
    <br>
    {{slotProps.item.body}}
</template>
```

2. Close btn

Use **closeButton** scope to personalize your clear button or use **autocomplete-clear-btn** to surcharge style.

```javascript
<template v-slot:closeButton>X</template>
```

#### Customise

The initial style is very basic to let you the power to custumize and integrate easily the component with your website design.

You have the possibility of surchage class or redifign them.

##### All class used

| Class                            |        surchage |
| -------------------------------- | --------------: |
| autocomplete                     |              no |
| autocomplete-input               |              no |
| utocomplete-input-custom         |      classInput |
| autocomplete-list-wrapper        |              no |
| autocomplete-list-wrapper-custom |    classWrapper |
| autocomplete-list                |              no |
| autocomplete-list-custom         |       classList |
| autocomplete-list-item           |              no |
| autocomplete-list-item-custom    |       classItem |
| autocomplete-item-active-custom  | classItemActive |
| search-result-item-disabled      |              no |
| autocomplete-clear-btn           |              no |
