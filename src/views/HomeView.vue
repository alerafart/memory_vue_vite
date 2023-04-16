<script setup>
import { ref, reactive, onUpdated, onMounted } from "vue";

import cardsData from "../data/animals.json";

//method to choose a number of cards from the whole deck
function pickRandomCards(arr, num) {
  const shuffled = [...arr].sort(() => 0.5 - Math.random());
  return shuffled.slice(0, num);
}

console.log(pickRandomCards(cardsData, 4));
let randomCards = pickRandomCards(cardsData, 9);

// duplicate each card of the deck
let duplicateCards = randomCards.concat(randomCards);

// rename each id with a different value, before we had each id twice and we need unique objects to flip each card sepparately
duplicateCards = duplicateCards.map((card, index) => {
  return {
    ...card,
    id: (index + 1).toString().padStart(2, "0"),
  };
});
const comparedCards = ref([]);
const found = ref([]);
let notFound = ref([]);
let clicksNumber = ref(0);
// let flippedCardsInNotFound = ref();
// shuffle cards mix order
let shuffledCardsData = shuffle(duplicateCards);

let cards = reactive(shuffledCardsData);
cards.forEach((card) => {
  card.isFlipped = false;
  // notFound.value.push(card.image);
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

// flip 2 cards and compare them
function flipCard(card) {
  if (comparedCards.value.length >= 2) {
    // console.log("if comparedCards.value.length >= 2");
    // <-- cannot have more than two cards open at the same time
    unflip(); //<--- abort timeout, unflip right away
  }
  if (card.isFlipped) {
    // console.log("flip card already flipped");
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

onUpdated(() => {
  let flippedNotFound = ref(
    notFound.value.filter((obj) => obj.isFlipped === true)
  );
  if (flippedNotFound.value.length > 2) {
    notFound.value.forEach((kard) => {
      kard.isFlipped = false;
    });
  }
});
</script>

<template>
  <div class="wrapper flex justify-center">
    <main class="grid grid-cols-6 gap-3">
      <div class="card w-40 h-40" v-for="(card, index) in cards" :key="index">
        <img
          class="rounded-lg shadow-lg shadow-green-700 border border-gray"
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
