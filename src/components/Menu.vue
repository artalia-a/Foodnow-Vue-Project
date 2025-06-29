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

// Reactive state to store the list of pizzas
const pizzas = ref(pizzaData?.data || [])
// Reactive state to store the list of toppings
const toppings = ref(toppingData?.data || [])
// Reactive state to store the list of sizes
const sizes = ref(sizeData?.data || [])
// Reactive state to control the visibility of the modal
const showModal = ref(false)

// Reactive state to store the selected pizza id
const selectedPizza = ref('')
// Reactive state to store the selected size id
const selectedSize = ref('')
// Reactive state to store the selected topping id
const selectedToppings = ref([])

// Computed to get the full detail of the selected pizza based on the id
const selectedPizzaDetails = computed(() => {
  return pizzas.value.find((pizza) => pizza.id === selectedPizza.value) || null
})
// Computed to get the full details of the selected size based on the id
const selectedSizeDetails = computed(() => {
  return sizes.value.find((size) => size.id === selectedSize.value) || null
})
// Computed to get the full details of the selected topping based on the id
const selectedToppingDetails = computed(() => {
  return toppings.value.filter((topping) => selectedToppings.value.includes(topping.id))
})
// Computed to get the list of allowed topping id for the selected pizza
const allowedToppingIds = computed(() => {
  return selectedPizzaDetails.value?.toppings || []
})
// Computed to calculate the total price of the selected toppings
const totalSelectedTopping = computed(() => {
  return selectedToppingDetails.value.reduce((acc, item) => acc + item.price, 0)
})
// Computed to calculated the final total price
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

// Watcher to monitor changes in selected pizza
// If the pizza changes, filter out toppings that are not allowed for the new pizza
watch(selectedPizza, () => {
  selectedToppings.value = selectedToppings.value.filter((toppingId) =>
    allowedToppingIds.value.includes(toppingId),
  )
})

// Whatcher to monitor changes in selected pizza
// If a pizza is selected and no size is selected, automatically selected the first available size
watch(selectedPizza, (newSelectedPizza) => {
  if (newSelectedPizza && !selectedSize.value && sizes.value.length > 0) {
    selectedSize.value = sizes.value[0].id
  }
})

// Function to handle placing an order
function placeOrder() {
  showModal.value = true
  selectedPizza.value = ''
  selectedSize.value = ''
  selectedToppings.value = []
}

// Mapping pizza id to images according to id
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
        <!-- Loop though the list of pizzas -->
        <div v-for="pizza in pizzas" :key="pizza.id">
          <label class="menu__choose-pizza">
            <div>
              <!-- Pizza images based on its id, mapped from PizzaImage object -->
              <img :src="PizzaImage[pizza.id]" class="menu__pizza-image" alt="Pizza" />
              <!-- Radio button to select the pizza, bound to selectedPizza -->
              <input type="radio" name="pizza" :value="pizza.id" v-model="selectedPizza" />
            </div>

            <div class="menu__choose-pizza-content">
              <!-- Offer badge image, only show if the discount is active -->
              <img :src="Offer" class="menu__offer-image" v-if="pizza.discount.is_active" />
              <strong>{{ pizza.name }}</strong>
              <!-- If there's an active discount, show discounted price and original price crocced out -->
              <p v-if="pizza.discount.is_active">
                ${{ pizza.discount.final_price }}
                <span class="menu__discount"> ${{ pizza.price }}</span>
              </p>
              <!-- If no discount, show normal price -->
              <p v-else>${{ pizza.price }}</p>
            </div>
          </label>
        </div>
      </div>

      <h2>Custom Pizza</h2>
      <div>
        <h5 class="menu__size-title">Size</h5>
        <div class="menu__size-container">
          <!-- Loop though the list of sizes -->
          <div v-for="size in sizes" :key="size.id">
            <label>
              <!-- Radio button to select size, bound to selectedSize -->
              <input type="radio" name="size" :value="size.id" v-model="selectedSize" />
              {{ size.name }}
              <!-- If the size has extra price, display it next to the name -->
              <span class="menu__size-price" v-if="size.extra_price != 0"
                >(+${{ size.extra_price }})</span
              >
            </label>
          </div>
        </div>
      </div>

      <h5 class="menu__size-title">Topping</h5>
      <div class="menu__topping-container">
        <!-- Loop through the list of toppings -->
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
            <!-- Checkbox to selected topping, bound to selectedToppings array, disabled if topping is not allowed for the selected pizza -->
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
      <!-- Show selected pizza details if a pizza is selected -->
      <p v-if="selectedPizzaDetails">
        <span>{{ selectedPizzaDetails.name }} </span>
        <span>{{ selectedPizzaDetails.price }}$</span>
      </p>
      <!-- Show selected size details if the size is selected -->
      <p v-if="selectedSizeDetails">
        <span>Size - {{ selectedSizeDetails.name }} </span>
        <span>{{ selectedSizeDetails.extra_price }}$</span>
      </p>
      <!-- Show selected toppings if any topping are selected -->
      <div v-if="selectedToppingDetails.length > 0">
        <ul>
          <!-- Loop through selected toppings and display name & price -->
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
      <!-- Show the total price and order button  -->
      <div v-if="total">
        <p class="menu__total">
          <span>Total Price</span><span>${{ total }}</span>
        </p>

        <button @click="placeOrder">Order Now</button>
      </div>
    </form>
    <!-- Order success modal, show when showModal is true -->
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
