<template>
  <div class="max-w-4xl mx-auto">
    <!-- Hero Section -->
    <div class="text-center mb-12">
      <h1 class="text-5xl font-bold text-white mb-6">
        Herramienta de Remeshing 3D Avanzado
      </h1>
      <p class="text-xl text-slate-300 mb-8 leading-relaxed">
        Plataforma especializada en remeshing de modelos 3D con algoritmos de vanguardia: Quadric Error Metrics y Diagramas de Voronoi
      </p>
      <div class="flex justify-center">
        <router-link 
          to="/visualizador"
          class="bg-gradient-to-r from-blue-600 to-purple-600 hover:from-blue-700 hover:to-purple-700 text-white px-8 py-3 rounded-lg font-semibold transition-all duration-300 transform hover:scale-105"
        >
          Comenzar Ahora
        </router-link>
      </div>
    </div>

    <!-- Características -->
    <div class="grid md:grid-cols-2 lg:grid-cols-4 gap-6 mb-12">
      <div class="bg-slate-800/50 backdrop-blur-sm rounded-xl p-6 border border-slate-700">
        <div class="w-12 h-12 bg-blue-600 rounded-lg flex items-center justify-center mb-4">
          <svg class="w-6 h-6 text-white" fill="none" stroke="currentColor" viewBox="0 0 24 24">
            <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M15 12a3 3 0 11-6 0 3 3 0 016 0z"></path>
            <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M2.458 12C3.732 7.943 7.523 5 12 5c4.478 0 8.268 2.943 9.542 7-1.274 4.057-5.064 7-9.542 7-4.477 0-8.268-2.943-9.542-7z"></path>
          </svg>
        </div>
        <h3 class="text-lg font-semibold text-white mb-2">Visualizador</h3>
        <p class="text-slate-400 text-sm">Carga y visualiza modelos OBJ con Three.js para máximo rendimiento</p>
      </div>

      <div class="bg-slate-800/50 backdrop-blur-sm rounded-xl p-6 border border-slate-700">
        <div class="w-12 h-12 bg-green-600 rounded-lg flex items-center justify-center mb-4">
          <svg class="w-6 h-6 text-white" fill="none" stroke="currentColor" viewBox="0 0 24 24">
            <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M9 3v2m6-2v2M9 19v2m6-2v2M5 9H3m2 6H3m18-6h-2m2 6h-2M7 19h10a2 2 0 002-2V7a2 2 0 00-2-2H7a2 2 0 00-2 2v10a2 2 0 002 2zM9 9h6v6H9V9z"></path>
          </svg>
        </div>
        <h3 class="text-lg font-semibold text-white mb-2">Remeshing QEM</h3>
        <p class="text-slate-400 text-sm">Implementación de Quadric Error Metrics para simplificación inteligente preservando características geométricas</p>
      </div>

      <div class="bg-slate-800/50 backdrop-blur-sm rounded-xl p-6 border border-slate-700">
        <div class="w-12 h-12 bg-purple-600 rounded-lg flex items-center justify-center mb-4">
          <svg class="w-6 h-6 text-white" fill="none" stroke="currentColor" viewBox="0 0 24 24">
            <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M19 11H5m14 0a2 2 0 012 2v6a2 2 0 01-2 2H5a2 2 0 01-2-2v-6a2 2 0 012-2m14 0V9a2 2 0 00-2-2M5 11V9a2 2 0 012-2m0 0V5a2 2 0 012-2h6a2 2 0 012 2v2M7 7h10"></path>
          </svg>
        </div>
        <h3 class="text-lg font-semibold text-white mb-2">Remeshing Voronoi</h3>
        <p class="text-slate-400 text-sm">Algoritmo basado en Diagramas de Voronoi para redistribución uniforme y optimización de mallas triangulares</p>
      </div>

      <div class="bg-slate-800/50 backdrop-blur-sm rounded-xl p-6 border border-slate-700">
        <div class="w-12 h-12 bg-orange-600 rounded-lg flex items-center justify-center mb-4">
          <svg class="w-6 h-6 text-white" fill="none" stroke="currentColor" viewBox="0 0 24 24">
            <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M9 19v-6a2 2 0 00-2-2H5a2 2 0 00-2 2v6a2 2 0 002 2h2a2 2 0 002-2zm0 0V9a2 2 0 012-2h2a2 2 0 012 2v10m-6 0a2 2 0 002 2h2a2 2 0 002-2m0 0V5a2 2 0 012-2h2a2 2 0 012 2v4a2 2 0 01-2 2h-2a2 2 0 00-2 2z"></path>
          </svg>
        </div>
        <h3 class="text-lg font-semibold text-white mb-2">Análisis</h3>
        <p class="text-slate-400 text-sm">Métricas detalladas, estadísticas y análisis geométrico completo</p>
      </div>
    </div>

    <!-- Instrucciones -->
    <div class="bg-slate-800/50 backdrop-blur-sm rounded-xl p-8 border border-slate-700">
      <h2 class="text-2xl font-bold text-white mb-6">Cómo usar la herramienta de Remeshing</h2>
      
      <div class="space-y-6">
        <div class="flex items-start space-x-4">
          <div class="w-8 h-8 bg-blue-600 rounded-full flex items-center justify-center text-white font-bold text-sm">1</div>
          <div>
            <h3 class="text-lg font-semibold text-white mb-2">Visualizador</h3>
            <p class="text-slate-300">Arrastra tu archivo .obj al área de visualización para cargarlo instantáneamente con Three.js. Usa los controles de órbita para explorar el modelo.</p>
          </div>
        </div>

        <div class="flex items-start space-x-4">
          <div class="w-8 h-8 bg-green-600 rounded-full flex items-center justify-center text-white font-bold text-sm">2</div>
          <div>
            <h3 class="text-lg font-semibold text-white mb-2">Algoritmos de Remeshing</h3>
            <p class="text-slate-300">Selecciona entre nuestras dos implementaciones: QEM para simplificación preservando detalles críticos, o Voronoi para redistribución uniforme de vértices. Compara resultados lado a lado.</p>
          </div>
        </div>

        <div class="flex items-start space-x-4">
          <div class="w-8 h-8 bg-purple-600 rounded-full flex items-center justify-center text-white font-bold text-sm">3</div>
          <div>
            <h3 class="text-lg font-semibold text-white mb-2">Análisis y Exportación</h3>
            <p class="text-slate-300">Analiza métricas de calidad del remeshing: reducción de polígonos, preservación de características y uniformidad de la malla. Exporta el resultado optimizado.</p>
          </div>
        </div>
      </div>
    </div>

    <!-- Información técnica -->
    <div class="mt-12 grid md:grid-cols-2 gap-8">
      <div class="bg-slate-800/50 backdrop-blur-sm rounded-xl p-6 border border-slate-700">
        <h3 class="text-xl font-semibold text-white mb-4">Formatos Soportados</h3>
        <ul class="space-y-2 text-slate-300">
          <li class="flex items-center">
            <span class="w-2 h-2 bg-green-500 rounded-full mr-3"></span>
            Archivos .obj (Wavefront OBJ)
          </li>
          <li class="flex items-center">
            <span class="w-2 h-2 bg-green-500 rounded-full mr-3"></span>
            Vértices y caras
          </li>
          <li class="flex items-center">
            <span class="w-2 h-2 bg-green-500 rounded-full mr-3"></span>
            Modelos complejos de alta resolución
          </li>
        </ul>
      </div>

      <div class="bg-slate-800/50 backdrop-blur-sm rounded-xl p-6 border border-slate-700">
        <h3 class="text-xl font-semibold text-white mb-4">Algoritmos Implementados</h3>
        <ul class="space-y-2 text-slate-300">
          <li class="flex items-center">
            <span class="w-2 h-2 bg-blue-500 rounded-full mr-3"></span>
            Quadric Error Metrics (QEM) - Simplificación inteligente
          </li>
          <li class="flex items-center">
            <span class="w-2 h-2 bg-blue-500 rounded-full mr-3"></span>
            Diagramas de Voronoi - Redistribución uniforme
          </li>
          <li class="flex items-center">
            <span class="w-2 h-2 bg-blue-500 rounded-full mr-3"></span>
            Análisis comparativo de calidad de malla
          </li>
        </ul>
      </div>
    </div>
  </div>
</template>
