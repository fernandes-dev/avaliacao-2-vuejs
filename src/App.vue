<template>
  <v-app>
    <v-app-bar app color="black" height="80px">
      <h3 class="white--text">Lista de Compras</h3>
      <v-spacer></v-spacer>
      <v-form ref="form">
        <v-row class="px-5">
          <v-col class="pt-5 pb-0" cols="4">
            <v-text-field
              :rules="[v => !!v || 'obrigatório']"
              v-model="new_item.name"
              solo
              flat
              dense
              label="Nome"
            ></v-text-field>
          </v-col>
          <v-col class="pt-5 pb-0" cols="2">
            <v-text-field
              :rules="[
                v => !!v || 'obrigatório',
                v =>
                  (v && v.length > 0 && !v.includes(',')) ||
                  'Não é permitido uso da vírgula'
              ]"
              v-model="new_item.price"
              solo
              flat
              dense
              label="Preço"
            ></v-text-field>
          </v-col>
          <v-col class="pt-5 pb-0" cols="3">
            <v-text-field
              :rules="[v => !!v || 'obrigatório']"
              v-model="new_item.qtd"
              solo
              flat
              type="number"
              dense
              label="Quantidade"
            ></v-text-field>
          </v-col>
          <v-col class="pb-0 pt-5" cols="3">
            <v-btn
              :class="new_item.id ? 'orange' : 'success'"
              @click="addItem"
              depressed
              class="white--text"
              block
            >
              Salvar
            </v-btn>
          </v-col>
        </v-row>
      </v-form>
      <v-icon color="white">mdi-list-status</v-icon>
    </v-app-bar>

    <v-main>
      <v-card height="100%" class="mx-auto">
        <v-card-text>
          <v-list v-if="itens.length > 0">
            <v-row justify="center" class="secondary--text mb-5">
              <v-col cols="4">
                <h2>Quantidade total: {{ itens.length }}</h2>
              </v-col>
              <v-col cols="4">
                <h2>Valor total: {{ toCurrency(total) }}</h2>
              </v-col>
              <v-spacer></v-spacer>
              <v-col>
                <v-btn color="primary" outlined @click="itens = []">
                  Limpar Lista
                </v-btn>
              </v-col>
            </v-row>

            <ListItem
              v-for="(item, index) in itens"
              :key="index"
              @increase="increase"
              @decrease="decrease"
              @update="update"
              @remove="remove"
              :item="item"
              :index="index"
            />

            <v-divider></v-divider>
          </v-list>
          <div v-else style="margin-top: 20%;" class="text-center">
            <span style="font-size: 20px;">Lista de compras está vazia!</span>
          </div>
        </v-card-text>
      </v-card>
    </v-main>
  </v-app>
</template>

<script>
import ListItem from "@/components/ListItem";
import { toCurrency } from "./utils/index";
import { v4 as uuid } from "uuid";

export default {
  name: "App",

  components: { ListItem },

  data: () => ({
    new_item: {
      id: null,
      name: null,
      price: null,
      qtd: null
    },
    itens: []
  }),
  computed: {
    total() {
      let total = 0;
      this.itens.forEach(item => {
        total += item.price * item.qtd;
      });
      return total;
    }
  },
  methods: {
    toCurrency,
    addItem() {
      if (this.$refs.form.validate()) {
        if (this.new_item.id) {
          const indexItem = this.itens.findIndex(
            item => this.new_item.id === item.id
          );

          this.itens[indexItem] = { ...this.new_item };
        } else {
          this.new_item.id = uuid();
          this.itens = [{ ...this.new_item }, ...this.itens];
        }

        this.$refs.form.reset();
        this.new_item.id = null;
      }
    },
    increase(index) {
      this.itens[index].qtd++;
    },
    decrease(index) {
      if (this.itens[index].qtd <= 0) {
        this.itens.splice(index, 1);
      } else {
        this.itens[index].qtd--;
      }
    },
    update(index) {
      this.new_item = this.itens[index];
    },
    remove(index) {
      this.itens.splice(index, 1);
    }
  }
};
</script>

<style>
.item-subtitle {
  font-weight: bold;
  font-size: 18px !important;
}
</style>
