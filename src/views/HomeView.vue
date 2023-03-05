<script setup>
import { ref, reactive, onMounted, onUpdated, toRefs } from "vue";
// import Vue from "vue";
import cardsData from "../data/animals.json";
console.log(cardsData);
function shuffle(array) {
  const newArray = [...array];
  const length = newArray.length;

  for (let start = 0; start < length; start++) {
    const randomPosition = Math.floor(
      (newArray.length - start) * Math.random()
    );
    const randomItem = newArray.splice(randomPosition, 1);
    newArray.push(...randomItem);
  }
  return newArray;
}

let shuffledCardsData = shuffle(cardsData);
let cards = reactive(shuffledCardsData);

onUpdated(() => {
  // console.log(...cards, "onUpdated");
});

const pairs = ref([]);
const found = ref([]);
// const flip = ref(null);
// const finished = ref(false);

// flip 2 cards and compare them
function flipCard(card) {
  console.log("flip called", card);
  // timerTriggerStart.value.start();
  card.isFlipped = true;
  pairs.value.push(card);
  if (pairs.value.length == 2) {
    console.log(pairs.value[0].image, pairs.value[1].image);
    compare(pairs.value[0].image, pairs.value[1].image);
  }
  // Vue.set(card, "isFlipped", true);
  // <-- update the property with Vue.set()
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
