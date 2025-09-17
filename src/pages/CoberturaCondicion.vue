<template>
  <q-page padding class="q-pa-md">
    <!-- Encabezado -->
    <q-card class="q-mb-md header-card">
      <q-card-section>
        <div class="text-h5 flex items-center">
          <q-icon name="fas fa-code" class="q-mr-sm text-white" />
          Pruebas de Caja Blanca - Cobertura de Condición
        </div>
        <div class="text-subtitle2 text-grey-2">
          Técnica que verifica que cada condición booleana en el código se evalúe tanto como
          verdadera como falsa
        </div>
      </q-card-section>
    </q-card>

    <div class="row q-col-gutter-md">
      <!-- Panel de código y condiciones -->
      <div class="col-12 col-lg-7">
        <q-card class="fade-in code-analysis-card">
          <!-- Simulador de IDE -->
          <div class="ide-header">
            <div class="ide-tabs">
              <div class="tab active">
                <q-icon name="description" class="q-mr-xs" size="14px" />
                LoginValidator.js
              </div>
              <div class="tab">
                <q-icon name="bug_report" class="q-mr-xs" size="14px" />
                TestRunner.js
              </div>
            </div>
            <div class="ide-controls">
              <q-icon name="close" size="16px" />
            </div>
          </div>

          <!-- Código con resaltado de condiciones -->
          <div class="code-container">
            <div class="line-numbers">
              <div v-for="i in 25" :key="i" class="line-number">{{ i }}</div>
            </div>
            <div class="code-content">
              <pre
                class="code-block"
              ><code><span class="keyword">function</span> <span class="function">validateLogin</span>(<span class="parameter">username, password, attempts</span>) {
  <span class="comment">// Condición 1: Usuario no puede estar vacío</span>
  <span class="keyword">if</span> (<span class="condition" :class="getConditionClass('userEmpty')" @click="testCondition('userEmpty')">username === '' || username == null</span>) {
    <span class="keyword">return</span> { <span class="property">success:</span> <span class="boolean">false</span>, <span class="property">error:</span> <span class="string">'Usuario requerido'</span> };
  }

  <span class="comment">// Condición 2: Contraseña no puede estar vacía</span>
  <span class="keyword">if</span> (<span class="condition" :class="getConditionClass('passEmpty')" @click="testCondition('passEmpty')">password === '' || password == null</span>) {
    <span class="keyword">return</span> { <span class="property">success:</span> <span class="boolean">false</span>, <span class="property">error:</span> <span class="string">'Contraseña requerida'</span> };
  }

  <span class="comment">// Condición 3: Verificar si la cuenta está bloqueada</span>
  <span class="keyword">if</span> (<span class="condition" :class="getConditionClass('blocked')" @click="testCondition('blocked')">attempts >= 3</span>) {
    <span class="keyword">return</span> { <span class="property">success:</span> <span class="boolean">false</span>, <span class="property">error:</span> <span class="string">'Cuenta bloqueada'</span> };
  }

  <span class="comment">// Condición 4: Usuario válido</span>
  <span class="keyword">const</span> <span class="variable">validUser</span> = <span class="condition" :class="getConditionClass('validUser')" @click="testCondition('validUser')">username === 'juan'</span>;

  <span class="comment">// Condición 5: Contraseña válida</span>
  <span class="keyword">const</span> <span class="variable">validPass</span> = <span class="condition" :class="getConditionClass('validPass')" @click="testCondition('validPass')">password === '1234'</span>;

  <span class="comment">// Condición 6: Ambas credenciales son correctas</span>
  <span class="keyword">if</span> (<span class="condition" :class="getConditionClass('bothValid')" @click="testCondition('bothValid')">validUser && validPass</span>) {
    <span class="keyword">return</span> { <span class="property">success:</span> <span class="boolean">true</span>, <span class="property">user:</span> username };
  }

  <span class="keyword">return</span> { <span class="property">success:</span> <span class="boolean">false</span>, <span class="property">error:</span> <span class="string">'Credenciales incorrectas'</span> };
}</code></pre>
            </div>
          </div>

          <!-- Panel de pruebas interactivas -->
          <q-card-section class="test-panel">
            <div class="text-h6 q-mb-md">
              <q-icon name="play_circle" class="q-mr-sm" />
              Panel de Pruebas Interactivas
            </div>

            <div class="row q-col-gutter-md">
              <div class="col-12 col-md-6">
                <q-input
                  v-model="testUsername"
                  label="Usuario de prueba"
                  outlined
                  dense
                  class="q-mb-sm"
                />
                <q-input
                  v-model="testPassword"
                  label="Contraseña de prueba"
                  outlined
                  dense
                  class="q-mb-sm"
                />
                <q-input
                  v-model.number="testAttempts"
                  label="Número de intentos"
                  type="number"
                  outlined
                  dense
                  class="q-mb-md"
                />
              </div>
              <div class="col-12 col-md-6">
                <q-btn
                  label="Ejecutar Prueba"
                  color="primary"
                  icon="play_arrow"
                  class="full-width q-mb-sm"
                  @click="runTest"
                />
                <q-btn
                  label="Limpiar Cobertura"
                  color="orange"
                  icon="refresh"
                  outline
                  class="full-width"
                  @click="clearCoverage"
                />
              </div>
            </div>

            <!-- Resultado de la prueba -->
            <div
              v-if="testResult"
              class="test-result"
              :class="testResult.success ? 'success' : 'error'"
            >
              <q-icon :name="testResult.success ? 'check_circle' : 'error'" class="q-mr-sm" />
              <strong>{{ testResult.success ? 'ÉXITO' : 'FALLO' }}:</strong>
              {{ testResult.message }}
            </div>
          </q-card-section>
        </q-card>
      </div>

      <!-- Panel de cobertura y métricas -->
      <div class="col-12 col-lg-5">
        <!-- Tabla de cobertura de condiciones -->
        <q-card class="fade-in q-mb-md coverage-card">
          <q-card-section>
            <div class="text-h6 flex items-center">
              <q-icon name="assessment" class="q-mr-sm text-primary" />
              Cobertura de Condiciones
            </div>
            <div class="text-caption text-grey-7">
              Haz clic en las condiciones del código para probarlas
            </div>
          </q-card-section>

          <q-card-section class="q-pt-none">
            <div class="coverage-table">
              <div class="coverage-header">
                <div class="condition-name">Condición</div>
                <div class="condition-status">Verdadero</div>
                <div class="condition-status">Falso</div>
                <div class="condition-coverage">Cobertura</div>
              </div>

              <div
                v-for="condition in conditions"
                :key="condition.id"
                class="coverage-row"
                :class="{ highlighted: highlightedCondition === condition.id }"
              >
                <div class="condition-name">
                  <q-tooltip>{{ condition.description }}</q-tooltip>
                  {{ condition.name }}
                </div>
                <div class="condition-status">
                  <q-icon
                    :name="condition.testedTrue ? 'check_circle' : 'radio_button_unchecked'"
                    :color="condition.testedTrue ? 'positive' : 'grey-4'"
                  />
                </div>
                <div class="condition-status">
                  <q-icon
                    :name="condition.testedFalse ? 'check_circle' : 'radio_button_unchecked'"
                    :color="condition.testedFalse ? 'positive' : 'grey-4'"
                  />
                </div>
                <div class="condition-coverage">
                  <q-circular-progress
                    :value="condition.coverage"
                    size="32px"
                    :thickness="0.15"
                    :color="getCoverageColor(condition.coverage)"
                    track-color="grey-3"
                  >
                    <span class="coverage-text">{{ condition.coverage }}%</span>
                  </q-circular-progress>
                </div>
              </div>
            </div>
          </q-card-section>
        </q-card>

        <!-- Métricas generales -->
        <q-card class="fade-in q-mb-md metrics-card">
          <q-card-section>
            <div class="text-h6 flex items-center">
              <q-icon name="analytics" class="q-mr-sm text-secondary" />
              Métricas de Cobertura
            </div>
          </q-card-section>

          <q-card-section class="q-pt-none">
            <div class="metrics-grid">
              <div class="metric-item">
                <div class="metric-value">{{ overallCoverage }}%</div>
                <div class="metric-label">Cobertura Total</div>
                <q-linear-progress
                  :value="overallCoverage / 100"
                  :color="getCoverageColor(overallCoverage)"
                  size="4px"
                  rounded
                />
              </div>

              <div class="metric-item">
                <div class="metric-value">{{ conditionsTested }}</div>
                <div class="metric-label">Condiciones Probadas</div>
              </div>

              <div class="metric-item">
                <div class="metric-value">{{ totalTests }}</div>
                <div class="metric-label">Total de Pruebas</div>
              </div>

              <div class="metric-item">
                <div class="metric-value">{{ completeCoverage }}</div>
                <div class="metric-label">100% Cubiertas</div>
              </div>
            </div>
          </q-card-section>
        </q-card>

        <!-- Casos de prueba sugeridos -->
        <q-card class="fade-in suggested-tests-card">
          <q-card-section>
            <div class="text-h6 flex items-center">
              <q-icon name="lightbulb" class="q-mr-sm text-amber" />
              Casos de Prueba Sugeridos
            </div>
          </q-card-section>

          <q-card-section class="q-pt-none">
            <div class="suggested-tests">
              <div
                v-for="test in suggestedTests"
                :key="test.id"
                class="test-suggestion"
                @click="applySuggestedTest(test)"
              >
                <div class="test-info">
                  <div class="test-title">{{ test.title }}</div>
                  <div class="test-description">{{ test.description }}</div>
                </div>
                <div class="test-action">
                  <q-btn
                    icon="play_arrow"
                    size="sm"
                    round
                    color="primary"
                    @click.stop="applySuggestedTest(test)"
                  />
                </div>
              </div>
            </div>
          </q-card-section>
        </q-card>
      </div>
    </div>

    <!-- Explicación de la técnica -->
    <q-card class="q-mt-lg fade-in explanation-card">
      <q-card-section>
        <div class="text-h6 flex items-center">
          <q-icon name="school" class="q-mr-sm text-indigo" />
          ¿Qué es la Cobertura de Condición?
        </div>
        <p class="q-mt-sm">
          La cobertura de condición es una métrica de pruebas de caja blanca que mide si cada
          condición booleana en el código se ha evaluado tanto como verdadera como falsa al menos
          una vez durante las pruebas.
        </p>
      </q-card-section>

      <q-separator />

      <q-card-section class="row q-col-gutter-md">
        <div class="col-12 col-md-3">
          <q-card flat bordered class="info-card">
            <q-card-section class="text-center">
              <q-avatar color="indigo" text-color="white" size="56px" class="q-mb-sm">
                <q-icon name="visibility" />
              </q-avatar>
              <div class="text-subtitle1 text-weight-medium">Caja Blanca</div>
              <p class="text-caption">
                Se basa en el conocimiento interno del código y su estructura lógica.
              </p>
            </q-card-section>
          </q-card>
        </div>
        <div class="col-12 col-md-3">
          <q-card flat bordered class="info-card">
            <q-card-section class="text-center">
              <q-avatar color="teal" text-color="white" size="56px" class="q-mb-sm">
                <q-icon name="rule" />
              </q-avatar>
              <div class="text-subtitle1 text-weight-medium">Criterio</div>
              <p class="text-caption">
                Cada condición debe evaluarse como True y False al menos una vez.
              </p>
            </q-card-section>
          </q-card>
        </div>
        <div class="col-12 col-md-3">
          <q-card flat bordered class="info-card">
            <q-card-section class="text-center">
              <q-avatar color="deep-orange" text-color="white" size="56px" class="q-mb-sm">
                <q-icon name="bug_report" />
              </q-avatar>
              <div class="text-subtitle1 text-weight-medium">Detecta</div>
              <p class="text-caption">
                Errores en condiciones lógicas que podrían no detectarse con otras técnicas.
              </p>
            </q-card-section>
          </q-card>
        </div>
        <div class="col-12 col-md-3">
          <q-card flat bordered class="info-card">
            <q-card-section class="text-center">
              <q-avatar color="purple" text-color="white" size="56px" class="q-mb-sm">
                <q-icon name="trending_up" />
              </q-avatar>
              <div class="text-subtitle1 text-weight-medium">Cobertura</div>
              <p class="text-caption">
                Proporciona métricas cuantificables de la calidad de las pruebas.
              </p>
            </q-card-section>
          </q-card>
        </div>
      </q-card-section>
    </q-card>
  </q-page>
</template>

<script setup>
import { ref, computed, watch } from 'vue'

// Estado de las pruebas
const testUsername = ref('')
const testPassword = ref('')
const testAttempts = ref(0)
const testResult = ref(null)
const highlightedCondition = ref(null)
const totalTests = ref(0)

// Estado de cobertura de condiciones
const conditions = ref([
  {
    id: 'userEmpty',
    name: 'Usuario Vacío',
    description: "username === '' || username == null",
    testedTrue: false,
    testedFalse: false,
    coverage: 0,
  },
  {
    id: 'passEmpty',
    name: 'Contraseña Vacía',
    description: "password === '' || password == null",
    testedTrue: false,
    testedFalse: false,
    coverage: 0,
  },
  {
    id: 'blocked',
    name: 'Cuenta Bloqueada',
    description: 'attempts >= 3',
    testedTrue: false,
    testedFalse: false,
    coverage: 0,
  },
  {
    id: 'validUser',
    name: 'Usuario Válido',
    description: "username === 'juan'",
    testedTrue: false,
    testedFalse: false,
    coverage: 0,
  },
  {
    id: 'validPass',
    name: 'Contraseña Válida',
    description: "password === '1234'",
    testedTrue: false,
    testedFalse: false,
    coverage: 0,
  },
  {
    id: 'bothValid',
    name: 'Ambas Válidas',
    description: 'validUser && validPass',
    testedTrue: false,
    testedFalse: false,
    coverage: 0,
  },
])

// Casos de prueba sugeridos
const suggestedTests = ref([
  {
    id: 1,
    title: 'Usuario vacío',
    description: 'Probar condición de usuario vacío',
    username: '',
    password: '1234',
    attempts: 0,
  },
  {
    id: 2,
    title: 'Contraseña vacía',
    description: 'Probar condición de contraseña vacía',
    username: 'juan',
    password: '',
    attempts: 0,
  },
  {
    id: 3,
    title: 'Cuenta bloqueada',
    description: 'Simular cuenta con 3+ intentos',
    username: 'juan',
    password: '1234',
    attempts: 3,
  },
  {
    id: 4,
    title: 'Credenciales correctas',
    description: 'Usuario y contraseña válidos',
    username: 'juan',
    password: '1234',
    attempts: 0,
  },
  {
    id: 5,
    title: 'Usuario incorrecto',
    description: 'Usuario inválido, contraseña válida',
    username: 'admin',
    password: '1234',
    attempts: 0,
  },
  {
    id: 6,
    title: 'Contraseña incorrecta',
    description: 'Usuario válido, contraseña inválida',
    username: 'juan',
    password: 'wrong',
    attempts: 0,
  },
])

// Función de validación (simulada)
const validateLogin = (username, password, attempts) => {
  const results = {}

  // Condición 1: Usuario vacío
  const userEmpty = username === '' || username == null
  updateConditionCoverage('userEmpty', userEmpty)
  results.userEmpty = userEmpty

  if (userEmpty) {
    return { success: false, error: 'Usuario requerido' }
  }

  // Condición 2: Contraseña vacía
  const passEmpty = password === '' || password == null
  updateConditionCoverage('passEmpty', passEmpty)
  results.passEmpty = passEmpty

  if (passEmpty) {
    return { success: false, error: 'Contraseña requerida' }
  }

  // Condición 3: Cuenta bloqueada
  const blocked = attempts >= 3
  updateConditionCoverage('blocked', blocked)
  results.blocked = blocked

  if (blocked) {
    return { success: false, error: 'Cuenta bloqueada' }
  }

  // Condición 4: Usuario válido
  const validUser = username === 'juan'
  updateConditionCoverage('validUser', validUser)
  results.validUser = validUser

  // Condición 5: Contraseña válida
  const validPass = password === '1234'
  updateConditionCoverage('validPass', validPass)
  results.validPass = validPass

  // Condición 6: Ambas credenciales correctas
  const bothValid = validUser && validPass
  updateConditionCoverage('bothValid', bothValid)
  results.bothValid = bothValid

  if (bothValid) {
    return { success: true, user: username }
  }

  return { success: false, error: 'Credenciales incorrectas' }
}

// Actualizar cobertura de condición
const updateConditionCoverage = (conditionId, value) => {
  const condition = conditions.value.find((c) => c.id === conditionId)
  if (condition) {
    if (value) {
      condition.testedTrue = true
    } else {
      condition.testedFalse = true
    }

    // Calcular porcentaje de cobertura
    condition.coverage = (condition.testedTrue ? 50 : 0) + (condition.testedFalse ? 50 : 0)
  }
}

// Ejecutar prueba
const runTest = () => {
  totalTests.value++
  const result = validateLogin(testUsername.value, testPassword.value, testAttempts.value)

  testResult.value = {
    success: result.success,
    message: result.success ? `Acceso concedido para: ${result.user}` : result.error,
  }
}

// Probar condición específica
const testCondition = (conditionId) => {
  highlightedCondition.value = conditionId
  setTimeout(() => {
    highlightedCondition.value = null
  }, 2000)
}

// Obtener clase CSS para condición
const getConditionClass = (conditionId) => {
  const condition = conditions.value.find((c) => c.id === conditionId)
  if (!condition) return ''

  if (condition.coverage === 100) return 'condition-complete'
  if (condition.coverage > 0) return 'condition-partial'
  return 'condition-untested'
}

// Aplicar prueba sugerida
const applySuggestedTest = (test) => {
  testUsername.value = test.username
  testPassword.value = test.password
  testAttempts.value = test.attempts

  // Ejecutar automáticamente después de un breve delay
  setTimeout(() => {
    runTest()
  }, 300)
}

// Limpiar cobertura
const clearCoverage = () => {
  conditions.value.forEach((condition) => {
    condition.testedTrue = false
    condition.testedFalse = false
    condition.coverage = 0
  })
  testResult.value = null
  totalTests.value = 0
}

// Obtener color según cobertura
const getCoverageColor = (coverage) => {
  if (coverage === 100) return 'positive'
  if (coverage >= 50) return 'warning'
  return 'negative'
}

// Métricas computadas
const overallCoverage = computed(() => {
  const totalCoverage = conditions.value.reduce((sum, c) => sum + c.coverage, 0)
  return Math.round(totalCoverage / conditions.value.length)
})

const conditionsTested = computed(() => {
  return conditions.value.filter((c) => c.coverage > 0).length
})

const completeCoverage = computed(() => {
  return conditions.value.filter((c) => c.coverage === 100).length
})
</script>

<style scoped>
/* Header styles */
.header-card {
  background: linear-gradient(135deg, #2d3748 0%, #1a202c 100%);
  color: white;
}

/* IDE Simulator */
.code-analysis-card {
  border-radius: 12px;
  overflow: hidden;
  box-shadow: 0 8px 32px rgba(0, 0, 0, 0.12);
}

.ide-header {
  background: #2d3748;
  display: flex;
  justify-content: space-between;
  align-items: center;
  padding: 8px 16px;
  color: white;
}

.ide-tabs {
  display: flex;
  gap: 8px;
}

.tab {
  background: rgba(255, 255, 255, 0.1);
  padding: 6px 12px;
  border-radius: 6px 6px 0 0;
  font-size: 12px;
  display: flex;
  align-items: center;
  cursor: pointer;
}

.tab.active {
  background: #1a1a1a;
}

.ide-controls {
  cursor: pointer;
}

/* Code container */
.code-container {
  background: #1a1a1a;
  color: #f8f8f2;
  display: flex;
  font-family: 'Monaco', 'Menlo', monospace;
  font-size: 13px;
  max-height: 400px;
  overflow-y: auto;
}

.line-numbers {
  background: #2d3748;
  color: #718096;
  padding: 16px 8px;
  text-align: right;
  min-width: 40px;
  user-select: none;
}

.line-number {
  height: 20px;
  line-height: 20px;
}

.code-content {
  flex: 1;
  padding: 16px;
  overflow-x: auto;
}

.code-block {
  margin: 0;
  line-height: 20px;
}

/* Syntax highlighting */
.keyword {
  color: #ff79c6;
}
.function {
  color: #50fa7b;
}
.parameter {
  color: #f1fa8c;
}
.comment {
  color: #6272a4;
  font-style: italic;
}
.string {
  color: #f1fa8c;
}
.boolean {
  color: #bd93f9;
}
.property {
  color: #8be9fd;
}
.variable {
  color: #50fa7b;
}

/* Condition highlighting */
.condition {
  padding: 2px 4px;
  border-radius: 4px;
  cursor: pointer;
  transition: all 0.2s;
}

.condition:hover {
  background: rgba(255, 255, 255, 0.1);
}

.condition-untested {
  background: rgba(239, 68, 68, 0.2);
  border: 1px solid rgba(239, 68, 68, 0.4);
}

.condition-partial {
  background: rgba(245, 158, 11, 0.2);
  border: 1px solid rgba(245, 158, 11, 0.4);
}

.condition-complete {
  background: rgba(34, 197, 94, 0.2);
  border: 1px solid rgba(34, 197, 94, 0.4);
}

/* Test panel */
.test-panel {
  background: #f8f9fa;
  border-top: 1px solid #e9ecef;
}

.test-result {
  margin-top: 16px;
  padding: 12px 16px;
  border-radius: 8px;
  display: flex;
  align-items: center;
  font-weight: 500;
}

.test-result.success {
  background: #d1fae5;
  color: #065f46;
  border: 1px solid #a7f3d0;
}

.test-result.error {
  background: #fee2e2;
  color: #991b1b;
  border: 1px solid #fecaca;
}

/* Coverage table */
.coverage-card {
  background: linear-gradient(135deg, #f8fafc 0%, #e2e8f0 100%);
}

.coverage-table {
  width: 100%;
}

.coverage-header,
.coverage-row {
  display: grid;
  grid-template-columns: 2fr 1fr 1fr 1fr;
  gap: 12px;
  padding: 8px 0;
  align-items: center;
}

.coverage-header {
  font-weight: 600;
  font-size: 12px;
  text-transform: uppercase;
  color: #64748b;
  border-bottom: 2px solid #e2e8f0;
  padding-bottom: 8px;
}

.coverage-row {
  border-bottom: 1px solid #f1f5f9;
  transition: all 0.2s;
  border-radius: 4px;
  padding: 12px 0;
}

.coverage-row:hover {
  background: rgba(59, 130, 246, 0.05);
}

.coverage-row.highlighted {
  background: rgba(59, 130, 246, 0.1);
  border: 1px solid rgba(59, 130, 246, 0.3);
}

.condition-name {
  font-weight: 500;
  font-size: 13px;
  cursor: help;
}

.condition-status {
  text-align: center;
}

.condition-coverage {
  text-align: center;
}

.coverage-text {
  font-size: 10px;
  font-weight: bold;
}

/* Metrics */
.metrics-card {
  background: linear-gradient(135deg, #fef3c7 0%, #f59e0b 100%);
  color: #92400e;
}

.metrics-grid {
  display: grid;
  grid-template-columns: repeat(2, 1fr);
  gap: 16px;
}

.metric-item {
  text-align: center;
  padding: 16px;
  background: rgba(255, 255, 255, 0.3);
  border-radius: 8px;
}

.metric-value {
  font-size: 24px;
  font-weight: bold;
  margin-bottom: 4px;
}

.metric-label {
  font-size: 12px;
  text-transform: uppercase;
  letter-spacing: 0.5px;
  margin-bottom: 8px;
}

/* Suggested tests */
.suggested-tests-card {
  background: linear-gradient(135deg, #fef7cd 0%, #fbbf24 100%);
}

.suggested-tests {
  display: flex;
  flex-direction: column;
  gap: 8px;
}

.test-suggestion {
  background: white;
  border-radius: 8px;
  padding: 12px;
  display: flex;
  justify-content: space-between;
  align-items: center;
  cursor: pointer;
  transition: all 0.2s;
  border: 1px solid #e5e7eb;
}

.test-suggestion:hover {
  transform: translateY(-2px);
  box-shadow: 0 4px 12px rgba(0, 0, 0, 0.1);
  border-color: #3b82f6;
}

.test-info {
  flex: 1;
}

.test-title {
  font-weight: 600;
  font-size: 14px;
  margin-bottom: 2px;
}

.test-description {
  font-size: 12px;
  color: #6b7280;
}

.test-action {
  margin-left: 12px;
}

/* Explanation */
.explanation-card {
  background: linear-gradient(135deg, #fff 0%, #f8fafc 100%);
}

.info-card {
  transition: all 0.3s ease;
  border: 1px solid #e2e8f0;
}

.info-card:hover {
  transform: translateY(-4px);
  box-shadow: 0 8px 25px rgba(0, 0, 0, 0.1);
  border-color: #3b82f6;
}

/* Animations */
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

/* Responsive design */
@media (max-width: 1024px) {
  .coverage-header,
  .coverage-row {
    grid-template-columns: 2fr 80px 80px 80px;
    gap: 8px;
  }

  .condition-name {
    font-size: 12px;
  }
}

@media (max-width: 768px) {
  .code-container {
    font-size: 11px;
  }

  .metrics-grid {
    grid-template-columns: 1fr;
  }

  .coverage-header,
  .coverage-row {
    grid-template-columns: 1fr;
    gap: 8px;
    text-align: center;
  }

  .coverage-header > div,
  .coverage-row > div {
    padding: 4px;
  }

  .ide-tabs {
    display: none;
  }

  .tab {
    font-size: 10px;
    padding: 4px 8px;
  }
}
</style>
