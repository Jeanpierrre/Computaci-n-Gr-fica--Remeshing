<template>
<div class="max-w-7xl mx-auto">
  <div class="text-center mb-8">
    <h1 class="text-4xl font-bold text-white mb-4">QEM Remeshing - Quadric Error Metrics</h1>
    <p class="text-slate-300">Simplificación inteligente de mallas preservando características geométricas críticas</p>
  </div>

  <!-- Panel Superior: Controles -->
  <div class="grid lg:grid-cols-3 gap-6 mb-8">
    <!-- Panel izquierdo: Procesamiento -->
    <div class="lg:col-span-1">
      <div class="bg-slate-800/50 backdrop-blur-sm rounded-xl p-6 border border-slate-700">
        <h3 class="text-xl font-semibold text-white mb-4 flex items-center">
          <svg class="w-5 h-5 mr-2 text-green-400" fill="none" stroke="currentColor" viewBox="0 0 24 24">
            <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M9 19v-6a2 2 0 00-2-2H5a2 2 0 00-2 2v6a2 2 0 002 2h2a2 2 0 002-2zm0 0V9a2 2 0 012-2h2a2 2 0 012 2v10m-6 0a2 2 0 002 2h2a2 2 0 002-2m0 0V5a2 2 0 012-2h2a2 2 0 012 2v4a2 2 0 01-2-2h-2a2 2 0 00-2 2z"></path>
          </svg>
          Estado QEM
        </h3>
        
        <div v-if="originalLoaded" class="space-y-3 text-sm">
          <div class="flex justify-between">
            <span class="text-slate-400">📁 Archivo:</span>
            <span class="font-mono text-white text-xs">{{ currentFileName }}</span>
          </div>
          <div class="flex justify-between">
            <span class="text-slate-400">📊 Estado Original:</span>
            <span class="text-green-400">✓ Cargado</span>
          </div>
          <div class="flex justify-between">
            <span class="text-slate-400">⚙️ Estado QEM:</span>
            <span :class="processedLoaded ? 'text-green-400' : (isProcessing ? 'text-yellow-400' : 'text-slate-400')">
              {{ processedLoaded ? '✓ Simplificado' : (isProcessing ? '⏳ Calculando...' : '⏸️ Pendiente') }}
            </span>
          </div>
          <div class="flex justify-between">
            <span class="text-slate-400">🔧 Factor Reducción:</span>
            <span class="text-purple-400">{{ escala.toFixed(2) }}</span>
          </div>
        </div>
        
        <div v-else class="text-center py-8">
          <svg class="w-12 h-12 mx-auto mb-4 text-slate-600" fill="none" stroke="currentColor" viewBox="0 0 24 24">
            <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M7 16a4 4 0 01-.88-7.903A5 5 0 1115.9 6L16 6a5 5 0 011 9.9M15 13l-3-3m0 0l-3 3m3-3v12"></path>
          </svg>
          <p class="text-slate-400 text-sm">
            Arrastra un archivo <code class="bg-slate-700 px-2 py-1 rounded text-blue-400">.obj</code> para aplicar QEM
          </p>
        </div>
      </div>
    </div>

    <!-- Control de escala y nuevos controles -->
    <div class="lg:col-span-1">
      <div class="bg-slate-800/50 backdrop-blur-sm rounded-xl p-6 border border-slate-700">
        <h3 class="text-xl font-semibold text-white mb-4 flex items-center">
          <svg class="w-5 h-5 mr-2 text-purple-400" fill="none" stroke="currentColor" viewBox="0 0 24 24">
            <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M12 6V4m0 2a2 2 0 100 4m0-4a2 2 0 110 4m-6 8a2 2 0 100-4m0 4a2 2 0 100 4m0-4v2m0-6V4m6 6v10m6-2a2 2 0 100-4m0 4a2 2 0 100 4m0-4v2m0-6V4"></path>
          </svg>
          Controles
        </h3>
        
        <div class="space-y-4">
          <div>
            <label class="block text-sm font-medium text-gray-300 mb-2">
              Factor de Reducción: {{ escala.toFixed(2) }}
            </label>
            <input 
              v-model.number="escala" 
              type="range" 
              min="0.1" 
              max="1.0" 
              step="0.1"
              class="w-full h-2 bg-slate-700 rounded-lg appearance-none cursor-pointer slider"
            >
            <div class="flex justify-between text-xs text-slate-500 mt-1">
              <span>Máxima reducción</span>
              <span>Sin reducción</span>
            </div>
          </div>

          <!-- Modo Wireframe -->
          <div class="flex items-center justify-between">
            <label class="text-sm font-medium text-gray-300 flex items-center">
              <svg class="w-4 h-4 mr-2" fill="none" stroke="currentColor" viewBox="0 0 24 24">
                <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M4 5a1 1 0 011-1h14a1 1 0 011 1v2a1 1 0 01-1 1H5a1 1 0 01-1-1V5zM4 13a1 1 0 011-1h6a1 1 0 011 1v6a1 1 0 01-1 1H5a1 1 0 01-1-1v-6zM16 13a1 1 0 011-1h2a1 1 0 011 1v6a1 1 0 01-1 1h-2a1 1 0 01-1-1v-6z"></path>
              </svg>
              Modo Wireframe
            </label>
            <input v-model="wireframeMode" type="checkbox" class="toggle-checkbox" @change="toggleWireframe" />
          </div>

          <!-- Rotación Automática -->
          <div class="flex items-center justify-between">
            <label class="text-sm font-medium text-gray-300 flex items-center">
              <svg class="w-4 h-4 mr-2" fill="none" stroke="currentColor" viewBox="0 0 24 24">
                <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M4 4v5h.582m15.356 2A8.001 8.001 0 004.582 9m0 0H9m11 11v-5h-.581m0 0a8.003 8.003 0 01-15.357-2m15.357 2H15"></path>
              </svg>
              Rotación Automática
            </label>
            <input v-model="autoRotate" type="checkbox" class="toggle-checkbox" @change="toggleAutoRotate" />
          </div>
          
          <div class="grid grid-cols-2 gap-2">
            <button 
              @click="resetCameras"
              class="py-2 px-3 bg-blue-600 hover:bg-blue-500 rounded-lg transition-colors text-white font-medium text-sm"
              :disabled="!originalLoaded"
            >
              Resetear Cámaras
            </button>
            <input 
              ref="fileInput"
              type="file" 
              accept=".obj"
              @change="loadFromInput"
              class="hidden"
            />
            <button 
              @click="$refs.fileInput.click()"
              class="py-2 px-3 bg-yellow-600 hover:bg-yellow-500 rounded-lg transition-colors text-white font-medium text-sm"
              :disabled="isProcessing"
            >
              Seleccionar
            </button>
          </div>
        </div>
      </div>
    </div>

    <!-- Panel derecho: Información -->
    <div class="lg:col-span-1">
      <div class="bg-slate-800/50 backdrop-blur-sm rounded-xl p-6 border border-slate-700">
        <h3 class="text-xl font-semibold text-white mb-4 flex items-center">
          <svg class="w-5 h-5 mr-2 text-orange-400" fill="none" stroke="currentColor" viewBox="0 0 24 24">
            <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M13 16h-1v-4h-1m1-4h.01M21 12a9 9 0 11-18 0 9 9 0 0118 0z"></path>
          </svg>
          Algoritmo QEM
        </h3>
        
        <div class="space-y-3 text-sm text-slate-300">
          <p>
            <strong class="text-green-400">Quadric Error Metrics</strong> utiliza error cuadrático para determinar qué vértices colapsar preservando la forma original.
          </p>
          
          <div class="bg-slate-700 rounded-lg p-3">
            <h4 class="font-semibold text-white mb-2 text-xs">Proceso QEM:</h4>
            <ul class="space-y-1 text-xs">
              <li>• Calcula error cuadrático para cada vértice</li>
              <li>• Evalúa costo de colapso de aristas</li>
              <li>• Colapsa aristas con menor error</li>
              <li>• Preserva características importantes</li>
            </ul>
          </div>

          <div v-if="mensaje" class="bg-green-900/30 border border-green-500/30 rounded-lg p-2">
            <h4 class="font-semibold text-green-400 mb-1 text-xs">Resultado QEM:</h4>
            <p class="text-green-300 text-xs">{{ mensaje }}</p>
          </div>
        </div>
      </div>
    </div>
  </div>

  <!-- Área de Visualización Grande -->
  <div class="grid lg:grid-cols-2 gap-8">
    <!-- Modelo Original -->
    <div class="bg-slate-800/50 backdrop-blur-sm rounded-xl p-6 border border-slate-700">
      <h3 class="text-2xl font-semibold text-white mb-4 text-center flex items-center justify-center">
        <div class="w-4 h-4 bg-blue-400 rounded-full mr-3"></div>
        Malla Original
      </h3>
      
      <div 
        ref="originalContainer" 
        class="w-full h-80 bg-white rounded-lg overflow-hidden relative border-2 border-dashed border-slate-600 hover:border-blue-400 transition-colors duration-300 mb-4"
        @dragover.prevent 
        @drop="onDrop"
      >
        <div v-if="!originalLoaded" class="absolute inset-0 flex items-center justify-center">
          <div class="text-center text-slate-600">
            <svg class="w-16 h-16 mx-auto mb-4 opacity-50" fill="none" stroke="currentColor" viewBox="0 0 24 24">
              <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M7 16a4 4 0 01-.88-7.903A5 5 0 1115.9 6L16 6a5 5 0 011 9.9M15 13l-3-3m0 0l-3 3m3-3v12"></path>
            </svg>
            <p class="text-lg font-medium">Arrastra archivo .obj aquí</p>
            <p class="text-sm mt-2">Malla de alta resolución</p>
          </div>
        </div>
      </div>

      <!-- Características del Modelo Original -->
      <div v-if="originalLoaded && originalStats" class="bg-slate-700 rounded-lg p-4">
        <h4 class="font-semibold text-blue-400 mb-3 text-center">📊 Malla Original</h4>
        <div class="grid grid-cols-2 gap-4 text-sm">
          <div class="text-center">
            <div class="text-2xl font-bold text-blue-400">{{ originalStats.vertices.toLocaleString() }}</div>
            <div class="text-xs text-slate-400">Vértices</div>
          </div>
          <div class="text-center">
            <div class="text-2xl font-bold text-blue-400">{{ originalStats.faces.toLocaleString() }}</div>
            <div class="text-xs text-slate-400">Caras</div>
          </div>
          <div class="text-center">
            <div class="text-lg font-bold text-blue-400">{{ originalStats.edges.toLocaleString() }}</div>
            <div class="text-xs text-slate-400">Aristas</div>
          </div>
          <div class="text-center">
            <div class="text-lg font-bold text-blue-400">{{ originalStats.volume.toFixed(2) }}</div>
            <div class="text-xs text-slate-400">Volumen</div>
          </div>
        </div>
        
        <div class="mt-3 pt-3 border-t border-slate-600">
          <h5 class="text-slate-300 text-xs mb-2 text-center">Dimensiones (BBox):</h5>
          <div class="grid grid-cols-3 gap-2 text-center">
            <div>
              <div class="text-slate-400 text-xs">Ancho</div>
              <div class="font-mono text-white text-sm">{{ originalStats.size.x.toFixed(2) }}</div>
            </div>
            <div>
              <div class="text-slate-400 text-xs">Alto</div>
              <div class="font-mono text-white text-sm">{{ originalStats.size.y.toFixed(2) }}</div>
            </div>
            <div>
              <div class="text-slate-400 text-xs">Profundidad</div>
              <div class="font-mono text-white text-sm">{{ originalStats.size.z.toFixed(2) }}</div>
            </div>
          </div>
        </div>
      </div>

      <div v-if="originalLoaded" class="mt-3 text-center text-sm text-slate-400">
        🖱️ Click y arrastra • 🔍 Scroll para zoom
      </div>
    </div>

    <!-- Modelo Procesado -->
    <div class="bg-slate-800/50 backdrop-blur-sm rounded-xl p-6 border border-slate-700">
      <h3 class="text-2xl font-semibold text-white mb-4 text-center flex items-center justify-center">
        <div class="w-4 h-4 bg-green-400 rounded-full mr-3"></div>
        Malla Simplificada (QEM)
      </h3>
      
      <div 
        ref="processedContainer" 
        class="w-full h-80 bg-white rounded-lg overflow-hidden relative mb-4"
      >
        <div v-if="!processedLoaded" class="absolute inset-0 flex items-center justify-center">
          <div class="text-center text-slate-600">
            <svg v-if="isProcessing" class="animate-spin w-12 h-12 mx-auto mb-4" fill="none" viewBox="0 0 24 24">
              <circle class="opacity-25" cx="12" cy="12" r="10" stroke="currentColor" stroke-width="4"></circle>
              <path class="opacity-75" fill="currentColor" d="M4 12a8 8 0 018-8V0C5.373 0 0 5.373 0 12h4zm2 5.291A7.962 7.962 0 014 12H0c0 3.042 1.135 5.824 3 7.938l3-2.647z"></path>
            </svg>
            <svg v-else class="w-12 h-12 mx-auto mb-4 opacity-50" fill="none" stroke="currentColor" viewBox="0 0 24 24">
              <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M12 6V4m0 2a2 2 0 100 4m0-4a2 2 0 110 4m-6 8a2 2 0 100-4m0 4a2 2 0 100 4m0-4v2m0-6V4m6 6v10m6-2a2 2 0 100-4m0 4a2 2 0 100 4m0-4v2m0-6V4"></path>
            </svg>
            <p class="text-lg font-medium">{{ isProcessing ? 'Aplicando QEM...' : 'Esperando simplificación' }}</p>
            <p v-if="isProcessing" class="text-sm mt-2">Factor: {{ escala.toFixed(2) }}</p>
          </div>
        </div>
      </div>

      <!-- Características del Modelo Procesado -->
      <div v-if="processedLoaded && processedStats" class="bg-slate-700 rounded-lg p-4">
        <h4 class="font-semibold text-green-400 mb-3 text-center">📊 Malla Simplificada</h4>
        <div class="grid grid-cols-2 gap-4 text-sm">
          <div class="text-center">
            <div class="text-2xl font-bold text-green-400">{{ processedStats.vertices.toLocaleString() }}</div>
            <div class="text-xs text-slate-400">Vértices</div>
          </div>
          <div class="text-center">
            <div class="text-2xl font-bold text-green-400">{{ processedStats.faces.toLocaleString() }}</div>
            <div class="text-xs text-slate-400">Caras</div>
          </div>
          <div class="text-center">
            <div class="text-lg font-bold text-green-400">{{ processedStats.edges.toLocaleString() }}</div>
            <div class="text-xs text-slate-400">Aristas</div>
          </div>
          <div class="text-center">
            <div class="text-lg font-bold text-green-400">{{ processedStats.volume.toFixed(2) }}</div>
            <div class="text-xs text-slate-400">Volumen</div>
          </div>
        </div>
        
        <div class="mt-3 pt-3 border-t border-slate-600">
          <h5 class="text-slate-300 text-xs mb-2 text-center">Dimensiones (BBox):</h5>
          <div class="grid grid-cols-3 gap-2 text-center">
            <div>
              <div class="text-slate-400 text-xs">Ancho</div>
              <div class="font-mono text-white text-sm">{{ processedStats.size.x.toFixed(2) }}</div>
            </div>
            <div>
              <div class="text-slate-400 text-xs">Alto</div>
              <div class="font-mono text-white text-sm">{{ processedStats.size.y.toFixed(2) }}</div>
            </div>
            <div>
              <div class="text-slate-400 text-xs">Profundidad</div>
              <div class="font-mono text-white text-sm">{{ processedStats.size.z.toFixed(2) }}</div>
            </div>
          </div>
        </div>

        <!-- Comparación -->
        <div class="mt-3 pt-3 border-t border-slate-600">
          <h5 class="text-purple-400 text-xs mb-2 text-center">📈 Eficiencia QEM:</h5>
          <div class="grid grid-cols-2 gap-2 text-center">
            <div>
              <div class="text-slate-400 text-xs">Reducción Lograda</div>
              <div class="font-mono text-purple-400 text-sm">
                {{ (100 - (processedStats.vertices / originalStats.vertices) * 100).toFixed(1) }}%
              </div>
            </div>
            <div>
              <div class="text-slate-400 text-xs">Factor Aplicado</div>
              <div class="font-mono text-purple-400 text-sm">{{ escala.toFixed(2) }}</div>
            </div>
          </div>
        </div>

        <!-- Agregar después del div de comparación -->
        <div class="mt-3 pt-3 border-t border-slate-600">
          <button 
            @click="downloadProcessedModel"
            class="w-full py-2 px-4 bg-green-600 hover:bg-green-500 rounded-lg transition-colors text-white font-medium text-sm flex items-center justify-center"
          >
            <svg class="w-4 h-4 mr-2" fill="none" stroke="currentColor" viewBox="0 0 24 24">
              <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M12 10v6m0 0l-4-4m4 4l4-4m-4-4V3"></path>
            </svg>
            Descargar Modelo QEM
          </button>
        </div>
      </div>

      <div v-if="processedLoaded" class="mt-3 text-center text-sm text-slate-400">
        🖱️ Click y arrastra • 🔍 Scroll para zoom
      </div>
    </div>
  </div>
</div>
</template>

<script setup>
import { ref, onMounted, onUnmounted, nextTick } from 'vue'
import * as THREE from 'three'
import { OBJLoader } from 'three/examples/jsm/loaders/OBJLoader.js'
import { OrbitControls } from 'three/examples/jsm/controls/OrbitControls.js'

// Referencias a contenedores
const originalContainer = ref(null)
const processedContainer = ref(null)

// Estados
const originalLoaded = ref(false)
const processedLoaded = ref(false)
const originalStats = ref(null)
const processedStats = ref(null)
const currentFileName = ref('')
const isProcessing = ref(false)
const mensaje = ref('')
const escala = ref(0.5)

// Nuevos controles
const wireframeMode = ref(false)
const autoRotate = ref(false)

// Agregar después de las otras variables reactivas
const processedObjContent = ref('')

// Variables Three.js para modelo original
let originalScene, originalCamera, originalRenderer, originalControls, originalLoader
let originalAnimationId = null

// Variables Three.js para modelo procesado
let processedScene, processedCamera, processedRenderer, processedControls, processedLoader
let processedAnimationId = null

// Agregar esta función antes del onMounted
function downloadProcessedModel() {
  if (!processedObjContent.value || !currentFileName.value) {
    alert('No hay modelo procesado para descargar')
    return
  }
  
  const blob = new Blob([processedObjContent.value], { type: 'text/plain' })
  const url = URL.createObjectURL(blob)
  const link = document.createElement('a')
  link.href = url
  link.download = `QEM_${currentFileName.value}`
  document.body.appendChild(link)
  link.click()
  document.body.removeChild(link)
  URL.revokeObjectURL(url)
}

onMounted(async () => {
  await nextTick()
  setTimeout(() => {
    initOriginalViewer()
    initProcessedViewer()
  }, 100)
})

onUnmounted(() => {
  if (originalAnimationId) cancelAnimationFrame(originalAnimationId)
  if (processedAnimationId) cancelAnimationFrame(processedAnimationId)
  if (originalRenderer) originalRenderer.dispose()
  if (processedRenderer) processedRenderer.dispose()
})

// ✅ FUNCIÓN PARA LIMPIAR TODOS LOS ESTADOS
function clearAllStates() {
  // Limpiar estados
  originalLoaded.value = false
  processedLoaded.value = false
  originalStats.value = null
  processedStats.value = null
  currentFileName.value = ''
  mensaje.value = ''
  isProcessing.value = false
  
  // Limpiar escenas
  if (originalScene) {
    while(originalScene.children.length > 0) {
      originalScene.remove(originalScene.children[0])
    }
    // Restaurar iluminación
    const ambientLight = new THREE.AmbientLight(0x404040, 0.6)
    originalScene.add(ambientLight)
    const directionalLight = new THREE.DirectionalLight(0xffffff, 0.8)
    directionalLight.position.set(1, 1, 1).normalize()
    directionalLight.castShadow = true
    originalScene.add(directionalLight)
  }
  
  if (processedScene) {
    while(processedScene.children.length > 0) {
      processedScene.remove(processedScene.children[0])
    }
    // Restaurar iluminación
    const ambientLight = new THREE.AmbientLight(0x404040, 0.6)
    processedScene.add(ambientLight)
    const directionalLight = new THREE.DirectionalLight(0xffffff, 0.8)
    directionalLight.position.set(1, 1, 1).normalize()
    directionalLight.castShadow = true
    processedScene.add(directionalLight)
  }
}

// FUNCIÓN 1: Inicializar y mostrar modelo original
function initOriginalViewer() {
  if (!originalContainer.value) return
  
  const width = originalContainer.value.clientWidth
  const height = originalContainer.value.clientHeight

  // Escena original
  originalScene = new THREE.Scene()
  originalScene.background = new THREE.Color(0xffffff) // Fondo blanco

  // Cámara original
  originalCamera = new THREE.PerspectiveCamera(75, width / height, 0.1, 1000)
  originalCamera.position.set(0, 0, 4)

  // Renderer original
  originalRenderer = new THREE.WebGLRenderer({ antialias: true })
  originalRenderer.setSize(width, height)
  originalRenderer.shadowMap.enabled = true
  originalRenderer.shadowMap.type = THREE.PCFSoftShadowMap
  originalContainer.value.appendChild(originalRenderer.domElement)

  // Iluminación original
  const ambientLight = new THREE.AmbientLight(0x404040, 0.6)
  originalScene.add(ambientLight)

  const directionalLight = new THREE.DirectionalLight(0xffffff, 0.8)
  directionalLight.position.set(1, 1, 1).normalize()
  directionalLight.castShadow = true
  originalScene.add(directionalLight)

  // Loader original
  originalLoader = new OBJLoader()

  // Controles originales
  originalControls = new OrbitControls(originalCamera, originalRenderer.domElement)
  originalControls.enableDamping = true
  originalControls.dampingFactor = 0.05
  originalControls.autoRotate = false
  originalControls.autoRotateSpeed = 2.0

  // Animación original
  function animateOriginal() {
    originalAnimationId = requestAnimationFrame(animateOriginal)
    if (originalControls) originalControls.update()
    if (originalRenderer && originalScene && originalCamera) {
      originalRenderer.render(originalScene, originalCamera)
    }
  }
  animateOriginal()
}

// FUNCIÓN 2: Inicializar visor procesado y llamar API
function initProcessedViewer() {
  if (!processedContainer.value) return
  
  const width = processedContainer.value.clientWidth
  const height = processedContainer.value.clientHeight

  // Escena procesada
  processedScene = new THREE.Scene()
  processedScene.background = new THREE.Color(0xffffff) // Fondo blanco

  // Cámara procesada
  processedCamera = new THREE.PerspectiveCamera(75, width / height, 0.1, 1000)
  processedCamera.position.set(0, 0, 4)

  // Renderer procesado
  processedRenderer = new THREE.WebGLRenderer({ antialias: true })
  processedRenderer.setSize(width, height)
  processedRenderer.shadowMap.enabled = true
  processedRenderer.shadowMap.type = THREE.PCFSoftShadowMap
  processedContainer.value.appendChild(processedRenderer.domElement)

  // Iluminación procesada
  const ambientLight = new THREE.AmbientLight(0x404040, 0.6)
  processedScene.add(ambientLight)

  const directionalLight = new THREE.DirectionalLight(0xffffff, 0.8)
  directionalLight.position.set(1, 1, 1).normalize()
  directionalLight.castShadow = true
  processedScene.add(directionalLight)

  // Loader procesado
  processedLoader = new OBJLoader()

  // Controles procesados
  processedControls = new OrbitControls(processedCamera, processedRenderer.domElement)
  processedControls.enableDamping = true
  processedControls.dampingFactor = 0.05
  processedControls.autoRotate = false
  processedControls.autoRotateSpeed = 2.0

  // Animación procesada
  function animateProcessed() {
    processedAnimationId = requestAnimationFrame(animateProcessed)
    if (processedControls) processedControls.update()
    if (processedRenderer && processedScene && processedCamera) {
      processedRenderer.render(processedScene, processedCamera)
    }
  }
  animateProcessed()
}

function loadOriginalModel(objContent, fileName) {
  const object = originalLoader.parse(objContent)
  
  // Limpiar escena original
  while(originalScene.children.length > 0) {
    originalScene.remove(originalScene.children[0])
  }
  
  // Restaurar iluminación
  const ambientLight = new THREE.AmbientLight(0x404040, 0.6)
  originalScene.add(ambientLight)
  
  const directionalLight = new THREE.DirectionalLight(0xffffff, 0.8)
  directionalLight.position.set(1, 1, 1).normalize()
  directionalLight.castShadow = true
  originalScene.add(directionalLight)

  // ✅ Material azul SÓLIDO para original (sin transparencia)
  const originalMaterial = new THREE.MeshLambertMaterial({ 
    color: 0x61dafb,
    wireframe: wireframeMode.value,
    transparent: false,  // ✅ Sin transparencia
    opacity: 1.0,        // ✅ Completamente opaco
    side: THREE.DoubleSide // ✅ Renderizar ambos lados
  })

  object.traverse(child => {
    if (child.isMesh) {
      child.material = originalMaterial
      child.geometry.computeVertexNormals()
      child.geometry.computeBoundingBox()
      child.castShadow = true
      child.receiveShadow = true
    }
  })

  // Centrar y escalar
  const box = new THREE.Box3().setFromObject(object)
  const objectCenter = box.getCenter(new THREE.Vector3())
  object.position.sub(objectCenter)

  const objectSize = box.getSize(new THREE.Vector3())
  const objectMaxDim = Math.max(objectSize.x, objectSize.y, objectSize.z)
  const objectScale = 2 / objectMaxDim
  object.scale.setScalar(objectScale)

  originalScene.add(object)
  originalLoaded.value = true
  currentFileName.value = fileName

  // Replace the existing vertex counting section with:
  // Parse OBJ content directly for accurate counts
  const lines = objContent.split('\n')
  let objVertices = 0
  let objFaces = 0

  for (const line of lines) {
    const trimmed = line.trim()
    if (trimmed.startsWith('v ')) {
      objVertices++
    } else if (trimmed.startsWith('f ')) {
      objFaces++
    }
  }

  originalStats.value = {
    vertices: objVertices,
    faces: objFaces,
    edges: Math.round(objFaces * 3 / 2),
    volume: objectSize.x * objectSize.y * objectSize.z,
    size: objectSize
  }

  // Ajustar cámara automáticamente
  const boundingBox = new THREE.Box3().setFromObject(object)
  const center = boundingBox.getCenter(new THREE.Vector3())
  const size = boundingBox.getSize(new THREE.Vector3())

  // Calcular distancia óptima basada en el tamaño del objeto
  const maxDim = Math.max(size.x, size.y, size.z)
  const fov = originalCamera.fov * (Math.PI / 180) // Convertir a radianes
  const distance = Math.abs(maxDim / Math.sin(fov / 2)) * 1.2 // Factor de seguridad

  // Posicionar cámara al frente del objeto
  originalCamera.position.set(0, 0, distance)
  originalCamera.lookAt(center)

  // Configurar controles
  originalControls.target.copy(center)
  originalControls.minDistance = distance * 0.5
  originalControls.maxDistance = distance * 3
  originalControls.update()
  
  // ✅ Render inmediato
  originalRenderer.render(originalScene, originalCamera)
}

async function processAndLoadModel(objContent) {
  isProcessing.value = true
  mensaje.value = 'Calculando error cuadrático...'

  try {
    const res = await fetch('http://localhost:3001/api/print', {
      method: 'POST',
      headers: { 'Content-Type': 'application/json' },
      body: JSON.stringify({ 
        obj: objContent, 
        escala: escala.value 
      })
    })
    
    if (!res.ok) {
      mensaje.value = `Error HTTP: ${res.status}`
      isProcessing.value = false
      return
    }
    
    const data = await res.json()
    // Después de: const data = await res.json()
    // Agregar esta línea:
    processedObjContent.value = data.obj
    mensaje.value = data.mensaje || 'Simplificación QEM completada'

    // Cargar modelo procesado
    const processedObject = processedLoader.parse(data.obj)
    
    // Limpiar escena procesada
    while(processedScene.children.length > 0) {
      processedScene.remove(processedScene.children[0])
    }
    
    // Restaurar iluminación
    const ambientLight = new THREE.AmbientLight(0x404040, 0.6)
    processedScene.add(ambientLight)
    
    const directionalLight = new THREE.DirectionalLight(0xffffff, 0.8)
    directionalLight.position.set(1, 1, 1).normalize()
    directionalLight.castShadow = true
    processedScene.add(directionalLight)

    // ✅ Material verde SÓLIDO para procesado (sin transparencia)
    const processedMaterial = new THREE.MeshLambertMaterial({ 
      color: 0x22c55e,
      wireframe: wireframeMode.value,
      transparent: false,  // ✅ Sin transparencia
      opacity: 1.0,        // ✅ Completamente opaco
      side: THREE.DoubleSide // ✅ Renderizar ambos lados
    })

    processedObject.traverse(child => {
      if (child.isMesh) {
        child.material = processedMaterial
        child.geometry.computeVertexNormals()
        child.geometry.computeBoundingBox()
        child.castShadow = true
        child.receiveShadow = true
      }
    })

    // Centrar y escalar
    const box = new THREE.Box3().setFromObject(processedObject)
    const objectCenter = box.getCenter(new THREE.Vector3())
    processedObject.position.sub(objectCenter)

    const objectSize = box.getSize(new THREE.Vector3())
    const objectMaxDim = Math.max(objectSize.x, objectSize.y, objectSize.z)
    const objectScale = 2 / objectMaxDim
    processedObject.scale.setScalar(objectScale)

    processedScene.add(processedObject)
    processedLoaded.value = true

    // Replace the processed vertex counting section with:
    // Parse processed OBJ content directly for accurate counts
    const processedLines = data.obj.split('\n')
    let processedObjVertices = 0
    let processedObjFaces = 0

    for (const line of processedLines) {
      const trimmed = line.trim()
      if (trimmed.startsWith('v ')) {
        processedObjVertices++
      } else if (trimmed.startsWith('f ')) {
        processedObjFaces++
      }
    }

    processedStats.value = {
      vertices: processedObjVertices,
      faces: processedObjFaces,
      edges: Math.round(processedObjFaces * 3 / 2),
      volume: objectSize.x * objectSize.y * objectSize.z,
      size: objectSize
    }

    // Ajustar cámara automáticamente
    const boundingBox = new THREE.Box3().setFromObject(processedObject)
    const center = boundingBox.getCenter(new THREE.Vector3())
    const size = boundingBox.getSize(new THREE.Vector3())

    // Calcular distancia óptima basada en el tamaño del objeto
    const maxDim = Math.max(size.x, size.y, size.z)
    const fov = processedCamera.fov * (Math.PI / 180) // Convertir a radianes
    const distance = Math.abs(maxDim / Math.sin(fov / 2)) * 1.2 // Factor de seguridad

    // Posicionar cámara al frente del objeto
    processedCamera.position.set(0, 0, distance)
    processedCamera.lookAt(center)

    // Configurar controles
    processedControls.target.copy(center)
    processedControls.minDistance = distance * 0.5
    processedControls.maxDistance = distance * 3
    processedControls.update()
    
    // ✅ Render inmediato
    processedRenderer.render(processedScene, processedCamera)

  } catch (error) {
    console.error('Error procesando modelo:', error)
    mensaje.value = 'Error al conectar con la API. Asegúrate de que el servidor esté ejecutándose en http://localhost:3001'
  } finally {
    isProcessing.value = false
  }
}

async function onDrop(event) {
  event.preventDefault()
  const file = event.dataTransfer.files[0]
  
  if (!file || !file.name.endsWith('.obj')) {
    alert('Por favor arrastra un archivo .obj válido.')
    return
  }

  // ✅ LIMPIAR TODO ANTES DE CARGAR NUEVO MODELO
  clearAllStates()

  const reader = new FileReader()
  reader.onload = async e => {
    const contents = e.target.result
    
    // FUNCIÓN 1: Cargar modelo original
    loadOriginalModel(contents, file.name)
    
    // FUNCIÓN 2: Procesar con API
    await processAndLoadModel(contents)
  }
  reader.readAsText(file)
}

function loadFromInput(event) {
  const file = event.target.files[0]
  if (!file) return

  // ✅ LIMPIAR TODO ANTES DE CARGAR NUEVO MODELO
  clearAllStates()

  const fakeEvent = {
    preventDefault: () => {},
    dataTransfer: { files: [file] }
  }
  
  onDrop(fakeEvent)
}

function resetCameras() {
  if (originalLoaded.value && originalScene.children.length > 0) {
    // Encontrar el objeto en la escena (excluyendo luces)
    const meshObject = originalScene.children.find(child => child.type === 'Group' || child.type === 'Mesh')
    if (meshObject) {
      const boundingBox = new THREE.Box3().setFromObject(meshObject)
      const center = boundingBox.getCenter(new THREE.Vector3())
      const size = boundingBox.getSize(new THREE.Vector3())
      const maxDim = Math.max(size.x, size.y, size.z)
      const fov = originalCamera.fov * (Math.PI / 180)
      const distance = Math.abs(maxDim / Math.sin(fov / 2)) * 1.2
      
      originalCamera.position.set(0, 0, distance)
      originalCamera.lookAt(center)
      originalControls.target.copy(center)
      originalControls.update()
      originalRenderer.render(originalScene, originalCamera)
    }
  }
  
  if (processedLoaded.value && processedScene.children.length > 0) {
    // Encontrar el objeto en la escena (excluyendo luces)
    const meshObject = processedScene.children.find(child => child.type === 'Group' || child.type === 'Mesh')
    if (meshObject) {
      const boundingBox = new THREE.Box3().setFromObject(meshObject)
      const center = boundingBox.getCenter(new THREE.Vector3())
      const size = boundingBox.getSize(new THREE.Vector3())
      const maxDim = Math.max(size.x, size.y, size.z)
      const fov = processedCamera.fov * (Math.PI / 180)
      const distance = Math.abs(maxDim / Math.sin(fov / 2)) * 1.2
      
      processedCamera.position.set(0, 0, distance)
      processedCamera.lookAt(center)
      processedControls.target.copy(center)
      processedControls.update()
      processedRenderer.render(processedScene, processedCamera)
    }
  }
}

function toggleWireframe() {
  // Aplicar wireframe a ambos modelos
  if (originalScene) {
    originalScene.traverse(child => {
      if (child.isMesh && child.material) {
        child.material.wireframe = wireframeMode.value
      }
    })
    originalRenderer.render(originalScene, originalCamera)
  }
  
  if (processedScene) {
    processedScene.traverse(child => {
      if (child.isMesh && child.material) {
        child.material.wireframe = wireframeMode.value
      }
    })
    processedRenderer.render(processedScene, processedCamera)
  }
}

function toggleAutoRotate() {
  if (originalControls) {
    originalControls.autoRotate = autoRotate.value
  }
  if (processedControls) {
    processedControls.autoRotate = autoRotate.value
  }
}
</script>

<style scoped>
.slider::-webkit-slider-thumb {
  appearance: none;
  height: 20px;
  width: 20px;
  border-radius: 50%;
  background: #3b82f6;
  cursor: pointer;
  border: 2px solid #1e293b;
}

.slider::-moz-range-thumb {
  height: 20px;
  width: 20px;
  border-radius: 50%;
  background: #3b82f6;
  cursor: pointer;
  border: 2px solid #1e293b;
}

.toggle-checkbox {
  appearance: none;
  width: 40px;
  height: 20px;
  background-color: #374151;
  border-radius: 10px;
  position: relative;
  cursor: pointer;
  transition: background-color 0.3s;
}

.toggle-checkbox:checked {
  background-color: #3b82f6;
}

.toggle-checkbox::before {
  content: '';
  position: absolute;
  width: 16px;
  height: 16px;
  border-radius: 50%;
  background-color: white;
  top: 2px;
  left: 2px;
  transition: transform 0.3s;
}

.toggle-checkbox:checked::before {
  transform: translateX(20px);
}
</style>
