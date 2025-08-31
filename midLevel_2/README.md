# Módulo 5: ES6+ y Características Modernas

## Descripción del Módulo
Este módulo cubre las características modernas de JavaScript introducidas en ES6+ (ES2015 y posteriores), incluyendo destructuring, template literals, módulos ES6, y patrones modernos de JavaScript.

## Objetivos de Aprendizaje
- Dominar destructuring de arrays y objetos
- Utilizar template literals y string interpolation
- Implementar módulos ES6 (import/export)
- Aplicar características modernas de JavaScript
- Desarrollar patrones funcionales modernos

## Contenido del Módulo

### 1. Destructuring
**Concepto**: Extraer valores de arrays y objetos de forma concisa.

**Destructuring de Arrays**:
```javascript
// Básico
const [first, second, third] = [1, 2, 3];

// Con valores por defecto
const [a, b, c = 3] = [1, 2];

// Saltar elementos
const [x, , z] = [1, 2, 3];

// Rest operator
const [head, ...tail] = [1, 2, 3, 4, 5];
```

**Destructuring de Objetos**:
```javascript
// Básico
const { name, age } = { name: 'John', age: 30 };

// Renombrar propiedades
const { name: userName, age: userAge } = { name: 'John', age: 30 };

// Valores por defecto
const { title = 'Default', author = 'Unknown' } = { title: 'My Book' };

// Nested destructuring
const { user: { name, profile: { email } } } = {
  user: { name: 'John', profile: { email: 'john@example.com' } }
};
```

### 2. Template Literals
**Concepto**: Strings multilínea con interpolación de variables.

**Sintaxis Básica**:
```javascript
const name = 'World';
const greeting = `Hello, ${name}!`;

// Multilínea
const multiline = `
  This is a
  multiline string
  with ${name}
`;

// Expresiones
const result = `2 + 2 = ${2 + 2}`;
```

**Tagged Templates**:
```javascript
function highlight(strings, ...values) {
  return strings.reduce((result, str, i) => {
    return result + str + (values[i] ? `<mark>${values[i]}</mark>` : '');
  }, '');
}

const highlighted = highlight`Hello ${'World'}!`;
// Result: "Hello <mark>World</mark>!"
```

### 3. Módulos ES6
**Concepto**: Sistema de módulos nativo para organizar código.

**Export**:
```javascript
// Named exports
export const PI = 3.14159;
export function multiply(a, b) { return a * b; }
export class Calculator { /* ... */ }

// Default export
export default class MathUtils { /* ... */ }

// Re-export
export { default as MathUtils } from './math-utils.js';
```

**Import**:
```javascript
// Named imports
import { PI, multiply, Calculator } from './math.js';

// Default import
import MathUtils from './math-utils.js';

// Mixed imports
import MathUtils, { PI, multiply } from './math.js';

// Namespace import
import * as Math from './math.js';
```

### 4. Características Modernas de Arrays
**Concepto**: Métodos modernos para manipulación de arrays.

**Métodos de Búsqueda**:
```javascript
const numbers = [1, 2, 3, 4, 5];

// find - primer elemento que cumple condición
const firstEven = numbers.find(num => num % 2 === 0);

// findIndex - índice del primer elemento
const evenIndex = numbers.findIndex(num => num % 2 === 0);

// includes - verificar si existe elemento
const hasThree = numbers.includes(3);
```

**Métodos de Transformación**:
```javascript
// flat - aplanar arrays anidados
const nested = [1, [2, 3], [4, [5, 6]]];
const flattened = nested.flat(2); // [1, 2, 3, 4, 5, 6]

// flatMap - mapear y aplanar
const sentences = ['Hello world', 'Good morning'];
const words = sentences.flatMap(sentence => sentence.split(' '));

// from - crear array desde iterable
const arrayFromString = Array.from('hello'); // ['h', 'e', 'l', 'l', 'o']
```

### 5. Características Modernas de Objetos
**Concepto**: Sintaxis moderna para crear y manipular objetos.

**Object Shorthand**:
```javascript
const name = 'John';
const age = 30;

// Antes
const person = { name: name, age: age };

// Ahora
const person = { name, age };
```

**Computed Properties**:
```javascript
const propName = 'age';
const person = {
  name: 'John',
  [propName]: 30,
  [`get${propName}`]() { return this[propName]; }
};
```

**Object Methods**:
```javascript
const calculator = {
  add(a, b) { return a + b; },
  subtract(a, b) { return a - b; }
};
```

### 6. Spread y Rest Operators
**Concepto**: Operadores para expandir y recoger elementos.

**Spread Operator**:
```javascript
// Arrays
const arr1 = [1, 2, 3];
const arr2 = [...arr1, 4, 5]; // [1, 2, 3, 4, 5]

// Objetos
const obj1 = { name: 'John' };
const obj2 = { ...obj1, age: 30 }; // { name: 'John', age: 30 }

// Strings
const chars = [...'hello']; // ['h', 'e', 'l', 'l', 'o']
```

**Rest Operator**:
```javascript
// En funciones
function sum(...numbers) {
  return numbers.reduce((total, num) => total + num, 0);
}

// En destructuring
const [first, ...rest] = [1, 2, 3, 4, 5];
const { name, ...otherProps } = { name: 'John', age: 30, city: 'NYC' };
```

### 7. Arrow Functions
**Concepto**: Sintaxis concisa para funciones.

**Sintaxis Básica**:
```javascript
// Función tradicional
function add(a, b) { return a + b; }

// Arrow function
const add = (a, b) => a + b;

// Con cuerpo
const multiply = (a, b) => {
  const result = a * b;
  return result;
};
```

**Características**:
```javascript
// this léxico
const obj = {
  name: 'John',
  traditional: function() {
    setTimeout(function() {
      console.log(this.name); // undefined
    }, 100);
  },
  arrow: function() {
    setTimeout(() => {
      console.log(this.name); // 'John'
    }, 100);
  }
};
```

### 8. Parámetros por Defecto y Rest
**Concepto**: Valores por defecto y parámetros variables.

**Parámetros por Defecto**:
```javascript
function greet(name = 'Guest', greeting = 'Hello') {
  return `${greeting}, ${name}!`;
}

// Con destructuring
function createUser({ name = 'Anonymous', age = 18, email = '' } = {}) {
  return { name, age, email };
}
```

**Parámetros Rest**:
```javascript
function log(...args) {
  console.log(args.join(' '));
}

function partial(fn, ...presetArgs) {
  return function(...laterArgs) {
    return fn(...presetArgs, ...laterArgs);
  };
}
```

### 9. Iteradores y Generadores
**Concepto**: Control avanzado sobre iteraciones.

**Iteradores Personalizados**:
```javascript
const range = {
  from: 1,
  to: 5,
  [Symbol.iterator]() {
    let current = this.from;
    let last = this.to;
    
    return {
      next() {
        if (current <= last) {
          return { done: false, value: current++ };
        } else {
          return { done: true };
        }
      }
    };
  }
};

for (let num of range) {
  console.log(num); // 1, 2, 3, 4, 5
}
```

**Generadores**:
```javascript
function* numberGenerator() {
  yield 1;
  yield 2;
  yield 3;
}

const gen = numberGenerator();
console.log(gen.next().value); // 1
console.log(gen.next().value); // 2
console.log(gen.next().value); // 3
```

### 10. Patrones Funcionales Modernos
**Concepto**: Patrones de programación funcional en JavaScript.

**Composición de Funciones**:
```javascript
const compose = (...fns) => x => fns.reduceRight((y, f) => f(y), x);
const pipe = (...fns) => x => fns.reduce((y, f) => f(y), x);

const addOne = x => x + 1;
const double = x => x * 2;
const square = x => x ** 2;

const composed = compose(square, double, addOne);
const piped = pipe(addOne, double, square);

console.log(composed(3)); // 64
console.log(piped(3)); // 64
```

**Currying**:
```javascript
const curry = (fn) => {
  const arity = fn.length;
  
  return function curried(...args) {
    if (args.length >= arity) {
      return fn.apply(this, args);
    }
    
    return function(...moreArgs) {
      return curried.apply(this, args.concat(moreArgs));
    };
  };
};

const add = curry((a, b, c) => a + b + c);
const addFive = add(5);
const addFiveAndThree = addFive(3);

console.log(addFiveAndThree(2)); // 10
```

## Ejercicios Prácticos

### Ejercicio 1: Destructuring Avanzado
Crea una función que use destructuring para extraer información de un objeto de usuario anidado y retorne un objeto simplificado.

### Ejercicio 2: Template Literals con Tags
Implementa un sistema de templates que permita formatear texto con diferentes estilos usando tagged templates.

### Ejercicio 3: Sistema de Módulos
Crea un sistema de módulos para una aplicación de gestión de biblioteca con separación de responsabilidades.

### Ejercicio 4: Manipulación de Arrays Moderna
Implementa funciones para filtrar, mapear y reducir arrays usando métodos modernos de ES6+.

### Ejercicio 5: Clonación de Objetos
Crea funciones para clonar objetos usando spread operator y Object.assign, comparando rendimiento.

### Ejercicio 6: Función de Composición
Implementa un sistema de composición de funciones que permita encadenar operaciones matemáticas.

### Ejercicio 7: Generador de Secuencias
Crea generadores para diferentes tipos de secuencias (Fibonacci, números primos, etc.).

### Ejercicio 8: Sistema de Validación
Implementa un sistema de validación usando destructuring y parámetros por defecto.

### Ejercicio 9: Currying y Aplicación Parcial
Crea funciones curried para operaciones matemáticas y de manipulación de strings.

### Ejercicio 10: Iteradores Personalizados
Implementa iteradores para estructuras de datos personalizadas como árboles y grafos.

## Proyecto Integrador: Sistema de Gestión de Datos Moderno

### Descripción
Desarrolla un sistema completo de gestión de datos que implemente todas las características modernas de ES6+.

### Características Requeridas
- **Módulos ES6**: Separación clara de responsabilidades
- **Destructuring**: Extracción eficiente de datos
- **Template Literals**: Generación de reportes y logs
- **Métodos Modernos**: Manipulación de arrays y objetos
- **Arrow Functions**: Funciones concisas y legibles
- **Spread/Rest**: Operaciones de datos flexibles
- **Generadores**: Procesamiento de datos en lotes
- **Patrones Funcionales**: Composición y currying

### Estructura del Proyecto
```
sistema-gestion-datos/
├── src/
│   ├── models/
│   │   ├── user.js
│   │   ├── product.js
│   │   └── order.js
│   ├── services/
│   │   ├── dataService.js
│   │   ├── validationService.js
│   │   └── reportService.js
│   ├── utils/
│   │   ├── formatters.js
│   │   ├── validators.js
│   │   └── transformers.js
│   └── index.js
├── tests/
├── package.json
└── README.md
```

### Funcionalidades
1. **CRUD Operations**: Crear, leer, actualizar y eliminar entidades
2. **Validación de Datos**: Sistema robusto de validación
3. **Generación de Reportes**: Reportes en diferentes formatos
4. **Procesamiento en Lotes**: Manejo eficiente de grandes volúmenes
5. **API Funcional**: Interfaz funcional para operaciones
6. **Testing**: Tests unitarios y de integración

## Evaluación del Módulo

### Criterios de Evaluación
- **Comprensión**: Dominio de características ES6+
- **Implementación**: Uso correcto de sintaxis moderna
- **Organización**: Estructura modular y limpia
- **Funcionalidad**: Sistema completo y funcional
- **Testing**: Cobertura de tests adecuada

### Puntos Clave
- Destructuring eficiente y legible
- Uso apropiado de template literals
- Arquitectura modular con ES6 modules
- Implementación de patrones funcionales
- Manejo de iteradores y generadores

## Recursos Adicionales

### Documentación
- [MDN Web Docs - ES6](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference)
- [ECMAScript Specification](https://tc39.es/ecma262/)
- [JavaScript.info - Modern JavaScript](https://javascript.info/)

### Herramientas
- **Babel**: Transpilador para compatibilidad
- **ESLint**: Linting de código moderno
- **Prettier**: Formateo de código
- **Jest**: Testing framework

### Próximos Pasos
- **Módulo 6**: Testing y Debugging
- **Módulo 7**: Arquitectura y Patrones Avanzados
- **Módulo 8**: Testing Avanzado y E2E
- **Módulo 9**: Performance y Optimización
- **Módulo 10**: DevOps y Deployment

---

**Nota**: Este módulo establece las bases para el desarrollo moderno de JavaScript, preparando al estudiante para trabajar con frameworks y librerías contemporáneas.
