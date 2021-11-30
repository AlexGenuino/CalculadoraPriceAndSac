<template>
  <v-container>
    <v-form v-model="valid">
      <v-row>
        <v-col cols="12" md="4">
          <v-text-field
            v-model="totalValue"
            :counter="50"
            label="Valor Total"
            type="number"
            prefix="R$"
            required
          ></v-text-field>
        </v-col>
      </v-row>
      <v-row>
        <v-col cols="12" md="4">
          <v-text-field
            v-model="Period"
            :counter="3"
            type="number"
            label="Periodo"
            required
          ></v-text-field>
        </v-col>
      </v-row>
      <v-row>
        <v-col cols="12" md="4">
          <v-text-field
            v-model="taxa"
            label="Taxa Juros"
            type="number"
            prefix="%"
            required
          ></v-text-field>
        </v-col>
      </v-row>
      <v-row>
        <v-col cols="12" md="4">
          <v-select :items="itemsJuros" label="Tipo" dense></v-select>
        </v-col>
      </v-row>
      <v-row>
        <v-col cols="12" md="4">
          <v-btn class="mr-4" @click="CalculateSac"> Calcular </v-btn>
        </v-col>
      </v-row>
    </v-form>
    <div v-if="ResultSac.length > 0" class="column-data">
      <v-card>
        <v-card-title>
          <v-text-field
            v-model="search"
            append-icon="mdi-magnify"
            label="Search"
            single-line
            hide-details
          ></v-text-field>
        </v-card-title>
        <v-data-table
          :headers="headers"
          :items="ResultSac"
          single-expand
          item-key="index"
          show-expand
          :search="search"
          class="elevation-1"
        >
          <template v-slot:items="{ index }">
            {{ index + 1 }}
          </template>
        </v-data-table>
      </v-card>
    </div>
  </v-container>
</template>

<script>
import { Sac } from "../formulas/sac";

export default {
  data() {
    return {
      taxa: null,
      totalValue: null,
      Period: null,
      ResultSac: [],

      itemsJuros: ["SAC", "PRICE"],
      search: "",
      headers: [
        {
          text: "SAC",
          align: "start",
          value: "index",
        },
        { text: "Amortização", value: "amortization", filterable: false },
        { text: "Juros", value: "interestRateBalance", filterable: false },
        { text: "Prestação", value: "portion", filterable: false },
        { text: "Valor Pago", value: "valuePaid", filterable: false },
        { text: "Saldo Devedor", value: "balance", filterable: false },
      ],

      desserts: [],
    };
  },
  methods: {
    CalculateSac() {
      const sac = new Sac(this.totalValue, this.Period, this.taxa);
      this.ResultSac = sac.doSac();
      console.log(this.ResultSac);
    },
  },
};
</script>

<style scoped>
.column-data {
  margin-top: 30px;
}
</style>
