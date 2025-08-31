# Módulo 6: Testing y Debugging

## Descripción del Módulo
Este módulo cubre las técnicas fundamentales de testing y debugging en JavaScript, incluyendo el framework Jest, testing unitario, debugging avanzado, y herramientas de desarrollo.

## Objetivos de Aprendizaje
- Implementar testing unitario con Jest
- Dominar técnicas de debugging avanzado
- Utilizar herramientas de desarrollo efectivamente
- Crear tests automatizados y mantenibles
- Implementar debugging en producción

## Contenido del Módulo

### 1. Introducción al Testing
**Concepto**: Testing es el proceso de verificar que el código funciona correctamente.

**Tipos de Testing**:
```javascript
// Unit Testing - Prueba funciones individuales
function add(a, b) { return a + b; }
// Test: add(2, 3) should return 5

// Integration Testing - Prueba interacciones entre componentes
// Test: UserService should work with DatabaseService

// E2E Testing - Prueba flujos completos de usuario
// Test: User can register, login, and create a post
```

**Pirámide de Testing**:
```
    /\
   /  \     E2E Tests (pocos, lentos)
  /____\    
 /      \   Integration Tests (algunos, medios)
/________\  Unit Tests (muchos, rápidos)
```

### 2. Framework Jest
**Concepto**: Jest es un framework de testing completo para JavaScript.

**Configuración Básica**:
```javascript
// package.json
{
  "scripts": {
    "test": "jest",
    "test:watch": "jest --watch",
    "test:coverage": "jest --coverage"
  },
  "jest": {
    "testEnvironment": "node",
    "collectCoverageFrom": [
      "src/**/*.js",
      "!src/**/*.test.js"
    ]
  }
}
```

**Estructura de Test Básica**:
```javascript
// math.test.js
describe('Math Operations', () => {
  test('adds two numbers correctly', () => {
    expect(add(2, 3)).toBe(5);
  });

  test('subtracts two numbers correctly', () => {
    expect(subtract(5, 3)).toBe(2);
  });
});
```

### 3. Testing Unitario
**Concepto**: Prueba funciones y métodos individuales de forma aislada.

**Matchers Básicos**:
```javascript
// toBe - Comparación estricta
expect(2 + 2).toBe(4);

// toEqual - Comparación de objetos
expect({ name: 'John' }).toEqual({ name: 'John' });

// toContain - Verificar arrays
expect([1, 2, 3]).toContain(2);

// toMatch - Expresiones regulares
expect('Hello World').toMatch(/World/);

// toBeNull, toBeUndefined
expect(null).toBeNull();
expect(undefined).toBeUndefined();
```

**Testing de Funciones**:
```javascript
// math.js
export function divide(a, b) {
  if (b === 0) {
    throw new Error('Division by zero');
  }
  return a / b;
}

// math.test.js
describe('divide function', () => {
  test('divides two numbers correctly', () => {
    expect(divide(10, 2)).toBe(5);
  });

  test('throws error for division by zero', () => {
    expect(() => divide(10, 0)).toThrow('Division by zero');
  });

  test('handles negative numbers', () => {
    expect(divide(-10, 2)).toBe(-5);
  });
});
```

### 4. Testing de Clases y Métodos
**Concepto**: Prueba métodos de clases y su comportamiento.

**Testing de Clases**:
```javascript
// user.js
export class User {
  constructor(name, email) {
    this.name = name;
    this.email = email;
    this.isActive = true;
  }

  activate() {
    this.isActive = true;
  }

  deactivate() {
    this.isActive = false;
  }

  updateProfile(updates) {
    Object.assign(this, updates);
  }
}

// user.test.js
describe('User class', () => {
  let user;

  beforeEach(() => {
    user = new User('John Doe', 'john@example.com');
  });

  test('creates user with correct properties', () => {
    expect(user.name).toBe('John Doe');
    expect(user.email).toBe('john@example.com');
    expect(user.isActive).toBe(true);
  });

  test('can activate user', () => {
    user.deactivate();
    expect(user.isActive).toBe(false);
    
    user.activate();
    expect(user.isActive).toBe(true);
  });

  test('can update profile', () => {
    user.updateProfile({ name: 'Jane Doe', age: 30 });
    expect(user.name).toBe('Jane Doe');
    expect(user.age).toBe(30);
  });
});
```

### 5. Testing de Promesas y Async
**Concepto**: Prueba código asíncrono y promesas.

**Testing de Promesas**:
```javascript
// userService.js
export class UserService {
  async getUser(id) {
    const response = await fetch(`/api/users/${id}`);
    if (!response.ok) {
      throw new Error('User not found');
    }
    return response.json();
  }

  async createUser(userData) {
    const response = await fetch('/api/users', {
      method: 'POST',
      headers: { 'Content-Type': 'application/json' },
      body: JSON.stringify(userData)
    });
    return response.json();
  }
}

// userService.test.js
describe('UserService', () => {
  let userService;

  beforeEach(() => {
    userService = new UserService();
  });

  test('fetches user successfully', async () => {
    const mockUser = { id: 1, name: 'John Doe' };
    global.fetch = jest.fn().mockResolvedValue({
      ok: true,
      json: async () => mockUser
    });

    const result = await userService.getUser(1);
    expect(result).toEqual(mockUser);
  });

  test('throws error for non-existent user', async () => {
    global.fetch = jest.fn().mockResolvedValue({
      ok: false
    });

    await expect(userService.getUser(999)).rejects.toThrow('User not found');
  });
});
```

### 6. Mocking y Stubbing
**Concepto**: Simular dependencias externas para testing aislado.

**Mocking de Funciones**:
```javascript
// calculator.js
export function calculate(operation, a, b) {
  return operation(a, b);
}

// calculator.test.js
describe('Calculator with mocked operations', () => {
  test('uses mocked operation function', () => {
    const mockOperation = jest.fn().mockReturnValue(42);
    
    const result = calculate(mockOperation, 10, 5);
    
    expect(result).toBe(42);
    expect(mockOperation).toHaveBeenCalledWith(10, 5);
    expect(mockOperation).toHaveBeenCalledTimes(1);
  });
});
```

**Mocking de Módulos**:
```javascript
// userRepository.js
export class UserRepository {
  async findById(id) {
    // Database call
    return { id, name: 'John Doe' };
  }
}

// userService.js
import { UserRepository } from './userRepository.js';

export class UserService {
  constructor(userRepo = new UserRepository()) {
    this.userRepo = userRepo;
  }

  async getUser(id) {
    return this.userRepo.findById(id);
  }
}

// userService.test.js
jest.mock('./userRepository.js');

describe('UserService with mocked repository', () => {
  test('gets user from repository', async () => {
    const mockUser = { id: 1, name: 'John Doe' };
    const mockRepository = {
      findById: jest.fn().mockResolvedValue(mockUser)
    };

    const userService = new UserService(mockRepository);
    const result = await userService.getUser(1);

    expect(result).toEqual(mockUser);
    expect(mockRepository.findById).toHaveBeenCalledWith(1);
  });
});
```

### 7. Testing de APIs y Servicios
**Concepto**: Prueba servicios que interactúan con APIs externas.

**Testing con MSW (Mock Service Worker)**:
```javascript
// handlers.js
import { rest } from 'msw';

export const handlers = [
  rest.get('/api/users/:id', (req, res, ctx) => {
    const { id } = req.params;
    return res(
      ctx.json({
        id: parseInt(id),
        name: 'John Doe',
        email: 'john@example.com'
      })
    );
  }),

  rest.post('/api/users', (req, res, ctx) => {
    const userData = req.body;
    return res(
      ctx.json({
        id: 1,
        ...userData,
        createdAt: new Date().toISOString()
      })
    );
  })
];

// setupTests.js
import { setupServer } from 'msw/node';
import { handlers } from './handlers';

export const server = setupServer(...handlers);

beforeAll(() => server.listen());
afterEach(() => server.resetHandlers());
afterAll(() => server.close());
```

### 8. Debugging Avanzado
**Concepto**: Técnicas para identificar y resolver problemas en el código.

**Console Methods**:
```javascript
// console.log básico
console.log('User data:', user);

// console.table para objetos y arrays
console.table([
  { name: 'John', age: 30, city: 'NYC' },
  { name: 'Jane', age: 25, city: 'LA' }
]);

// console.group para agrupar logs
console.group('User Authentication');
console.log('Checking credentials...');
console.log('Validating token...');
console.groupEnd();

// console.time para medir performance
console.time('API Call');
await fetch('/api/users');
console.timeEnd('API Call');
```

**Debugger Statement**:
```javascript
function processUser(user) {
  debugger; // Pausa la ejecución aquí
  const processed = {
    ...user,
    fullName: `${user.firstName} ${user.lastName}`,
    isAdult: user.age >= 18
  };
  return processed;
}
```

### 9. Herramientas de Desarrollo
**Concepto**: Utilizar herramientas del navegador y Node.js para debugging.

**Chrome DevTools**:
```javascript
// Sources Panel
// - Breakpoints
// - Call Stack
// - Scope Variables
// - Watch Expressions

// Console Panel
// - $0-$4 para elementos seleccionados
// - $_ para último resultado
// - $i para instalar npm packages

// Network Panel
// - XHR requests
// - Response data
// - Timing information
```

**Node.js Debugging**:
```javascript
// package.json
{
  "scripts": {
    "debug": "node --inspect-brk src/index.js"
  }
}

// Código con debugging
const debug = require('debug')('app:user-service');

class UserService {
  async getUser(id) {
    debug('Fetching user with ID:', id);
    try {
      const user = await this.repository.findById(id);
      debug('User found:', user);
      return user;
    } catch (error) {
      debug('Error fetching user:', error);
      throw error;
    }
  }
}
```

### 10. Testing de Performance
**Concepto**: Medir y optimizar el rendimiento del código.

**Performance Testing**:
```javascript
// performance.test.js
describe('Performance Tests', () => {
  test('array operations performance', () => {
    const largeArray = Array.from({ length: 100000 }, (_, i) => i);
    
    const startTime = performance.now();
    const result = largeArray
      .filter(x => x % 2 === 0)
      .map(x => x * 2)
      .reduce((sum, x) => sum + x, 0);
    const endTime = performance.now();
    
    const executionTime = endTime - startTime;
    expect(executionTime).toBeLessThan(100); // Menos de 100ms
    expect(result).toBeGreaterThan(0);
  });

  test('memory usage', () => {
    const initialMemory = process.memoryUsage().heapUsed;
    
    // Operación que consume memoria
    const largeObject = {};
    for (let i = 0; i < 10000; i++) {
      largeObject[i] = `value-${i}`;
    }
    
    const finalMemory = process.memoryUsage().heapUsed;
    const memoryIncrease = finalMemory - initialMemory;
    
    expect(memoryIncrease).toBeLessThan(50 * 1024 * 1024); // Menos de 50MB
  });
});
```

## Ejercicios Prácticos

### Ejercicio 1: Testing de Funciones Matemáticas
Crea tests para funciones matemáticas básicas (suma, resta, multiplicación, división) incluyendo casos edge.

### Ejercicio 2: Testing de Clase de Usuario
Implementa tests completos para una clase User con métodos de activación, desactivación y actualización de perfil.

### Ejercicio 3: Testing de Servicios con Promesas
Crea tests para un servicio que maneje operaciones CRUD con promesas y manejo de errores.

### Ejercicio 4: Mocking de Dependencias
Implementa tests usando mocks para un servicio que dependa de una base de datos externa.

### Ejercicio 5: Testing de APIs con MSW
Crea tests para un cliente de API usando Mock Service Worker para simular respuestas del servidor.

### Ejercicio 6: Debugging de Código Asíncrono
Identifica y resuelve problemas en código asíncrono usando técnicas de debugging avanzado.

### Ejercicio 7: Testing de Performance
Implementa tests de performance para operaciones de arrays y objetos de gran tamaño.

### Ejercicio 8: Testing de Validación
Crea tests para funciones de validación de formularios con diferentes tipos de entrada.

### Ejercicio 9: Testing de Manejo de Errores
Implementa tests para verificar el correcto manejo de errores en diferentes escenarios.

### Ejercicio 10: Testing de Integración
Crea tests de integración para un flujo completo de autenticación de usuario.

## Proyecto Integrador: Biblioteca de Utilidades con Testing Completo

### Descripción
Desarrolla una biblioteca de utilidades JavaScript con cobertura completa de testing, incluyendo tests unitarios, de integración y de performance.

### Características Requeridas
- **Testing Unitario**: Cobertura del 90%+ con Jest
- **Testing de Integración**: Pruebas de interacción entre módulos
- **Mocking**: Simulación de dependencias externas
- **Performance Testing**: Medición de rendimiento
- **Debugging**: Herramientas de debugging integradas
- **Documentación**: Tests como documentación viva

### Estructura del Proyecto
```
biblioteca-utilidades/
├── src/
│   ├── math/
│   │   ├── calculator.js
│   │   ├── statistics.js
│   │   └── geometry.js
│   ├── string/
│   │   ├── formatter.js
│   │   ├── validator.js
│   │   └── parser.js
│   ├── array/
│   │   ├── sorter.js
│   │   ├── filter.js
│   │   └── transformer.js
│   └── index.js
├── tests/
│   ├── unit/
│   ├── integration/
│   └── performance/
├── package.json
└── README.md
```

### Funcionalidades
1. **Operaciones Matemáticas**: Calculadora, estadísticas, geometría
2. **Manipulación de Strings**: Formateo, validación, parsing
3. **Operaciones de Arrays**: Ordenamiento, filtrado, transformación
4. **Testing Completo**: Unit, integration, performance
5. **Debugging**: Herramientas integradas
6. **Documentación**: Tests como ejemplos de uso

## Evaluación del Módulo

### Criterios de Evaluación
- **Testing**: Implementación correcta de tests unitarios
- **Mocking**: Uso apropiado de mocks y stubs
- **Debugging**: Dominio de técnicas de debugging
- **Cobertura**: Cobertura de tests adecuada
- **Herramientas**: Uso efectivo de herramientas de desarrollo

### Puntos Clave
- Framework Jest para testing
- Testing de código asíncrono
- Mocking de dependencias
- Técnicas de debugging avanzado
- Testing de performance

## Recursos Adicionales

### Documentación
- [Jest Documentation](https://jestjs.io/docs/getting-started)
- [MSW Documentation](https://mswjs.io/docs/)
- [Chrome DevTools](https://developers.google.com/web/tools/chrome-devtools)

### Herramientas
- **Jest**: Framework de testing
- **MSW**: Mock Service Worker
- **Chrome DevTools**: Debugging del navegador
- **Node.js Inspector**: Debugging de Node.js

### Próximos Pasos
- **Módulo 7**: Arquitectura y Patrones Avanzados
- **Módulo 8**: Testing Avanzado y E2E
- **Módulo 9**: Performance y Optimización
- **Módulo 10**: DevOps y Deployment

---

**Nota**: Este módulo establece las bases para testing profesional en JavaScript, preparando al estudiante para desarrollo de calidad empresarial.
