# 🚀 Módulo 2: Funciones y Scope

## 📚 Descripción del Módulo

Este módulo profundiza en las funciones de JavaScript, el concepto de scope y los closures. Aprenderás a crear código reutilizable, modular y bien estructurado. Las funciones son el corazón de la programación en JavaScript y entender cómo funcionan es fundamental para escribir código profesional.

## 🎯 Objetivos de Aprendizaje

- Dominar la declaración y expresión de funciones
- Comprender el concepto de scope y lexical environment
- Implementar arrow functions y funciones modernas
- Crear closures efectivos y entender su funcionamiento
- Aplicar conceptos de hoisting y temporal dead zone

## 📖 Contenido Teórico

### 1. Declaración de Funciones

Las funciones son bloques de código reutilizables que pueden recibir parámetros y retornar valores.

#### Function Declaration (Declaración de Función)
```javascript
// Declaración tradicional
function saludar(nombre) {
    return `¡Hola ${nombre}!`;
}

// Función con múltiples parámetros
function sumar(a, b) {
    return a + b;
}

// Función sin parámetros
function obtenerFechaActual() {
    return new Date().toLocaleDateString();
}

// Función con parámetros por defecto
function crearUsuario(nombre, edad = 18, activo = true) {
    return {
        nombre,
        edad,
        activo,
        fechaCreacion: new Date()
    };
}
```

#### Function Expression (Expresión de Función)
```javascript
// Función anónima asignada a variable
const multiplicar = function(a, b) {
    return a * b;
};

// Función nombrada (permite recursión)
const factorial = function fact(n) {
    if (n <= 1) return 1;
    return n * fact(n - 1);
};

// Función inmediatamente invocada (IIFE)
const resultado = (function() {
    const secreto = "valor oculto";
    return secreto.toUpperCase();
})();
```

### 2. Arrow Functions (Funciones Flecha)

Las arrow functions son una sintaxis más concisa introducida en ES6.

```javascript
// Sintaxis básica
const cuadrado = (x) => x * x;

// Con múltiples parámetros
const dividir = (a, b) => a / b;

// Con cuerpo de función
const esPar = (numero) => {
    if (numero % 2 === 0) {
        return true;
    }
    return false;
};

// Retorno implícito de objeto
const crearPersona = (nombre, edad) => ({
    nombre,
    edad,
    esAdulto: edad >= 18
});

// Arrow function como callback
const numeros = [1, 2, 3, 4, 5];
const cuadrados = numeros.map(n => n * n);
const pares = numeros.filter(n => n % 2 === 0);
```

**Ventajas de Arrow Functions:**
- Sintaxis más concisa
- No tienen su propio `this` (heredan del contexto padre)
- No tienen `arguments` object
- No pueden ser usadas como constructores

### 3. Scope y Lexical Environment

El scope determina la accesibilidad de variables en diferentes partes del código.

#### Global Scope
```javascript
// Variables globales (accesibles en todo el código)
const API_URL = "https://api.ejemplo.com";
let contadorGlobal = 0;

function incrementarGlobal() {
    contadorGlobal++;
    console.log(contadorGlobal);
}
```

#### Function Scope
```javascript
function ejemploScope() {
    // Variable local a esta función
    let variableLocal = "Solo visible aquí";
    
    if (true) {
        // Variable de bloque (ES6+)
        let variableBloque = "Visible solo en este bloque";
        console.log(variableBloque); // ✅ Funciona
    }
    
    // console.log(variableBloque); // ❌ Error - no definida
    console.log(variableLocal); // ✅ Funciona
}

// console.log(variableLocal); // ❌ Error - no definida
```

#### Block Scope (ES6+)
```javascript
{
    let variableBloque = "Solo visible en este bloque";
    const constanteBloque = "Constante de bloque";
    
    console.log(variableBloque); // ✅ Funciona
}

// console.log(variableBloque); // ❌ Error - no definida
```

### 4. Closures

Un closure es una función que tiene acceso a variables de su scope externo incluso después de que la función externa haya terminado de ejecutarse.

```javascript
// Ejemplo básico de closure
function crearContador() {
    let contador = 0;
    
    return function() {
        contador++;
        return contador;
    };
}

const miContador = crearContador();
console.log(miContador()); // 1
console.log(miContador()); // 2
console.log(miContador()); // 3

// Closure con parámetros
function crearMultiplicador(factor) {
    return function(numero) {
        return numero * factor;
    };
}

const duplicar = crearMultiplicador(2);
const triplicar = crearMultiplicador(3);

console.log(duplicar(5));  // 10
console.log(triplicar(5)); // 15

// Closure para encapsulación
function crearBanco() {
    let saldo = 0;
    
    return {
        depositar: function(cantidad) {
            saldo += cantidad;
            return `Depositado: $${cantidad}. Saldo: $${saldo}`;
        },
        retirar: function(cantidad) {
            if (cantidad <= saldo) {
                saldo -= cantidad;
                return `Retirado: $${cantidad}. Saldo: $${saldo}`;
            }
            return "Saldo insuficiente";
        },
        obtenerSaldo: function() {
            return `Saldo actual: $${saldo}`;
        }
    };
}

const miBanco = crearBanco();
console.log(miBanco.depositar(100)); // "Depositado: $100. Saldo: $100"
console.log(miBanco.retirar(30));    // "Retirado: $30. Saldo: $70"
console.log(miBanco.obtenerSaldo()); // "Saldo actual: $70"
```

### 5. Hoisting y Temporal Dead Zone

#### Hoisting con var
```javascript
// El hoisting "eleva" las declaraciones
console.log(miVariable); // undefined (no error)
var miVariable = "valor";

// Es equivalente a:
var miVariable;
console.log(miVariable); // undefined
miVariable = "valor";
```

#### Temporal Dead Zone con let/const
```javascript
// let y const no tienen hoisting
console.log(miVariable); // ❌ Error: Cannot access before initialization
let miVariable = "valor";

// const también
console.log(miConstante); // ❌ Error: Cannot access before initialization
const miConstante = "valor";
```

#### Hoisting de Funciones
```javascript
// Las declaraciones de función se elevan completamente
saludar("Juan"); // ✅ Funciona

function saludar(nombre) {
    console.log(`Hola ${nombre}`);
}

// Las expresiones de función NO se elevan
// saludarExpresion("Juan"); // ❌ Error: saludarExpresion is not a function

const saludarExpresion = function(nombre) {
    console.log(`Hola ${nombre}`);
};
```

### 6. Parámetros y Argumentos

```javascript
// Parámetros por defecto
function crearPerfil(nombre, edad = 25, ciudad = "Desconocida") {
    return {
        nombre,
        edad,
        ciudad
    };
}

// Parámetros rest (ES6+)
function sumar(...numeros) {
    return numeros.reduce((total, num) => total + num, 0);
}

console.log(sumar(1, 2, 3, 4, 5)); // 15

// Destructuring de parámetros
function procesarUsuario({ nombre, edad, email }) {
    console.log(`Procesando usuario: ${nombre}, ${edad} años, ${email}`);
}

const usuario = {
    nombre: "Ana",
    edad: 30,
    email: "ana@ejemplo.com"
};

procesarUsuario(usuario);

// Argumentos object (funciones tradicionales)
function mostrarArgumentos() {
    console.log("Número de argumentos:", arguments.length);
    for (let i = 0; i < arguments.length; i++) {
        console.log(`Argumento ${i}:`, arguments[i]);
    }
}

mostrarArgumentos("a", "b", "c");
```

### 7. Callbacks y Funciones de Orden Superior

```javascript
// Función de orden superior
function procesarArray(array, callback) {
    const resultado = [];
    for (let i = 0; i < array.length; i++) {
        resultado.push(callback(array[i], i, array));
    }
    return resultado;
}

// Uso con diferentes callbacks
const numeros = [1, 2, 3, 4, 5];

const duplicados = procesarArray(numeros, n => n * 2);
const cuadrados = procesarArray(numeros, n => n ** 2);
const comoTexto = procesarArray(numeros, n => `Número ${n}`);

console.log(duplicados);  // [2, 4, 6, 8, 10]
console.log(cuadrados);   // [1, 4, 9, 16, 25]
console.log(comoTexto);   // ["Número 1", "Número 2", ...]

// Callback con setTimeout
function ejecutarDespues(callback, tiempo) {
    setTimeout(callback, tiempo);
}

ejecutarDespues(() => {
    console.log("Ejecutado después de 2 segundos");
}, 2000);

// Callback con eventos
function crearBoton(texto, onClick) {
    const boton = document.createElement('button');
    boton.textContent = texto;
    boton.addEventListener('click', onClick);
    return boton;
}

const miBoton = crearBoton("Haz clic", () => {
    alert("¡Botón clickeado!");
});
```

## 🧪 Ejercicios Prácticos

### Ejercicio 1: Calculadora de Operaciones
Crea una calculadora que use funciones para diferentes operaciones matemáticas.

```javascript
// Tu código aquí
const calculadora = {
    sumar: (a, b) => a + b,
    restar: (a, b) => a - b,
    multiplicar: (a, b) => a * b,
    dividir: (a, b) => b !== 0 ? a / b : "Error: División por cero"
};
```

### Ejercicio 2: Generador de Contraseñas con Closures
Implementa un generador de contraseñas que mantenga un historial usando closures.

```javascript
// Tu código aquí
function crearGeneradorContraseñas() {
    let historial = [];
    
    return {
        generar: function(longitud, incluirSimbolos = true) {
            // Implementa la generación
        },
        obtenerHistorial: function() {
            return [...historial];
        }
    };
}
```

### Ejercicio 3: Sistema de Validación
Crea un sistema de validación modular con funciones específicas para cada tipo de validación.

```javascript
// Tu código aquí
const validador = {
    email: (email) => {
        // Validar formato de email
    },
    telefono: (telefono) => {
        // Validar formato de teléfono
    },
    contraseña: (contraseña) => {
        // Validar fortaleza de contraseña
    }
};
```

### Ejercicio 4: Gestor de Eventos
Implementa un sistema de gestión de eventos con suscripción y cancelación.

```javascript
// Tu código aquí
function crearGestorEventos() {
    const eventos = {};
    
    return {
        suscribir: function(evento, callback) {
            // Implementa la suscripción
        },
        emitir: function(evento, datos) {
            // Implementa la emisión
        },
        cancelar: function(evento, callback) {
            // Implementa la cancelación
        }
    };
}
```

### Ejercicio 5: Memoización
Crea un sistema de memoización para optimizar funciones costosas.

```javascript
// Tu código aquí
function memoizar(funcion) {
    const cache = new Map();
    
    return function(...args) {
        // Implementa la memoización
    };
}
```

### Ejercicio 6: Composición de Funciones
Implementa un sistema de composición de funciones para crear pipelines de procesamiento.

```javascript
// Tu código aquí
function componer(...funciones) {
    return function(valor) {
        // Implementa la composición
    };
}
```

### Ejercicio 7: Throttling y Debouncing
Crea funciones de throttling y debouncing para optimizar el rendimiento.

```javascript
// Tu código aquí
function throttling(funcion, limite) {
    // Implementa throttling
}

function debouncing(funcion, delay) {
    // Implementa debouncing
}
```

### Ejercicio 8: Sistema de Middleware
Implementa un sistema de middleware similar a Express.js.

```javascript
// Tu código aquí
function crearAplicacion() {
    const middlewares = [];
    
    return {
        usar: function(middleware) {
            // Añadir middleware
        },
        ejecutar: function(datos) {
            // Ejecutar pipeline de middlewares
        }
    };
}
```

### Ejercicio 9: Factory Pattern
Implementa el patrón Factory para crear diferentes tipos de usuarios.

```javascript
// Tu código aquí
function crearFactoryUsuarios() {
    return {
        crearAdmin: function(datos) {
            // Crear usuario administrador
        },
        crearCliente: function(datos) {
            // Crear usuario cliente
        },
        crearModerador: function(datos) {
            // Crear usuario moderador
        }
    };
}
```

### Ejercicio 10: Sistema de Logging
Crea un sistema de logging con diferentes niveles y formateadores.

```javascript
// Tu código aquí
function crearLogger() {
    const niveles = {
        ERROR: 0,
        WARN: 1,
        INFO: 2,
        DEBUG: 3
    };
    
    return {
        error: function(mensaje) {
            // Log de error
        },
        warn: function(mensaje) {
            // Log de advertencia
        },
        info: function(mensaje) {
            // Log de información
        },
        debug: function(mensaje) {
            // Log de debug
        }
    };
}
```

## 🎯 Proyecto Integrador: Gestor de Tareas Avanzado

### Descripción
Crea un sistema completo de gestión de tareas que implemente todos los conceptos aprendidos en este módulo.

### Requisitos
- Sistema de tareas con prioridades y categorías
- Filtros y búsqueda avanzada
- Persistencia local con localStorage
- Sistema de notificaciones
- Estadísticas y reportes
- Interfaz web responsive

### Funcionalidades a Implementar
1. **Gestión de Tareas**: CRUD completo con closures
2. **Sistema de Filtros**: Funciones de orden superior para filtrado
3. **Persistencia**: Closures para encapsular localStorage
4. **Notificaciones**: Sistema de eventos personalizado
5. **Estadísticas**: Funciones de procesamiento de datos
6. **Validación**: Sistema modular de validación

### Estructura del Proyecto
```
gestor-tareas/
├── index.html
├── styles.css
├── js/
│   ├── app.js
│   ├── taskManager.js
│   ├── storage.js
│   ├── notifications.js
│   └── validators.js
└── README.md
```

## 📚 Recursos Adicionales

### Documentación
- [MDN - Funciones](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Guide/Functions)
- [MDN - Scope](https://developer.mozilla.org/en-US/docs/Glossary/Scope)
- [MDN - Closures](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Closures)

### Libros Recomendados
- "You Don't Know JS: Scope & Closures" por Kyle Simpson
- "Eloquent JavaScript" por Marijn Haverbeke

### Herramientas de Práctica
- [JS Bin](https://jsbin.com/) - Testing de funciones
- [CodePen](https://codepen.io/) - Experimentos con closures

## 🎯 Evaluación del Módulo

### Criterios de Evaluación
- ✅ Comprensión de diferentes tipos de funciones
- ✅ Implementación correcta de closures
- ✅ Uso apropiado de scope y lexical environment
- ✅ Aplicación de conceptos de hoisting
- ✅ Completación del proyecto integrador

### Puntos Clave a Dominar
1. **Funciones**: Declaración, expresión, arrow functions
2. **Scope**: Global, función, bloque, lexical environment
3. **Closures**: Creación y uso efectivo
4. **Hoisting**: Diferencias entre var, let, const y funciones
5. **Parámetros**: Default, rest, destructuring
6. **Callbacks**: Funciones de orden superior

---

**¡Completa todos los ejercicios y el proyecto integrador antes de continuar al siguiente módulo!** 🚀

*El siguiente módulo cubrirá DOM y Eventos, donde aprenderás a interactuar con el navegador web.*

## 🧭 Navegación del Curso

- **⬅️ Anterior**: [Módulo 1: Fundamentos de JavaScript](../junior_1/README.md)
- **➡️ Siguiente**: [Módulo 3: DOM y Eventos](../junior_3/README.md)
- **📚 [Índice Completo](../INDICE_COMPLETO.md)** | **[🧭 Navegación Rápida](../NAVEGACION_RAPIDA.md)**

---
