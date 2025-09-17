<template>
  <q-page padding class="q-pa-md">
    <!-- Encabezado -->
    <q-card class="q-mb-md header-card">
      <q-card-section>
        <div class="text-h5 flex items-center">
          <q-icon name="fas fa-diagram-project" class="q-mr-sm text-primary" />
          Pruebas de Transición de Estado
        </div>
        <div class="text-subtitle2 text-grey-3">
          Una técnica de testing que verifica el comportamiento de un sistema en diferentes estados
          y las transiciones entre ellos
        </div>
      </q-card-section>
    </q-card>

    <div class="row q-col-gutter-md">
      <!-- Panel de simulación con mini web embebida -->
      <div class="col-12 col-md-6">
        <q-card class="fade-in browser-card">
          <!-- Barra de navegador -->
          <div class="browser-bar">
            <div class="browser-controls">
              <div class="control-dot red"></div>
              <div class="control-dot yellow"></div>
              <div class="control-dot green"></div>
            </div>
            <div class="address-bar">
              <q-icon name="fas fa-lock" class="text-grey-6 q-mr-xs" size="12px" />
              <span class="text-grey-7">https://mi-app-segura.com/login</span>
            </div>
            <div class="browser-menu">
              <q-icon name="more_vert" class="text-grey-6" />
            </div>
          </div>

          <!-- Contenido de la mini web -->
          <div class="mini-web-content">
            <!-- Header de la app embebida -->
            <div class="app-header">
              <div class="logo-section">
                <div class="app-logo">
                  <q-icon name="fas fa-shield-halved" size="24px" class="text-white" />
                </div>
                <div class="app-name">SecureApp</div>
              </div>
              <div class="nav-links">
                <span class="nav-link">Inicio</span>
                <span class="nav-link active">Login</span>
                <span class="nav-link">Ayuda</span>
              </div>
            </div>

            <!-- Formulario moderno -->
            <div class="login-container">
              <div class="login-form-wrapper">
                <div class="form-header">
                  <h3 class="form-title">Iniciar Sesión</h3>
                  <p class="form-subtitle">Accede a tu cuenta segura</p>
                </div>

                <div class="form-content">
                  <!-- Campo Usuario con diseño moderno -->
                  <div class="input-group">
                    <div class="input-wrapper">
                      <q-icon name="person" class="input-icon" />
                      <q-input
                        v-model="username"
                        placeholder="Ingresa tu usuario"
                        outlined
                        dense
                        :disable="isBlocked || accessGranted"
                        class="modern-input"
                        hide-bottom-space
                      />
                    </div>
                  </div>

                  <!-- Campo Contraseña con diseño moderno -->
                  <div class="input-group">
                    <div class="input-wrapper">
                      <q-icon name="lock" class="input-icon" />
                      <q-input
                        v-model="password"
                        type="password"
                        placeholder="Ingresa tu contraseña"
                        outlined
                        dense
                        :disable="isBlocked || accessGranted"
                        class="modern-input"
                        hide-bottom-space
                      />
                    </div>
                  </div>

                  <!-- Botón de login moderno -->
                  <q-btn
                    label="Iniciar Sesión"
                    color="primary"
                    class="login-btn"
                    @click="handleLogin"
                    :disable="isBlocked || accessGranted"
                    :loading="isLoading"
                    no-caps
                    rounded
                  >
                    <template v-slot:loading>
                      <q-spinner-hourglass class="on-left" />
                      Verificando...
                    </template>
                  </q-btn>

                  <!-- Contador de intentos -->
                  <div class="attempts-counter">
                    <q-linear-progress
                      :value="attempts / 3"
                      color="warning"
                      class="q-mb-sm"
                      rounded
                    />
                    <div class="text-caption text-center">
                      Intentos: <strong>{{ attempts }}</strong> de 3
                    </div>
                  </div>

                  <!-- Estado visual mejorado -->
                  <div class="status-display" :class="stateDisplayClass">
                    <q-avatar :color="stateColor" text-color="white" size="48px" class="q-mb-sm">
                      <q-icon :name="stateIcon" size="24px" />
                    </q-avatar>
                    <div class="status-title">{{ stateTitle }}</div>
                    <div class="status-description">{{ stateDescription }}</div>
                  </div>

                  <!-- Links adicionales -->
                  <div class="form-links">
                    <a href="#" class="form-link">¿Olvidaste tu contraseña?</a>
                    <a href="#" class="form-link">Crear cuenta nueva</a>
                  </div>
                </div>
              </div>
            </div>

            <!-- Footer de la mini web -->
            <div class="app-footer">
              <div class="footer-content">
                <span>© 2024 SecureApp - Todos los derechos reservados</span>
                <div class="social-links">
                  <q-icon name="fab fa-twitter" class="social-icon" />
                  <q-icon name="fab fa-facebook" class="social-icon" />
                  <q-icon name="fab fa-linkedin" class="social-icon" />
                </div>
              </div>
            </div>
          </div>

          <!-- Botón de reinicio fuera del contexto de la mini web -->
          <q-card-section class="q-pt-none">
            <q-btn
              label="Reiniciar Simulación"
              color="orange"
              icon="refresh"
              class="full-width"
              @click="resetSimulation"
              outline
              rounded
            />
          </q-card-section>
        </q-card>
      </div>

      <!-- Diagrama de estados mejorado -->
      <div class="col-12 col-md-6">
        <q-card class="fade-in diagram-card">
          <q-card-section>
            <div class="text-h6 flex items-center">
              <q-icon name="fas fa-network-wired" class="q-mr-sm text-primary" />
              Diagrama de Transición de Estados
            </div>
          </q-card-section>

          <q-card-section>
            <div class="state-diagram">
              <!-- Estado inicial -->
              <div class="state-node" :class="{ active: currentState === 'initial' }">
                <q-avatar color="primary" text-color="white" size="40px">
                  <q-icon name="home" />
                </q-avatar>
                <div class="state-title">Estado Inicial</div>
                <div class="state-caption">Home Page</div>
              </div>

              <!-- Flecha -->
              <q-icon name="keyboard_arrow_down" class="transition-arrow" />

              <!-- Estado login -->
              <div class="state-node" :class="{ active: currentState === 'login' }">
                <q-avatar color="info" text-color="white" size="40px">
                  <q-icon name="login" />
                </q-avatar>
                <div class="state-title">Página de Login</div>
                <div class="state-caption">Ingreso de Credenciales</div>
              </div>

              <!-- Ramificación -->
              <div class="state-branches">
                <div class="branch-left">
                  <q-icon name="keyboard_arrow_down" class="transition-arrow" />
                  <div class="state-node small" :class="{ active: currentState === 'attempt1' }">
                    <q-avatar color="warning" text-color="white" size="32px">
                      <span class="text-caption">1</span>
                    </q-avatar>
                    <div class="state-title small">Intento 1</div>
                  </div>

                  <q-icon name="keyboard_arrow_down" class="transition-arrow" />
                  <div class="state-node small" :class="{ active: currentState === 'attempt2' }">
                    <q-avatar color="warning" text-color="white" size="32px">
                      <span class="text-caption">2</span>
                    </q-avatar>
                    <div class="state-title small">Intento 2</div>
                  </div>

                  <q-icon name="keyboard_arrow_down" class="transition-arrow" />
                  <div class="state-node small" :class="{ active: currentState === 'blocked' }">
                    <q-avatar color="negative" text-color="white" size="32px">
                      <q-icon name="block" />
                    </q-avatar>
                    <div class="state-title small">Bloqueado</div>
                  </div>
                </div>

                <div class="branch-right">
                  <q-icon name="keyboard_arrow_down" class="transition-arrow" />
                  <div class="state-node" :class="{ active: currentState === 'access' }">
                    <q-avatar color="positive" text-color="white" size="40px">
                      <q-icon name="check_circle" />
                    </q-avatar>
                    <div class="state-title">Acceso Concedido</div>
                    <div class="state-caption">Credenciales Correctas</div>
                  </div>
                </div>
              </div>
            </div>
          </q-card-section>
        </q-card>
      </div>
    </div>

    <!-- Explicación -->
    <q-card class="q-mt-lg fade-in explanation-card">
      <q-card-section>
        <div class="text-h6 flex items-center">
          <q-icon name="fas fa-lightbulb" class="q-mr-sm text-amber" />
          ¿Qué son las Pruebas de Transición de Estado?
        </div>
        <p class="q-mt-sm">
          Las pruebas de transición de estado verifican cómo un sistema cambia entre diferentes
          estados en respuesta a diversos eventos. Esta técnica es especialmente útil para sistemas
          que tienen un comportamiento basado en estados definidos.
        </p>
      </q-card-section>

      <q-separator />

      <q-card-section class="row q-col-gutter-md">
        <div class="col-12 col-md-4">
          <q-card flat bordered class="info-card">
            <q-card-section class="text-center">
              <q-avatar color="primary" text-color="white" size="56px" class="q-mb-sm">
                <q-icon name="target" />
              </q-avatar>
              <div class="text-subtitle1 text-weight-medium">Objetivo</div>
              <p class="text-caption">
                Asegurar que todas las transiciones entre estados se comporten como se espera.
              </p>
            </q-card-section>
          </q-card>
        </div>
        <div class="col-12 col-md-4">
          <q-card flat bordered class="info-card">
            <q-card-section class="text-center">
              <q-avatar color="positive" text-color="white" size="56px" class="q-mb-sm">
                <q-icon name="check_circle" />
              </q-avatar>
              <div class="text-subtitle1 text-weight-medium">Ventajas</div>
              <p class="text-caption">
                Identifica errores en la lógica de transiciones entre estados y ayuda a cubrir casos
                complejos.
              </p>
            </q-card-section>
          </q-card>
        </div>
        <div class="col-12 col-md-4">
          <q-card flat bordered class="info-card">
            <q-card-section class="text-center">
              <q-avatar color="info" text-color="white" size="56px" class="q-mb-sm">
                <q-icon name="devices" />
              </q-avatar>
              <div class="text-subtitle1 text-weight-medium">Aplicaciones</div>
              <p class="text-caption">
                Ideal para sistemas de login, compras, flujos de trabajo y cualquier sistema con
                estados definidos.
              </p>
            </q-card-section>
          </q-card>
        </div>
      </q-card-section>
    </q-card>
  </q-page>
</template>

<script setup>
import { ref, computed } from 'vue'

// Credenciales correctas
const validUser = 'juan'
const validPass = '1234'

// Estados reactivos
const username = ref('')
const password = ref('')
const attempts = ref(0)
const accessGranted = ref(false)
const isBlocked = ref(false)
const currentState = ref('initial')
const isLoading = ref(false)

// Manejar el intento de login
const handleLogin = () => {
  if (isBlocked.value || accessGranted.value) return

  isLoading.value = true
  currentState.value = 'login'

  setTimeout(() => {
    if (username.value === validUser && password.value === validPass) {
      accessGranted.value = true
      currentState.value = 'access'
    } else {
      attempts.value++
      if (attempts.value === 1) {
        currentState.value = 'attempt1'
      } else if (attempts.value === 2) {
        currentState.value = 'attempt2'
      } else if (attempts.value >= 3) {
        isBlocked.value = true
        currentState.value = 'blocked'
      }
    }
    isLoading.value = false
  }, 1500)
}

// Reiniciar la simulación
const resetSimulation = () => {
  username.value = ''
  password.value = ''
  attempts.value = 0
  accessGranted.value = false
  isBlocked.value = false
  currentState.value = 'initial'
  isLoading.value = false
}

// Computadas para visualización
const stateDisplayClass = computed(() => {
  if (accessGranted.value) return 'status-success'
  if (isBlocked.value) return 'status-error'
  if (attempts.value > 0) return 'status-warning'
  return 'status-default'
})

const stateColor = computed(() => {
  if (accessGranted.value) return 'positive'
  if (isBlocked.value) return 'negative'
  if (attempts.value > 0) return 'warning'
  return 'grey-6'
})

const stateIcon = computed(() => {
  if (accessGranted.value) return 'check_circle'
  if (isBlocked.value) return 'error'
  if (attempts.value > 0) return 'warning'
  return 'info'
})

const stateTitle = computed(() => {
  if (accessGranted.value) return '¡Acceso Concedido!'
  if (isBlocked.value) return 'Cuenta Bloqueada'
  if (attempts.value > 0) return 'Credenciales Incorrectas'
  return 'Esperando Credenciales'
})

const stateDescription = computed(() => {
  if (accessGranted.value) return 'Las credenciales son correctas. Bienvenido al sistema.'
  if (isBlocked.value) return 'Demasiados intentos fallidos. La cuenta ha sido bloqueada.'
  if (attempts.value > 0)
    return `Intento ${attempts.value} de 3 fallido. Por favor, intenta nuevamente.`
  return 'Ingresa tu usuario y contraseña para continuar.'
})
</script>

<style scoped>
/* Estilos para el header */
.header-card {
  background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
  color: white;
}

/* Estilos para simular un navegador */
.browser-card {
  border-radius: 12px;
  overflow: hidden;
  box-shadow: 0 10px 30px rgba(0, 0, 0, 0.15);
}

.browser-bar {
  background: #f5f5f5;
  display: flex;
  align-items: center;
  padding: 8px 16px;
  border-bottom: 1px solid #e0e0e0;
  height: 44px;
}

.browser-controls {
  display: flex;
  gap: 6px;
  margin-right: 16px;
}

.control-dot {
  width: 12px;
  height: 12px;
  border-radius: 50%;
}

.control-dot.red {
  background: #ff5f57;
}
.control-dot.yellow {
  background: #ffbd2e;
}
.control-dot.green {
  background: #28ca42;
}

.address-bar {
  flex: 1;
  background: white;
  border: 1px solid #ddd;
  border-radius: 16px;
  padding: 6px 12px;
  font-size: 13px;
  display: flex;
  align-items: center;
}

.browser-menu {
  margin-left: 16px;
}

/* Mini web content */
.mini-web-content {
  background: #f8f9fa;
  min-height: 500px;
  position: relative;
}

.app-header {
  background: linear-gradient(135deg, #1976d2 0%, #1565c0 100%);
  padding: 16px 24px;
  display: flex;
  justify-content: space-between;
  align-items: center;
  color: white;
}

.logo-section {
  display: flex;
  align-items: center;
  gap: 12px;
}

.app-logo {
  width: 40px;
  height: 40px;
  border-radius: 8px;
  background: rgba(255, 255, 255, 0.2);
  display: flex;
  align-items: center;
  justify-content: center;
}

.app-name {
  font-size: 20px;
  font-weight: 600;
}

.nav-links {
  display: flex;
  gap: 24px;
}

.nav-link {
  padding: 8px 16px;
  border-radius: 6px;
  cursor: pointer;
  transition: all 0.2s;
  font-size: 14px;
}

.nav-link.active {
  background: rgba(255, 255, 255, 0.2);
}

.nav-link:hover {
  background: rgba(255, 255, 255, 0.1);
}

/* Login container */
.login-container {
  display: flex;
  justify-content: center;
  align-items: center;
  min-height: 400px;
  padding: 24px;
}

.login-form-wrapper {
  background: white;
  border-radius: 16px;
  padding: 32px;
  box-shadow: 0 8px 32px rgba(0, 0, 0, 0.1);
  width: 100%;
  max-width: 400px;
}

.form-header {
  text-align: center;
  margin-bottom: 32px;
}

.form-title {
  margin: 0 0 8px 0;
  font-size: 24px;
  font-weight: 600;
  color: #1a1a1a;
}

.form-subtitle {
  margin: 0;
  color: #666;
  font-size: 14px;
}

.input-group {
  margin-bottom: 20px;
}

.input-wrapper {
  position: relative;
  display: flex;
  align-items: center;
}

.input-icon {
  position: absolute;
  left: 16px;
  z-index: 1;
  color: #666;
}

.modern-input {
  width: 100%;
}

.modern-input :deep(.q-field__control) {
  padding-left: 48px;
  border-radius: 12px;
  border-color: #e1e5e9;
}

.modern-input :deep(.q-field__control):hover {
  border-color: #1976d2;
}

.login-btn {
  width: 100%;
  height: 48px;
  font-size: 16px;
  font-weight: 600;
  margin: 24px 0;
  background: linear-gradient(135deg, #1976d2 0%, #1565c0 100%);
}

.attempts-counter {
  margin: 20px 0;
}

.status-display {
  text-align: center;
  padding: 24px;
  border-radius: 12px;
  margin: 24px 0;
  transition: all 0.3s ease;
}

.status-default {
  background: #f5f5f5;
  color: #666;
}

.status-success {
  background: #e8f5e8;
  color: #2e7d32;
}

.status-error {
  background: #fce4e4;
  color: #c62828;
}

.status-warning {
  background: #fff3cd;
  color: #f57c00;
}

.status-title {
  font-size: 18px;
  font-weight: 600;
  margin-bottom: 8px;
}

.status-description {
  font-size: 14px;
  opacity: 0.8;
}

.form-links {
  display: flex;
  justify-content: space-between;
  margin-top: 24px;
}

.form-link {
  font-size: 14px;
  color: #1976d2;
  text-decoration: none;
}

.form-link:hover {
  text-decoration: underline;
}

.app-footer {
  background: #2c3e50;
  color: white;
  padding: 16px 24px;
  position: absolute;
  bottom: 0;
  left: 0;
  right: 0;
}

.footer-content {
  display: flex;
  justify-content: space-between;
  align-items: center;
  font-size: 12px;
}

.social-links {
  display: flex;
  gap: 12px;
}

.social-icon {
  cursor: pointer;
  opacity: 0.7;
  transition: opacity 0.2s;
}

.social-icon:hover {
  opacity: 1;
}

/* Diagrama de estados mejorado */
.diagram-card {
  background: linear-gradient(135deg, #f8f9fa 0%, #e9ecef 100%);
}

.state-diagram {
  display: flex;
  flex-direction: column;
  align-items: center;
  gap: 16px;
}

.state-node {
  background: white;
  border: 2px solid #e0e0e0;
  border-radius: 16px;
  padding: 16px;
  text-align: center;
  transition: all 0.3s ease;
  box-shadow: 0 2px 8px rgba(0, 0, 0, 0.1);
  min-width: 180px;
}

.state-node.small {
  min-width: 120px;
  padding: 12px;
}

.state-node.active {
  border-color: #1976d2;
  background: linear-gradient(135deg, #e3f2fd 0%, #f3e5f5 100%);
  transform: scale(1.05);
  box-shadow: 0 4px 16px rgba(25, 118, 210, 0.3);
}

.state-title {
  font-weight: 600;
  margin: 8px 0 4px 0;
}

.state-title.small {
  font-size: 14px;
  margin: 4px 0;
}

.state-caption {
  font-size: 12px;
  color: #666;
}

.transition-arrow {
  color: #1976d2;
  font-size: 24px;
}

.state-branches {
  display: flex;
  gap: 48px;
  width: 100%;
  justify-content: center;
}

.branch-left,
.branch-right {
  display: flex;
  flex-direction: column;
  align-items: center;
  gap: 12px;
}

/* Cards de explicación */
.explanation-card {
  background: linear-gradient(135deg, #fff 0%, #f8f9fa 100%);
}

.info-card {
  transition: transform 0.2s ease;
}

.info-card:hover {
  transform: translateY(-4px);
}

/* Animaciones */
.fade-in {
  animation: fadeIn 0.6s ease-in-out;
}

@keyframes fadeIn {
  from {
    opacity: 0;
    transform: translateY(20px);
  }
  to {
    opacity: 1;
    transform: translateY(0);
  }
}

/* Responsive */
@media (max-width: 768px) {
  .nav-links {
    display: none;
  }

  .state-branches {
    flex-direction: column;
    gap: 24px;
  }

  .login-form-wrapper {
    padding: 24px;
  }

  .app-header {
    padding: 12px 16px;
  }

  .footer-content {
    flex-direction: column;
    gap: 8px;
    text-align: center;
  }
}
</style>
