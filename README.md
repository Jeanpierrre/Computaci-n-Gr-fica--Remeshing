# Remeshing 🔍
## 📝 Descripción del Proyecto
lorem
## 🧑‍🤝‍🧑 Integrantes del Equipo

Un equipo diverso y apasionado de estudiantes está detrás de este proyecto, listo para sumergirse en los algoritmos de remeshing y sus optimizaciones. Permítanos presentarnos:

|    Jean Pierre Sotomayor    |    Leandro Machaca    |    Gonzalo Rodriguez    |
| ----------- | ----------- | ----------- |
| ![](https://avatars.githubusercontent.com/u/85184643?v=4) | ![](https://avatars.githubusercontent.com/u/102132128?s=400&v=4) | ![](https://avatars.githubusercontent.com/u/85299734?v=4) |
| [github.com/JeanPierre](https://github.com/Jeanpierrre) | [github.com/JLeandroJM](https://github.com/JLeandroJM) | [github.com/Gonzalo-Rod](https://github.com/Gonzalo-Rod) 


## 📂 Estructura del Repositorio

- 📁 `backend`: En esta carpeta se encuentran los archivos necesarios para ejecutar el backend del proyecto
- | 📄 `backend.js`: En esta carpeta se encuentran los documentos de texto que explican el proyecto.
- | 📄 `quadric_remeshing.exe`: Ejecutable del modelo de Quadric Error.
- | 📄 `quadratic_remesh.exe`: Ejecutable del modelo de Voronoi.
- 📁 `frontend`: En esta carpeta se encuentran los archivos necesarios para ejecutar el frontend del proyecto
- | 📁 `public`
- | 📁 `src`
- | | 📄 `main.js`
- | | 📄 `App.vue`
- | | 📁 `components`
- | 📄 `angle-visualizer.vue`
- | 📄 `index.html`
- | 📄 `package-lock.json`
- | 📄 `package.json`
- 📁 `QEM`: En esta carpeta se encuentran los archivos del modelo de Quadric Errors
- | 📁 `src`
- | 📁 `dependencies`
- | 📄 `CMakeList.txt`
- | 📄 `main.cpp`

## Ejecucion del Proyecto

### Ejecución con Página Web

#### Backend:

1. Navega a la carpeta `Backend`:
   ```bash
   cd backend
   ```
2. Instala las dependencias necesarias:
   ```bash
   npm install express cors
   ```
3. Inicia el servidor:
   ```bash
   node backend.js
   ```

#### FrontEnd:

1. Instala las dependencias necesarias:
   ```bash
   npm install
   ```
2. Inicia el servidor de desarrollo:
   ```bash
   npm run dev
   ```

### Ejecución por Consola

1. Navega a la carpeta del modelo:
   ```bash
   cd "modelo"
   ```
2. Crea una carpeta de compilación:
   ```bash
   mkdir build
   cd build
   ```
3. Configura el proyecto con CMake:
   ```bash
   cmake ..
   ```
4. Extrae la carpeta de dependencias.
5. Compila el proyecto:
   ```bash
   cmake --build .
   ```
   Esto generará el archivo ejecutable `modelo.exe`.
6. Para ejecutar el archivo ejecutable:
   ```bash
   modelo.exe input_obj output_obj
   ```
