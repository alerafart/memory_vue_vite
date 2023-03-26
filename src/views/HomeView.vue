<script setup>
import { ref, reactive, onUpdated, onMounted } from "vue";

import cardsData from "../data/animals.json";
onMounted(() => {
  console.log("onMounted notfound value :", notFound.value);
  console.log("onMounted found value :", found.value);
});
onUpdated(() => {
  console.log("onUpdated notfound value :", notFound.value);
  console.log("onUpdated found value :", found.value);
});
// duplicate each card of the deck
let duplicateCards = cardsData.concat(cardsData);

// rename each id with a different value, before we had each id twice and we need unique objects to flip each card sepparately
duplicateCards = duplicateCards.map((card, index) => {
  return {
    ...card,
    id: (index + 1).toString().padStart(2, "0"),
  };
});
const pairs = ref([]);
const found = ref([]);
let notFound = ref([]);

// shuffle cards mix order
let shuffledCardsData = shuffle(duplicateCards);

let cards = reactive(shuffledCardsData);
cards.forEach((card) => {
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

// const flip = ref(null);
// const finished = ref(false);

// flip 2 cards and compare them
function flipCard(card) {
  // timerTriggerStart.value.start();
  card.isFlipped = true;

  pairs.value.push(card);
  if (pairs.value.length == 2) {
    compare(pairs.value[0].image, pairs.value[1].image);
  }
}
// compares 2 flipped cards
function compare(a, b) {
  if (a == b) {
    console.log("match", a, b);
    // found.value.push(a, b);

    let pairOfCardsFound = cards.filter((element) => element.image == a);
    //returns object with 2 cards found which share same image

    found.value.push(...pairOfCardsFound);

    // remove found values from not found
    notFound.value = notFound.value.filter(
      (item) => !found.value.includes(item)
    );
  } else {
    // unflip cards after 1 second
    pairs.value.forEach((card) => {
      setTimeout(() => {
        card.isFlipped = false;
      }, 1500);
    });
  }
  pairs.value = [];
}

// TODO
// watch flipped cards number in the not found array
// il flipped cards >2 unflipp all from not found
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
