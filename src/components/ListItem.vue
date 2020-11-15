<template>
  <v-list-item>
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
      <v-btn :color="item.color" @click="item.action(index)" small icon>
        <v-icon>{{ item.icon }}</v-icon>
      </v-btn>
    </v-list-item-action>
  </v-list-item>
</template>

<script>
import { toCurrency } from "../utils/index";

export default {
  props: {
    item: Object,
    index: Number
  },
  methods: {
    toCurrency
  },
  computed: {
    actions() {
      return [
        {
          icon: "mdi-plus-circle",
          action: index => this.$emit("increase", index),
          color: "success"
        },
        {
          icon: "mdi-minus-circle",
          action: index => this.$emit("decrease", index),
          color: "secondary"
        },
        {
          icon: "mdi-pencil-circle",
          action: index => this.$emit("update", index),
          color: "primary"
        },
        {
          icon: "mdi-delete-circle",
          action: index => this.$emit("remove", index),
          color: "error"
        }
      ];
    }
  }
};
</script>
