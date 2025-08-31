# 🚀 Módulo 1: Fundamentos de JavaScript

## 🧭 Navegación del Curso

- **⬅️ Anterior**: [🏠 Página Principal](../README.md)
- **➡️ Siguiente**: [Módulo 2: Funciones y Scope](../junior_2/README.md)
- **📚 [Índice Completo](../INDICE_COMPLETO.md)** | **[🧭 Navegación Rápida](../NAVEGACION_RAPIDA.md)**

---

## 📚 Descripción del Módulo

Este módulo te introduce a los conceptos fundamentales de JavaScript. Aprenderás sobre variables, tipos de datos, operadores, estructuras de control, arrays y objetos básicos. Estos son los bloques de construcción esenciales para cualquier aplicación JavaScript.

## 🎯 Objetivos de Aprendizaje

- Comprender la sintaxis básica de JavaScript
- Dominar el uso de variables y tipos de datos
- Aplicar operadores y estructuras de control
- Trabajar con arrays y objetos básicos
- Implementar lógica de programación fundamental

## 📖 Contenido Teórico

### 1. Introducción a JavaScript

JavaScript es un lenguaje de programación interpretado, orientado a objetos, que se ejecuta principalmente en navegadores web. Es fundamental para crear aplicaciones web interactivas y dinámicas.

**Características principales:**
- **Interpretado**: No requiere compilación previa
- **Dinámico**: Los tipos se determinan en tiempo de ejecución
- **Orientado a objetos**: Basado en prototipos
- **Multiplataforma**: Funciona en navegadores y Node.js

### 2. Variables y Declaración

Las variables son contenedores para almacenar datos. En JavaScript moderno, usamos `let`, `const` y `var`.

```javascript
// Declaración con let (re-asignable)
let nombre = "Juan";
let edad = 25;

// Declaración con const (no re-asignable)
const PI = 3.14159;
const API_URL = "https://api.ejemplo.com";

// Declaración con var (hoisting, scope de función)
var apellido = "Pérez";
```

**Diferencias importantes:**
- `let`: Scope de bloque, no hoisting
- `const`: No re-asignable, scope de bloque
- `var`: Hoisting, scope de función (evitar en código moderno)

### 3. Tipos de Datos Primitivos

JavaScript tiene 7 tipos de datos primitivos:

```javascript
// String - Texto
let texto = "Hola Mundo";
let texto2 = 'JavaScript';
let template = `Hola ${nombre}`;

// Number - Números (enteros y decimales)
let entero = 42;
let decimal = 3.14;
let negativo = -17;
let infinito = Infinity;
let noNumero = NaN;

// Boolean - Valores lógicos
let verdadero = true;
let falso = false;

// Undefined - Variable declarada sin valor
let sinDefinir;

// Null - Valor nulo intencional
let nulo = null;

// Symbol - Identificador único
let simbolo = Symbol('descripcion');

// BigInt - Números enteros grandes
let numeroGrande = 9007199254740991n;
```

### 4. Operadores

#### Operadores Aritméticos
```javascript
let a = 10;
let b = 3;

let suma = a + b;        // 13
let resta = a - b;       // 7
let multiplicacion = a * b; // 30
let division = a / b;    // 3.333...
let modulo = a % b;      // 1 (resto de la división)
let potencia = a ** b;   // 1000 (10^3)
let incremento = ++a;    // 11 (incrementa y retorna)
let decremento = --b;    // 2 (decrementa y retorna)
```

#### Operadores de Comparación
```javascript
let x = 5;
let y = "5";

x == y;   // true (comparación de valor)
x === y;  // false (comparación de valor y tipo)
x != y;   // false
x !== y;  // true
x > 3;    // true
x <= 5;   // true
```

#### Operadores Lógicos
```javascript
let esVerdadero = true;
let esFalso = false;

esVerdadero && esFalso;  // false (AND)
esVerdadero || esFalso;  // true (OR)
!esVerdadero;            // false (NOT)
```

### 5. Estructuras de Control

#### Condicionales
```javascript
// if...else
let edad = 18;

if (edad >= 18) {
    console.log("Eres mayor de edad");
} else if (edad >= 13) {
    console.log("Eres adolescente");
} else {
    console.log("Eres menor de edad");
}

// Operador ternario
let mensaje = edad >= 18 ? "Mayor de edad" : "Menor de edad";

// switch
let dia = "lunes";
switch (dia) {
    case "lunes":
        console.log("Inicio de semana");
        break;
    case "viernes":
        console.log("¡Fin de semana!");
        break;
    default:
        console.log("Día normal");
}
```

#### Bucles
```javascript
// for tradicional
for (let i = 0; i < 5; i++) {
    console.log(`Iteración ${i}`);
}

// for...of (para arrays)
let frutas = ["manzana", "banana", "naranja"];
for (let fruta of frutas) {
    console.log(fruta);
}

// for...in (para propiedades de objetos)
let persona = { nombre: "Juan", edad: 25 };
for (let propiedad in persona) {
    console.log(`${propiedad}: ${persona[propiedad]}`);
}

// while
let contador = 0;
while (contador < 3) {
    console.log(contador);
    contador++;
}

// do...while
let numero = 0;
do {
    console.log(numero);
    numero++;
} while (numero < 3);
```

### 6. Arrays

Los arrays son estructuras de datos ordenadas que pueden contener múltiples valores.

```javascript
// Creación de arrays
let colores = ["rojo", "verde", "azul"];
let numeros = [1, 2, 3, 4, 5];
let mixto = ["texto", 42, true, null];

// Acceso a elementos
let primerColor = colores[0];  // "rojo"
let ultimoColor = colores[colores.length - 1]; // "azul"

// Métodos básicos
colores.push("amarillo");      // Añade al final
colores.pop();                 // Elimina el último
colores.unshift("negro");      // Añade al inicio
colores.shift();               // Elimina el primero

// Iteración
colores.forEach((color, index) => {
    console.log(`${index}: ${color}`);
});

// Métodos de transformación
let numerosDobles = numeros.map(n => n * 2);
let numerosPares = numeros.filter(n => n % 2 === 0);
let sumaTotal = numeros.reduce((acc, n) => acc + n, 0);
```

### 7. Objetos Básicos

Los objetos son colecciones de pares clave-valor.

```javascript
// Creación de objetos
let persona = {
    nombre: "María",
    edad: 30,
    ciudad: "Madrid",
    activo: true
};

// Acceso a propiedades
let nombrePersona = persona.nombre;        // Notación de punto
let edadPersona = persona["edad"];         // Notación de corchetes

// Modificación de propiedades
persona.edad = 31;
persona["ciudad"] = "Barcelona";

// Añadir nuevas propiedades
persona.profesion = "Desarrolladora";
persona["telefono"] = "123-456-789";

// Eliminar propiedades
delete persona.activo;

// Verificación de propiedades
if ("nombre" in persona) {
    console.log("La persona tiene nombre");
}

// Iteración sobre propiedades
Object.keys(persona).forEach(key => {
    console.log(`${key}: ${persona[key]}`);
});
```

## 🧪 Ejercicios Prácticos

### Ejercicio 1: Calculadora Básica
Crea una calculadora que pueda realizar operaciones básicas (suma, resta, multiplicación, división).

```javascript
// Tu código aquí
function calculadora(operacion, a, b) {
    // Implementa la lógica
}
```

### Ejercicio 2: Validación de Formulario
Crea una función que valide un formulario de registro con nombre, email y edad.

```javascript
// Tu código aquí
function validarFormulario(nombre, email, edad) {
    // Implementa las validaciones
}
```

### Ejercicio 3: Gestor de Lista
Crea un sistema para gestionar una lista de tareas con funciones de añadir, eliminar y marcar como completada.

```javascript
// Tu código aquí
class GestorTareas {
    // Implementa la clase
}
```

### Ejercicio 4: Conversor de Temperatura
Implementa un conversor entre Celsius, Fahrenheit y Kelvin.

```javascript
// Tu código aquí
function convertirTemperatura(valor, desde, hacia) {
    // Implementa las conversiones
}
```

### Ejercicio 5: Generador de Contraseñas
Crea una función que genere contraseñas seguras con diferentes criterios.

```javascript
// Tu código aquí
function generarContraseña(longitud, incluirMayusculas, incluirNumeros, incluirSimbolos) {
    // Implementa la generación
}
```

### Ejercicio 6: Calculadora de Estadísticas
Implementa funciones para calcular media, mediana, moda y rango de un array de números.

```javascript
// Tu código aquí
class CalculadoraEstadisticas {
    // Implementa los métodos estadísticos
}
```

### Ejercicio 7: Validador de Tarjeta de Crédito
Crea una función que valide números de tarjeta de crédito usando el algoritmo de Luhn.

```javascript
// Tu código aquí
function validarTarjetaCredito(numero) {
    // Implementa el algoritmo de Luhn
}
```

### Ejercicio 8: Gestor de Inventario
Implementa un sistema de inventario con productos, cantidades y precios.

```javascript
// Tu código aquí
class GestorInventario {
    // Implementa la gestión de inventario
}
```

### Ejercicio 9: Encriptador de Texto
Crea un sistema simple de encriptación y desencriptación de texto.

```javascript
// Tu código aquí
function encriptar(texto, clave) {
    // Implementa la encriptación
}

function desencriptar(textoEncriptado, clave) {
    // Implementa la desencriptación
}
```

### Ejercicio 10: Simulador de Banco
Implementa un sistema bancario básico con cuentas, depósitos, retiros y transferencias.

```javascript
// Tu código aquí
class CuentaBancaria {
    // Implementa la funcionalidad bancaria
}
```

## 🎯 Proyecto Integrador: Calculadora Avanzada

### Descripción
Crea una calculadora web completa que incluya operaciones básicas, científicas y un historial de operaciones.

### Requisitos
- Interfaz web responsive
- Operaciones matemáticas básicas
- Funciones científicas (seno, coseno, logaritmos)
- Historial de operaciones
- Validación de entrada
- Manejo de errores

### Estructura del Proyecto
```
calculadora/
├── index.html
├── styles.css
├── script.js
└── README.md
```

### Funcionalidades a Implementar
1. **Operaciones Básicas**: Suma, resta, multiplicación, división
2. **Operaciones Avanzadas**: Potencias, raíces, porcentajes
3. **Funciones Científicas**: Trigonometría, logaritmos, exponenciales
4. **Historial**: Guardar y mostrar operaciones previas
5. **Validación**: Manejo de errores y entrada inválida
6. **Interfaz**: Diseño responsive y accesible

## 📚 Recursos Adicionales

### Documentación
- [MDN - Variables](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Guide/Grammar_and_types#declarations)
- [MDN - Tipos de Datos](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Data_structures)
- [MDN - Operadores](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Guide/Expressions_and_Operators)

### Herramientas de Práctica
- [CodePen](https://codepen.io/) - Para experimentar con código
- [JSFiddle](https://jsfiddle.net/) - Testing rápido de JavaScript
- [Replit](https://replit.com/) - Entorno de desarrollo online

### Ejercicios Adicionales
- [JavaScript30](https://javascript30.com/) - 30 proyectos en 30 días
- [Codewars](https://www.codewars.com/) - Desafíos de programación
- [HackerRank](https://www.hackerrank.com/) - Problemas de algoritmos

## 🎯 Evaluación del Módulo

### Criterios de Evaluación
- ✅ Comprensión de conceptos básicos
- ✅ Implementación correcta de ejercicios
- ✅ Uso apropiado de sintaxis JavaScript
- ✅ Resolución de problemas de programación
- ✅ Completación del proyecto integrador

### Puntos Clave a Dominar
1. **Variables y tipos de datos**: Comprensión completa de `let`, `const`, tipos primitivos
2. **Operadores**: Uso correcto de operadores aritméticos, de comparación y lógicos
3. **Estructuras de control**: Implementación de condicionales y bucles
4. **Arrays y objetos**: Manipulación básica de estructuras de datos
5. **Lógica de programación**: Resolución de problemas usando JavaScript

---

**¡Completa todos los ejercicios y el proyecto integrador antes de continuar al siguiente módulo!** 🚀

*El siguiente módulo cubrirá Funciones y Scope, donde aprenderás a crear código reutilizable y modular.*

## 🧭 Navegación del Curso

- **⬅️ Anterior**: [🏠 Página Principal](../README.md)
- **➡️ Siguiente**: [Módulo 2: Funciones y Scope](../junior_2/README.md)
- **📚 [Índice Completo](../INDICE_COMPLETO.md)** | **[🧭 Navegación Rápida](../NAVEGACION_RAPIDA.md)**

---
