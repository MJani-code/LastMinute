<template>
  <div class="w-full">
    <section id="products" class="w-full pb-24"></section>
    <section
      class="max-w-screen-xl mx-2 sm:mx-auto px-4 sm:px-6 lg:px-0 py-6 pb-20 sm:py-8 rounded-[2.25rem] sm:rounded-xl bg-white shadow-lg sm:shadow-md transform lg:-translate-y-12"
    >
      <v-app>
        <v-stepper v-model="e6" vertical class="m-4">
          <v-stepper-step :complete="e6 > 1" step="1">
            Kosár véglegesítése
            <!-- <small>Summarize if needed</small> -->
          </v-stepper-step>

          <v-stepper-content step="1">
            <v-card v-if="cart.items.length > 0">
              <v-card-text>
                  <v-list flat>
                    <v-list-item-group>
                      <v-list-item
                        v-for="(item, index) in cart.items"
                        :key="index"
                        :ripple="false"
                        class="col col-sm-12 col-md-12"
                      >
                        <v-list-item-content>
                          <v-card-title>
                            {{ item.title }}
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
                                v-model="item.quantityToBuy"
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
                          <v-card-title>
                            {{ item.totalValue + " " + item.currency }}
                          </v-card-title>
                        </v-list-item-content>
                        <v-list-item-content>
                          <v-btn
                            class="mx-2"
                            icon
                            small
                            color="primary"
                            @click="removeTodo(item, index)"
                          >
                            <v-icon class="mdi mdi-close-circle-outline">
                            </v-icon>
                          </v-btn>
                        </v-list-item-content>
                      </v-list-item>
                    </v-list-item-group>
                  </v-list>
                Összesen:
                <v-card-title v-model="cart.cartValue">
                {{ cart.cartValue + " " + cart.items[0].currency}}
              </v-card-title>
              </v-card-text>
            </v-card>
            <v-card v-else>
              <v-card-title> Nincs megjeleníthető elem </v-card-title>
            </v-card>
            <v-btn
              color="primary mt-4"
              @click="e6 = 2"
              v-if="cart.items.length > 0"
            >
              Tovább
            </v-btn>
          </v-stepper-content>

          <v-stepper-step :complete="e6 > 2" step="2"> Adatok </v-stepper-step>

          <v-stepper-content step="2">
            <v-card class="mb-4 pb-4">
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
                  v-model="checkboxAszf"
                  :rules="checkboxRulesAszf"
                  label="Elfogadom az általános szerződési feltételket"
                  required
                ></v-checkbox>

                <v-checkbox
                  v-model="checkboxGdpr"
                  :rules="checkboxRulesGdpr"
                  label="Elfogadom az adatvédelmi szabályzatot"
                  required
                ></v-checkbox>
              </v-form>
            </v-card>
            <v-btn
              color="primary"
              :disabled="!isNextButtonVisible"
              @click="e6 = 3"
            >
              Tovább
            </v-btn>
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
      cart:{
        items: this.$store.state.cart.items,
        cartValue: this.$store.state.cart.cartValue,
      },
      valid: false,
      name: "",
      nameRules: [
        (v) => !!v || "Kötelező mező",
      ],
      email: "",
      emailRules: [
        (v) => !!v || "Kötelező mező",
        (v) => /.+@.+\..+/.test(v) || "Nem valós email cím",
      ],
      checkboxRulesAszf: [(v) => !!v || "Kötelező elfogadni!"],
      checkboxRulesGdpr: [(v) => !!v || "Kötelező elfogadni!"],
      checkboxAszf: false,
      checkboxGdpr: false,
    };
  },
  computed: {
    isNextButtonVisible() {
      return (
        this.nameRules.every((rule) => rule(this.name) === true) &&
        this.emailRules.every((rule) => rule(this.email) === true) &&
        this.checkboxRulesAszf.every((rule) => rule(this.checkboxAszf) === true) &&
        this.checkboxRulesGdpr.every((rule) => rule(this.checkboxGdpr) === true)
      );
    },
  },
  mounted() {
    this.valid = false;
    console.log(this.$store.state.cart);
  },
  methods: {
    validate() {
      this.$refs.form.validate();
    },
    increaseQuantityToBuy(index) {
      this.cart.items[index].quantityToBuy++;
      this.cart.items[index].totalValue =
        this.cart.items[index].quantityToBuy * this.cart.items[index].grossPrice;

        this.cart.cartValue +=
        this.cart.items[index].grossPrice;

        this.$store.state.cart.cartValue +=
        this.cart.items[index].grossPrice;
    },
    decreaseQuantityToBuy(index) {
      if (this.cart.items[index].quantityToBuy > 1) {
        this.cart.items[index].quantityToBuy--;
        this.cart.items[index].totalValue =
          this.cart.items[index].quantityToBuy *
          this.cart.items[index].grossPrice;

          this.cart.cartValue -=
          this.cart.items[index].grossPrice;

          this.$store.state.cart.cartValue -=
          this.cart.items[index].grossPrice;
      }
    },
    removeTodo(item, index) {
      this.cart.items.splice(index, 1);
      console.log(item.quantityToBuy, item.grossPrice);

      this.cart.cartValue -= item.quantityToBuy * item.grossPrice

      this.$store.state.cart.cartValue -= item.quantityToBuy * item.grossPrice
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

@media (max-width: 576px) {
  .v-list-item{
    display: block;
  }
  .v-input__control{
    width: max-content;
  }
}

/* Kis kijelzők (tablet és nagyobb telefonok) */
@media (min-width: 577px) and (max-width: 768px) {
  .v-list-item{
    display: block;
  }
  .v-input__control{
    width: max-content;
  }
}

</style>

<style>
.v-text-field__slot input[type="number"]{
  text-align: center !important;
}

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