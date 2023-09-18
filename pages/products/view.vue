<template>
  <div class="w-full">
    <ResponseHandlerModal></ResponseHandlerModal>
    <section class="w-full pb-24"></section>
    <section
      class="max-w-screen-xl mx-2 sm:mx-auto px-4 sm:px-6 lg:px-0 py-6 pb-20 sm:py-8 rounded-[2.25rem] sm:rounded-xl bg-white shadow-lg sm:shadow-md transform lg:-translate-y-12"
    >
      <v-app>
        <v-row>
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
            <v-card-title class="text-xl font-semibold tracking-tight">
              {{ product[0].title }}
            </v-card-title>
            <v-card-text>
              <v-row align="center" class="mb-1"> </v-row>
              <div class="pb-1">
                <v-icon class="mt-2 text-subtitle-1 mdi mdi-office-building">
                  {{ product[0].shopName }}
                </v-icon>
              </div>
              <div class="pb-1 d-flex" :style="{ 'justify-content': 'unset' }">
                <v-icon class="text-subtitle-1 mdi mdi-map-marker"> </v-icon>
                <span>
                  {{
                    product[0].reedemPostalCode +
                    " " +
                    product[0].reedemCity +
                    " " +
                    product[0].reedemAddress
                  }}
                </span>
              </div>
              <div class="pb-1 d-flex" :style="{ 'justify-content': 'unset' }">
                <v-icon class="text-subtitle-1 mdi mdi-shape-outline"> </v-icon>
                <span>
                  {{ product[0].category }}
                </span>
              </div>
            </v-card-text>
            <v-divider class="mx-4"></v-divider>
            <div class="font-italic">
              <p>Leírás:</p>
            </div>

            <div class="text-justify">
              <p>{{ product[0].description }}</p>
            </div>
            <div>
              <v-chip v-for="(tag, index) in tags" :key="index">
                <span>{{ tag }}</span>
              </v-chip>
              <p></p>
            </div>
            <div class="font-italic">
              <p>Mennyiség:</p>
            </div>

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
                  readonly
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
              <span class="text-3xl font-bold">{{
                totalValue + " " + product[0].currency
              }}</span>
            </div>
            <base-button
              @click="
                addToCart({
                  id: product[0].id,
                  title: product[0].title,
                  quantity: product[0].quantity,
                  grossPrice: product[0].grossPrice,
                  totalValue: totalValue,
                  currency: product[0].currency,
                  quantityToBuy: quantityToBuy
                })
              "
              class="px-8 xl:px-10 py-3 mt-6 bg-gradient-to-r from-[#468ef9] to-[#0c66ee] text-white"
            >
              Kosárba
            </base-button>
          </div>
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
    addToCart(product) {
      this.$store.dispatch("addToCart", product);
    },
  },

  mounted() {
    this.totalValue = this.product[0].grossPrice;
    this.tags = this.product[0].tags.split(",");
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
.v-chip {
  background: #0c66ee !important;
  color: white !important;
}
.v-text-field .v-input__control {
  border-radius: 50px !important;
}
</style>