<template>
  <BotaoBase type="button" @click="alertar">
    <span class="btn__icon">+</span>
    <span class="btn__label">Alerta da useCadastro</span>
  </BotaoBase>

  <!-- Botão para abrir cadastro -->
  <BotaoBase type="button" @click="abrirModalCadastro">
    <span class="btn__icon">+</span>
    <span class="btn__label">Novo Contato</span>
  </BotaoBase>

  <!-- Modal de cadastro -->
  <div v-if="modalCadastro" class="modal">
    <form class="form" @submit.prevent="adicionar">
      <CampoBase
        type="text"
        placeholder="Nome completo"
        v-model="formulario.nome"
      />

      <BotaoBase type="submit"> Cadastrar </BotaoBase>

      <BotaoBase type="button" @click="fecharModalCadastro">
        Cancelar
      </BotaoBase>
    </form>
  </div>

  <!-- Modal de edição -->
  <div v-if="modalEditar" class="modal">
    <form class="form" @submit.prevent="salvar">
      <CampoBase
        type="text"
        placeholder="Nome completo"
        v-model="formEditar.nome"
      />

      <BotaoBase type="submit"> Salvar </BotaoBase>

      <BotaoBase type="button" @click="cancelarEdicao"> Cancelar </BotaoBase>
    </form>
  </div>

  <!-- Busca -->
  <CampoBase type="text" v-model="termoBusca" placeholder="Buscar contato" />

  <hr />

  <!-- Lista de contatos -->
  <p v-if="filtrados.length === 0">Nenhum contato encontrado</p>

  <ul>
    <li v-for="contato in filtrados" :key="contato.id">
      <p>{{ contato.nome }}</p>

      <BotaoBase type="button" @click="remover(contato.id)"> X </BotaoBase>

      <BotaoBase type="button" @click="editar(contato.id)"> Editar </BotaoBase>
    </li>
  </ul>
</template>

<script setup lang="ts">
import { ref, computed } from 'vue';
import { useCadastro } from '../../Composable/useCadastro';
const { alertar } = useCadastro();
import CampoBase from './CampoBase.vue';
import BotaoBase from './BotaoBase.vue';

// Modais
const modalCadastro = ref(false);
const modalEditar = ref(false);

const abrirModalCadastro = () => {
  modalCadastro.value = true;
};

const fecharModalCadastro = () => {
  modalCadastro.value = false;
};

const abrirModalEditar = () => {
  modalEditar.value = true;
};

const fecharModalEditar = () => {
  modalEditar.value = false;
};

// Interfaces
interface Formulario {
  nome: string;
}

interface Contatos extends Formulario {
  id: number;
}

// Formulários
const formulario = ref<Formulario>({
  nome: '',
});

const formEditar = ref<Formulario>({
  nome: '',
});

// Lista de contatos
const contatos = ref<Contatos[]>([
  {
    id: 1,
    nome: 'Carlos',
  },
  {
    id: 2,
    nome: 'Janaina',
  },
]);

// Cadastro
const adicionar = () => {
  if (!formulario.value.nome.trim()) return;

  contatos.value.push({
    id: Date.now(),
    nome: formulario.value.nome.trim(),
  });

  limparCampos();
  fecharModalCadastro();
};

// Remover
const remover = (id: number) => {
  contatos.value = contatos.value.filter((u) => u.id !== id);

  if (usuarioId.value === id) {
    cancelarEdicao();
  }
};

// Identificação do contato em edição
const usuarioId = ref<number | null>(null);

// Editar
const editar = (id: number) => {
  const usuarioExiste = contatos.value.find((u) => u.id === id);

  if (!usuarioExiste) return;

  usuarioId.value = id;

  formEditar.value = {
    nome: usuarioExiste.nome,
  };

  abrirModalEditar();
};

// Salvar edição
const salvar = () => {
  if (!formEditar.value.nome.trim()) return;

  const indice = contatos.value.findIndex((u) => u.id === usuarioId.value);

  if (indice === -1) return;

  contatos.value[indice].nome = formEditar.value.nome.trim();

  cancelarEdicao();
};

// Cancelar edição
const cancelarEdicao = () => {
  formEditar.value.nome = '';
  usuarioId.value = null;
  fecharModalEditar();
};

// Limpar cadastro
const limparCampos = () => {
  formulario.value.nome = '';
};

// Busca
const termoBusca = ref('');

const filtrados = computed(() => {
  return contatos.value.filter((u) =>
    u.nome.toLowerCase().includes(termoBusca.value.trim().toLowerCase())
  );
});
</script>

<style scoped>
.form {
  padding: 2rem;
}

.modal {
  background: blue;
  padding: 1rem;
  margin: 1rem 0;
}
</style>
