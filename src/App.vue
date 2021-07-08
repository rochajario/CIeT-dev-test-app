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
      this.$http.get('https://localhost:5001/api/ListarTarefas')
        .then(res => res.json())
        .then(tarefas => this.tarefas = tarefas, err => console.log(err));
    },
    novaTarefa(){
      if(this.inputTarefa != ""){
        let endpoint = 'https://localhost:5001/api/NovaTarefa/'+this.inputTarefa
        this.realizaChamadaEExibeAlerta(endpoint);
      }
      this.atualizarLista();
    },
    deletaTarefa(id){
      let endpoint = 'https://localhost:5001/api/RemoverTarefa/'+id;
      this.realizaChamadaEExibeAlerta(endpoint);
      this.atualizarLista();
    },
    finalizarTarefa(id){
      let endpoint = 'https://localhost:5001/api/FinalizarTarefa/'+id;
      this.realizaChamadaEExibeAlerta(endpoint);
      this.atualizarLista();
    },
    finalizarTarefaSilenciosamente(id){
        this.$http.get('https://localhost:5001/api/FinalizarTarefa/'+id)
        .then(res => res.json())
        .then(status => window.alert(status.message), err => console.log("Não foi possível realizar a tarefa solicitada. Id:"+id))
    },
    editarTarefa(id){
      let descricao = window.prompt("Digite a Nova Descrição:");
      let endpoint = 'https://localhost:5001/api/EditarTarefa/'+id+'/'+descricao;
      if(descricao!=null){
        this.realizaChamadaEExibeAlerta(endpoint);
      }
      this.atualizarLista();
    },
    realizaChamadaEExibeAlerta(endpoint){
      this.$http.get(endpoint)
        .then(res => res.json())
        .then(status => window.alert(status.message), err => window.alert("Não foi possível realizar a tarefa solicitada."))
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
                finalizarTarefaSilenciosamente(id);
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

