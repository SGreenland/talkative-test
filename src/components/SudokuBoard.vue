<script setup>
import { ref, computed, watch } from "vue";

const props = defineProps({
  arrayToCheck: Array,
});

const computedArray = computed(() => props.arrayToCheck);

const dataToDisplay = ref([]);
const rowDuplicates = ref([]);
const colDuplicates = ref([]);
const boxDuplicates = ref([]);

watch(computedArray, (value) => {
  dataToDisplay.value = value.flat();
  validateSolution();
});

function getTextClass() {
  //wanted to just display actual errors but settled for whole grid in the end
  if (
    rowDuplicates.value.concat(colDuplicates.value).concat(boxDuplicates.value)
      .length
  ) {
    return "text-red-600";
  } else return "text-green-600";
}

function onlyNums(array) {
  return array.filter((x) => x != ".");
}

function validateSolution() {
  //reset data after each file
  rowDuplicates.value = [];
  colDuplicates.value = [];
  boxDuplicates.value = [];
  //variables;
  let x = 0;
  let y = 0;
  let columns = [];
  let boxes = [];
  let boxIndexes = [0, 1, 2, 9, 10, 11, 18, 19, 20];
  //get columns;
  while (x < computedArray.value.length) {
    columns.push(computedArray.value.map((row) => row[x]));
    x++;
    y++;
  }
  //get boxes
  function getBoxes(indexes) {
    let box = [];
    for (let i = 0; i <= indexes[8]; i++) {
      if (boxIndexes.includes(i)) {
        box.push(dataToDisplay.value[i]);
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
    array.forEach((box) => {
      const justNumbers = onlyNums(box);
      let duplicates = justNumbers.filter((x, index) => {
        return justNumbers.indexOf(x) !== index;
      });
      if (duplicates.length) {
        duplicates.forEach((dup) => {
          switch (type) {
            case "box":
              boxDuplicates.value.push(dup);
              break;
            case "row":
              rowDuplicates.value.push(dup);
              break;
            case "col":
              colDuplicates.value.push(dup);
              break;
            default:
              return;
          }
        });
      }
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
    alert("Invalid Solution!");
  } else alert("Successfully solved so far!");
}
</script>

<template>
  <div class="sudoku-grid">
    <div
      class="border grid place-items-center"
      v-for="(val, index) in dataToDisplay"
      :key="index"
    >
      <div :class="getTextClass(val)">{{ val != "." ? val : "" }}</div>
    </div>
  </div>
</template>

<style scoped>
.sudoku-grid {
  display: grid;
  padding: 1rem;
  grid-template-rows: repeat(9, 1fr);
  grid-template-columns: repeat(9, 1fr);
  width: 500px;
  height: 500px;
}
</style>
