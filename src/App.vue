<script setup>
import TheHeader from "@/components/TheHeader.vue";
import ProductCard from "@/components/ProductCard.vue";


import { useProductStore } from "./stores/ProductStore";
import { useCartStore } from "./stores/CartStore";
const cartStore = useCartStore()
const productStore = useProductStore()
productStore.fill()



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
      <AppButton @click="cartStore.unDo">Undo</AppButton>
      <AppButton class="ml-2" @click="cartStore.reDo">Re-do</AppButton>
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
