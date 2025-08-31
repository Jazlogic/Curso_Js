# Módulo 7: Arquitectura y Patrones Avanzados

## Descripción del Módulo
Este módulo cubre patrones de diseño avanzados, arquitectura limpia, principios SOLID, inyección de dependencias, y arquitectura orientada a eventos para aplicaciones JavaScript escalables.

## Objetivos de Aprendizaje
- Implementar patrones de diseño avanzados
- Aplicar principios SOLID en JavaScript
- Diseñar arquitectura limpia y mantenible
- Implementar inyección de dependencias
- Crear sistemas orientados a eventos

## Contenido del Módulo

### 1. Principios SOLID
**Concepto**: Principios fundamentales para diseño de software mantenible.

**Single Responsibility Principle (SRP)**:
```javascript
// ❌ Violación: Clase con múltiples responsabilidades
class UserManager {
  createUser(userData) { /* ... */ }
  sendEmail(user) { /* ... */ }
  validateUser(user) { /* ... */ }
  saveToDatabase(user) { /* ... */ }
}

// ✅ Correcto: Separación de responsabilidades
class UserService {
  constructor(userRepo, emailService, validator) {
    this.userRepo = userRepo;
    this.emailService = emailService;
    this.validator = validator;
  }

  async createUser(userData) {
    const user = this.validator.validate(userData);
    const savedUser = await this.userRepo.save(user);
    await this.emailService.sendWelcomeEmail(savedUser);
    return savedUser;
  }
}
```

**Open/Closed Principle (OCP)**:
```javascript
// ✅ Extensible sin modificar código existente
class PaymentProcessor {
  processPayment(paymentMethod, amount) {
    return paymentMethod.process(amount);
  }
}

class CreditCardPayment {
  process(amount) {
    return `Processing ${amount} via credit card`;
  }
}

class PayPalPayment {
  process(amount) {
    return `Processing ${amount} via PayPal`;
  }
}

// Fácil agregar nuevos métodos de pago
class CryptoPayment {
  process(amount) {
    return `Processing ${amount} via cryptocurrency`;
  }
}
```

### 2. Patrones de Diseño
**Concepto**: Soluciones reutilizables para problemas comunes de diseño.

**Factory Pattern**:
```javascript
class UserFactory {
  static createUser(type, userData) {
    switch (type) {
      case 'admin':
        return new AdminUser(userData);
      case 'regular':
        return new RegularUser(userData);
      case 'premium':
        return new PremiumUser(userData);
      default:
        throw new Error(`Unknown user type: ${type}`);
    }
  }
}

class AdminUser {
  constructor(data) {
    this.name = data.name;
    this.role = 'admin';
    this.permissions = ['read', 'write', 'delete', 'admin'];
  }
}

class RegularUser {
  constructor(data) {
    this.name = data.name;
    this.role = 'user';
    this.permissions = ['read', 'write'];
  }
}
```

**Observer Pattern**:
```javascript
class EventEmitter {
  constructor() {
    this.events = {};
  }

  on(event, callback) {
    if (!this.events[event]) {
      this.events[event] = [];
    }
    this.events[event].push(callback);
  }

  emit(event, data) {
    if (this.events[event]) {
      this.events[event].forEach(callback => callback(data));
    }
  }

  off(event, callback) {
    if (this.events[event]) {
      this.events[event] = this.events[event].filter(cb => cb !== callback);
    }
  }
}

// Uso
const userEvents = new EventEmitter();

userEvents.on('userCreated', (user) => {
  console.log('Welcome email sent to:', user.email);
});

userEvents.on('userCreated', (user) => {
  console.log('User profile created for:', user.name);
});

userEvents.emit('userCreated', { name: 'John', email: 'john@example.com' });
```

### 3. Arquitectura en Capas
**Concepto**: Separación clara de responsabilidades en capas independientes.

**Estructura de Capas**:
```javascript
// Presentation Layer (Controllers)
class UserController {
  constructor(userService) {
    this.userService = userService;
  }

  async createUser(req, res) {
    try {
      const user = await this.userService.createUser(req.body);
      res.status(201).json(user);
    } catch (error) {
      res.status(400).json({ error: error.message });
    }
  }
}

// Business Logic Layer (Services)
class UserService {
  constructor(userRepository, emailService, validator) {
    this.userRepository = userRepository;
    this.emailService = emailService;
    this.validator = validator;
  }

  async createUser(userData) {
    const validatedData = this.validator.validate(userData);
    const user = await this.userRepository.create(validatedData);
    await this.emailService.sendWelcomeEmail(user);
    return user;
  }
}

// Data Access Layer (Repositories)
class UserRepository {
  constructor(database) {
    this.database = database;
  }

  async create(userData) {
    const query = 'INSERT INTO users (name, email) VALUES (?, ?)';
    const result = await this.database.execute(query, [userData.name, userData.email]);
    return { id: result.insertId, ...userData };
  }
}
```

### 4. Inyección de Dependencias
**Concepto**: Proporcionar dependencias desde el exterior en lugar de crearlas internamente.

**Container de Dependencias**:
```javascript
class Container {
  constructor() {
    this.services = new Map();
    this.singletons = new Map();
  }

  register(name, factory, options = {}) {
    this.services.set(name, { factory, singleton: options.singleton || false });
  }

  resolve(name) {
    const service = this.services.get(name);
    if (!service) {
      throw new Error(`Service ${name} not registered`);
    }

    if (service.singleton) {
      if (!this.singletons.has(name)) {
        this.singletons.set(name, service.factory(this));
      }
      return this.singletons.get(name);
    }

    return service.factory(this);
  }
}

// Configuración
const container = new Container();

container.register('database', () => new Database(config));
container.register('userRepository', (container) => 
  new UserRepository(container.resolve('database')), { singleton: true }
);
container.register('emailService', () => new EmailService(config));
container.register('userService', (container) => 
  new UserService(
    container.resolve('userRepository'),
    container.resolve('emailService')
  )
);

// Uso
const userService = container.resolve('userService');
```

### 5. Repository Pattern
**Concepto**: Abstracción de la capa de acceso a datos.

**Implementación Base**:
```javascript
class BaseRepository {
  constructor(model) {
    this.model = model;
  }

  async findById(id) {
    return this.model.findByPk(id);
  }

  async findAll(options = {}) {
    return this.model.findAll(options);
  }

  async create(data) {
    return this.model.create(data);
  }

  async update(id, data) {
    const entity = await this.findById(id);
    if (!entity) {
      throw new Error('Entity not found');
    }
    return entity.update(data);
  }

  async delete(id) {
    const entity = await this.findById(id);
    if (!entity) {
      throw new Error('Entity not found');
    }
    await entity.destroy();
    return true;
  }
}

class UserRepository extends BaseRepository {
  constructor() {
    super(User);
  }

  async findByEmail(email) {
    return this.model.findOne({ where: { email } });
  }

  async findActiveUsers() {
    return this.model.findAll({ where: { isActive: true } });
  }
}
```

### 6. Command Pattern
**Concepto**: Encapsular solicitudes como objetos para parametrizar clientes.

**Implementación**:
```javascript
class Command {
  execute() {
    throw new Error('execute method must be implemented');
  }

  undo() {
    throw new Error('undo method must be implemented');
  }
}

class CreateUserCommand extends Command {
  constructor(userService, userData) {
    super();
    this.userService = userService;
    this.userData = userData;
    this.createdUser = null;
  }

  async execute() {
    this.createdUser = await this.userService.createUser(this.userData);
    return this.createdUser;
  }

  async undo() {
    if (this.createdUser) {
      await this.userService.deleteUser(this.createdUser.id);
    }
  }
}

class CommandInvoker {
  constructor() {
    this.commands = [];
    this.executedCommands = [];
  }

  executeCommand(command) {
    const result = command.execute();
    this.executedCommands.push(command);
    return result;
  }

  undoLastCommand() {
    const lastCommand = this.executedCommands.pop();
    if (lastCommand) {
      lastCommand.undo();
    }
  }
}
```

### 7. Event-Driven Architecture
**Concepto**: Arquitectura basada en eventos para sistemas desacoplados.

**Event Bus**:
```javascript
class EventBus {
  constructor() {
    this.handlers = new Map();
    this.middleware = [];
  }

  use(middleware) {
    this.middleware.push(middleware);
  }

  async publish(event, data) {
    const handlers = this.handlers.get(event) || [];
    
    for (const handler of handlers) {
      try {
        // Aplicar middleware
        let processedData = data;
        for (const mw of this.middleware) {
          processedData = await mw(event, processedData);
        }
        
        await handler(processedData);
      } catch (error) {
        console.error(`Error in event handler for ${event}:`, error);
      }
    }
  }

  subscribe(event, handler) {
    if (!this.handlers.has(event)) {
      this.handlers.set(event, []);
    }
    this.handlers.get(event).push(handler);
  }

  unsubscribe(event, handler) {
    if (this.handlers.has(event)) {
      const handlers = this.handlers.get(event);
      const index = handlers.indexOf(handler);
      if (index > -1) {
        handlers.splice(index, 1);
      }
    }
  }
}

// Middleware de logging
const loggingMiddleware = async (event, data) => {
  console.log(`[${new Date().toISOString()}] Event: ${event}`, data);
  return data;
};

// Uso
const eventBus = new EventBus();
eventBus.use(loggingMiddleware);

eventBus.subscribe('user.created', async (user) => {
  await sendWelcomeEmail(user);
});

eventBus.subscribe('user.created', async (user) => {
  await createUserProfile(user);
});

await eventBus.publish('user.created', { name: 'John', email: 'john@example.com' });
```

### 8. CQRS (Command Query Responsibility Segregation)
**Concepto**: Separar operaciones de lectura y escritura.

**Implementación**:
```javascript
// Commands (Write Operations)
class CreateUserCommand {
  constructor(userData) {
    this.userData = userData;
  }
}

class UpdateUserCommand {
  constructor(id, userData) {
    this.id = id;
    this.userData = userData;
  }
}

// Queries (Read Operations)
class GetUserQuery {
  constructor(id) {
    this.id = id;
  }
}

class GetUsersQuery {
  constructor(filters = {}) {
    this.filters = filters;
  }
}

// Command Handler
class CreateUserCommandHandler {
  constructor(userRepository, eventBus) {
    this.userRepository = userRepository;
    this.eventBus = eventBus;
  }

  async handle(command) {
    const user = await this.userRepository.create(command.userData);
    await this.eventBus.publish('user.created', user);
    return user;
  }
}

// Query Handler
class GetUserQueryHandler {
  constructor(userRepository) {
    this.userRepository = userRepository;
  }

  async handle(query) {
    return this.userRepository.findById(query.id);
  }
}

// Bus de Comandos y Queries
class CommandQueryBus {
  constructor() {
    this.commandHandlers = new Map();
    this.queryHandlers = new Map();
  }

  registerCommand(commandType, handler) {
    this.commandHandlers.set(commandType, handler);
  }

  registerQuery(queryType, handler) {
    this.queryHandlers.set(queryType, handler);
  }

  async executeCommand(command) {
    const handler = this.commandHandlers.get(command.constructor);
    if (!handler) {
      throw new Error(`No handler registered for ${command.constructor.name}`);
    }
    return handler.handle(command);
  }

  async executeQuery(query) {
    const handler = this.queryHandlers.get(query.constructor);
    if (!handler) {
      throw new Error(`No handler registered for ${query.constructor.name}`);
    }
    return handler.handle(query);
  }
}
```

### 9. Microservices Architecture
**Concepto**: Arquitectura de servicios independientes y desacoplados.

**Service Discovery**:
```javascript
class ServiceRegistry {
  constructor() {
    this.services = new Map();
  }

  register(serviceName, serviceInfo) {
    this.services.set(serviceName, {
      ...serviceInfo,
      registeredAt: new Date(),
      lastHeartbeat: new Date()
    });
  }

  deregister(serviceName) {
    this.services.delete(serviceName);
  }

  getService(serviceName) {
    return this.services.get(serviceName);
  }

  getAllServices() {
    return Array.from(this.services.entries());
  }

  heartbeat(serviceName) {
    const service = this.services.get(serviceName);
    if (service) {
      service.lastHeartbeat = new Date();
    }
  }
}

class ServiceClient {
  constructor(serviceRegistry) {
    this.registry = serviceRegistry;
  }

  async callService(serviceName, endpoint, data) {
    const service = this.registry.getService(serviceName);
    if (!service) {
      throw new Error(`Service ${serviceName} not found`);
    }

    const response = await fetch(`${service.url}${endpoint}`, {
      method: 'POST',
      headers: { 'Content-Type': 'application/json' },
      body: JSON.stringify(data)
    });

    return response.json();
  }
}
```

### 10. Circuit Breaker Pattern
**Concepto**: Prevenir fallas en cascada en sistemas distribuidos.

**Implementación**:
```javascript
class CircuitBreaker {
  constructor(failureThreshold = 5, resetTimeout = 60000) {
    this.failureThreshold = failureThreshold;
    this.resetTimeout = resetTimeout;
    this.failureCount = 0;
    this.lastFailureTime = null;
    this.state = 'CLOSED'; // CLOSED, OPEN, HALF_OPEN
  }

  async execute(operation) {
    if (this.state === 'OPEN') {
      if (this.shouldAttemptReset()) {
        this.state = 'HALF_OPEN';
      } else {
        throw new Error('Circuit breaker is OPEN');
      }
    }

    try {
      const result = await operation();
      this.onSuccess();
      return result;
    } catch (error) {
      this.onFailure();
      throw error;
    }
  }

  onSuccess() {
    this.failureCount = 0;
    this.state = 'CLOSED';
  }

  onFailure() {
    this.failureCount++;
    this.lastFailureTime = Date.now();

    if (this.failureCount >= this.failureThreshold) {
      this.state = 'OPEN';
    }
  }

  shouldAttemptReset() {
    return Date.now() - this.lastFailureTime >= this.resetTimeout;
  }

  getState() {
    return this.state;
  }
}

// Uso
const circuitBreaker = new CircuitBreaker(3, 30000);

const riskyOperation = async () => {
  // Simular operación que puede fallar
  if (Math.random() > 0.7) {
    throw new Error('Service unavailable');
  }
  return 'Success';
};

try {
  const result = await circuitBreaker.execute(riskyOperation);
  console.log('Result:', result);
} catch (error) {
  console.log('Circuit breaker state:', circuitBreaker.getState());
  console.log('Error:', error.message);
}
```

## Ejercicios Prácticos

### Ejercicio 1: Implementación de SOLID
Crea una aplicación de gestión de biblioteca aplicando todos los principios SOLID.

### Ejercicio 2: Patrón Observer
Implementa un sistema de notificaciones usando el patrón Observer para diferentes tipos de eventos.

### Ejercicio 3: Arquitectura en Capas
Diseña una API REST con separación clara de capas (Controller, Service, Repository).

### Ejercicio 4: Inyección de Dependencias
Crea un container de dependencias que maneje servicios singleton y transitorios.

### Ejercicio 5: Repository Pattern
Implementa un sistema de repositorios para diferentes entidades con operaciones CRUD.

### Ejercicio 6: Command Pattern
Crea un sistema de comandos para operaciones de usuario con capacidad de undo/redo.

### Ejercicio 7: Event-Driven Architecture
Implementa un sistema de eventos para un e-commerce con múltiples handlers.

### Ejercicio 8: CQRS Implementation
Diseña un sistema CQRS para gestión de usuarios con separación de comandos y queries.

### Ejercicio 9: Microservices Communication
Crea un sistema de comunicación entre microservicios usando service discovery.

### Ejercicio 10: Circuit Breaker
Implementa un circuit breaker para proteger llamadas a APIs externas.

## Proyecto Integrador: Framework de Arquitectura Modular

### Descripción
Desarrolla un framework completo que implemente todos los patrones arquitectónicos aprendidos, permitiendo crear aplicaciones escalables y mantenibles.

### Características Requeridas
- **Arquitectura en Capas**: Separación clara de responsabilidades
- **Inyección de Dependencias**: Container robusto y configurable
- **Event-Driven**: Sistema de eventos con middleware
- **CQRS**: Separación de comandos y queries
- **Patrones de Diseño**: Factory, Observer, Command, Repository
- **Testing**: Tests unitarios y de integración completos

### Estructura del Proyecto
```
framework-arquitectura/
├── src/
│   ├── core/
│   │   ├── container.js
│   │   ├── eventBus.js
│   │   └── commandBus.js
│   ├── patterns/
│   │   ├── factory.js
│   │   ├── observer.js
│   │   ├── command.js
│   │   └── repository.js
│   ├── architecture/
│   │   ├── layers/
│   │   ├── cqrs/
│   │   └── microservices/
│   └── index.js
├── examples/
├── tests/
├── package.json
└── README.md
```

### Funcionalidades
1. **Container de Dependencias**: Gestión de servicios y dependencias
2. **Event Bus**: Sistema de eventos con middleware
3. **Command Bus**: Ejecución de comandos y queries
4. **Patrones de Diseño**: Implementaciones reutilizables
5. **Arquitectura en Capas**: Base para aplicaciones modulares
6. **Testing**: Suite completa de tests

## Evaluación del Módulo

### Criterios de Evaluación
- **Arquitectura**: Diseño limpio y mantenible
- **Patrones**: Implementación correcta de patrones de diseño
- **SOLID**: Aplicación de principios SOLID
- **Testing**: Cobertura de tests adecuada
- **Documentación**: Código bien documentado

### Puntos Clave
- Principios SOLID en JavaScript
- Patrones de diseño avanzados
- Arquitectura en capas
- Inyección de dependencias
- Event-driven architecture

## Recursos Adicionales

### Documentación
- [SOLID Principles](https://en.wikipedia.org/wiki/SOLID)
- [Design Patterns](https://refactoring.guru/design-patterns)
- [Clean Architecture](https://blog.cleancoder.com/uncle-bob/2012/08/13/the-clean-architecture.html)

### Herramientas
- **InversifyJS**: Container de dependencias
- **EventEmitter**: Sistema de eventos nativo
- **Jest**: Testing framework
- **ESLint**: Linting de código

### Próximos Pasos
- **Módulo 8**: Testing Avanzado y E2E
- **Módulo 9**: Performance y Optimización
- **Módulo 10**: DevOps y Deployment

---

**Nota**: Este módulo establece las bases para arquitectura empresarial en JavaScript, preparando al estudiante para sistemas complejos y escalables.
