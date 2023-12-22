<script setup lang="ts">

import axios from "axios";
import { ref, computed } from "vue";

const text = ref('');
const enteredText = ref('');

const computedText = computed(() => {
	return text.value.split('');
})

const gameOver = computed(() => {
	return enteredText.value.length >= text.value.length;
})

const highlightedText = computed(() => {
	return computedText.value.map((char, index) => { 
		if(index > enteredText.value.length - 1)
			return char;
		const enteredChar = enteredText.value[index];
		return char === enteredChar ? wrapCorrect(char) : wrapIncorrect(char);
	})
})

const decomputedText = computed(() => {
	return highlightedText.value.join('');
})

const getText = async () => {
	const req = `https://fish-text.ru/get`;

	const { data } = await axios(req);
	text.value = data.text;
}

const wrapCorrect = (str: string) => {
	return `<span class="correct">${str}</span>`;
}

const wrapIncorrect = (str: string) => {
	return `<span class="incorrect">${str}</span>`;
}

const restart = () => {
	getText();
	enteredText.value = "";
}

const keyPress = (event: any) => {
	const c = event.key;
	enteredText.value = enteredText.value.concat(c);
}

getText();
document.addEventListener("keypress", keyPress);

</script>

<template>
	<main>
		<span v-html="decomputedText"></span>
		<button v-if="gameOver" @click="restart()">Restart</button>
	</main>
	
</template>

<style scoped>

main {
	height: 100vh;
	width: 100%;
	display: flex;
	justify-content: center;
	align-items: center;
	display: flex;
	flex-direction: column;
	gap: 5rem;
}

span {
	max-width: 1280px;
	font-size: large;
	color: whitesmoke;
}

/* CSS */
button {
  --c: #fff;
  /* text color */
  background: linear-gradient(90deg, #0000 33%, #fff5, #0000 67%) var(--_p,100%)/300% no-repeat,
    #004dff;
  /* background color */
  color: #0000;
  border: none;
  transform: perspective(500px) rotateY(calc(20deg*var(--_i,-1)));
  text-shadow: calc(var(--_i,-1)* 0.08em) -.01em 0   var(--c),
    calc(var(--_i,-1)*-0.08em)  .01em 2px #0004;
  outline-offset: .1em;
  transition: 0.3s;
}

button:hover,
button:focus-visible {
  --_p: 0%;
  --_i: 1;
}

button:active {
  text-shadow: none;
  color: var(--c);
  box-shadow: inset 0 0 9e9q #0005;
  transition: 0s;
}

button {
  font-weight: bold;
  font-size: 2rem;
  margin: 0;
  cursor: pointer;
  padding: .1em .3em;
}

</style>
