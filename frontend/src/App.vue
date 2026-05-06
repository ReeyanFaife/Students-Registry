<template>
  <Login v-if="!autenticado" :erro="erroLogin" @login="entrar" @register="registrar" />

  <main v-else class="page-shell">
    <section class="hero">
      <div>
        <span class="eyebrow">
          <i class="fa-solid fa-graduation-cap"></i>
          Sistema académico
        </span>
        <h1>Registo de Estudantes</h1>
        <p>Cadastre, consulte e atualize estudantes em uma interface mais limpa e organizada.</p>
      </div>
      <button class="ghost-button" type="button" @click="confirmarSair">
        <i class="fa-solid fa-right-from-bracket"></i>
        Sair
      </button>
    </section>

    <section class="stats-grid" aria-label="Resumo">
      <div class="stat">
        <i class="fa-solid fa-users"></i>
        <div>
          <strong>{{ estudantes.length }}</strong>
          <span>Estudantes</span>
        </div>
      </div>

      <div class="stat">
        <i class="fa-solid fa-book-open-reader"></i>
        <div>
          <strong>{{ totalCursos }}</strong>
          <span>Cursos</span>
        </div>
      </div>

      <div class="stat">
        <i class="fa-solid fa-clock"></i>
        <div>
          <strong>{{ saudacao }}</strong>
          <span>{{ horaAtual }}</span>
        </div>
      </div>
    </section>

    <section class="workspace">
      <form @submit.prevent="salvar" class="panel form-panel">
        <div class="panel-heading">
          <div>
            <span class="section-label">Formulário</span>
            <h2>{{ form.id ? 'Editar estudante' : 'Novo estudante' }}</h2>
          </div>
          <button v-if="form.id" type="button" class="ghost-button" @click="limparFormulario">
            <i class="fa-solid fa-xmark"></i>
            Cancelar
          </button>
        </div>

        <label class="input-group">
          <span>Nome completo</span>
          <div>
            <i class="fa-solid fa-user"></i>
            <input v-model="form.nome" placeholder="Ex.: Ana Mucavele" required />
          </div>
        </label>

        <label class="input-group">
          <span>Número de estudante</span>
          <div>
            <i class="fa-solid fa-id-card"></i>
            <input v-model="form.numero" placeholder="Ex.: 2026-001" required />
          </div>
        </label>

        <label class="input-group">
          <span>Curso</span>
          <div>
            <i class="fa-solid fa-book"></i>
            <input v-model="form.curso" placeholder="Ex.: Engenharia Informática" required />
          </div>
        </label>

        <button class="primary-button" type="submit" :disabled="salvando">
          <i :class="salvando ? 'fa-solid fa-spinner fa-spin' : form.id ? 'fa-solid fa-pen-to-square' : 'fa-solid fa-plus'"></i>
          {{ salvando ? 'A guardar...' : form.id ? 'Atualizar estudante' : 'Criar estudante' }}
        </button>
      </form>

      <section class="panel list-panel">
        <div class="panel-heading">
          <div>
            <span class="section-label">Lista</span>
            <h2>Estudantes registados</h2>
          </div>
          <button class="refresh-button" type="button" @click="carregar" :disabled="carregando">
            <i :class="carregando ? 'fa-solid fa-spinner fa-spin' : 'fa-solid fa-rotate-right'"></i>
            Atualizar
          </button>
        </div>

        <p v-if="erro" class="message error">
          <i class="fa-solid fa-circle-exclamation"></i>
          {{ erro }}
        </p>

        <p v-if="sucesso" class="message success">
          <i class="fa-solid fa-check"></i>
          {{ sucesso }}
        </p>

        <div v-else-if="carregando" class="message">
          <i class="fa-solid fa-spinner fa-spin"></i>
          A carregar estudantes...
        </div>

        <div v-else-if="!estudantes.length" class="empty-state">
          <i class="fa-solid fa-user-plus"></i>
          <strong>Nenhum estudante registado</strong>
          <span>Preencha o formulário para adicionar o primeiro estudante.</span>
        </div>

        <ul v-else class="student-list">
          <li v-for="e in estudantes" :key="e.id">
            <div class="avatar">
              {{ iniciais(e.nome) }}
            </div>

            <div class="student-info">
              <strong>{{ e.nome }}</strong>
              <span>
                <i class="fa-solid fa-id-badge"></i>
                {{ e.numero }}
              </span>
              <span>
                <i class="fa-solid fa-book-open"></i>
                {{ e.curso }}
              </span>
            </div>

            <div class="actions">
              <button class="icon-button edit" type="button" title="Editar estudante" @click="editar(e)">
                <i class="fa-solid fa-pen"></i>
              </button>
              <button class="icon-button delete" type="button" title="Remover estudante" @click="confirmarRemover(e.id)">
                <i class="fa-solid fa-trash"></i>
              </button>
            </div>
          </li>
        </ul>
      </section>
    </section>
  </main>
</template>

<script setup>
import { computed, onBeforeUnmount, onMounted, ref } from 'vue'
import axios from 'axios'
import Login from './Login.vue'

const API = import.meta.env.VITE_API_URL || 'http://localhost:8000'

const autenticado = ref(false)
const usuario = ref(null)
const erroLogin = ref('')
const estudantes = ref([])
const form = ref({ nome: '', numero: '', curso: '' })
const carregando = ref(false)
const salvando = ref(false)
const erro = ref('')
const sucesso = ref('')
const agora = ref(new Date())

let relogioId

const totalCursos = computed(() => {
  return new Set(estudantes.value.map((e) => e.curso.trim()).filter(Boolean)).size
})

const saudacao = computed(() => {
  const hora = agora.value.getHours()
  if (hora >= 5 && hora < 12) return 'Bom dia'
  if (hora >= 12 && hora < 18) return 'Boa tarde'
  return 'Boa noite'
})

const horaAtual = computed(() => {
  return agora.value.toLocaleTimeString('pt-PT', { hour: '2-digit', minute: '2-digit' })
})

const entrar = async (login) => {
  erroLogin.value = ''

  try {
    const res = await axios.post(`${API}?acao=login`, login)
    usuario.value = res.data.usuario
    autenticado.value = true
    await carregar()
  } catch (error) {
    erroLogin.value = 'Email ou senha invalida.'
  }
}

const registrar = async (dados) => {
  erroLogin.value = ''

  try {
    const res = await axios.post(`${API}?acao=registro`, dados)
    usuario.value = res.data.usuario
    autenticado.value = true
    await carregar()
  } catch (error) {
    erroLogin.value = error?.response?.data?.message || 'Nao foi possivel criar a conta.'
  }
}

const sair = () => {
  autenticado.value = false
  usuario.value = null
  estudantes.value = []
  limparFormulario()
}

const carregar = async () => {
  carregando.value = true
  erro.value = ''
  sucesso.value = ''

  try {
    const res = await axios.get(API)
    estudantes.value = res.data
  } catch (error) {
    erro.value = 'Não foi possível carregar os estudantes. Verifique se a API está ativa.'
  } finally {
    carregando.value = false
  }
}

const limparFormulario = () => {
  form.value = { nome: '', numero: '', curso: '' }
}

const salvar = async () => {
  salvando.value = true
  erro.value = ''
  sucesso.value = ''

  try {
    if (form.value.id) {
      await axios.put(API, form.value)
    } else {
      await axios.post(API, form.value)
    }

    sucesso.value = form.value.id ? 'Estudante atualizado com sucesso!' : 'Estudante criado com sucesso!'
    limparFormulario()
    await carregar()
    setTimeout(() => sucesso.value = '', 3000)
  } catch (error) {
    erro.value = 'Não foi possível guardar o estudante. Tente novamente.'
  } finally {
    salvando.value = false
  }
}

const editar = (e) => {
  form.value = { ...e }
}

const remover = async (id) => {
  erro.value = ''

  try {
    await axios.delete(`${API}?id=${id}`)
    await carregar()
  } catch (error) {
    erro.value = 'Não foi possível remover o estudante.'
  }
}

const confirmarRemover = async (id) => {
  if (confirm('Tem certeza que deseja remover este estudante?')) {
    await remover(id)
  }
}

const confirmarSair = () => {
  if (confirm('Tem certeza que deseja sair?')) {
    sair()
  }
}

const iniciais = (nome) => {
  return nome
    .split(' ')
    .filter(Boolean)
    .slice(0, 2)
    .map((parte) => parte[0])
    .join('')
    .toUpperCase()
}

onMounted(() => {
  relogioId = setInterval(() => {
    agora.value = new Date()
  }, 60000)
})

onBeforeUnmount(() => {
  clearInterval(relogioId)
})
</script>

<style scoped>
.page-shell {
  width: min(1180px, calc(100% - 32px));
  margin: 0 auto;
  padding: 14px 0;
}

.hero {
  display: flex;
  align-items: flex-start;
  justify-content: space-between;
  gap: 18px;
  padding: 10px 0 10px;
}

.eyebrow,
.section-label {
  display: inline-flex;
  align-items: center;
  gap: 8px;
  color: #0369a1;
  font-size: 0.78rem;
  font-weight: 800;
  letter-spacing: 0.08em;
  text-transform: uppercase;
}

h1,
h2,
p {
  margin: 0;
}

h1 {
  max-width: 560px;
  margin-top: 6px;
  color: #0f172a;
  font-size: clamp(1.2rem, 2.6vw, 2rem);
  line-height: 1.08;
}

.hero p {
  max-width: 520px;
  margin-top: 8px;
  color: #475569;
  font-size: 0.9rem;
  line-height: 1.4;
}

.hero-actions {
  display: flex;
  gap: 10px;
  flex-wrap: wrap;
  justify-content: flex-end;
}

.icon-link,
.ghost-button,
.refresh-button,
.primary-button,
.icon-button {
  display: inline-flex;
  align-items: center;
  justify-content: center;
  gap: 8px;
  border-radius: 8px;
  cursor: pointer;
  font-weight: 800;
  text-decoration: none;
  transition: transform 0.18s ease, box-shadow 0.18s ease, background 0.18s ease;
}

.icon-link {
  min-height: 40px;
  padding: 0 14px;
  color: #0f172a;
  background: rgba(255, 255, 255, 0.78);
  box-shadow: 0 10px 26px rgba(15, 23, 42, 0.08);
}

.icon-link:hover,
.primary-button:hover,
.refresh-button:hover,
.ghost-button:hover,
.icon-button:hover {
  transform: translateY(-1px);
}

.stats-grid {
  display: grid;
  grid-template-columns: repeat(3, minmax(0, 1fr));
  gap: 10px;
  margin-bottom: 10px;
}

.stat {
  display: flex;
  align-items: center;
  gap: 10px;
  min-height: 70px;
  padding: 14px;
  border: 1px solid rgba(148, 163, 184, 0.28);
  border-radius: 8px;
  background: rgba(255, 255, 255, 0.82);
  box-shadow: 0 18px 45px rgba(15, 23, 42, 0.06);
}

.stat > i {
  display: grid;
  width: 38px;
  height: 38px;
  place-items: center;
  border-radius: 8px;
  color: #0369a1;
  background: #e0f2fe;
  font-size: 1rem;
}

.stat strong,
.student-info strong {
  display: block;
  color: #0f172a;
}

.stat strong {
  font-size: 1.2rem;
}

.stat span,
.student-info span,
.empty-state span {
  color: #64748b;
}

.workspace {
  display: grid;
  grid-template-columns: minmax(300px, 420px) minmax(0, 1fr);
  gap: 18px;
  align-items: start;
}

.panel {
  border: 1px solid rgba(148, 163, 184, 0.32);
  border-radius: 8px;
  background: rgba(255, 255, 255, 0.92);
  box-shadow: 0 24px 60px rgba(15, 23, 42, 0.08);
}

.form-panel,
.list-panel {
  padding: 22px;
}

.panel-heading {
  display: flex;
  align-items: flex-start;
  justify-content: space-between;
  gap: 16px;
  margin-bottom: 22px;
}

h2 {
  margin-top: 6px;
  color: #0f172a;
  font-size: 1.25rem;
}

.input-group {
  display: block;
  margin-bottom: 16px;
}

.input-group > span {
  display: block;
  margin-bottom: 8px;
  color: #334155;
  font-size: 0.92rem;
  font-weight: 800;
}

.input-group div {
  position: relative;
}

.input-group i {
  position: absolute;
  left: 14px;
  top: 50%;
  color: #64748b;
  transform: translateY(-50%);
}

.input-group input {
  width: 100%;
  min-height: 48px;
  padding: 0 14px 0 42px;
  border: 1px solid #cbd5e1;
  border-radius: 8px;
  color: #0f172a;
  background: #f8fafc;
  outline: none;
  transition: border-color 0.18s ease, box-shadow 0.18s ease, background 0.18s ease;
}

.input-group input:focus {
  border-color: #0284c7;
  background: #fff;
  box-shadow: 0 0 0 4px rgba(14, 165, 233, 0.16);
}

.primary-button,
.refresh-button,
.ghost-button {
  min-height: 44px;
  padding: 0 16px;
}

.primary-button {
  width: 100%;
  color: #fff;
  background: #0369a1;
  box-shadow: 0 12px 26px rgba(3, 105, 161, 0.28);
}

.primary-button:hover,
.refresh-button:hover {
  background: #075985;
}

.primary-button:disabled,
.refresh-button:disabled {
  cursor: wait;
  opacity: 0.72;
  transform: none;
}

.refresh-button {
  color: #fff;
  background: #0f766e;
}

.ghost-button {
  color: #475569;
  background: #f1f5f9;
}

.message,
.empty-state {
  display: flex;
  align-items: center;
  gap: 10px;
  padding: 16px;
  border-radius: 8px;
  color: #475569;
  background: #f8fafc;
}

.message.error {
  color: #991b1b;
  background: #fee2e2;
}

.empty-state {
  min-height: 210px;
  flex-direction: column;
  justify-content: center;
  text-align: center;
}

.empty-state i {
  display: grid;
  width: 58px;
  height: 58px;
  place-items: center;
  border-radius: 8px;
  color: #0f766e;
  background: #ccfbf1;
  font-size: 1.45rem;
}

.student-list {
  display: grid;
  gap: 12px;
  margin: 0;
  padding: 0;
  list-style: none;
}

.student-list li {
  display: grid;
  grid-template-columns: auto minmax(0, 1fr) auto;
  gap: 14px;
  align-items: center;
  padding: 14px;
  border: 1px solid #e2e8f0;
  border-radius: 8px;
  background: #fff;
}

.avatar {
  display: grid;
  width: 48px;
  height: 48px;
  place-items: center;
  border-radius: 8px;
  color: #fff;
  background: linear-gradient(135deg, #0369a1, #0f766e);
  font-weight: 900;
}

.student-info {
  min-width: 0;
}

.student-info strong {
  overflow: hidden;
  text-overflow: ellipsis;
  white-space: nowrap;
}

.student-info span {
  display: inline-flex;
  align-items: center;
  gap: 6px;
  margin-top: 6px;
  margin-right: 14px;
  font-size: 0.92rem;
}

.actions {
  display: flex;
  gap: 8px;
}

.icon-button {
  width: 38px;
  height: 38px;
  color: #0f172a;
  background: #f1f5f9;
}

.icon-button.edit:hover {
  color: #854d0e;
  background: #fef3c7;
}

.icon-button.delete:hover {
  color: #991b1b;
  background: #fee2e2;
}

@media (max-width: 860px) {
  .hero,
  .panel-heading {
    flex-direction: column;
  }

  .hero-actions {
    justify-content: flex-start;
  }

  .stats-grid,
  .workspace {
    grid-template-columns: 1fr;
  }
}

@media (max-width: 560px) {
  .page-shell {
    width: min(100% - 20px, 1180px);
    padding: 10px 0;
  }

  .form-panel,
  .list-panel {
    padding: 16px;
  }

  .student-list li {
    grid-template-columns: auto minmax(0, 1fr);
  }

  .actions {
    grid-column: 1 / -1;
  }

  .icon-button {
    flex: 1;
  }
}
</style>
