<script setup>
import TheHeader from "@/components/TheHeader.vue";
import ProductCard from "@/components/ProductCard.vue";


import { useProductStore } from "./stores/ProductStore";
import { useCartStore } from "./stores/CartStore";
import { ref, toRef, reactive } from "vue";

const productStore = useProductStore()
const cartStore = useCartStore()

productStore.fill()

const future = reactive([])
const history = reactive([])

const doingHistory = ref(false)

const unDo = () => {
  if(history.length === 1) return
  doingHistory.value = true
  future.push(history.pop())
  cartStore.$state = JSON.parse(history.at(-1))
  doingHistory.value = false
}

const reDo = () => {
  const latestState = future.pop()
  if(!latestState) return
  doingHistory.value = true
  history.push(latestState)
  cartStore.$state = JSON.parse(latestState)
  doingHistory.value = false
}


history.push(JSON.stringify(cartStore.$state))
cartStore.$subscribe((mutation, state) => {
  if(!doingHistory.value) {
    history.push(JSON.stringify(state))
    future.splice(0, future.length)
  }
})

cartStore.$onAction(({
  name,
  store,
  args,
  after,
  onError
}) => {
  if(name === 'addItems') {
    after(() => {
      console.log(args[0]);
    }),
    onError((error) => {
      console.log(error.message);
    })
  }
})

</script>

<template>
  <div class="container">
    <TheHeader />
    <div class="mb-5 flex justify-center">
      <AppButton @click="unDo">Undo</AppButton>
      <AppButton class="ml-2" @click="reDo">Re-do</AppButton>
    </div>
    <ul class="sm:flex flex-wrap lg:flex-nowrap gap-5">
      <ProductCard
        v-for="product in productStore.products"
        :key="product.name"
        :product="product"
        @addToCart="cartStore.addItems($event, product)"
      />
    </ul>
  </div>
</template>
