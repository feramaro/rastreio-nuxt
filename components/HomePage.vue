<template>
  <div>
    <custom-nav-bar />
    <b-container>
      <b-row class="text-center">
        <b-col>
          <h1>Rastreador de Encomendas</h1>
          <form class="form-group">
            <input
              minlength="13"
              maxlength="13"
              type="text"
              class="form-control"
              v-model="codRastreio"
              required
            />
            <b-button
              type="submit"
              :variant="alteraBotao(codRastreio, 13)"
              :disabled="codRastreio.length != 13"
              @click="rastrear"
              class="mt-3"
              >Pesquisar</b-button
            >
          </form>
          <div>
            <b-modal title="Erro" v-model="modalShow" ok-only>{{
              error.errorMessage
            }}</b-modal>
            <img
              v-if="isRequesting"
              src="/assets/spinner.svg"
              alt="Carregando..."
            />
          </div>
          <div v-if="response.length != 0">
            <b-table-simple responsive>
              <b-thead>
                <b-th>Status</b-th>
                <b-th>Data</b-th>
                <b-th>Hora</b-th>
                <b-th>Local</b-th>
              </b-thead>
              <b-tbody>
                <b-tr v-for="(item, index) in response" :key="index">
                  <b-th>{{ item.status }}</b-th>
                  <b-th>{{ item.data }}</b-th>
                  <b-th>{{ item.hora }}</b-th>
                  <b-th>{{ item.local }}</b-th>
                </b-tr>
              </b-tbody>
            </b-table-simple>
          </div>
        </b-col>
      </b-row>
    </b-container>
  </div>
</template>

<script>
import CustomNavBar from "./CustomNavBar.vue";
export default {
  components: { CustomNavBar },
  data() {
    return {
      title: 'Rastreamento Correios',
      codRastreio: "",
      response: [],
      error: {
        error: false,
        errorMessage: "",
      },
      modalShow: false,
      isRequesting: false,
    };
  },
  head() {
    return {
      title: this.title
    }
  },
  methods: {
    rastrear: async function (e) {
      e.preventDefault();
      try {
        this.response = [];
        this.isRequesting = true;
        let res = await this.$axios.$get(`/rastreio/${this.codRastreio}`);
        this.response = res;
        this.error.error = false;
        this.isRequesting = false;
      } catch (e) {
        if (e.response === undefined || e.response.status >= 404) {
          this.modalShow = true;
          this.isRequesting = false;
          this.error = {
            error: true,
            errorMessage: `Encomenda não encontrada`,
          };
        }
      }
    },
    alteraBotao: function (txt, len) {
      return txt.length === len ? "primary" : "secondary";
    },
  },
};
</script>

<style scoped>
</style>