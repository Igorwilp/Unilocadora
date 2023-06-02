
<template>
  <q-page class="flex flex-center">
    
    <div class="row" v-if="naoLogado()">
      <div>
        <div class="text-h4 text-bold">Faça seu Login</div>
        <div class="column">
          <q-input outlined label="Digite seu Email">
              <template v-slot:append>
                <q-icon name="email"></q-icon>
              </template>
           </q-input>
           <q-input outlined label="Digite sua senha">
              <template v-slot:append>
                <q-icon name="lock"></q-icon>
              </template>
           </q-input>
        </div>
        <div class="column">
          <q-btn 
            color="grey"
            @click="login">
            Login
          </q-btn>
          <q-btn @click="showFormCliente = !showFormCliente">
            Novo Cliente
          </q-btn>
        </div>
      </div>
    </div>
    <!--
    <RouterLink to="/cadastroFilme">Cadastro de Filmes</RouterLink>
    <a href="/cadastroFilme">Cadastro de Filmes</a>
    -->

    <form-cliente
      @salvarCliente="onSalvarCliente"
      v-if="showFormCliente"
    />

    <FilmeCard
      :logado="!naoLogado()"
      v-for="f in filmes"
      v-bind:key="f.id"
      :filme="f"
      @locarFilme="onLocarFilme"
      @comparFilme="onComprarFilme"
    /> 
    
  </q-page>
</template>

<script>
import { ref } from "vue";
import { useQuasar } from "quasar";
import FormCliente from "src/components/FormCliente.vue";
import Services from "src/services";
import { appStore } from "src/stores/appStore";
import { defineComponent } from "vue";
import FilmeCard from "../components/FilmeCard.vue";

const form= ref({
    email: {
      value:'',
      email: true,
      required: true
    },
    password: {
      value:'',
      required: true
    }
  })

export default defineComponent({
  components: { FormCliente, FilmeCard },
  name: "IndexPage",
  data() {
    return {
      filmes: Array,
      showFormCliente: false,
    };
  },
  created() {
    Services.getAllFilmes((data) => {
      this.filmes = data;
    });
  },
  methods: {
    login() {
      appStore.cliente = {
        id: 1,
        nome: "teste",
        email: "",
      };
    },
    onLocarFilme(filme) {
      appStore.carrinho.cliente = appStore.cliente;
      appStore.carrinho.filmes.push(filme);
      appStore.carrinho.data = new Date();
    },
    naoLogado() {
      console.log(appStore.cliente == null);
      return appStore.cliente == null;
    },
    onSalvarCliente(cliente) {
      Services.saveCliente(cliente, (ok) => {
        console.log("no onsalvar " + ok);
        if (ok) {
          this.showFormCliente = false;
          const $q = useQuasar();
          $q.notify({
            message: "Cliente salvo com sucesso!\nbem vindo " + cliente.nome,
            color: "positive",
            icon: "check",
          });
        }
      });
    },
  },
});
</script>
