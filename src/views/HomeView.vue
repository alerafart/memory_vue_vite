<script setup>
import { ref, reactive } from "vue";

import cardsData from "../data/animals.json";

// duplicate each card of the deck
let duplicateCards = cardsData.concat(cardsData);

// rename each id with a different value, before we had each id twice and we need unique objects to flip each card sepparately
duplicateCards = duplicateCards.map((card, index) => {
  return {
    ...card,
    id: (index + 1).toString().padStart(2, "0"),
  };
});

// shuffle cards mix order
let shuffledCardsData = shuffle(duplicateCards);

let cards = reactive(shuffledCardsData);
cards.forEach((card) => {
  card.isFlipped = false;
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
const pairs = ref([]);
const found = ref([]);
// const flip = ref(null);
// const finished = ref(false);

// flip 2 cards and compare them
function flipCard(card) {
  console.log("flip called", card, typeof card);
  // timerTriggerStart.value.start();
  card.isFlipped = true;
  pairs.value.push(card);
  if (pairs.value.length == 2) {
    console.log(pairs.value[0].image, pairs.value[1].image);
    compare(pairs.value[0].image, pairs.value[1].image);
  }
}
// compares 2 flipped cards
function compare(a, b) {
  if (a == b) {
    console.log("match");
    found.value.push(a, b);
    console.log("pairs found" + found.value);
  } else {
    console.log("no match");

    // unflip cards after 1 second
    pairs.value.forEach((card) => {
      setTimeout(() => {
        card.isFlipped = false;
      }, 1500);
    });
  }
  pairs.value = [];
}
</script>

<template>
  <div class="wrapper flex justify-center">
    <main class="grid grid-cols-6 gap-1">
      <div class="card w-40 h-40" v-for="(card, index) in cards" :key="index">
        <img
          @click="flipCard(card)"
          :src="
            card.isFlipped
              ? '../img/animals/' + card.image
              : '../img/square.jpg'
          "
          :alt="card.id"
        />
      </div>
    </main>
  </div>
</template>
