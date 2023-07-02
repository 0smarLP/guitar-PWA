<script setup>
import { ref, onMounted, watch } from "vue";
import { db } from "./data/guitars";
import Header from "./components/Header.vue";
import Guitar from "./components/Guitars.vue";
import Footer from "./components/Footer.vue";

//Ref se usa para variables independientes
const guitarras = ref([]);
const cart = ref([]);
const guitar = ref({});

watch(
  cart,
  () => {
    saveLocalStorage();
  },
  {
    deep: true,
  }
);

onMounted(() => {
  guitarras.value = db;
  guitar.value = db[3];

  const cartLocalStorage = localStorage.getItem("cart");
  if (cartLocalStorage) {
    cart.value = JSON.parse(cartLocalStorage);
  }
});

const addCart = (guitarra) => {
  const existCart = cart.value.findIndex(
    (product) => product.id === guitarra.id
  );
  if (existCart >= 0) {
    cart.value[existCart].cantidad++;
  } else {
    guitarra.cantidad = 1;
    cart.value.push(guitarra);
  }
};

const emptyCart = () => {
  cart.value = [];
};

const saveLocalStorage = () => {
  localStorage.setItem("cart", JSON.stringify(cart.value));
};

const removeItem = (id) => {
  const index = cart.value.findIndex((product) => product.id === id);
  cart.value.splice(index, 1);
};

const increaseQuantity = (id) => {
  const index = cart.value.findIndex((product) => product.id === id);
  if (cart.value[index].cantidad >= 5) return;
  cart.value[index].cantidad++;
};

const decreaseQuantity = (id) => {
  const index = cart.value.findIndex((product) => product.id === id);
  if (cart.value[index].cantidad === 1) {
    cart.value.splice(index, 1);
  } else {
    cart.value[index].cantidad--;
  }
};
</script>

<template>
  <Header
    :cart="cart"
    :guitar="guitar"
    @increase-quantity="increaseQuantity"
    @decrease-quantity="decreaseQuantity"
    @add-cart="addCart"
    @remove-item="removeItem"
    @empty-cart="emptyCart"
  />
  <main class="container-xl mt-5">
    <h2 class="text-center">Nuestra Colección</h2>

    <div class="row mt-5">
      <Guitar
        v-for="guitarra in guitarras"
        :key="guitarra.id"
        :guitarra="guitarra"
        @add-cart="addCart"
      />
    </div>
  </main>

  <Footer />
</template>