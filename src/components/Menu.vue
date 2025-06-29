<script setup>
import { ref, computed, watch } from 'vue'
import pizzaData from '@/assets/data/pizza-list.json'
import toppingData from '@/assets/data/topping-list.json'
import sizeData from '@/assets/data/size-list.json'
import '@/assets/styles/menu.scss'
import CheesePizza from '@/assets/images/pizza/Cheese Pizza.png'
import ClassicalPizza from '@/assets/images/pizza/Classical Pizza.png'
import VeggiePizza from '@/assets/images/pizza/Veggie Pizza.png'
import OrderSuccess from '@/assets/images/icons/SuccessOrder.svg'
import Offer from '@/assets/images/ribbon.svg'

const pizzas = ref(pizzaData?.data || [])
const toppings = ref(toppingData?.data || [])
const sizes = ref(sizeData?.data || [])
const showModal = ref(false)

const selectedPizza = ref('')
const selectedSize = ref('')
const selectedToppings = ref([])

const selectedPizzaDetails = computed(() => {
  return pizzas.value.find((pizza) => pizza.id === selectedPizza.value) || null
})

const selectedSizeDetails = computed(() => {
  return sizes.value.find((size) => size.id === selectedSize.value) || null
})

const selectedToppingDetails = computed(() => {
  return toppings.value.filter((topping) => selectedToppings.value.includes(topping.id))
})

const allowedToppingIds = computed(() => {
  return selectedPizzaDetails.value?.toppings || []
})

const totalSelectedTopping = computed(() => {
  return selectedToppingDetails.value.reduce((acc, item) => acc + item.price, 0)
})

const total = computed(() => {
  if (selectedPizzaDetails.value && selectedSizeDetails.value) {
    return (
      selectedPizzaDetails.value.price +
      selectedSizeDetails.value.extra_price +
      totalSelectedTopping.value
    )
  }
  return 0
})

watch(selectedPizza, () => {
  selectedToppings.value = selectedToppings.value.filter((toppingId) =>
    allowedToppingIds.value.includes(toppingId),
  )
})

watch(selectedPizza, (newSelectedPizza) => {
  if (newSelectedPizza && !selectedSize.value && sizes.value.length > 0) {
    selectedSize.value = sizes.value[0].id
  }
})

function placeOrder() {
  showModal.value = true
  selectedPizza.value = ''
  selectedSize.value = ''
  selectedToppings.value = []
}

const PizzaImage = {
  1: CheesePizza,
  2: ClassicalPizza,
  3: VeggiePizza,
}
</script>

<template>
  <div class="menu">
    <form>
      <h2>Choose your pizza</h2>
      <div class="menu__choose-pizza-container">
        <div v-for="pizza in pizzas" :key="pizza.id">
          <label class="menu__choose-pizza">
            <div>
              <img :src="PizzaImage[pizza.id]" class="menu__pizza-image" alt="Pizza" />
              <input type="radio" name="pizza" :value="pizza.id" v-model="selectedPizza" />
            </div>

            <div class="menu__choose-pizza-content">
              <img :src="Offer" class="menu__offer-image" v-if="pizza.discount.is_active" />
              <strong>{{ pizza.name }}</strong>

              <p v-if="pizza.discount.is_active">
                ${{ pizza.discount.final_price }}
                <span class="menu__discount"> ${{ pizza.price }}</span>
              </p>
              <p v-else>${{ pizza.price }}</p>
            </div>
          </label>
        </div>
      </div>

      <h2>Custom Pizza</h2>
      <div>
        <h5 class="menu__size-title">Size</h5>
        <div class="menu__size-container">
          <div v-for="size in sizes" :key="size.id">
            <label>
              <input type="radio" name="size" :value="size.id" v-model="selectedSize" />
              {{ size.name }}
              <span class="menu__size-price" v-if="size.extra_price != 0"
                >(+${{ size.extra_price }})</span
              >
            </label>
          </div>
        </div>
      </div>

      <h5 class="menu__size-title">Topping</h5>
      <div class="menu__topping-container">
        <div
          v-for="topping in toppings"
          :key="topping.id"
          class="menu__topping-menu"
          :class="{
            'menu__topping-menu--selected': selectedToppings.includes(topping.id),
            disabled: !allowedToppingIds.includes(topping.id),
          }"
        >
          <label>
            <input
              type="checkbox"
              name="topping[]"
              :value="topping.id"
              v-model="selectedToppings"
              :disabled="!allowedToppingIds.includes(topping.id)"
            />
            {{ topping.name }}
          </label>
        </div>
      </div>
    </form>

    <form class="menu__payment-summary">
      <h2>Payment summary</h2>

      <p v-if="selectedPizzaDetails">
        <span>{{ selectedPizzaDetails.name }} </span>
        <span>{{ selectedPizzaDetails.price }}$</span>
      </p>
      <p v-if="selectedSizeDetails">
        <span>Size - {{ selectedSizeDetails.name }} </span>
        <span>{{ selectedSizeDetails.extra_price }}$</span>
      </p>
      <div v-if="selectedToppingDetails.length > 0">
        <ul>
          <li
            v-for="selectedToppingDetail in selectedToppingDetails"
            :key="selectedToppingDetail.id"
          >
            <span>{{ selectedToppingDetail.name }}</span>
            <span>{{ selectedToppingDetail.price }}$</span>
          </li>
        </ul>
      </div>
      <hr />
      <div v-if="total">
        <p class="menu__total">
          <span>Total Price</span> <span class="menu__total-price">${{ total }}</span>
        </p>

        <button @click="placeOrder">Order Now</button>
      </div>
    </form>
    <div v-if="showModal" class="menu__modal-overlay" @click.self="showModal = false">
      <div class="menu__modal-content">
        <img :src="OrderSuccess" alt="OrderSuccess" />
        <h5 class="menu__order-title">Order Success</h5>
        <p>Thank you, we have received your order successfully</p>
        <button @click="showModal = false">Close</button>
      </div>
    </div>
  </div>
</template>
