<script setup>
import { ref, computed, watch } from "vue";

const props = defineProps({
  arrayToCheck: Array,
});

const computedArray = computed(() => props.arrayToCheck);

const dataToDisplay = ref(Array(81));
const rowDuplicates = ref([]);
const colDuplicates = ref([]);
const boxDuplicates = ref([]);
const validationMessage = ref("");

watch(computedArray, (value) => {
  dataToDisplay.value = value.flat();
  validateSolution();
});

function getTextClass(index) {
  if (
    rowDuplicates.value.concat(colDuplicates.value).concat(boxDuplicates.value)
      .includes(index)
  ) {
    return "text-red-600 font-bold";
  } else return "text-green-600";
}

function validateSolution() {
  //reset data after each file
  rowDuplicates.value = [];
  colDuplicates.value = [];
  boxDuplicates.value = [];
  //variables;
  let x = 0;
  // let y = 0;
  let columns = [];
  let boxes = [];
  let boxIndexes = [0, 1, 2, 9, 10, 11, 18, 19, 20];
  //get columns;
  while (x < computedArray.value.length) {
    columns.push(computedArray.value.map((row) => row[x]));
    x++;
    // y++;
  }
  //get boxes
  function getBoxes(indexes) {
    let box = [];
    for (let i = 0; i <= indexes[8]; i++) {
      if (boxIndexes.includes(i)) {
        box.push(({value: dataToDisplay.value[i], flatArrIndex: i}));
      }
    }
    boxes.push(box);
  }
  //gets first box
  getBoxes(boxIndexes);

  //gets other boxes
  while (boxes.length <= 8) {
    const lastIndex = boxIndexes[8];
    if (lastIndex == 26) {
      boxIndexes = [27, 28, 29, 36, 37, 38, 45, 46, 47];
    } else if (lastIndex == 53) {
      boxIndexes = [54, 55, 56, 63, 64, 65, 72, 73, 74];
    } else {
      boxIndexes = boxIndexes.map((x) => (x += 3));
    }

    getBoxes(boxIndexes);
  }

  function checkForDuplicates(array, type) {
    array.forEach((subArray,index) => {
      //push flattened array index if duplicates
      const arrayIndex = index;
      subArray.filter((x, index) => {
        if(type == 'box'){
          if(x.value != "." && subArray.map(box => box.value).indexOf(x.value) != index){
            boxDuplicates.value.push(x.flatArrIndex);
          }
        }
        else if(x != "." && subArray.indexOf(x) != index){
          switch (type) {
            case "row":
              rowDuplicates.value.push(arrayIndex * 9 + index);
              break;
            case "col":
              colDuplicates.value.push(index * 9 + arrayIndex);
              break;
            default:
              return;
          }
        }
      })
    });
  }

  checkForDuplicates(computedArray.value, "row");
  checkForDuplicates(columns, "col");
  checkForDuplicates(boxes, "box");

  if (
    rowDuplicates.value.length ||
    colDuplicates.value.length ||
    boxDuplicates.value.length
  ) {
    validationMessage.value = "Invalid entries!";
  } else validationMessage.value = "So far, so good!";
}
</script>

<template>
  <div v-if="computedArray.length">{{ validationMessage }}</div>
  <div class="sudoku-grid m-2 border border-gray-400">
    <div
      class="border border-gray-300 grid place-items-center"
      v-for="(val, index) in dataToDisplay"
      :key="index"
    >
      <div :class="getTextClass(index)">{{ val != "." ? val : "" }}</div>
    </div>
  </div>
</template>

<style scoped>
.sudoku-grid {
  display: grid;
  grid-template-rows: repeat(9, 1fr);
  grid-template-columns: repeat(9, 1fr);
  width: 500px;
  height: 500px;
}
</style>
