# 🚀 Módulo 3: DOM y Eventos

## 📚 Descripción del Módulo

Este módulo te introduce al Document Object Model (DOM) y al sistema de eventos de JavaScript. Aprenderás a manipular elementos HTML dinámicamente, crear contenido interactivo y responder a las acciones del usuario. El DOM es la interfaz entre JavaScript y el HTML, permitiendo crear aplicaciones web dinámicas y responsivas.

## 🎯 Objetivos de Aprendizaje

- Comprender la estructura del DOM y su jerarquía
- Dominar la selección y manipulación de elementos HTML
- Implementar manejo de eventos de manera efectiva
- Crear contenido dinámico y formularios interactivos
- Aplicar buenas prácticas de manipulación del DOM

## 📖 Contenido Teórico

### 1. Introducción al DOM

El DOM (Document Object Model) es una representación en memoria del documento HTML que permite a JavaScript acceder y modificar la estructura, contenido y estilo de una página web.

#### Estructura del DOM
```javascript
// El DOM representa el HTML como un árbol de nodos
document                    // Nodo raíz
├── html                   // Elemento HTML
│   ├── head              // Elemento HEAD
│   │   ├── title         // Elemento TITLE
│   │   └── meta          // Elemento META
│   └── body              // Elemento BODY
│       ├── h1            // Elemento H1
│       ├── p             // Elemento P
│       └── div           // Elemento DIV
```

#### Tipos de Nodos
```javascript
// Nodo de elemento (Element Node)
const elemento = document.createElement('div');

// Nodo de texto (Text Node)
const texto = document.createTextNode('Hola Mundo');

// Nodo de atributo (Attribute Node)
const atributo = document.createAttribute('class');

// Nodo de comentario (Comment Node)
const comentario = document.createComment('Este es un comentario');
```

### 2. Selección de Elementos

#### Métodos de Selección Básicos
```javascript
// Selección por ID (retorna un elemento)
const elemento = document.getElementById('miId');

// Selección por clase (retorna HTMLCollection)
const elementos = document.getElementsByClassName('miClase');

// Selección por tag (retorna HTMLCollection)
const parrafos = document.getElementsByTagName('p');

// Selección por nombre (retorna NodeList)
const inputs = document.getElementsByName('miInput');
```

#### Métodos de Selección Modernos (ES6+)
```javascript
// querySelector (primer elemento que coincida)
const primerElemento = document.querySelector('.miClase');
const primerParrafo = document.querySelector('p');
const elementoConId = document.querySelector('#miId');

// querySelectorAll (todos los elementos que coincidan)
const todosLosElementos = document.querySelectorAll('.miClase');
const todosLosParrafos = document.querySelectorAll('p');

// Selección por atributos
const elementosConData = document.querySelectorAll('[data-attr]');
const inputsRequeridos = document.querySelectorAll('input[required]');
const enlacesExternos = document.querySelectorAll('a[target="_blank"]');
```

#### Selección Relativa
```javascript
// Selección desde un elemento padre
const contenedor = document.querySelector('.contenedor');
const hijos = contenedor.querySelectorAll('.hijo');

// Navegación por el DOM
const primerHijo = contenedor.firstElementChild;
const ultimoHijo = contenedor.lastElementChild;
const siguienteHermano = contenedor.nextElementSibling;
const hermanoAnterior = contenedor.previousElementSibling;
const padre = contenedor.parentElement;
```

### 3. Creación y Manipulación de Elementos

#### Creación de Elementos
```javascript
// Crear un nuevo elemento
const nuevoDiv = document.createElement('div');
const nuevoParrafo = document.createElement('p');
const nuevoBoton = document.createElement('button');

// Crear nodos de texto
const texto = document.createTextNode('Este es un texto');
const textoInterpolado = document.createTextNode(`Usuario: ${nombre}`);

// Crear fragmentos (para mejor performance)
const fragmento = document.createDocumentFragment();
for (let i = 0; i < 100; i++) {
    const li = document.createElement('li');
    li.textContent = `Elemento ${i}`;
    fragmento.appendChild(li);
}
```

#### Manipulación de Contenido
```javascript
// Cambiar contenido de texto
elemento.textContent = 'Nuevo texto';
elemento.innerText = 'Texto con formato preservado';

// Cambiar HTML interno
elemento.innerHTML = '<strong>Texto en negrita</strong>';

// Cambiar atributos
elemento.setAttribute('class', 'nuevaClase');
elemento.setAttribute('data-id', '123');
elemento.removeAttribute('data-old');

// Acceso directo a propiedades comunes
elemento.className = 'clase1 clase2';
elemento.id = 'nuevoId';
elemento.href = 'https://ejemplo.com';
```

#### Manipulación de Clases CSS
```javascript
// Añadir clases
elemento.classList.add('nuevaClase');
elemento.classList.add('clase1', 'clase2');

// Remover clases
elemento.classList.remove('claseAntigua');
elemento.classList.remove('clase1', 'clase2');

// Alternar clases
elemento.classList.toggle('claseActiva');
elemento.classList.toggle('claseVisible', true); // Forzar estado

// Verificar si tiene una clase
if (elemento.classList.contains('claseEspecial')) {
    console.log('El elemento tiene la clase especial');
}

// Reemplazar clases
elemento.classList.replace('claseVieja', 'claseNueva');
```

### 4. Manipulación de la Estructura del DOM

#### Añadir Elementos
```javascript
// appendChild (añade al final)
contenedor.appendChild(nuevoElemento);

// insertBefore (añade en posición específica)
contenedor.insertBefore(nuevoElemento, elementoReferencia);

// append (múltiples elementos, ES6+)
contenedor.append(elemento1, elemento2, texto);

// prepend (añade al inicio, ES6+)
contenedor.prepend(elemento1, elemento2, texto);

// after (después del elemento, ES6+)
elementoReferencia.after(nuevoElemento);

// before (antes del elemento, ES6+)
elementoReferencia.before(nuevoElemento);
```

#### Remover y Reemplazar Elementos
```javascript
// Remover elemento
elemento.remove(); // ES6+

// Remover hijo específico
contenedor.removeChild(elementoHijo);

// Reemplazar elemento
contenedor.replaceChild(nuevoElemento, elementoViejo);

// Reemplazar con after/before
elementoViejo.replaceWith(nuevoElemento); // ES6+
```

#### Clonación de Elementos
```javascript
// Clonación superficial (solo el elemento)
const clonSuperficial = elemento.cloneNode(false);

// Clonación profunda (elemento y todos sus hijos)
const clonProfundo = elemento.cloneNode(true);

// Modificar el clon antes de insertarlo
clonProfundo.id = 'nuevoId';
clonProfundo.classList.add('clonado');
contenedor.appendChild(clonProfundo);
```

### 5. Sistema de Eventos

#### Conceptos Básicos de Eventos
Un evento es una acción que ocurre en el navegador (click, submit, keypress, etc.). JavaScript puede "escuchar" estos eventos y ejecutar código en respuesta.

#### Tipos de Eventos Comunes
```javascript
// Eventos del mouse
'click'        // Clic del botón izquierdo
'dblclick'     // Doble clic
'mousedown'    // Presionar botón del mouse
'mouseup'      // Soltar botón del mouse
'mousemove'    // Mover el mouse
'mouseenter'   // Entrar en el elemento
'mouseleave'   // Salir del elemento
'mouseover'    // Sobre el elemento
'mouseout'     // Fuera del elemento

// Eventos del teclado
'keydown'      // Presionar tecla
'keyup'        // Soltar tecla
'keypress'     // Presionar tecla (solo caracteres)

// Eventos de formulario
'submit'       // Enviar formulario
'change'       // Cambiar valor de input
'input'        // Entrada en tiempo real
'focus'        // Elemento recibe foco
'blur'         // Elemento pierde foco

// Eventos del documento
'DOMContentLoaded'  // DOM completamente cargado
'load'              // Página completamente cargada
'resize'            // Redimensionar ventana
'scroll'            // Hacer scroll
```

#### Agregar Event Listeners
```javascript
// Método addEventListener (recomendado)
elemento.addEventListener('click', function(evento) {
    console.log('Elemento clickeado:', evento.target);
});

// Arrow function como callback
elemento.addEventListener('mouseenter', (evento) => {
    evento.target.style.backgroundColor = 'yellow';
});

// Función nombrada
function manejarClick(evento) {
    console.log('Click en:', evento.target);
}
elemento.addEventListener('click', manejarClick);

// Múltiples eventos en el mismo elemento
elemento.addEventListener('click', manejarClick);
elemento.addEventListener('mouseenter', manejarMouseEnter);
elemento.addEventListener('mouseleave', manejarMouseLeave);
```

#### Remover Event Listeners
```javascript
// Remover listener específico
elemento.removeEventListener('click', manejarClick);

// Remover todos los listeners (no es posible directamente)
// Se debe mantener referencia a la función
```

#### Event Object
```javascript
elemento.addEventListener('click', function(evento) {
    // Propiedades del evento
    console.log('Tipo de evento:', evento.type);
    console.log('Elemento objetivo:', evento.target);
    console.log('Elemento actual:', evento.currentTarget);
    console.log('Coordenadas del mouse:', evento.clientX, evento.clientY);
    console.log('Tecla presionada:', evento.key);
    console.log('Teclas modificadoras:', evento.ctrlKey, evento.shiftKey, evento.altKey);
    
    // Prevenir comportamiento por defecto
    evento.preventDefault();
    
    // Detener propagación
    evento.stopPropagation();
});
```

### 6. Event Delegation

La delegación de eventos es una técnica que aprovecha el bubbling de eventos para manejar eventos en elementos hijos desde un elemento padre.

```javascript
// En lugar de agregar listeners a cada elemento hijo
const botones = document.querySelectorAll('button');
botones.forEach(boton => {
    boton.addEventListener('click', manejarClick);
});

// Usar delegación en el contenedor padre
const contenedor = document.querySelector('.contenedor');
contenedor.addEventListener('click', function(evento) {
    // Verificar si el elemento clickeado es un botón
    if (evento.target.matches('button')) {
        console.log('Botón clickeado:', evento.target.textContent);
    }
    
    // Verificar múltiples tipos de elementos
    if (evento.target.matches('button.delete')) {
        eliminarElemento(evento.target);
    } else if (evento.target.matches('button.edit')) {
        editarElemento(evento.target);
    }
});
```

### 7. Formularios y Validación

#### Acceso a Formularios
```javascript
// Seleccionar formulario
const formulario = document.querySelector('form');
const formularioPorId = document.getElementById('miFormulario');

// Acceso a elementos del formulario
const nombreInput = formulario.querySelector('input[name="nombre"]');
const emailInput = formulario.querySelector('input[type="email"]');
const submitBoton = formulario.querySelector('button[type="submit"]');
```

#### Manejo de Eventos de Formulario
```javascript
// Evento submit
formulario.addEventListener('submit', function(evento) {
    evento.preventDefault(); // Prevenir envío por defecto
    
    // Obtener valores
    const nombre = nombreInput.value.trim();
    const email = emailInput.value.trim();
    
    // Validar
    if (validarFormulario(nombre, email)) {
        enviarFormulario({ nombre, email });
    }
});

// Evento input (tiempo real)
nombreInput.addEventListener('input', function(evento) {
    const valor = evento.target.value;
    validarCampoEnTiempoReal('nombre', valor);
});

// Evento change
emailInput.addEventListener('change', function(evento) {
    const email = evento.target.value;
    if (validarEmail(email)) {
        evento.target.classList.add('valido');
        evento.target.classList.remove('invalido');
    } else {
        evento.target.classList.add('invalido');
        evento.target.classList.remove('valido');
    }
});
```

#### Validación de Formularios
```javascript
function validarFormulario(nombre, email) {
    const errores = [];
    
    // Validar nombre
    if (!nombre || nombre.length < 2) {
        errores.push('El nombre debe tener al menos 2 caracteres');
    }
    
    // Validar email
    const emailRegex = /^[^\s@]+@[^\s@]+\.[^\s@]+$/;
    if (!email || !emailRegex.test(email)) {
        errores.push('El email no tiene un formato válido');
    }
    
    // Mostrar errores
    if (errores.length > 0) {
        mostrarErrores(errores);
        return false;
    }
    
    return true;
}

function mostrarErrores(errores) {
    const contenedorErrores = document.querySelector('.errores');
    contenedorErrores.innerHTML = '';
    
    errores.forEach(error => {
        const elementoError = document.createElement('div');
        elementoError.className = 'error';
        elementoError.textContent = error;
        contenedorErrores.appendChild(elementoError);
    });
}
```

### 8. Manipulación Avanzada del DOM

#### Trabajar con Estilos
```javascript
// Acceso directo a estilos inline
elemento.style.backgroundColor = 'red';
elemento.style.fontSize = '16px';
elemento.style.border = '1px solid black';

// Múltiples estilos
Object.assign(elemento.style, {
    backgroundColor: 'blue',
    color: 'white',
    padding: '10px',
    borderRadius: '5px'
});

// Obtener estilos computados
const estilos = window.getComputedStyle(elemento);
const colorFondo = estilos.backgroundColor;
const ancho = estilos.width;
```

#### Trabajar con Dimensiones y Posición
```javascript
// Dimensiones del elemento
const ancho = elemento.offsetWidth;
const alto = elemento.offsetHeight;
const anchoCliente = elemento.clientWidth;
const altoCliente = elemento.clientHeight;
const anchoScroll = elemento.scrollWidth;
const altoScroll = elemento.scrollHeight;

// Posición del elemento
const rect = elemento.getBoundingClientRect();
const top = rect.top;
const left = rect.left;
const right = rect.right;
const bottom = rect.bottom;

// Posición de scroll
const scrollTop = elemento.scrollTop;
const scrollLeft = elemento.scrollLeft;
```

#### Trabajar con Datos Personalizados
```javascript
// Establecer data attributes
elemento.dataset.id = '123';
elemento.dataset.usuario = 'juan';
elemento.dataset.activo = 'true';

// Obtener data attributes
const id = elemento.dataset.id;
const usuario = elemento.dataset.usuario;
const activo = elemento.dataset.activo === 'true';

// También se puede usar setAttribute/getAttribute
elemento.setAttribute('data-categoria', 'tecnologia');
const categoria = elemento.getAttribute('data-categoria');
```

## 🧪 Ejercicios Prácticos

### Ejercicio 1: Galería de Imágenes
Crea una galería de imágenes con navegación y zoom.

```javascript
// Tu código aquí
class GaleriaImagenes {
    constructor(contenedor) {
        this.contenedor = contenedor;
        this.imagenes = [];
        this.imagenActual = 0;
        this.inicializar();
    }
    
    inicializar() {
        // Implementa la inicialización
    }
    
    mostrarImagen(index) {
        // Implementa la visualización
    }
    
    siguiente() {
        // Implementa navegación
    }
    
    anterior() {
        // Implementa navegación
    }
}
```

### Ejercicio 2: Validador de Formularios
Implementa un sistema de validación en tiempo real para formularios.

```javascript
// Tu código aquí
class ValidadorFormulario {
    constructor(formulario) {
        this.formulario = formulario;
        this.reglas = {};
        this.inicializar();
    }
    
    agregarRegla(campo, regla) {
        // Implementa reglas de validación
    }
    
    validarCampo(campo) {
        // Implementa validación individual
    }
    
    validarFormulario() {
        // Implementa validación completa
    }
}
```

### Ejercicio 3: Drag and Drop
Crea un sistema de arrastrar y soltar elementos.

```javascript
// Tu código aquí
class DragAndDrop {
    constructor(elementos) {
        this.elementos = elementos;
        this.elementoArrastrado = null;
        this.inicializar();
    }
    
    inicializar() {
        // Implementa eventos de drag and drop
    }
    
    manejarDragStart(evento) {
        // Implementa inicio de arrastre
    }
    
    manejarDragOver(evento) {
        // Implementa arrastre sobre
    }
    
    manejarDrop(evento) {
        // Implementa soltar elemento
    }
}
```

### Ejercicio 4: Modal Dinámico
Implementa un sistema de modales reutilizable.

```javascript
// Tu código aquí
class Modal {
    constructor(opciones = {}) {
        this.opciones = { ...this.opcionesPorDefecto, ...opciones };
        this.modal = null;
        this.crear();
    }
    
    crear() {
        // Implementa creación del modal
    }
    
    mostrar() {
        // Implementa mostrar modal
    }
    
    ocultar() {
        // Implementa ocultar modal
    }
    
    destruir() {
        // Implementa destrucción
    }
}
```

### Ejercicio 5: Sistema de Tabs
Crea un sistema de pestañas dinámico y accesible.

```javascript
// Tu código aquí
class SistemaTabs {
    constructor(contenedor) {
        this.contenedor = contenedor;
        this.tabs = [];
        this.tabActiva = 0;
        this.inicializar();
    }
    
    inicializar() {
        // Implementa inicialización
    }
    
    cambiarTab(index) {
        // Implementa cambio de tab
    }
    
    agregarTab(titulo, contenido) {
        // Implementa añadir nueva tab
    }
}
```

### Ejercicio 6: Carousel de Contenido
Implementa un carousel automático con controles manuales.

```javascript
// Tu código aquí
class Carousel {
    constructor(contenedor, opciones = {}) {
        this.contenedor = contenedor;
        this.opciones = { ...this.opcionesPorDefecto, ...opciones };
        this.slideActual = 0;
        this.intervalo = null;
        this.inicializar();
    }
    
    inicializar() {
        // Implementa inicialización
    }
    
    siguiente() {
        // Implementa siguiente slide
    }
    
    anterior() {
        // Implementa slide anterior
    }
    
    irASlide(index) {
        // Implementa ir a slide específico
    }
    
    iniciarAutoplay() {
        // Implementa reproducción automática
    }
    
    detenerAutoplay() {
        // Implementa detener autoplay
    }
}
```

### Ejercicio 7: Editor de Texto en Tiempo Real
Crea un editor de texto con formato básico.

```javascript
// Tu código aquí
class EditorTexto {
    constructor(contenedor) {
        this.contenedor = contenedor;
        this.contenido = '';
        this.historial = [];
        this.posicionHistorial = -1;
        this.inicializar();
    }
    
    inicializar() {
        // Implementa inicialización
    }
    
    aplicarFormato(tipo) {
        // Implementa formato (negrita, cursiva, etc.)
    }
    
    deshacer() {
        // Implementa deshacer
    }
    
    rehacer() {
        // Implementa rehacer
    }
    
    guardar() {
        // Implementa guardado
    }
}
```

### Ejercicio 8: Sistema de Notificaciones
Implementa un sistema de notificaciones toast.

```javascript
// Tu código aquí
class SistemaNotificaciones {
    constructor() {
        this.notificaciones = [];
        this.contenedor = null;
        this.inicializar();
    }
    
    inicializar() {
        // Implementa inicialización
    }
    
    mostrar(mensaje, tipo = 'info', duracion = 5000) {
        // Implementa mostrar notificación
    }
    
    ocultar(id) {
        // Implementa ocultar notificación
    }
    
    limpiarTodas() {
        // Implementa limpiar todas
    }
}
```

### Ejercicio 9: Gestor de Archivos
Crea un gestor de archivos con vista previa.

```javascript
// Tu código aquí
class GestorArchivos {
    constructor(contenedor) {
        this.contenedor = contenedor;
        this.archivos = [];
        this.inicializar();
    }
    
    inicializar() {
        // Implementa inicialización
    }
    
    agregarArchivo(archivo) {
        // Implementa añadir archivo
    }
    
    eliminarArchivo(id) {
        // Implementa eliminar archivo
    }
    
    mostrarVistaPrevia(archivo) {
        // Implementa vista previa
    }
    
    descargarArchivo(archivo) {
        // Implementa descarga
    }
}
```

### Ejercicio 10: Sistema de Búsqueda en Tiempo Real
Implementa un sistema de búsqueda con filtrado instantáneo.

```javascript
// Tu código aquí
class BuscadorTiempoReal {
    constructor(contenedor, datos) {
        this.contenedor = contenedor;
        this.datos = datos;
        this.resultados = [];
        this.filtros = {};
        this.inicializar();
    }
    
    inicializar() {
        // Implementa inicialización
    }
    
    buscar(termino) {
        // Implementa búsqueda
    }
    
    aplicarFiltro(tipo, valor) {
        // Implementa filtros
    }
    
    mostrarResultados() {
        // Implementa visualización
    }
    
    ordenarResultados(criterio) {
        // Implementa ordenamiento
    }
}
```

## 🎯 Proyecto Integrador: Galería de Imágenes Interactiva

### Descripción
Crea una galería de imágenes completa con funcionalidades avanzadas de manipulación del DOM y manejo de eventos.

### Requisitos
- Galería responsive con grid de imágenes
- Lightbox para visualización ampliada
- Filtros por categorías y tags
- Búsqueda en tiempo real
- Sistema de favoritos
- Galería de miniaturas
- Navegación por teclado
- Gestión de estado con localStorage

### Funcionalidades a Implementar
1. **Grid de Imágenes**: Layout responsive con CSS Grid/Flexbox
2. **Lightbox**: Modal para visualización ampliada
3. **Filtros**: Por categoría, color, tamaño
4. **Búsqueda**: Filtrado en tiempo real
5. **Favoritos**: Sistema de marcado y persistencia
6. **Navegación**: Teclado, mouse, touch
7. **Optimización**: Lazy loading de imágenes
8. **Accesibilidad**: ARIA labels, navegación por teclado

### Estructura del Proyecto
```
galeria-imagenes/
├── index.html
├── styles.css
├── js/
│   ├── app.js
│   ├── galeria.js
│   ├── lightbox.js
│   ├── filtros.js
│   ├── favoritos.js
│   └── utils.js
├── data/
│   └── imagenes.json
└── README.md
```

## 📚 Recursos Adicionales

### Documentación
- [MDN - DOM](https://developer.mozilla.org/en-US/docs/Web/API/Document_Object_Model)
- [MDN - Eventos](https://developer.mozilla.org/en-US/docs/Web/Events)
- [MDN - Formularios](https://developer.mozilla.org/en-US/docs/Learn/Forms)

### Herramientas de Desarrollo
- [Chrome DevTools](https://developers.google.com/web/tools/chrome-devtools)
- [Firefox Developer Tools](https://developer.mozilla.org/en-US/docs/Tools)
- [DOM Inspector](https://addons.mozilla.org/en-US/firefox/addon/dom-inspector-6622/)

### Bibliotecas Útiles
- [jQuery](https://jquery.com/) - Manipulación del DOM simplificada
- [Lodash](https://lodash.com/) - Utilidades para arrays y objetos
- [Hammer.js](https://hammerjs.github.io/) - Gestos táctiles

## 🎯 Evaluación del Módulo

### Criterios de Evaluación
- ✅ Comprensión de la estructura del DOM
- ✅ Selección y manipulación efectiva de elementos
- ✅ Implementación correcta de event listeners
- ✅ Manejo apropiado de formularios
- ✅ Completación del proyecto integrador

### Puntos Clave a Dominar
1. **DOM**: Estructura, nodos, navegación
2. **Selección**: Métodos modernos y compatibilidad
3. **Manipulación**: Creación, modificación, eliminación
4. **Eventos**: Tipos, delegación, manejo
5. **Formularios**: Validación, eventos, manejo
6. **Performance**: Event delegation, fragmentos, optimización

---

**¡Completa todos los ejercicios y el proyecto integrador antes de continuar al siguiente módulo!** 🚀

*El siguiente módulo cubrirá Programación Asíncrona, donde aprenderás a manejar operaciones no bloqueantes y APIs.*
