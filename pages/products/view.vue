<template>
  <div class="w-full">
    <ResponseHandlerModal></ResponseHandlerModal>
    <section class="w-full pb-24"></section>
    <section
      class="max-w-screen-xl mx-2 sm:mx-auto px-4 sm:px-6 lg:px-0 py-6 pb-20 sm:py-8 rounded-[2.25rem] sm:rounded-xl bg-white shadow-lg sm:shadow-md transform lg:-translate-y-12"
    >
      <v-app>
        <v-row>
          <!-- <div class="flex"> -->
          <!-- Carousel -->
          <div
            :style="{ width: '700px' }"
            class="m-4 col-12 col-md-5 col-lg-5 col-xl-5"
          >
            <template>
              <v-carousel hide-delimiters>
                <v-carousel-item
                  v-for="(item, i) in items"
                  :key="i"
                  :src="item.src"
                  eager
                ></v-carousel-item>
              </v-carousel>
            </template>
          </div>

          <!-- Card details -->
          <div class="p-8 col-12 col-md-6 col-lg-6 col-xl-6">
            <h5 class="text-xl font-semibold tracking-tight">
              {{ product[0].title }}
            </h5>

            <div>
              <p>{{ product[0].shopName }}</p>
            </div>
            <div>
              <p>Beváltóhely</p>
            </div>
            <div>
              <p>{{ product[0].category }}</p>
            </div>
            <div>
              <p>{{ product[0].description }}</p>
            </div>
            <div>
              <v-chip v-for="(tag, index) in tags" :key="index">
                <span>{{ tag }}</span>
              </v-chip>
              <p></p>
            </div>
            <p>Mennyiség:</p>
            <div class="flex quantity-to-buy-container">
              <div class="decrease-quantity-to-buy">
                <base-button
                  class="relative mr-2"
                  @click="decreaseQuantityToBuy"
                >
                  <v-icon
                    class="px-6 xl:px-6 py-3 mt-2 bg-inherit text-gradient border-[#0c66ee] mdi mdi-minus-circle-outline"
                  >
                  </v-icon>
                </base-button>
              </div>
              <template>
                <v-text-field
                  v-model="quantityToBuy"
                  outlined
                  type="number"
                  class="quantity-to-buy"
                ></v-text-field>
              </template>
              <div class="increase-quantity-to-buy">
                <base-button
                  class="relative ml-2"
                  @click="increaseQuantityToBuy"
                >
                  <v-icon
                    class="px-6 xl:px-6 py-3 mt-2 bg-inherit text-gradient border-[#0c66ee] mdi mdi-plus-circle-outline"
                  >
                  </v-icon>
                </base-button>
              </div>
            </div>
            <div class="flex items-center justify-between">
              <span class="text-3xl font-bold">{{ totalValue + " Ft" }}</span>
            </div>
            <div>
              <v-icon class="text-subtitle-1 mdi mdi-cart-outline"> </v-icon>
            </div>
          </div>
          <!-- </div> -->
        </v-row>
      </v-app>
    </section>
  </div>
</template>

<script>
import { APIGET, APIPOST } from "../../api/apiHelper";
import ResponseHandlerModal from "../../components/ResponseHandlerModal.vue";

export default {
  name: "ProductViewPage",
  components: {
    ResponseHandlerModal,
  },
  data() {
    return {
      product: [],
      quantityToBuy: 1,
      totalValue: 0,
      tags: [],
      items: [
        {
          src: require("~/assets/img/7446783_3661698.png"),
        },
        {
          src: require("~/assets/img/11235588_10814.jpg"),
        },
        {
          src: require("~/assets/img/eco-system-5019188-4196246.png"),
        },
        {
          src: require("~/assets/img/save-planet-8429926-6723330.png"),
        },
      ],
    };
  },
  async asyncData({ query }) {
    const productId = parseInt(query.id);

    try {
      const response = await APIPOST("getViewData", {
        id: productId,
      });
      const product = response.data;

      return { product };
    } catch (error) {
      console.log(error);
      return { error };
    }
  },

  watch: {
    quantityToBuy(newVal) {
      if (newVal !== null && newVal < 1) {
        this.quantityToBuy = 1; // Ha negatív számot írnak be, automatikusan 1 kerül a helyére
        console.log("mínuszba mentünk");
      }
    },
  },

  computed: {
    //
  },
  methods: {
    increaseQuantityToBuy() {
      this.quantityToBuy++;
      this.totalValue = this.quantityToBuy * this.product[0].grossPrice;
    },
    decreaseQuantityToBuy() {
      if (this.quantityToBuy > 1) {
        this.quantityToBuy--;
        this.totalValue = this.quantityToBuy * this.product[0].grossPrice;
      }
    },
  },

  mounted() {
    this.totalValue = this.product[0].grossPrice;
    this.tags = this.product[0].tags.split(",");

    console.log(this.tags);
    // this.$store.state.responseHandler = {
    //   title: 'Teszt',
    //   show: true,
    //   type: "success",

    // };
  },
};
export const head = {
  link: [{}],
};
</script>

<style >
[type="number"]:focus {
  --tw-ring-offset-shadow: unset;
  --tw-ring-shadow: unset;
}
.quantity-to-buy {
  max-width: 40px;
}
.quantity-to-buy-container {
  position: relative;
}
</style>