<template>
  <main class="login-shell">
    <section class="login-panel">
      <div class="login-brand">
        <span class="eyebrow">
          <i class="fa-solid fa-graduation-cap"></i>
          Sistema academico
        </span>
        <h1>{{ modo === 'login' ? 'Entrar no Registo de Estudantes' : 'Criar conta no sistema' }}</h1>
        <p>
          {{
            modo === 'login'
              ? 'Acesse a area administrativa para gerir estudantes, cursos e numeros de inscricao.'
              : 'Crie uma conta para entrar no sistema e comecar a gerir o registo academico.'
          }}
        </p>
      </div>

      <form class="login-form" @submit.prevent="submeter">
        <div class="form-badge">
          <i :class="modo === 'login' ? 'fa-solid fa-user-lock' : 'fa-solid fa-user-plus'"></i>
        </div>

        <div class="mode-switch" role="tablist" aria-label="Modo de acesso">
          <button
            type="button"
            class="mode-button"
            :class="{ active: modo === 'login' }"
            @click="trocarModo('login')"
          >
            Entrar
          </button>
          <button
            type="button"
            class="mode-button"
            :class="{ active: modo === 'register' }"
            @click="trocarModo('register')"
          >
            Cadastrar
          </button>
        </div>

        <label v-if="modo === 'register'" class="input-group">
          <span>Nome completo</span>
          <div>
            <i class="fa-solid fa-user"></i>
            <input v-model="form.nome" placeholder="Ex.: Ana Mucavele" required />
          </div>
        </label>

        <label class="input-group">
          <span>Email</span>
          <div>
            <i class="fa-solid fa-envelope"></i>
            <input v-model="form.email" type="email" placeholder="admin@escola.co.mz" required />
          </div>
        </label>

        <label class="input-group">
          <span>Senha</span>
          <div>
            <i class="fa-solid fa-lock"></i>
            <input v-model="form.senha" type="password" placeholder="Digite a senha" required />
          </div>
        </label>

        <p v-if="erro" class="message error">
          <i class="fa-solid fa-circle-exclamation"></i>
          {{ erro }}
        </p>

        <button class="primary-button" type="submit">
          <i :class="modo === 'login' ? 'fa-solid fa-right-to-bracket' : 'fa-solid fa-user-plus'"></i>
          {{ modo === 'login' ? 'Entrar' : 'Criar conta' }}
        </button>
      </form>
    </section>
  </main>
</template>

<script setup>
import { ref } from 'vue'

defineProps({
  erro: {
    type: String,
    default: ''
  }
})

const emit = defineEmits(['login', 'register'])

const modo = ref('login')
const form = ref({ nome: '', email: '', senha: '' })

const limpar = () => {
  form.value = { nome: '', email: '', senha: '' }
}

const trocarModo = (novoModo) => {
  modo.value = novoModo
  limpar()
}

const submeter = () => {
  if (modo.value === 'login') {
    emit('login', { email: form.value.email, senha: form.value.senha })
    return
  }

  emit('register', { ...form.value })
}
</script>

<style scoped>
.login-shell {
  display: grid;
  width: min(100% - 32px, 980px);
  min-height: 100vh;
  margin: 0 auto;
  place-items: center;
  padding: 32px 0;
}

.login-panel {
  display: grid;
  grid-template-columns: minmax(0, 1fr) minmax(320px, 420px);
  gap: 28px;
  width: 100%;
  padding: 28px;
  border: 1px solid rgba(148, 163, 184, 0.32);
  border-radius: 8px;
  background:
    linear-gradient(180deg, rgba(255, 255, 255, 0.98), rgba(248, 250, 252, 0.94)),
    radial-gradient(circle at top left, rgba(14, 165, 233, 0.12), transparent 16rem);
  box-shadow: 0 24px 60px rgba(15, 23, 42, 0.08);
}

.login-brand {
  display: flex;
  min-height: 420px;
  flex-direction: column;
  justify-content: center;
  padding: 18px;
}

.eyebrow {
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
p {
  margin: 0;
}

h1 {
  max-width: 540px;
  margin-top: 12px;
  color: #0f172a;
  font-size: clamp(1.8rem, 4vw, 3rem);
  line-height: 1.04;
}

.login-brand p {
  max-width: 520px;
  margin-top: 16px;
  color: #475569;
  font-size: 1rem;
  line-height: 1.6;
}

.login-form {
  align-self: center;
  padding: 22px;
  border: 1px solid #e2e8f0;
  border-radius: 8px;
  background: #fff;
}

.form-badge {
  display: grid;
  width: 62px;
  height: 62px;
  margin: -54px auto 18px;
  place-items: center;
  border-radius: 8px;
  color: #fff;
  background: linear-gradient(135deg, #0369a1, #0f766e);
  box-shadow: 0 14px 28px rgba(3, 105, 161, 0.26);
  font-size: 1.4rem;
}

.mode-switch {
  display: grid;
  grid-template-columns: repeat(2, 1fr);
  gap: 8px;
  margin-bottom: 18px;
  padding: 6px;
  border-radius: 8px;
  background: #e2e8f0;
}

.mode-button {
  min-height: 40px;
  border: 0;
  border-radius: 8px;
  color: #334155;
  background: transparent;
  cursor: pointer;
  font-weight: 800;
  transition: background 0.18s ease, color 0.18s ease, transform 0.18s ease;
}

.mode-button.active {
  color: #fff;
  background: #0369a1;
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

.primary-button {
  display: inline-flex;
  align-items: center;
  justify-content: center;
  gap: 8px;
  width: 100%;
  min-height: 44px;
  padding: 0 16px;
  border: 0;
  border-radius: 8px;
  color: #fff;
  background: #0369a1;
  box-shadow: 0 12px 26px rgba(3, 105, 161, 0.28);
  cursor: pointer;
  font-weight: 800;
  transition: transform 0.18s ease, box-shadow 0.18s ease, background 0.18s ease;
}

.primary-button:hover,
.mode-button:hover {
  transform: translateY(-1px);
}

.primary-button:hover {
  background: #075985;
}

.message {
  display: flex;
  align-items: center;
  gap: 10px;
  padding: 16px;
  border-radius: 8px;
  color: #475569;
  background: #f8fafc;
}

.message.error {
  margin-bottom: 16px;
  color: #991b1b;
  background: #fee2e2;
}

@media (max-width: 860px) {
  .login-panel {
    grid-template-columns: 1fr;
  }

  .login-brand {
    min-height: auto;
    padding: 4px;
  }
}

@media (max-width: 560px) {
  .login-panel,
  .login-form {
    padding: 16px;
  }

  .form-badge {
    margin-top: -44px;
  }
}
</style>
