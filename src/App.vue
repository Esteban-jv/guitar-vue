<script setup>
  import { ref, reactive, onMounted, watch } from 'vue'
  import { db } from './data/guitarras'
  import Guitar from './components/Guitar.vue';
  import Header from './components/Header.vue';
  import Footer from './components/Footer.vue';

  const guitars = ref([])
  const cart = ref([])
  const guitar = ref({})
  /*
  const state = reactive({
    guitars: []
  })
  */

  watch(cart, () => {
    saveInLocalStorage();
  }, {
    deep: true //Si la estructura es muy grande, puede afectar el performance
  })

  onMounted(() => {
    guitars.value = db
    // state.guitars = db
    guitar.value = db[3]

    const storageCart = localStorage.getItem('cart')
    if(storageCart) {
        cart.value = JSON.parse(storageCart)
    }
  })

  const saveInLocalStorage = () => {
    localStorage.setItem('cart', JSON.stringify(cart.value))
  }

  const addToCart = (guitar) => {
        const cartExists = cart.value.findIndex(product => product.id === guitar.id)
        if(cartExists >= 0) {
            cart.value[cartExists].stock++
        } else {
            guitar.stock = 1
            cart.value.push(guitar)
        }
    }
    const decrementStock = (id) => {
        const index = cart.value.findIndex(product => product.id === id)
        cart.value[index].stock--
    }
    const incrementStock = (id) => {
        const index = cart.value.findIndex(product => product.id === id)
        cart.value[index].stock++
    }

    const deleteProduct = (id) => {
        cart.value = cart.value.filter( product => product.id !== id )
    }

    const emptyCart = () => {
        cart.value = []

    }

</script>

<template>
    <Header
        :cart="cart"
        :guitar="guitar"
        @increment-stock="incrementStock"
        @decrement-stock="decrementStock"
        @add-to-cart="addToCart"
        @delete-product="deleteProduct"
        @empty-cart="emptyCart"
    />
    <main class="container-xl mt-5">
        <h2 class="text-center">Nuestra Colección</h2>
        <div class="row mt-5">
            <Guitar
                v-for="guitar in guitars"
                :guitar="guitar"
                @add-to-cart="addToCart"
            />
        </div>
    </main>
    <Footer />
</template>