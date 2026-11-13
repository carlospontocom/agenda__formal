<script setup lang="ts">
import { ref } from 'vue';

// 1. Interfaces
interface Formulario {
  nome: string;
}

interface Contato extends Formulario {
  id: number;
}

// 2. Estados
const contatos = ref<Contato[]>([
  { id: 1, nome: "chaves" }
]);

const formulario = ref<Formulario>({ nome: '' });
const formEditar = ref<Formulario>({ nome: '' });

// Controle do modal e do ID que está sendo editado
const modalEditarAberto = ref(false);
const contatoEditandoId = ref<number | null>(null);

// 3. Funções
function adicionarContato(): void {
  if (!formulario.value.nome.trim()) {
    alert("Digite um nome válido!");
    return;
  }

  contatos.value.push({
    id: Date.now(), // Gera um ID único
    nome: formulario.value.nome.trim()
  });

  formulario.value.nome = ''; // Limpa o campo após adicionar
}

function removerContato(id: number): void {
  // Filtra a lista, mantendo apenas os contatos com ID diferente do clicado
  contatos.value = contatos.value.filter(contato => contato.id !== id);
}

function abrirEdicao(contato: Contato): void {
  contatoEditandoId.value = contato.id;
  formEditar.value.nome = contato.nome;
  modalEditarAberto.value = true;
}

function salvarEdicao(): void {
  if (!formEditar.value.nome.trim() || contatoEditandoId.value === null) return;

  // Encontra o índice do contato na lista
  const index = contatos.value.findIndex(c => c.id === contatoEditandoId.value);
  
  if (index !== -1) {
    // Atualiza o nome no contato específico
    contatos.value[index].nome = formEditar.value.nome.trim();
  }

  // Fecha o modal e limpa os estados
  modalEditarAberto.value = false;
  contatoEditandoId.value = null;
  formEditar.value.nome = '';
}

function cancelarEdicao(): void {
  modalEditarAberto.value = false;
  contatoEditandoId.value = null;
  formEditar.value.nome = '';
}
</script>

<template>
  <div class="container">
    
    <!-- Formulário de Cadastro -->
    <form class="form-cadastro" @submit.prevent="adicionarContato">
      <input 
        type="text" 
        placeholder="Nome" 
        v-model="formulario.nome" 
      />
      <button type="submit">Adicionar novo</button>
    </form>

    <hr />

    <!-- Lista de Contatos -->
    <p v-if="contatos.length === 0">Nenhum contato na lista.</p>
    
    <ul class="lista-contatos">
      <li v-for="contato in contatos" :key="contato.id">
        <p>{{ contato.nome }}</p>
        
        <div class="acoes">
          <button class="btn-editar" @click="abrirEdicao(contato)">
            Editar
          </button>
          
          <button class="btn-remover" @click="removerContato(contato.id)">
            X
          </button>
        </div>
      </li>
    </ul>

    <!-- Modal de Edição -->
    <div v-if="modalEditarAberto" class="modal">
      <div class="modal__content">
        <h2>Editar Contato</h2>
        
        <!-- Formulário de Edição -->
        <form @submit.prevent="salvarEdicao">
          <input 
            type="text" 
            placeholder="Nome completo" 
            v-model="formEditar.nome" 
          />
          
          <div class="modal__acoes">
            <button type="submit" class="btn-salvar">Salvar</button>
            <button type="button" class="btn-cancelar" @click="cancelarEdicao">Cancelar</button>
          </div>
        </form>
      </div>
    </div>

  </div>
</template>

<style scoped>
* {
  box-sizing: border-box;
}

.container {
  padding: 2rem;
  font-family: sans-serif;
  max-width: 600px;
  margin: 0 auto;
}

.form-cadastro {
  display: flex;
  gap: 0.5rem;
  margin-bottom: 1rem;
}

.form-cadastro input {
  padding: 0.5rem;
  flex: 1; /* Faz o input ocupar todo o espaço restante */
  border: 1px solid #ccc;
  border-radius: 4px;
}

.form-cadastro button {
  padding: 0.5rem 1rem;
  background-color: blueviolet;
  color: white;
  border: none;
  border-radius: 4px;
  cursor: pointer;
}

.lista-contatos {
  list-style: none;
  padding: 0;
  display: flex;
  flex-direction: column;
  gap: 0.5rem;
}

.lista-contatos li {
  display: flex;
  justify-content: space-between;
  align-items: center;
  padding: 0.8rem;
  background-color: #f4f4f4;
  border-radius: 4px;
  border: 1px solid #ddd;
}

.lista-contatos p {
  margin: 0;
  font-weight: bold;
}

.acoes {
  display: flex;
  gap: 0.5rem;
}

.btn-editar {
  background-color: #007bff;
  color: white;
  border: none;
  padding: 0.4rem 0.8rem;
  border-radius: 4px;
  cursor: pointer;
}

.btn-remover {
  background-color: tomato;
  color: white;
  border: none;
  padding: 0.4rem 0.8rem;
  border-radius: 4px;
  cursor: pointer;
  font-weight: bold;
}

/* Estilos do Modal */
.modal {
  position: fixed;
  top: 0;
  left: 0;
  width: 100%;
  height: 100vh;
  background-color: rgba(0, 0, 0, 0.5);
  display: flex;
  align-items: center;
  justify-content: center;
}

.modal__content {
  background: white;
  padding: 2rem;
  border-radius: 8px;
  width: 100%;
  max-width: 400px;
}

.modal__content h2 {
  margin-top: 0;
  margin-bottom: 1rem;
}

.modal__content input {
  width: 100%;
  padding: 0.8rem;
  border: 1px solid #ccc;
  border-radius: 4px;
  margin-bottom: 1rem;
}

.modal__acoes {
  display: flex;
  gap: 0.5rem;
  justify-content: flex-end;
}

.btn-salvar {
  background-color: green;
  color: white;
  border: none;
  padding: 0.6rem 1.2rem;
  border-radius: 4px;
  cursor: pointer;
}

.btn-cancelar {
  background-color: #ccc;
  color: #333;
  border: none;
  padding: 0.6rem 1.2rem;
  border-radius: 4px;
  cursor: pointer;
}
</style>