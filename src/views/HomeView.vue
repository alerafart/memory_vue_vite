<script setup>
import {
  ref,
  reactive,
  onUpdated,
  onMounted,
  watch,
  watchPostEffect,
} from "vue";

import cardsData from "../data/animals.json";

//method to choose a number of cards from the whole deck
function pickRandomCards(arr, num) {
  const shuffled = [...arr].sort(() => 0.5 - Math.random());
  return shuffled.slice(0, num);
}

let randomCards = pickRandomCards(cardsData, 8);

// duplicate each card of the deck
let duplicateCards = randomCards.concat(randomCards);

// rename each id with a different value, before we had each id twice and we need unique objects to flip each card sepparately
duplicateCards = duplicateCards.map((card, index) => {
  return {
    ...card,
    id: (index + 1).toString().padStart(2, "0"),
  };
});
let totalCardsNumber = duplicateCards.length;
console.log(totalCardsNumber);
const comparedCards = ref([]);
const found = ref([]);
let notFound = ref([]);
let clicksNumber = ref(0);
let isMatchFinished = ref(false);
// shuffle cards mix order
let shuffledCardsData = shuffle(duplicateCards);

let gridCards = reactive(shuffledCardsData);
gridCards.forEach((card) => {
  card.isFlipped = false;
  notFound.value.push(card);
});

function shuffle(array) {
  const newArray = [...array];
  const length = newArray.length;
  for (let start = 0; start < length; start++) {
    const randomPosition = Math.floor(
      (newArray.length - start) * Math.random()
    ); // generate random integer between 0 and newArray.length - start - 1,
    //and assign it to randomPosition, to be sure we only shuffle the remaining elements in the array.
    const randomItem = newArray.splice(randomPosition, 1); //remove element at randomPosition from the newArray, and assign it to randomItem.
    newArray.push(...randomItem); //add the removed item from the newArray back to the end of the newArray
    //to effectively shuffle
  }
  return newArray;
}

let pairsRemaining = ref(gridCards.length / 2);

// flip 2 cards and compare them
function flipCard(card) {
  if (comparedCards.value.length >= 2) {
    // <-- cannot have more than two cards open at the same time
    unflip(); //<--- abort timeout, unflip right away
  }
  if (card.isFlipped) {
    //<--- prevent flipping the same card twice
    return;
  }
  card.isFlipped = true;

  clicksNumber.value += 1;
  comparedCards.value.push(card);
  if (comparedCards.value.length === 2) {
    compare(comparedCards.value[0], comparedCards.value[1]);
  }
}

let timeoutId = null;

function unflip() {
  console.log("unflip");
  if (timeoutId) {
    console.log("unflip if timeout id clear", timeoutId);
    clearTimeout(timeoutId);
    timeoutId = null;
  }
  comparedCards.value.forEach((card) => (card.isFlipped = false));
  comparedCards.value = [];
}

// compares 2 flipped cards
function compare(a, b) {
  if (a.image == b.image) {
    found.value.push(a, b);
    // remove found values from not found
    notFound.value = notFound.value.filter(
      (item) => !found.value.includes(item)
    );

    comparedCards.value = [];
  } else {
    timeoutId = setTimeout(unflip, 1500); // <--- wait to unflip
  }
}

// watchPostEffect(() => {
//   /* executed after Vue updates */
//   if (isMatchFinished.value) {
//     // .value = true;
//     alert("win");
//   }
// });
// if (notFound.value.length === 0) {
//
//   } alert("win");
// watch(
//   () => isMatchFinished.value,
//   () => {
//     // fires only when state.someObject is replaced
//     setTimeout(alert("win"), 3000);
//   }
// );
// // watch(isMatchFinished.value, (newisMatchFinished) => {
//   console.log(`x is ${newisMatchFinished}`);
// });
onUpdated(() => {
  console.log(gridCards);
  pairsRemaining.value = notFound.value.length / 2;
});
</script>

<template>
  <main class="w-full flex justify-center">
    <div
      class="wrapper flex-col justify-center align-center content-center m-10 w-5/6"
    >
      <div
        class="flex justify-between items-center w-full border-b pb-2 mb-4 border-green-500"
      >
        <div class="flex justify-center items-center my-2">
          <span class="text-2xl mx-4">Pairs remaining</span>
          <div
            class="flex justify-center text-4xl items-center border border-green rounded-full w-16 h-16"
          >
            {{ pairsRemaining }}
          </div>
        </div>
        <div class="flex justify-center items-center my-2">
          <span class="text-2xl mx-4">You clicked</span>
          <div
            class="flex justify-center text-4xl items-center border border-green rounded-full w-16 h-16"
          >
            {{ clicksNumber }}
          </div>
          <span class="text-2xl mx-4">times</span>
        </div>
      </div>
      <section class="grid grid-cols-4 gap-3 text-center">
        <div class="card" v-for="(card, index) in gridCards" :key="index">
          <img
            class="rounded-lg shadow-lg shadow-green-700 border border-gray w-52 h-52"
            @click="flipCard(card)"
            :src="
              card.isFlipped
                ? '../img/animals/' + card.image
                : '../img/question.jpg'
            "
            :alt="card.id"
          />
        </div>
      </section>
    </div>
  </main>
</template>
