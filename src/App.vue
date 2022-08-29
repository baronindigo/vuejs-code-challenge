<template>
	<main>
		<div class="ly-row">
			<div v-show="isVisible===true" class="card bg-accent text-white text-left">
				<ul>
					<li>Selected Fruit is: {{survey.fruit}}</li>
					<li>Selected Food is: {{survey.food}}</li>
					<li>My favorite Food is: {{survey.favorite}}</li>
					<li>My Food Budget is: {{survey.budget}}</li>
				</ul>
			</div>
		</div>
		<div>
			<a href="#" @click="openModal" class="btn btn-primary" data-open-modal="surveymodal" ref="modalOpener">Show Modal</a>
		</div>

		<div class="overlay" ref="overlay">
			<div id="surveymodal" class="modal modal-md" ref="modal">
				<div class="ly-spacebetween-center mb-1">
					<h2 class="font-weight-bold">Supermarket Survey!</h2>
					<a href="#" @click="closeModal" class="modal-close btn btn-noborder btn-icon"><i class="lpi-close text-dark"></i><span>Close</span></a>
				</div>
				<div class="ly-spacearound-center text-left mb-1">
					<div class="custom-even-2">
						<DropdownFilter
							name="select-food"
							classes="h4"
							label="Select a Fruit"
							placeholder="Select one"
							:payload="items.fruits"
							@surveySubmit="getFruit" />

						<DropdownFilter
							name="select-food"
							classes="h4"
							label="Select a Food Item"
							withFilter="true"
							placeholder="Select one"
							:payload="items.vegetables" 
							@surveySubmit="getFood" />
					</div>

					<div class="custom-even-2">
						<InputWordCount 
							max=20
							classes="h4" 
							name="favorite-food" 
							label="Favorite Food" 
							exampleText="E.g. Seafood Pizza"
							@surveySubmit="getFavoriteFood" />

						<InputNumber 
							type="currency"
							maxValue="999999999"
							classes="h4"
							name="food-budget"
							label="Food Budget"
							@submitFunction="submitFunction" 
							@surveySubmit="getBudget" />
					</div>
					<div class="ly-end-center text-right">
						<button @click="closeModal" class="btn btn-lg">Cancel</button>
						<button @click="submitFunction" class="btn btn-primary btn-lg">Submit</button>
					</div>
				</div>
				
			</div>
		</div>
	</main>
</template>

<script lang="ts">
import { defineComponent, ref } from 'vue';
import InputWordCount from "./components/InputWordCount.vue";
import InputNumber from "./components/InputNumber.vue";
import DropdownFilter from "./components/DropdownFilter.vue";

import Survey from './types/Survey';

export default defineComponent({
	name: 'App',
	components: {
		InputWordCount,
		InputNumber,
		DropdownFilter,
	},

	setup() {
		const config = require('./config.js');
		const items = config.default;

		const overlay = ref();
		const modal = ref();

		const isVisible = ref(false)

		const survey = ref<Survey>({
			fruit: '',
			food: '',
			favorite: '',
			budget: ''
		});

		// This shows the overlay and the modal by changing 
		// the style display attribute to flex
		const openModal = () => {				
			overlay.value.style.display = 'flex';
			modal.value.style.display = 'block';
			isVisible.value = false;
		}

		// This returns the display style attribute
		// to blank
		const closeModal = () => {
			overlay.value.style.display = '';
			modal.value.style.display = '';
		}

		// Once we trigger the submit button
		// we show the card with the values
		// received from the modal survey
		const submitFunction = () => {
			isVisible.value = true;
			closeModal()
		}

		// We get the selected fruit
		// from the component
		const getFruit = (value:string) => {
			survey.value.fruit = value;
		}

		// We get the selected food
		// from the component
		const getFood = (value:string) => {
			survey.value.food = value;
		}

		// We get the favorite food
		// from the component
		const getFavoriteFood = (value:string) => {
			survey.value.favorite = value;
		}

		// We get the budget
		// from the component
		const getBudget = (value:string) => {
			survey.value.budget = value;
		}

		return {
			items,
			overlay,
			modal,
			survey,
			isVisible,
			openModal,
			closeModal,
			submitFunction,
			getFruit,
			getFood,
			getFavoriteFood,
			getBudget,
		}
	}
});
</script>

<style lang="scss">
$tablet:  750px;
$desktop:  980px;

@mixin mobile {
  @media (max-width: #{$tablet - 1px}) {
    @content;
  }
}

@mixin tablet {
  @media (min-width: #{$tablet}) {
    @content;
  }
}

@mixin desktop {
  @media (min-width: #{$desktop}) {
    @content;
  }
}

#surveymodal {
	position: relative;
	
	.modal-close {
		position: absolute;
    	top: 10px;
    	right: 10px;
	}

	.custom-even-2 {
		display: grid;
		grid-gap: 1rem;
		grid-template-columns: 1fr;

		@include tablet {
			display: grid;
			grid-template-columns: 1fr 1fr;
		}
	}
}


#app {	
	font-family: Avenir, Helvetica, Arial, sans-serif;
    -webkit-font-smoothing: antialiased;
    -moz-osx-font-smoothing: grayscale;
    text-align: center;
    color: #2c3e50;
    margin-top: 60px;
}	
.info-container {
    display: flex;
    justify-content: space-between;
}
</style>
