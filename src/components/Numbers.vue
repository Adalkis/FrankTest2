<script setup lang="ts">
import {ref, reactive, watch, onMounted} from "vue"
import { RecycleScroller } from 'vue-virtual-scroller'
import 'vue-virtual-scroller/dist/vue-virtual-scroller.css'
const numbers = [];
let limit = ref(10000);
let originalRandomNumbers = ref(new Map([]))
let randomNumbers = ref(new Map())
let itemList = ref([])


onMounted(() => {sortedNumbers()})

function sortedNumbers() {
	let numbers = [];
	for(let i = 0; i <= limit.value; i++) { 
		numbers.push(i)
	}

	let sortedNumbers = numbers.sort(() => Math.random() - 0.5);

	const sortedNumbersMap = numbers.reduce((mapAccumulator, randomNumber) => {
		mapAccumulator.set(randomNumber, {n: randomNumber})
		return mapAccumulator
	},new Map())

	originalRandomNumbers.value = new Map(sortedNumbersMap)
	randomNumbers.value = sortedNumbersMap
	itemList.value = Array.from(sortedNumbersMap.values());

}

function hov(number) {
	const squareRoot = parseInt(Math.sqrt(number));

	for(let i = 1 ; i <= squareRoot; i++) {
		const pairDivisor = number / i
		const isNotDecimal = number % i == 0

		if(isNotDecimal) {
		randomNumbers.value.set(i, {n: i, class:'active'})
		randomNumbers.value.set(pairDivisor, {n: pairDivisor, class:'active'})
		}
	}
}

function reset()
{
	randomNumbers.value = new Map(originalRandomNumbers.value)
}
</script>


<template>
	<div class="wrapper-numbers">
		<input type="number" v-model="limit" @input="sortedNumbers"/><br /><br />
		<RecycleScroller
			class="scroller"
			:items="itemList"
			:item-size="32"
			key-field="n"
			v-slot="{ item }"
			direction="vertical"
			:grid="true"
		>
		<div :class="`number ${item.class}`">
		{{ item.n }}
		</div>
		</RecycleScroller>
	</div>
</template>

<style scoped>
.wrapper-numbers {
	width: 1200px
}
.scroller {
  height: 100vh;
}

.number {
	display: inline-block;
	padding: 5px;
	background-color: lightgrey;
	margin: 5px;
}

.active {
	background-color: red;
}
</style>
