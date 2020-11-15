<template>
  <v-app>
    <v-app-bar app color="primary">
      <h3 class="white--text">Lista de Compras</h3>
      <v-spacer></v-spacer>
      <v-form ref="form">
        <v-row class="px-5">
          <v-col class="py-0" cols="4">
            <v-text-field
              v-model="new_item.name"
              solo
              flat
              hide-details
              dense
              label="Nome"
            ></v-text-field>
          </v-col>
          <v-col class="py-0" cols="2">
            <v-text-field
              v-model="new_item.price"
              solo
              flat
              hide-details
              dense
              label="Preço"
            ></v-text-field>
          </v-col>
          <v-col class="py-0" cols="3">
            <v-text-field
              v-model="new_item.qtd"
              solo
              flat
              hide-details
              type="number"
              dense
              label="Quantidade"
            ></v-text-field>
          </v-col>
          <v-col class="py-0" cols="3">
            <v-btn @click="addItem" depressed color="success" block
              >Salvar</v-btn
            >
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
            <v-list-item v-for="(item, index) in itens" :key="index">
              <v-list-item-subtitle
                class="item-subtitle"
                v-text="'Nome: ' + item.name"
              ></v-list-item-subtitle>
              <v-list-item-subtitle
                class="item-subtitle"
                v-text="'Valor: ' + toCurrency(item.price)"
              ></v-list-item-subtitle>
              <v-list-item-subtitle
                class="item-subtitle"
                v-text="'Quantidade: ' + item.qtd"
              ></v-list-item-subtitle>
              <v-list-item-subtitle class="item-subtitle">
                Total do item:
                {{ toCurrency(item.qtd * item.price) }}
              </v-list-item-subtitle>
              <v-list-item-action
                class="mr-2"
                v-for="(item, actionIndex) in actions"
                :key="actionIndex"
              >
                <v-btn
                  :color="item.color"
                  @click="item.action(index)"
                  small
                  icon
                >
                  <v-icon>{{ item.icon }}</v-icon>
                </v-btn>
              </v-list-item-action>
            </v-list-item>
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
import { v4 as uuid } from "uuid";
export default {
  name: "App",

  components: {},

  data: () => ({
    new_item: {
      id: null,
      name: "Banana",
      price: 2,
      qtd: 1
    },
    itens: []
  }),
  computed: {
    actions() {
      return [
        {
          icon: "mdi-plus-circle",
          action: index => this.increase(index),
          color: "success"
        },
        {
          icon: "mdi-minus-circle",
          action: index => this.decrease(index),
          color: "secondary"
        },
        {
          icon: "mdi-pencil-circle",
          action: index => this.update(index),
          color: "primary"
        },
        {
          icon: "mdi-delete-circle",
          action: index => this.remove(index),
          color: "error"
        }
      ];
    },
    total() {
      let total = 0;
      this.itens.forEach(item => {
        total += item.price * item.qtd;
      });
      return total;
    }
  },
  methods: {
    toCurrency(value) {
      return new Intl.NumberFormat("pt-BR", {
        style: "currency",
        currency: "BRL"
      }).format(value);
    },
    addItem() {
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
