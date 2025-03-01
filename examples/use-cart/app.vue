<script setup lang="ts">
const product = ref();

const { search } = useProductSearch();
const {
  removeItem,
  cartItems,
  count,
  refreshCart,
  totalPrice,
  subtotal,
  shippingTotal,
  changeProductQuantity,
  addProduct,
  addPromotionCode,
} = useCart();
const { getFormattedPrice } = usePrice();
const { unitPrice } = useProductPrice(product);

const promotionCode = ref("SWFRONTENDS");

const proxyAddToCart = async (quantity = 1) => {
  await addProduct({ id: product.value?.id, quantity });
  refreshCart();
};

const changeItemQuantity = async (e: Event) => {
  const target = e.target as HTMLInputElement;
  await changeProductQuantity({
    id: target.id,
    quantity: Number.parseInt(target.value),
  });
  refreshCart();
};

onMounted(async () => {
  refreshCart();
  const productResponse = await search("85a0d7e39bdf49d0a6f6318c6e464cc1");
  product.value = productResponse.product;
});
</script>
<template>
  <div test-id="test-wrapper">
    <div
      v-if="product"
      class="w-full max-w-sm bg-white rounded-lg shadow-md dark:bg-gray-800 dark:border-gray-700"
    >
      <div class="px-5 pb-5">
        <div
          class="text-xl font-semibold tracking-tight text-gray-900 dark:text-white"
        >
          {{ product?.translated.name }}
        </div>

        <div class="flex items-center justify-between mt-4">
          <span class="text-3xl font-bold text-gray-900 dark:text-white">{{
            getFormattedPrice(unitPrice)
          }}</span>
          <button
            @click="proxyAddToCart(1)"
            class="text-white bg-blue-700 hover:bg-blue-800 focus:ring-4 focus:outline-none focus:ring-blue-300 font-medium rounded-lg text-sm px-5 py-2.5 text-center dark:bg-blue-600 dark:hover:bg-blue-700 dark:focus:ring-blue-800"
          >
            🛍 Add to cart
          </button>
        </div>
      </div>
    </div>
    <div
      class="w-full mt-4 max-w-sm bg-white rounded-lg shadow-md dark:bg-gray-800 dark:border-gray-700"
    >
      <div class="px-5 pb-5">
        <h5
          class="text-xl font-semibold tracking-tight text-gray-900 dark:text-white"
        >
          Add Promotion code
        </h5>

        <div class="flex items-center justify-between mt-4">
          <input
            v-model="promotionCode"
            type="text"
            class="inline-flex justify-center items-center mr-4 w-1/2 p-4 text-xs font-semibold text-gray-800 bg-gray-200 rounded-md"
          />
          <button
            @click="addPromotionCode(promotionCode)"
            class="text-white bg-blue-700 hover:bg-blue-800 focus:ring-4 focus:outline-none focus:ring-blue-300 font-medium rounded-lg text-sm px-5 py-2.5 text-center dark:bg-blue-600 dark:hover:bg-blue-700 dark:focus:ring-blue-800"
          >
            Apply a code
          </button>
        </div>
      </div>
    </div>
    <div
      class="w-full mt-8 max-w-sm bg-white rounded-lg shadow-md dark:bg-gray-800 dark:border-gray-700"
    >
      <div class="px-5 pb-5">
        <h5
          class="text-xl font-semibold tracking-tight text-gray-900 dark:text-white"
        >
          Cart items ({{ count }})
        </h5>

        <div class="mt-8">
          <ul
            v-if="cartItems.length"
            class="w-full text-sm font-medium text-gray-900 bg-white rounded-lg border border-gray-200 dark:bg-gray-700 dark:border-gray-600 dark:text-white"
          >
            <li
              v-for="cartItem in cartItems"
              :key="cartItem.id"
              :class="{ 'text-gray-400': cartItem.type == 'promotion' }"
              class="py-2 px-4 w-full rounded-t-lg border-b border-gray-200 dark:border-gray-600"
            >
              {{ cartItem.label }}

              <span v-if="cartItem.type == 'promotion'">
                {{ getFormattedPrice(cartItem?.price?.totalPrice) }}
              </span>
              <div class="inline-flex items-center" v-else>
                <span
                  class="inline-flex justify-center items-center ml-2 w-4 h-4 text-xs font-semibold text-gray-800 bg-gray-200 rounded-full"
                >
                  {{ cartItem.quantity }}
                </span>
                <input
                  class="w-12 ml-6 border-2 border-gray-300 rounded-lg text-center"
                  type="number"
                  v-model="cartItem.quantity"
                  :id="cartItem.id"
                  @change="changeItemQuantity"
                />
                <button
                  class="inline-flex ml-4 px-2 py-2 bg-red-600 hover:bg-red-700 text-white text-sm font-medium rounded-md"
                  @click="removeItem(cartItem)"
                >
                  <svg
                    xmlns="http://www.w3.org/2000/svg"
                    class="h-4 w-4"
                    fill="none"
                    viewBox="0 0 24 24"
                    stroke="currentColor"
                  >
                    <path
                      stroke-linecap="round"
                      stroke-linejoin="round"
                      stroke-width="2"
                      d="M19 7l-.867 12.142A2 2 0 0116.138 21H7.862a2 2 0 01-1.995-1.858L5 7m5 4v6m4-6v6m1-10V4a1 1 0 00-1-1h-4a1 1 0 00-1 1v3M4 7h16"
                    />
                  </svg>
                </button>
              </div>
            </li>
          </ul>
          <div
            v-else
            class="p-4 mb-4 text-sm text-blue-700 bg-blue-100 rounded-lg dark:bg-blue-200 dark:text-blue-800"
            role="alert"
          >
            <span class="font-medium">Cart is empty!</span> There are no items
            in the cart.
          </div>
        </div>
        <div class="mt-8 text-right">
          <div>
            totalPrice: <strong>{{ getFormattedPrice(totalPrice) }}</strong>
          </div>
          <div>
            subtotal: <strong>{{ getFormattedPrice(subtotal) }}</strong>
          </div>
          <div>
            shipping cost:
            <strong>{{ getFormattedPrice(shippingTotal) }}</strong>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>
