<template>
  <v-container style="height: 200vh">
    <div class="topo-page">
      <v-form v-model="valid" style="width: 50%">
        <v-row>
          <v-col cols="12" md="8">
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
          <v-col cols="12" md="8">
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
          <v-col cols="12" md="8">
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
          <v-col cols="12" md="8">
            <v-select
              :items="itemsJuros"
              v-model="Selected"
              label="Tipo"
              dense
            ></v-select>
          </v-col>
        </v-row>
        <v-row>
          <v-col cols="12" md="12">
            <v-btn class="mr-4" @click="Calcular"> Calcular </v-btn>
            <v-btn class="mr-4" @click="ResetCampos"> Limpar Campos </v-btn>
          </v-col>
        </v-row>
      </v-form>
      <v-card v-if="Result.length > 0" class="mx-auto ajust-tamanho" max-width="344" outlined>
        <v-list-item three-line>
          <v-list-item-content>
            <div class="text-overline mb-4">CALCULADORA SAC/PRICE</div>
            <v-list-item-title class="text-h5 mb-1">
              {{ tipoFinancimento }}
            </v-list-item-title>
            <v-list-item-subtitle
              >Condições do Financiamento</v-list-item-subtitle
            >
            <v-list-item>
              <v-list-item-content>
                <v-list-item-title class="mt-4"
                  >Valor da Prestação</v-list-item-title
                >
                <v-list-item-subtitle>{{
                  valorPrestacao
                }}</v-list-item-subtitle>

                <v-list-item-title class="mt-4">Juros Total</v-list-item-title>
                <v-list-item-subtitle>{{
                  valorTotalJuros
                }}</v-list-item-subtitle>

                <v-list-item-title class="mt-4"
                  >N de Parcelas</v-list-item-title
                >
                <v-list-item-subtitle>{{
                  numberoParcelas
                }}</v-list-item-subtitle>

                <v-list-item-title class="mt-4">Juros Taxa</v-list-item-title>
                <v-list-item-subtitle>% {{ jurosTaxa }}</v-list-item-subtitle>

                <v-list-item-title class="mt-4"
                  >Valor do Financiamento</v-list-item-title
                >
                <v-list-item-subtitle>{{
                  valorTotalFinanciamento
                }}</v-list-item-subtitle>
              </v-list-item-content>
            </v-list-item>
          </v-list-item-content>

          <v-list-item-avatar tile size="80" color="grey"></v-list-item-avatar>
        </v-list-item>
      </v-card>
    </div>
    <div v-if="Result.length > 0" class="column-data">
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
          :items="Result"
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
import { Financiamento } from "../formulas/financiamento";

export default {
  data() {
    return {
      taxa: null,
      totalValue: null,
      Period: null,
      Result: [],

      ResultPrice: null,

      Selected: "",

      Card: {
        tipoFinancimento: "",
        valorPrestacao: "",
        valorTotalFinanciamento: "",
        valorTotalJuros: "",
        numberoParcelas: "",
        jurosTaxa: "",
      },

      itemsJuros: ["SAC", "PRICE"],
      search: "",
      headers: [
        {
          text: this.tipoFinancimento,
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
      const sac = new Financiamento(this.totalValue, this.taxa, this.Period);
      sac.formatarDados();
      var SacResult = sac.financiarSac();

      this.Result = SacResult.prestacoes;
      console.log(SacResult);
      this.jurosTaxa = this.taxa;
      this.numberoParcelas = this.Period;
      this.tipoFinancimento = this.Selected;
      this.valorPrestacao = "VÁRIÁVEL";
      this.valorTotalFinanciamento = SacResult.valorTotalFinanciamento;
      this.valorTotalJuros = SacResult.valorTotalJuros;
    },
    CalculatePrice() {
      const price = new Financiamento(this.totalValue, this.taxa, this.Period);
      price.formatarDados();
      var PriceResult = price.financiarPrice();
      var valorTotais = price.CalcularTotais();
      this.Result = PriceResult;
      console.log(PriceResult);
      this.ResultPrice = true;
      console.log(this.ResultPrice);
      this.jurosTaxa = this.taxa;
      this.numberoParcelas = this.Period;
      this.tipoFinancimento = this.Selected;
      this.valorPrestacao = valorTotais.valorPrestacao;
      this.valorTotalFinanciamento = valorTotais.valorTotalFinanciamento;
      this.valorTotalJuros = valorTotais.valorTotalJuros;
    },

    ResetCampos() {
      (this.taxa = null),
        (this.totalValue = null),
        (this.Period = null),
        (this.Result = []);
      this.Selected = "";
    },
    Calcular() {
      if (this.Selected != null) {
        if (this.Selected == "SAC") {
          this.CalculateSac();
        }
        if (this.Selected == "PRICE") {
          console.log("entrou");
          this.CalculatePrice();
        }
      } else {
        alert("Escolha tipo de Financiamento");
      }
    },
  },
};
</script>

<style scoped>
@media (max-width: 860px) {
  .topo-page {
    display: flex;
    flex-direction: column;
    width: 100%;
    margin: 20px;
    margin-top: 50px;
  }

  .ajust-tamanho{
    margin-top: 50px;
  }
}

.column-data {
  margin-top: 30px;
}

.topo-page {
  display: flex;
  width: 100%;
}
</style>
