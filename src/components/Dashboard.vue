<template>
  <div id="burger-table">
     <Message :msg="msg" v-show="msg" />
    <div>
      <div id="burger-table-heading">
        <div class="order-id">#:</div>
        <div>Nome:</div>
        <div>Pão:</div>
        <div>Carne:</div>
        <div>Opcionais:</div>
        <div>Ações:</div>
      </div>
      <div id="burger-table-rows">
        <div
          class="burger-table-row"
          v-for="burger in burgers"
          :key="burger.id"
        >
          <div class="order-numb">{{ burger.id }}</div>
          <div>{{ burger.nome }}</div>
          <div>{{ burger.pao }}</div>
          <div>{{ burger.carne }}</div>
          <div>
            <ul>
              <li v-for="(opcional, index) in burger.opcionais" :key="index">
                {{ opcional }}
              </li>
            </ul>
          </div>
          <div>
            <select name="status" class="status" @change="updateBurger($event, burger.id)"> 
              <option value="">Selecione</option>
              <option v-for="s in status" :key="s.id" :value="s.tipo" :selected="burger.status == s.tipo">
                  {{ s.tipo }}
              </option>
            </select>
            <button class="delete-btn" @click="deleteBurger(burger.id)">Cancelar</button>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>


<script>
import Message from "./Message.vue";

export default {
  name: "Dashboard",
  components:{
    Message
  },
  data() {
    return {
      burgers: null,
      burgers_id: null,
      status: [],
      msg: null
    };
  },
  methods: {
    async getPedidos() {
      //Funcão para pegar os pedidos

      const req = await fetch("http://localhost:3000/burgers"); // Fazer requisição do link e rota que vai acessar

      const data = await req.json(); // Transformar a requisição em JSON

      this.burgers = data; //Estou dizendo que burgers é igual a data, ou seja...Os dados que vieram na requisição, estou transformando no burgers que criei dentro de data(). Ou seja, o valor de "null" é trocado pelo valor que veio do servidor

      //Resgatar os status
    this.getStatus()
    },

    async getStatus(){

        const req = await fetch("http://localhost:3000/status");

        const data = await req.json();
        
        this.status = data;
    },

    async deleteBurger(id){

        const req = await fetch(`http://localhost:3000/burgers/${id}`,{
            method: "DELETE"
        })

        const res = await req.json();

        //msg 
               // Colocar uma mensagem no sistema
        this.msg = `Pedido removido com sucesso!`;
        // limpar msg na tela 
        setTimeout(() => this.msg = "", 3000);


        this.getPedidos()
    },

    async updateBurger(event, id){
        
        const option = event.target.value; // Com isso sabemos qual o status que o usuário está colocando

        const dataJson = JSON.stringify({ status: option}); // Aqui estamos colocando em string para poder atualizar no banco JSON 

        const req = await fetch(`http://localhost:3000/burgers/${id}`, {
            method:"PATCH", //Funciona como o Update, mas atualiza somente o que enviamos, ou seja, somente o status
            headers: {"Content-Type" : "application/json"},
            body: dataJson
        });

        const res = await req.json();

              // Colocar uma mensagem no sistema
        this.msg = `O pedido N° ${res.id} foi atualizado para ${res.status}!`;
        // limpar msg na tela 
        setTimeout(() => this.msg = "", 3000);

    }
  },
  mounted() {
    this.getPedidos(); // -> Estou chamando o getPedidos, para poder ter acesso aos burgers criados
  }
}
</script>

<style scoped>
#burger-table {
  max-width: 1200px;
  margin: 0 auto;
}

#burger-table-heading,
#burger-table-rows,
.burger-table-row {
  display: flex;
  flex-wrap: wrap;
}

#burger-table-heading {
  font-weight: bold;
  padding: 12px;
  border-bottom: 3px solid #333;
}

#burger-table-heading div,
.burger-table-row div {
  width: 19%;
}

.burger-table-row {
  width: 100%;
  padding: 12px;
  border-bottom: 1px solid #ccc;
}

#burger-table-heading .order-id,
.burger-table-row .order-numb {
  width: 5%;
}

select {
  padding: 12px 6px;
  margin-right: 12px;
}

.delete-btn {
  background-color: #222;
  color: #fcba03;
  font-weight: bold;
  border: 2px solid #222;
  padding: 10px;
  margin: 0 auto;
  cursor: pointer;
  transition: 0.5s;
}

.delete-btn:hover {
  background-color: transparent;
  color: #222;
}
</style>