<script setup lang="ts">
import { ref, computed } from 'vue';
import UiBotao from './UiBotao.vue';
const msgError = ref(true);

const termoBusca = ref('');
const modalCadastro = ref(false);
const modalAtualizarDados = ref(false);

interface Formulario {
  nome: string;
  email: string;
  telefone: string;
  persona: string;
}

const formEditar = ref<Formulario>({
  nome: '',
  email: '',
  telefone: '',
  persona: '',
});

interface Contato extends Formulario {
  id: number;
}

const formulario = ref<Formulario>({
  nome: '',
  email: '',
  telefone: '',
  persona: '',
});

const contatos = ref<Contato[]>([
  {
    id: 1,
    nome: 'jose',
    email: 'jose@gmail.com',
    telefone: '88-99876-5432',
    persona: 'masculino',
  },
  {
    id: 2,
    nome: 'maria',
    email: 'maria@gmail.com',
    telefone: '88-99876-5432',
    persona: 'feminino',
  },
  {
    id: 3,
    nome: 'emanuel',
    email: 'emanuel-1@gmail.com',
    telefone: '88-91000-5432',
    persona: 'masculino',
  },
]);

const filtrados = computed(() => {
  return contatos.value.filter(
    (u) =>
      u.nome.toLowerCase().includes(termoBusca.value.toLowerCase()) ||
      u.email.toLowerCase().includes(termoBusca.value.toLowerCase())
  );
});

function abrirModalCadastro() {
  modalCadastro.value = true;
}

function fecharModalCadastro() {
  modalCadastro.value = false;
}

function adicionarNovoUsuario(): void {
  //*** */ if (!formulario.value.nome.trim() ||
  ///  !formulario.value.email.trim() ||
  // !formulario.value.telefone.trim() ||
  //!formulario.value.persona.trim()) {
  //alert("Campos obrigatórios!")
  //return
  //}

  contatos.value.push({
    id: Date.now(),
    nome: formulario.value.nome,
    email: formulario.value.email,
    telefone: formulario.value.telefone,
    persona: formulario.value.persona,
  });
  limparCampos();
  alert('Adicionado com sucesso!');
  fecharModalCadastro();
}

const limparCampos = () => {
  formulario.value.nome = '';
  formulario.value.email = '';
  formulario.value.telefone = '';
  formulario.value.persona = '';
};

function remover(id: number): void {
  if (confirm('Deseja excluir?')) {
    contatos.value = contatos.value.filter((c) => c.id !== id);
  }
}

const editarId = ref<number>();

function editarContato(id: number): void {
  abrirModalAtualizarContato();

  editarId.value = id;

  const usuarioExistente = contatos.value.find((u) => u.id === id);

  if (usuarioExistente) {
    formEditar.value = {
      nome: usuarioExistente.nome,
      email: usuarioExistente.email,
      telefone: usuarioExistente.telefone,
      persona: usuarioExistente.persona,
    };
  }
}

function salvarEdicao(): void {
  const indice = contatos.value.findIndex(
    (contato) => contato.id === editarId.value
  );

  if (indice !== -1) {
    contatos.value[indice] = {
      ...contatos.value[indice],
      nome: formEditar.value.nome,
      email: formEditar.value.email,
      telefone: formEditar.value.telefone,
      persona: formEditar.value.persona,
    };

    fecharModalAtualizarContato();

    formEditar.value = {
      nome: '',
      email: '',
      telefone: '',
      persona: '',
    };

    alert('Contato atualizado com sucesso!');
  }
}

function cancelarEdicao(): void {
  formEditar.value.nome = '';
  formEditar.value.email = '';
  formEditar.value.telefone = '';
  formEditar.value.persona = '';
}

function abrirModalAtualizarContato() {
  modalAtualizarDados.value = true;
}

function fecharModalAtualizarContato() {
  modalAtualizarDados.value = false;
}
</script>

<template>
  <!-- message error -->
  <p class="msg__error">Algo errado!</p>

  <!-- modal atualizar -->
  <div class="modal" v-if="modalAtualizarDados">
    <div class="modal__content">
      <h2 class="form__title">Atualizar dados</h2>
      <form class="form" @submit.prevent="salvarEdicao">
        <input
          type="text"
          v-model="formEditar.nome"
          placeholder="Nome completo"
          class="field"
        />
        <input
          type="text"
          v-model="formEditar.email"
          placeholder="E-mail"
          class="field"
        />
        <input
          type="text"
          v-model="formEditar.telefone"
          placeholder="88-9.9123-4567"
          class="field"
        />
        <select v-model="formEditar.persona" class="field">
          <option disabled value="Selecione gênero">Selecione gênero</option>
          <option value="feminino">feminino</option>
          <option value="masculino">masculino</option>
        </select>
        <UiBotao class="btn btn__solid-primary" type="submit">
          salvar edição
        </UiBotao>
      </form>
      <UiBotao
        class="btn btn__solid-cancel btn__flutuant"
        @click="fecharModalAtualizarContato"
        >X</UiBotao
      >
    </div>
  </div>
  <!-- /modal atualizar -->

  <div class="container">
    <header>
      <div class="header__content">
        <h1 class="title__main">
          Agenda<span class="t__realce">Virtual</span>
        </h1>
        <UiBotao class="btn btn__solid-primary" @click="abrirModalCadastro">
          Abrir modal
        </UiBotao>
      </div>
      <input class="field" placeholder="Nome ou e-mail" v-model="termoBusca" />
    </header>

    <div class="cards__content">
      <h2 class="title__contacts">Lista de contatos</h2>
      <p class="not__found" v-if="filtrados.length < 1">
        Nenhum contato encontrado!
      </p>
      <ul class="cards">
        <li v-for="contato in filtrados" :key="contato.id" class="card">
          <img
            :src="
              contato.persona === 'feminino'
                ? 'https://img.icons8.com/color/48/businesswoman.png'
                : 'https://img.icons8.com/color/48/user-male-circle--v9.png'
            "
            :alt="contato.nome"
            class="persona"
          />

          <p class="item__nome">{{ contato.nome }}</p>
          <p class="item__email">{{ contato.email }}</p>
          <p class="item__telefone">{{ contato.telefone }}</p>
          <div class="actions">
            <UiBotao @click="remover(contato.id)">
              <img
                width="26"
                height="26"
                src="https://img.icons8.com/fluency/48/delete"
                alt="botão deletar"
              />
            </UiBotao>
            <UiBotao @click="editarContato(contato.id)">
              <img
                width="26"
                height="26"
                src="https://img.icons8.com/fluency/48/edit--v1.png"
                alt="botão editar"
              />
            </UiBotao>
          </div>
        </li>
      </ul>
    </div>
  </div>

  <div class="modal" v-if="modalCadastro">
    <div class="modal__content">
      <div class="form__content">
        <h2 class="form__title">Formulario de cadastro</h2>

        <form class="form" @submit.prevent="adicionarNovoUsuario">
          <input
            type="text"
            v-model="formulario.nome"
            placeholder="Nome completo"
            class="field"
          />
          <input
            type="text"
            v-model="formulario.email"
            placeholder="E-mail"
            class="field"
          />
          <input
            type="text"
            v-model="formulario.telefone"
            placeholder="88-9.9123-4567"
            class="field"
          />
          <select v-model="formEditar.persona" class="field">
            <option disabled value="Selecione gênero">Selecione gênero</option>
            <option value="feminino">feminino</option>
            <option value="masculino">masculino</option>
          </select>
          <UiBotao class="btn btn__solid-primary" type="submit"
            >Adicionar</UiBotao
          >
          <UiBotao class="btn btn__solid-cancel" @click="fecharModalCadastro"
            >Cancelar</UiBotao
          >
        </form>
      </div>
    </div>
  </div>
</template>
