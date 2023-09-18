<template>
  <div class="w-full">
    <section id="products" class="w-full pb-24"></section>
    <section
      class="max-w-screen-xl mx-2 sm:mx-auto px-4 sm:px-6 lg:px-0 py-6 pb-20 sm:py-8 rounded-[2.25rem] sm:rounded-xl bg-white shadow-lg sm:shadow-md transform lg:-translate-y-12"
    >
      <v-app>
        <v-stepper v-model="e6" vertical>
          <v-stepper-step :complete="e6 > 1" step="1">
            Kosár véglegesítése
            <!-- <small>Summarize if needed</small> -->
          </v-stepper-step>

          <v-stepper-content step="1">
            <v-card v-if="cartItems.length > 0">
              <v-card-text>
                <div class="">
                  <v-list flat>
                    <v-list-item-group>
                      <v-list-item
                        v-for="(cartItem, index) in cartItems"
                        :key="index"
                        :ripple="false"
                      >
                        <v-list-item-content>
                          <v-card-title>
                            {{ cartItem.title }}
                          </v-card-title>
                        </v-list-item-content>
                        <v-list-item-content class="justify-content-center">
                          <div class="d-flex quantity-to-buy-container">
                            <div class="decrease-quantity-to-buy">
                              <base-button
                                class="relative mr-2"
                                @click="decreaseQuantityToBuy(index)"
                              >
                                <v-icon
                                  class="px-6 xl:px-6 py-3 mt-2 bg-inherit text-gradient border-[#0c66ee] mdi mdi-minus-circle-outline"
                                >
                                </v-icon>
                              </base-button>
                            </div>
                            <template>
                              <v-text-field
                                v-model="cartItem.quantityToBuy"
                                outlined
                                type="number"
                                class="quantity-to-buy"
                                readonly
                                :hide-details="true"
                              ></v-text-field>
                            </template>
                            <div class="increase-quantity-to-buy">
                              <base-button
                                class="relative ml-2"
                                @click="increaseQuantityToBuy(index)"
                              >
                                <v-icon
                                  class="px-6 xl:px-6 py-3 mt-2 bg-inherit text-gradient border-[#0c66ee] mdi mdi-plus-circle-outline"
                                >
                                </v-icon>
                              </base-button>
                            </div>
                          </div>
                        </v-list-item-content>
                        <v-list-item-content>
                          {{ cartItem.totalValue + " " + cartItem.currency }}
                        </v-list-item-content>
                        <v-list-item-content>
                          <v-btn
                            class="mx-2"
                            icon
                            small
                            color="primary"
                            @click="removeTodo(index)"
                          >
                            <v-icon class="mdi mdi-close-circle-outline">
                            </v-icon>
                          </v-btn>
                        </v-list-item-content>
                      </v-list-item>
                    </v-list-item-group>
                  </v-list>
                </div>
              </v-card-text>
            </v-card>
            <v-card v-else>
              <v-card-title> Nincs megjeleníthető elem </v-card-title>
            </v-card>
            <v-btn
              color="primary mt-4"
              @click="e6 = 2"
              v-if="cartItems.length > 0"
            >
              Tovább
            </v-btn>
          </v-stepper-content>

          <v-stepper-step :complete="e6 > 2" step="2"> Adatok </v-stepper-step>

          <v-stepper-content step="2">
            <v-card>
              <v-form ref="form" v-model="valid" lazy-validation>
                <v-text-field
                  v-model="name"
                  :rules="nameRules"
                  label="Name"
                  required
                ></v-text-field>

                <v-text-field
                  v-model="email"
                  :rules="emailRules"
                  label="E-mail"
                  required
                ></v-text-field>

                <v-checkbox
                  v-model="checkbox"
                  :rules="[(v) => !!v || 'You must agree to continue!']"
                  label="Do you agree?"
                  required
                ></v-checkbox>

              </v-form>
            </v-card>
            <v-btn color="primary" @click="e6 = 3"> Tovább </v-btn>
            <v-btn text @click="e6 = 1"> Vissza </v-btn>
          </v-stepper-content>

          <v-stepper-step :complete="e6 > 3" step="3"> Fizetés </v-stepper-step>

          <v-stepper-content step="3">
            <v-card
              color="grey lighten-1"
              class="mb-12"
              height="200px"
            ></v-card>
            <v-btn color="primary" @click="e6 = 4"> Tovább </v-btn>
            <v-btn text @click="e6 = 2"> Vissza </v-btn>
          </v-stepper-content>

          <v-stepper-step step="4"> Visszaigazolás</v-stepper-step>
          <v-stepper-content step="4">
            <v-card
              color="grey lighten-1"
              class="mb-12"
              height="200px"
            ></v-card>
            <v-btn color="primary" @click="e6 = 1"> Tovább </v-btn>
            <v-btn text @click="e6 = 3"> Vissza </v-btn>
          </v-stepper-content>
        </v-stepper>
      </v-app>
    </section>
  </div>
</template>

<script>
export default {
  data() {
    return {
      e6: 1,
      cartItems: this.$store.state.cartItems,
      valid: true,
      name: '',
      nameRules: [
        v => !!v || 'Name is required',
        v => (v && v.length <= 10) || 'Name must be less than 10 characters',
      ],
      email: '',
      emailRules: [
        v => !!v || 'E-mail is required',
        v => /.+@.+\..+/.test(v) || 'E-mail must be valid',
      ],
      checkbox: false,
    };
  },
  mounted() {},
  methods: {
    //

    increaseQuantityToBuy(index) {
      this.cartItems[index].quantityToBuy++;
      this.cartItems[index].totalValue =
        this.cartItems[index].quantityToBuy * this.cartItems[index].grossPrice;
    },
    decreaseQuantityToBuy(index) {
      if (this.cartItems[index].quantityToBuy > 1) {
        this.cartItems[index].quantityToBuy--;
        this.cartItems[index].totalValue =
          this.cartItems[index].quantityToBuy *
          this.cartItems[index].grossPrice;
      }
    },
    removeTodo(index) {
      this.cartItems.splice(index, 1);
    },
  },
};
</script>

<style scoped>
.cart-item {
  width: 100%;
  margin-bottom: 10px;
}
.v-list-item__content,
.quantity-to-buy-container {
  justify-content: center;
}
.v-list-item__content > * {
  flex: unset;
}
.v-list-item {
  border: solid 0.5px lightgray;
  border-radius: 20px;
  margin-bottom: 10px;
}
</style>