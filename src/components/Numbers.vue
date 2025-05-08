<script setup lang="ts">
import { ref, onMounted, computed } from "vue"

import { DynamicScroller } from 'vue-virtual-scroller'
import 'vue-virtual-scroller/dist/vue-virtual-scroller.css'

import { useVirtualList } from '@vueuse/core'
import Grid from "vue-virtual-scroll-grid"


const numbers = ref([]);
const hoveredNumber = ref(undefined)
let limit = ref(100);
let startIndexElementsOnScreen = 0
let endIndexElementsOnScreen = 50
let currentElementsOnScreen = null
let test = ref(100)


// <div v-bind="containerProps" style="height: 300px">
// 		<div v-bind="wrapperProps" class="grid">
// 		<div v-for="item in list" :key="item.index" style="height: 22px" class="number">
// 			<div v-for="number in item.data"> {{number}}</div>
// 		</div>
		
// 		</div>
const { list, containerProps, wrapperProps } = useVirtualList(
  test,
  {
    // Keep `itemHeight` in sync with the item's row.
	itemHeight: 22
  },
)


function sortedNumbers()
{
	let unsortedNumbers = []
	for(let i = 1; i <= limit.value; i++) { unsortedNumbers.push(i) }
	numbers.value = unsortedNumbers.sort(() => Math.random() - 0.5);
}

function isDivisible(item) {
	if(!hoveredNumber.value) return ''
	return item % hoveredNumber.value === 0 ? 'active' : ''
}



onMounted(() => {
	sortedNumbers()
	pageProvider()
})


function styleTest(index) {
	console.log(index)
	if(index == 0 || index == 20 || index == 40 || index == 60 || index == 80 || index == 100) return 'height:22px'
	return ''
}

async function pageProvider(pageNumber, pageSize) {

	// let numberElementsPerRow = []
	// const amountOFRows = limit.value / 20

	// for(let i = 0; i < amountOFRows; i++) {
	// 	startIndexElementsOnScreen = i * 20;
	// 	endIndexElementsOnScreen = 20 * (i + 1);
	// 	numberElementsPerRow.push(numbers.value.slice(startIndexElementsOnScreen, endIndexElementsOnScreen))
	// }

	// test.value= numberElementsPerRow

	
	// console.log('PAGE PROVIDER', pageNumber, pageSize)
	// 0  startIndex = pageNumber * 40
	// 1 startIndex = pageNumber * 40

	startIndexElementsOnScreen = pageNumber * pageSize;
	endIndexElementsOnScreen = pageSize * (pageNumber + 1);


	currentElementsOnScreen = numbers.value.slice(startIndexElementsOnScreen, endIndexElementsOnScreen);
	return  currentElementsOnScreen
}
</script>

<template>
	<div>
		<input type="number" v-model="limit" @blur="sortedNumbers"/><br /><br />
		

		<Grid
			:length="1000"
			:pageProvider="pageProvider"
			:pageSize="40"
			:scrollTo="10"
			class="grid"
			>
			<template v-slot:probe>
				<div class="item">Probe</div>
			</template>
			<template v-slot:default="{ item, style, index }">
  <div :style="style">{{ item }} {{ index }}</div>
</template>
			</Grid>

	</div>
</template>

<style>
.test {
	width: 30px;
	height: 30px;
}
.scroller-wrapper {
  height: 600px;
  overflow-y: auto;
}

.grid {
   display: grid;
  grid-template-columns: repeat(20, 1fr);
}

.grid-item {
  background: #f0f0f0;
  padding: 8px;
  border-radius: 6px;
  text-align: center;
}
.wrapper-numbers {
	max-height: 500px
	
}
.wrapper-list {
	display: grid;
	grid-template-columns: repeat(10, 1fr);
}

.number {
	padding: 5px;
	background-color: lightgrey;
	margin: 5px;
}
.active {
	background-color: red;
}
</style>
