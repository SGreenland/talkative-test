<script setup>
import SudokuBoard from "./components/SudokuBoard.vue";
import { ref } from "vue";

const arrayToCheck = ref([]);

function handleChange(event) {
  if (event.target.files[0].type != "application/json") {
    alert("Please only upload a valid JSON file.");
    return;
  } else {
    //this was from stackoverflow
    var reader = new FileReader();
    reader.onload = onReaderLoad;
    reader.readAsText(event.target.files[0]);
  }
}

function onReaderLoad(event) {
  //validate format of arrays
  //used chatGPT to generate this (required a lot of back and forth and tweaking)
  const regex =
    /^\[\[(?:"(?:[1-9]|\.)"\s*,\s*){8}"(?:[1-9]|\.)"\s*\](,\s*\[(?:"(?:[1-9]|\.)"\s*,\s*){8}"(?:[1-9]|\.)"\s*\]){8}\]$/;
  const cleanedJsonString = event.target.result.replace(/\s/g, "");
  const errorMessage = `
      Input must be a JSON array containing 9 sub-arrays.
      Each sub-array contains 9 values. Each value represents a cell in that
      row.
      A cell contains either:
      A string value of 1-9, which represents a filled cell of that value.
      A '.' , which represents an unfilled cell.
  `;
  if (cleanedJsonString.match(regex)) {
    arrayToCheck.value = JSON.parse(event.target.result);
  } else alert(errorMessage);
}
</script>

<template>
  <div>
    <div>
      <div class="text-lg font-bold p-4">
        Upload a JSON file to validate Sudoku!
      </div>
      <input @change="handleChange($event)" type="file" accept=".json" />
    </div>
    <!--Suduko board goes here-->
    <SudokuBoard :arrayToCheck="arrayToCheck"> </SudokuBoard>
  </div>
</template>
