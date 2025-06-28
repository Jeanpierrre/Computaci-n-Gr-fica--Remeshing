<template>
<div class="max-w-7xl mx-auto">
  <div class="text-center mb-8">
    <h1 class="text-4xl font-bold text-white mb-4">Remeshing con Diagramas de Voronoi</h1>
    <p class="text-slate-300">Redistribución uniforme y optimización de mallas triangulares mediante diagramas de Voronoi</p>
  </div>

  <!-- Panel Superior: Controles -->
  <div class="grid lg:grid-cols-3 gap-6 mb-8">
    <!-- Panel izquierdo: Procesamiento -->
    <div class="lg:col-span-1">
      <div class="bg-slate-800/50 backdrop-blur-sm rounded-xl p-6 border border-slate-700">
        <h3 class="text-xl font-semibold text-white mb-4 flex items-center">
          <svg class="w-5 h-5 mr-2 text-purple-400" fill="none" stroke="currentColor" viewBox="0 0 24 24">
            <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M19 11H5m14 0a2 2 0 012 2v6a2 2 0 01-2 2H5a2 2 0 01-2-2v-6a2 2 0 012-2m14 0V9a2 2 0 00-2-2M5 11V9a2 2 0 012-2m0 0V5a2 2 0 012-2h6a2 2 0 012 2v2M7 7h10"></path>
          </svg>
          Estado Voronoi
        </h3>
        
        <div v-if="originalModel.vertices.length > 0" class="space-y-3 text-sm">
          <div class="flex justify-between">
            <span class="text-slate-400">📁 Archivo:</span>
            <span class="font-mono text-white text-xs">{{ originalModel.name }}</span>
          </div>
          <div class="flex justify-between">
            <span class="text-slate-400">📊 Estado Original:</span>
            <span class="text-green-400">✓ Cargado</span>
          </div>
          <div class="flex justify-between">
            <span class="text-slate-400">⚙️ Estado Voronoi:</span>
            <span :class="processedModel.vertices.length > 0 ? 'text-green-400' : (isProcessing ? 'text-yellow-400' : 'text-slate-400')">
              {{ processedModel.vertices.length > 0 ? '✓ Redistribuido' : (isProcessing ? '⏳ Calculando diagramas...' : '⏸️ Pendiente') }}
            </span>
          </div>
        </div>
        
        <div v-else class="text-center py-8">
          <svg class="w-12 h-12 mx-auto mb-4 text-slate-600" fill="none" stroke="currentColor" viewBox="0 0 24 24">
            <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M7 16a4 4 0 01-.88-7.903A5 5 0 1115.9 6L16 6a5 5 0 011 9.9M15 13l-3-3m0 0l-3 3m3-3v12"></path>
          </svg>
          <p class="text-slate-400 text-sm">
            Arrastra un archivo <code class="bg-slate-700 px-2 py-1 rounded text-purple-400">.obj</code> para aplicar Voronoi
          </p>
        </div>
      </div>
    </div>

    <!-- Controles simplificados -->
    <div class="lg:col-span-1">
      <div class="bg-slate-800/50 backdrop-blur-sm rounded-xl p-6 border border-slate-700">
        <h3 class="text-xl font-semibold text-white mb-4 flex items-center">
          <svg class="w-5 h-5 mr-2 text-purple-400" fill="none" stroke="currentColor" viewBox="0 0 24 24">
            <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M12 6V4m0 2a2 2 0 100 4m0-4a2 2 0 110 4m-6 8a2 2 0 100-4m0 4a2 2 0 100 4m0-4v2m0-6V4m6 6v10m6-2a2 2 0 100-4m0 4a2 2 0 100 4m0-4v2m0-6V4"></path>
          </svg>
          Controles
        </h3>
        
        <div class="space-y-4">
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
              :disabled="originalModel.vertices.length === 0"
            >
              Resetear Cámaras
            </button>
            <input 
              ref="fileInput"
              type="file" 
              accept=".obj"
              @change="loadObjFile"
              class="hidden"
            />
            <button 
              @click="$refs.fileInput.click()"
              class="py-2 px-3 bg-purple-600 hover:bg-purple-500 rounded-lg transition-colors text-white font-medium text-sm"
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
          Algoritmo Voronoi
        </h3>
        
        <div class="space-y-3 text-sm text-slate-300">
          <p>
            <strong class="text-purple-400">Diagramas de Voronoi</strong> redistribuyen automáticamente los vértices para optimizar la calidad de la malla triangular.
          </p>
          
          <div class="bg-slate-700 rounded-lg p-3">
            <h4 class="font-semibold text-white mb-2 text-xs">Proceso Automático:</h4>
            <ul class="space-y-1 text-xs">
              <li>• Genera sitios de Voronoi distribuidos</li>
              <li>• Calcula celdas de Voronoi</li>
              <li>• Redistribuye vértices uniformemente</li>
              <li>• Optimiza ángulos de triángulos</li>
            </ul>
          </div>

          <div v-if="mensaje" class="bg-purple-900/30 border border-purple-500/30 rounded-lg p-2">
            <h4 class="font-semibold text-purple-400 mb-1 text-xs">Resultado Voronoi:</h4>
            <p class="text-purple-300 text-xs">{{ mensaje }}</p>
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
        class="w-full h-80 bg-white rounded-lg overflow-hidden relative border-2 border-dashed border-slate-600 hover:border-purple-400 transition-colors duration-300 mb-4"
        @dragover.prevent 
        @drop="onDrop"
      >
        <div v-if="originalModel.vertices.length === 0" class="absolute inset-0 flex items-center justify-center">
          <div class="text-center text-slate-600">
            <svg class="w-16 h-16 mx-auto mb-4 opacity-50" fill="none" stroke="currentColor" viewBox="0 0 24 24">
              <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M7 16a4 4 0 01-.88-7.903A5 5 0 1115.9 6L16 6a5 5 0 011 9.9M15 13l-3-3m0 0l-3 3m3-3v12"></path>
            </svg>
            <p class="text-lg font-medium">Arrastra archivo .obj aquí</p>
            <p class="text-sm mt-2">Redistribución automática con Voronoi</p>
          </div>
        </div>
      </div>

      <!-- Características del Modelo Original -->
      <div v-if="originalModel.vertices.length > 0" class="bg-slate-700 rounded-lg p-4">
        <h4 class="font-semibold text-blue-400 mb-3 text-center">📊 Malla Original</h4>
        <div class="grid grid-cols-2 gap-4 text-sm">
          <div class="text-center">
            <div class="text-2xl font-bold text-blue-400">{{ originalModel.vertices.length.toLocaleString() }}</div>
            <div class="text-xs text-slate-400">Vértices</div>
          </div>
          <div class="text-center">
            <div class="text-2xl font-bold text-blue-400">{{ originalModel.faces.length.toLocaleString() }}</div>
            <div class="text-xs text-slate-400">Caras</div>
          </div>
        </div>
      </div>

      <div v-if="originalModel.vertices.length > 0" class="mt-3 text-center text-sm text-slate-400">
        🖱️ Click y arrastra • 🔍 Scroll para zoom
      </div>
    </div>

    <!-- Modelo Procesado -->
    <div class="bg-slate-800/50 backdrop-blur-sm rounded-xl p-6 border border-slate-700">
      <h3 class="text-2xl font-semibold text-white mb-4 text-center flex items-center justify-center">
        <div class="w-4 h-4 bg-purple-400 rounded-full mr-3"></div>
        Malla Redistribuida (Voronoi)
      </h3>
      
      <div 
        ref="processedContainer" 
        class="w-full h-80 bg-white rounded-lg overflow-hidden relative mb-4"
      >
        <div v-if="processedModel.vertices.length === 0" class="absolute inset-0 flex items-center justify-center">
          <div class="text-center text-slate-600">
            <svg v-if="isProcessing" class="animate-spin w-12 h-12 mx-auto mb-4" fill="none" viewBox="0 0 24 24">
              <circle class="opacity-25" cx="12" cy="12" r="10" stroke="currentColor" stroke-width="4"></circle>
              <path class="opacity-75" fill="currentColor" d="M4 12a8 8 0 018-8V0C5.373 0 0 5.373 0 12h4zm2 5.291A7.962 7.962 0 014 12H0c0 3.042 1.135 5.824 3 7.938l3-2.647z"></path>
            </svg>
            <svg v-else class="w-12 h-12 mx-auto mb-4 opacity-50" fill="none" stroke="currentColor" viewBox="0 0 24 24">
              <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M19 11H5m14 0a2 2 0 012 2v6a2 2 0 01-2 2H5a2 2 0 01-2-2v-6a2 2 0 012-2m14 0V9a2 2 0 00-2-2M5 11V9a2 2 0 012-2m0 0V5a2 2 0 012-2h6a2 2 0 012 2v2M7 7h10"></path>
            </svg>
            <p class="text-lg font-medium">{{ isProcessing ? 'Aplicando Voronoi...' : 'Esperando redistribución automática' }}</p>
          </div>
        </div>
      </div>

      <!-- Características del Modelo Procesado -->
      <div v-if="processedModel.vertices.length > 0" class="bg-slate-700 rounded-lg p-4">
        <h4 class="font-semibold text-purple-400 mb-3 text-center">📊 Malla Redistribuida</h4>
        <div class="grid grid-cols-2 gap-4 text-sm">
          <div class="text-center">
            <div class="text-2xl font-bold text-purple-400">{{ processedModel.vertices.length.toLocaleString() }}</div>
            <div class="text-xs text-slate-400">Vértices</div>
          </div>
          <div class="text-center">
            <div class="text-2xl font-bold text-purple-400">{{ processedModel.faces.length.toLocaleString() }}</div>
            <div class="text-xs text-slate-400">Caras</div>
          </div>
        </div>

        <!-- Comparación -->
        <div class="mt-3 pt-3 border-t border-slate-600">
          <h5 class="text-purple-400 text-xs mb-2 text-center">📈 Redistribución Voronoi:</h5>
          <div class="grid grid-cols-2 gap-2 text-center">
            <div>
              <div class="text-slate-400 text-xs">Cambio en Vértices</div>
              <div class="font-mono text-purple-400 text-sm">
                {{ originalModel.vertices.length > 0 ? ((processedModel.vertices.length / originalModel.vertices.length) * 100).toFixed(1) : 0 }}%
              </div>
            </div>
            <div>
              <div class="text-slate-400 text-xs">Procesamiento</div>
              <div class="font-mono text-purple-400 text-sm">Automático</div>
            </div>
          </div>
        </div>
      </div>

      <div v-if="processedModel.vertices.length > 0" class="mt-3 text-center text-sm text-slate-400">
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
const isProcessing = ref(false)
const mensaje = ref('')

// Controles
const wireframeMode = ref(false)
const autoRotate = ref(false)

const originalModel = ref({
  name: '',
  vertices: [],
  faces: []
})

const processedModel = ref({
  name: '',
  vertices: [],
  faces: []
})

// Variables Three.js
let originalScene, originalCamera, originalRenderer, originalControls
let processedScene, processedCamera, processedRenderer, processedControls
let objLoader
let animationId

onMounted(async () => {
  await nextTick()
  setTimeout(() => {
    initThreeJS()
  }, 100)
})

onUnmounted(() => {
  if (animationId) {
    cancelAnimationFrame(animationId)
  }
  if (originalRenderer) originalRenderer.dispose()
  if (processedRenderer) processedRenderer.dispose()
})

// ✅ FUNCIÓN PARA LIMPIAR TODOS LOS ESTADOS
function clearAllStates() {
  // Limpiar estados
  originalModel.value = { name: '', vertices: [], faces: [] }
  processedModel.value = { name: '', vertices: [], faces: [] }
  mensaje.value = ''
  isProcessing.value = false
  
  // Limpiar escenas
  if (originalScene) {
    while(originalScene.children.length > 0) {
      originalScene.remove(originalScene.children[0])
    }
    // Restaurar iluminación
    const originalLight = new THREE.DirectionalLight(0xffffff, 1)
    originalLight.position.set(1, 1, 1).normalize()
    originalScene.add(originalLight)
  }
  
  if (processedScene) {
    while(processedScene.children.length > 0) {
      processedScene.remove(processedScene.children[0])
    }
    // Restaurar iluminación
    const processedLight = new THREE.DirectionalLight(0xffffff, 1)
    processedLight.position.set(1, 1, 1).normalize()
    processedScene.add(processedLight)
  }
}

const initThreeJS = () => {
  objLoader = new OBJLoader()
  
  // Configurar escena original
  if (originalContainer.value) {
    const width = originalContainer.value.clientWidth
    const height = originalContainer.value.clientHeight

    originalScene = new THREE.Scene()
    originalScene.background = new THREE.Color(0xffffff)
    
    originalCamera = new THREE.PerspectiveCamera(75, width / height, 0.1, 1000)
    originalCamera.position.set(0, 0, 4)
    
    originalRenderer = new THREE.WebGLRenderer({ antialias: true })
    originalRenderer.setSize(width, height)
    originalContainer.value.appendChild(originalRenderer.domElement)
    
    const originalLight = new THREE.DirectionalLight(0xffffff, 1)
    originalLight.position.set(1, 1, 1).normalize()
    originalScene.add(originalLight)
    
    originalControls = new OrbitControls(originalCamera, originalRenderer.domElement)
    originalControls.enableDamping = true
    originalControls.dampingFactor = 0.05
    originalControls.autoRotate = false
    originalControls.autoRotateSpeed = 2.0
  }
  
  // Configurar escena procesada
  if (processedContainer.value) {
    const width = processedContainer.value.clientWidth
    const height = processedContainer.value.clientHeight

    processedScene = new THREE.Scene()
    processedScene.background = new THREE.Color(0xffffff)
    
    processedCamera = new THREE.PerspectiveCamera(75, width / height, 0.1, 1000)
    processedCamera.position.set(0, 0, 4)
    
    processedRenderer = new THREE.WebGLRenderer({ antialias: true })
    processedRenderer.setSize(width, height)
    processedContainer.value.appendChild(processedRenderer.domElement)
    
    const processedLight = new THREE.DirectionalLight(0xffffff, 1)
    processedLight.position.set(1, 1, 1).normalize()
    processedScene.add(processedLight)
    
    processedControls = new OrbitControls(processedCamera, processedRenderer.domElement)
    processedControls.enableDamping = true
    processedControls.dampingFactor = 0.05
    processedControls.autoRotate = false
    processedControls.autoRotateSpeed = 2.0
  }
  
  animate()
}

const animate = () => {
  animationId = requestAnimationFrame(animate)
  
  if (originalControls) {
    originalControls.update()
    originalRenderer.render(originalScene, originalCamera)
  }
  
  if (processedControls) {
    processedControls.update()
    processedRenderer.render(processedScene, processedCamera)
  }
}

const loadObjFile = async (event) => {
  const file = event.target.files[0]
  if (!file) return
  
  // ✅ LIMPIAR TODO ANTES DE CARGAR NUEVO MODELO
  clearAllStates()
  
  try {
    const text = await file.text()
    const parsed = parseObjFile(text)
    
    originalModel.value = {
      name: file.name,
      vertices: parsed.vertices,
      faces: parsed.faces
    }
    
    // Cargar en Three.js
    const originalObject = objLoader.parse(text)
    
    // Limpiar escena original
    while(originalScene.children.length > 0) {
      originalScene.remove(originalScene.children[0])
    }
    
    // Restaurar luz
    const originalLight = new THREE.DirectionalLight(0xffffff, 1)
    originalLight.position.set(1, 1, 1).normalize()
    originalScene.add(originalLight)
    
    // ✅ Aplicar material SÓLIDO (sin transparencia)
    const material = new THREE.MeshLambertMaterial({ 
      color: 0x61dafb,
      wireframe: wireframeMode.value,
      transparent: false,  // ✅ Sin transparencia
      opacity: 1.0,        // ✅ Completamente opaco
      side: THREE.DoubleSide // ✅ Renderizar ambos lados
    })
    originalObject.traverse(child => {
      if (child.isMesh) {
        child.material = material
        child.geometry.computeVertexNormals()
      }
    })
    
    originalScene.add(originalObject)
    
    // ✅ Render inmediato
    originalRenderer.render(originalScene, originalCamera)
    
    // Procesar automáticamente
    await processModel(text)
    
  } catch (error) {
    console.error('Error cargando archivo OBJ:', error)
    alert('Error al cargar el archivo. Asegúrate de que sea un archivo .obj válido.')
  }
}

const processModel = async (objContent) => {
  isProcessing.value = true
  mensaje.value = 'Calculando diagramas de Voronoi...'
  
  try {
    const res = await fetch('http://localhost:3001/api/voronoi', {
      method: 'POST',
      headers: { 'Content-Type': 'application/json' },
      body: JSON.stringify({ 
        obj: objContent,
        nombre: originalModel.value?.name || 'modelo'
      })
    })

    const data = await res.json()
    mensaje.value = data.mensaje || 'Redistribución Voronoi completada'
    
    if (data.obj) {
      const parsed = parseObjFile(data.obj)
      processedModel.value = {
        name: 'Voronoi - ' + (originalModel.value?.name || 'modelo'),
        vertices: parsed.vertices,
        faces: parsed.faces
      }
      
      // Cargar en Three.js
      const processedObject = objLoader.parse(data.obj)
      
      // Limpiar escena procesada
      while (processedScene.children.length > 0) {
        processedScene.remove(processedScene.children[0])
      }
      
      // Restaurar luz
      const processedLight = new THREE.DirectionalLight(0xffffff, 1)
      processedLight.position.set(1, 1, 1).normalize()
      processedScene.add(processedLight)
      
      // ✅ Aplicar material SÓLIDO (sin transparencia)
      const material = new THREE.MeshLambertMaterial({ 
        color: 0x8b5cf6,
        wireframe: wireframeMode.value,
        transparent: false,  // ✅ Sin transparencia
        opacity: 1.0,        // ✅ Completamente opaco
        side: THREE.DoubleSide // ✅ Renderizar ambos lados
      })

      processedObject.traverse(child => {
        if (child.isMesh) {
          child.material = material
          child.geometry.computeVertexNormals()
        }
      })
      
      processedScene.add(processedObject)
      
      // ✅ Render inmediato
      processedRenderer.render(processedScene, processedCamera)
    }
    
  } catch (error) {
    console.error('Error procesando modelo:', error)
    mensaje.value = '❌ Error al conectar con la API. Asegúrate de que el servidor esté ejecutándose en http://localhost:3001'
  } finally {
    isProcessing.value = false
  }
}

const parseObjFile = (text) => {
  const vertices = []
  const faces = []
  
  const lines = text.split('\n')
  
  for (const line of lines) {
    const parts = line.trim().split(/\s+/)
    
    if (parts[0] === 'v') {
      const x = parseFloat(parts[1])
      const y = parseFloat(parts[2])
      const z = parseFloat(parts[3])
      vertices.push([x, y, z])
    } else if (parts[0] === 'f') {
      const faceVertices = []
      for (let i = 1; i < parts.length; i++) {
        const vertexIndex = parseInt(parts[i].split('/')[0]) - 1
        faceVertices.push(vertexIndex)
      }
      faces.push(faceVertices)
    }
  }
  
  return { vertices, faces }
}

const onDrop = (event) => {
  event.preventDefault()
  const file = event.dataTransfer.files[0]
  
  if (!file || !file.name.endsWith('.obj')) {
    alert('Por favor arrastra un archivo .obj válido.')
    return
  }

  // ✅ LIMPIAR TODO ANTES DE CARGAR NUEVO MODELO
  clearAllStates()

  const fakeEvent = { target: { files: [file] } }
  loadObjFile(fakeEvent)
}

function resetCameras() {
  if (originalControls) {
    originalCamera.position.set(4, 4, 4)
    originalControls.target.set(0, 0, 0)
    originalControls.update()
    originalRenderer.render(originalScene, originalCamera)
  }
  
  if (processedControls) {
    processedCamera.position.set(4, 4, 4)
    processedControls.target.set(0, 0, 0)
    processedControls.update()
    processedRenderer.render(processedScene, processedCamera)
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
  background-color: #8b5cf6;
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
