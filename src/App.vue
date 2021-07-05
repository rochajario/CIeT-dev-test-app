<template>
  <div id="app" class='container-flex'>
    
    <!--Menu de Navegação -->
    <div class='nav-bar'>
      <div>
        <i class="far fa-calendar-check"></i>Minha Lista de Tarefas
      </div>
      <div>
          <label for='nova-tarefa'>Descrição: </label>
          <input name='nova-tarefa' type='text' placeholder="Cadastrar Nova Tarefa" v-on:input="inputTarefa = $event.target.value">
          <button v-on:click="novaTarefa()">Cadastrar</button>
      </div>
    </div>

    <!--Área de Tarefas-->
    <div v-if="inputTarefa == true" class='editar-tarefa tarefas'>
      <input type='text' placeholder="Nova Descricao"><br>
      <button>Editar</button>
      <button v-on:click="this.inputTarefa=false">Cancelar</button>      
    </div>
    <div class='tarefas' v-if="tarefas.length > 0">
      <table>
        <thead>
          <th colspan="3">Tarefas</th>
          <th colspan="3">Ações</th>
        </thead>
        <tr v-for="tarefa in tarefas" :key="tarefa.Id">
          <td>
            <input type="checkbox">
          </td>
          <td class='tarefa-descricao'>
            {{tarefa.Descricao}}
          <td>
          <td style="text-align: center">
            <span v-if="tarefa.Aberto == 'true'" class="tarefa-aberta">à Fazer</span>
            <span v-else class="tarefa-fechada">Feito</span>
          </td>
          <td>
            <a href="#">
              <i class="far fa-edit"></i>
            </a>
          </td>
          <td>
            <a v-on:click="deletaTarefa(tarefa.Id)">
              <i class="far fa-trash-alt"></i>
            </a>
          </td>
          <td>
            <a v-on:click="finalizarTarefa(tarefa.Id)">
              <i class="far fa-check-circle"></i>
            </a>
          </td>
        </tr>
      </table>
    </div>
  </div>
</template>

<script>
export default {
  name: 'app',
  data () {
    return {
      tarefas:[],
      inputTarefa:false
    }
  },
  created(){
    this.atualizarLista();
  },
  mounted: function () {
    window.setInterval(() => {
      this.atualizarLista()
    }, 100000000000)
  },
  methods:{
    atualizarLista(){
      this.$http.get('https://localhost:5001/api/ListarTarefas')
        .then(res => res.json())
        .then(tarefas => this.tarefas = tarefas, err => console.log(err));
    },
    novaTarefa(){
      if(this.inputTarefa != ""){
        this.$http.get('https://localhost:5001/api/NovaTarefa/'+this.inputTarefa)
          .then(res => res.json())
          .then(status => window.alert(status.message));
          this.atualizarLista();
      }
    },
    deletaTarefa(id){
      this.$http.get('https://localhost:5001/api/RemoverTarefa/'+id)
      .then(res => res.json())
      .then(status => window.alert(status.message));
      this.atualizarLista();
    },
    finalizarTarefa(id){
      this.$http.get('https://localhost:5001/api/FinalizarTarefa/'+id)
        .then(res => res.json())
        .then(status => window.alert(status.message));
        this.atualizarLista();
    },
    exibeBoxEditarTarefa(tarefa){
      let id = tarefa.Id;
      this.inputTarefa=true;  
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
    height: 50px;
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
</style>
