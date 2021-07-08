
<!--Código Antigo-->
<template>
  <div id="app" class='container-flex'>
    
    <!--Menu de Navegação -->
    <div class='nav-bar'>
      <div>
        <i class="far fa-calendar-check"></i> Minha Lista de Tarefas
      </div>
      <div>
        <a href="https://github.com/dhiegobastos/dev-test" target="_blank">
          <i class="fas fa-info-circle"></i>
          Sobre o Desafio
        </a>
      </div>
      <div>
        <a href="https://localhost:5001/swagger/index.html" target="_blank">
          <i class="fas fa-plug"></i>
          Documentação da API
        </a>
      </div>
      <div>
          <a v-on:click="featureToggle()">
            <i class="fas fa-plus-circle"></i> 
            Nova Tarefa 
          </a>
      </div>
    </div>

    <!--Cadastro de Nova Tarefa-->
    <div v-if="toggle == true " class="tarefas">
        Nova Tarefa
        <input name='nova-tarefa' type='text' placeholder="Descrição" v-on:input="inputTarefa = $event.target.value">
        <button v-on:click="novaTarefa()">Cadastrar</button>
        <button v-on:click="featureToggle()">Cancelar</button>
    </div>

    <!--Área de Tarefas-->
    <div class='tarefas' v-if="tarefas.length > 0">
      <table>
        <thead>
          <th colspan="3" class="tabela-texto-centro">Tarefas</th>
          <th colspan="3" class="tabela-texto-centro">Ações</th>
        </thead>
        <tr v-for="tarefa in tarefas" :key="tarefa.Id">
          <td style="text-align: center">
            <span v-if="tarefa.Aberto == 'true'" class="tarefa-aberta">à Fazer</span>
            <span v-else class="tarefa-fechada">Feito</span>
          </td>
          <td>
            <input type="checkbox" v-bind:id="tarefa.Id">
          </td>
          <td class='tarefa-descricao'>
            {{tarefa.Descricao}}
          </td>
          <td class="tabela-texto-centro edit-link">
            <a v-on:click="editarTarefa(tarefa.Id)">
              <i class="far fa-edit"></i>
            </a>
          </td>
          <td class="tabela-texto-centro remove-link">
            <a v-on:click="deletaTarefa(tarefa.Id)" class="remove-link">
              <i class="far fa-trash-alt"></i>
            </a>
          </td>
          <td class="tabela-texto-centro check-link">
            <a v-on:click="finalizarTarefa(tarefa.Id)">
              <i class="far fa-check-circle"></i>
            </a>
          </td>
        </tr>
      </table>
      <div class="alinhamento-a-esquerda">
        <span>
          <button v-on:click="marcarTodasTarefas()">
            <i class="fas fa-check-double"></i> Selecionar Todos
          </button>
        </span>
        <span>
          <button v-on:click="finalizarSelecionados()">
          <i class="fas fa-check-circle"></i> Finalizar Selecionados
        </button>
        </span>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  name: 'app',
  data () {
    return {
      tarefas:[],
      inputTarefa:"",
      toggle: false
    }
  },
  created(){
    this.atualizarLista();
  },
  methods:{
    atualizarLista(){
      this.$http.get('https://lista-de-tarefas-api.herokuapp.com/api/ListarTarefas')
        .then(res => res.json())
        .then(tarefas => this.tarefas = tarefas, err => console.log(err));
    },
    novaTarefa(){
      if(this.inputTarefa != ""){
        let endpoint = 'https://lista-de-tarefas-api.herokuapp.com/api/NovaTarefa/'+this.inputTarefa
        this.realizaChamadaEExibeAlerta(endpoint);
      }
      this.atualizarLista();
    },
    deletaTarefa(id){
      let endpoint = 'https://lista-de-tarefas-api.herokuapp.com/api/RemoverTarefa/'+id;
      this.realizaChamadaEExibeAlerta(endpoint);
      this.atualizarLista();
    },
    finalizarTarefa(id){
      let endpoint = 'https://lista-de-tarefas-api.herokuapp.com/api/FinalizarTarefa/'+id;
      this.realizaChamadaEExibeAlerta(endpoint);
      this.atualizarLista();
    },
    finalizarTarefaSilenciosamente(id){
        this.$http.get('https://lista-de-tarefas-api.herokuapp.com/api/FinalizarTarefa/'+id)
        .then(res => res.json())
        .then(status => window.alert(status.message)
        .catch(console.log("Não foi possível realizar a tarefa solicitada. Id:"+id)));
    },
    editarTarefa(id){
      let descricao = window.prompt("Digite a Nova Descrição:");
      let endpoint = 'https://lista-de-tarefas-api.herokuapp.com/api/EditarTarefa/'+id+'/'+descricao;
      if(descricao!=null){
        this.realizaChamadaEExibeAlerta(endpoint);
      }
      this.atualizarLista();
    },
    realizaChamadaEExibeAlerta(endpoint){
      this.$http.get(endpoint)
        .then(res => res.json())
        .then(status => window.alert(status.message))
        .catch(window.alert("Não foi possível realizar a tarefa solicitada."));
    },
    marcarTodasTarefas() {
     var checkboxes = new Array();
     checkboxes = document.getElementsByTagName('input');
      for (var i = 0; i < checkboxes.length; i++) {
          if (checkboxes[i].type == 'checkbox') {
              checkboxes[i].setAttribute('checked', true)
          }
      }
    },
    finalizarSelecionados(){
     var checkboxes = new Array();
     checkboxes = document.getElementsByTagName('input');
      for (var i = 0; i < checkboxes.length; i++) {
          if (checkboxes[i].type == 'checkbox') {
              if(checkboxes[i].checked ==1){
                this.finalizarTarefaSilenciosamente(checkboxes[i]);
              }
          }
      }
    },
    featureToggle(){
      this.toggle ? this.toggle = false : this.toggle = true;
    }
  }
}
</script>
<style>
@import url('https://fonts.googleapis.com/css2?family=Mukta&display=swap');

body{
  font-family: 'Mukta', sans-serif;
  margin: 0;
  padding: 0;
  background-image: url("https://www.teahub.io/photos/full/102-1028638_office-work-wallpapers-free-photos-office-work.jpg");
  background-repeat: no-repeat;
  background-size: cover;
}

.container-flex{
  display: flex;
  flex-direction: column;
  justify-content: center;
  align-content: center;
  text-align: center;
  align-items: center;
}
.nav-bar{
  display: flex;
  flex-direction: row;
  justify-content: space-between;
  height: auto;
  width: 100%;
}
.nav-bar div{
  background-color: rgba(5, 30, 53, 0.719);
  color:darkgrey;
  text-shadow: 1px 1px 2px rgba(35, 53, 53, 0.767);
  width: 50%;
  margin:0;
  padding:15px;
}
.nav-bar a{
    color:darkgrey;
    text-decoration: none;
}
.nav-bar a:hover{
    color:rgb(235, 235, 235);
    text-decoration: none;
}

.alinhamento-a-esquerda{
    display: flex;
    align-items: flex-start;
    flex-direction: row;
}
.alinhamento-a-esquerda span{
    padding: 10px;
}

.tarefas{
  margin-top: 15px;
  color:rgba(5, 30, 53, 0.904);
  background-color: rgba(236, 235, 231, 0.747);
  border-radius: 15px;
  padding: 15px;
}

.editar-tarefa{
  width: 250px;
}

.tarefa-descricao{
  min-width: 80%;
}
.tarefa-aberta{
  width: max-content;
  border-radius: 5px;
  margin: 5px;
  padding: 0px;
  padding-left: 2px;
  padding-right: 2px;
  background-color: rgba(201, 143, 67, 0.788);
}
.tarefa-fechada{
  width: max-content;
  border-radius: 5px;
  margin: 5px;
  padding: 0px;
  padding-left: 2px;
  padding-right: 2px;
  background-color: rgba(34, 177, 177, 0.788);
}

table{
  margin:0;
  padding: 25px;
  min-width: 900px;
  text-align: start;
}
.tabela-text-centro{
    text-align: center;
    font-size: large;
}
input{
    padding: 5px;
    border-radius: 5px;
    border-color: transparent;
    box-shadow: inset 1px 1px 2px rgba(128, 128, 128, 0.623);
    background-color: rgba(191, 219, 219, 0.664);
}
button{
    padding: 5px;
    border-radius: 5px;
    color: rgba(191, 219, 219, 0.664);
    background-color: rgba(31, 31, 116, 0.849);
    border-color: transparent;
    box-shadow: 1px 1px 2px rgba(32, 32, 32, 0.623);
}
button:hover{
    padding: 5px;
    border-radius: 5px;
    color:rgb(235, 235, 235);
    background-color: rgba(31, 31, 116, 0.849);
    border-color: transparent;
    box-shadow: 1px 1px 2px rgba(32, 32, 32, 0.623);
}

.edit-link a:hover{
    color: rgba(202, 114, 7, 0.904);
    text-decoration: none;
}
.remove-link a:hover{
    color: rgba(206, 9, 2, 0.945);
    text-decoration: none;
}
.check-link a:hover{
    color: rgba(4, 148, 76, 0.945);
    text-decoration: none;
}
</style>
