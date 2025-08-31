# 🔧 Módulo 4: Programación Asíncrona

## 🧭 Navegación del Curso

- **⬅️ Anterior**: [Módulo 3: DOM y Eventos](../junior_3/README.md)
- **➡️ Siguiente**: [Módulo 5: ES6+ y Características Modernas](../midLevel_2/README.md)
- **📚 [Índice Completo](../INDICE_COMPLETO.md)** | **[🧭 Navegación Rápida](../NAVEGACION_RAPIDA.md)**

---

## 📚 Descripción del Módulo

Este módulo te introduce a la programación asíncrona en JavaScript, un concepto fundamental para crear aplicaciones web modernas y responsivas. Aprenderás sobre callbacks, promesas, async/await y cómo manejar operaciones que no se completan inmediatamente, como llamadas a APIs, operaciones de archivo y temporizadores.

## 🎯 Objetivos de Aprendizaje

- Comprender el modelo de ejecución asíncrona de JavaScript
- Dominar el uso de callbacks y manejo de errores
- Implementar promesas y encadenamiento efectivo
- Aplicar async/await para código más legible
- Manejar operaciones asíncronas complejas y APIs

## 📖 Contenido Teórico

### 1. Introducción a la Programación Asíncrona

La programación asíncrona permite que JavaScript ejecute código sin bloquear la ejecución del programa principal, especialmente importante para operaciones que pueden tomar tiempo como llamadas a APIs, operaciones de archivo o temporizadores.

#### ¿Por qué es Necesaria la Programación Asíncrona?
```javascript
// Código síncrono (bloqueante)
console.log('Inicio');
const resultado = operacionLenta(); // Bloquea aquí
console.log('Resultado:', resultado);
console.log('Fin');

// Código asíncrono (no bloqueante)
console.log('Inicio');
operacionLentaAsync((resultado) => {
    console.log('Resultado:', resultado);
});
console.log('Fin'); // Se ejecuta inmediatamente
```

#### Modelo de Event Loop
```javascript
// JavaScript usa un modelo de event loop
// 1. Call Stack: Ejecuta código síncrono
// 2. Web APIs: Maneja operaciones asíncronas
// 3. Callback Queue: Cola de callbacks listos
// 4. Event Loop: Mueve callbacks del queue al stack

console.log('1. Inicio');
setTimeout(() => console.log('4. Timeout'), 0);
Promise.resolve().then(() => console.log('3. Promise'));
console.log('2. Fin');

// Salida:
// 1. Inicio
// 2. Fin
// 3. Promise
// 4. Timeout
```

### 2. Callbacks

Los callbacks son funciones que se pasan como argumentos a otras funciones y se ejecutan cuando se completa una operación asíncrona.

#### Callbacks Básicos
```javascript
// Función que acepta un callback
function procesarDatos(datos, callback) {
    console.log('Procesando datos...');
    
    // Simular operación asíncrona
    setTimeout(() => {
        const resultado = datos.toUpperCase();
        callback(null, resultado);
    }, 1000);
}

// Uso del callback
procesarDatos('hola mundo', (error, resultado) => {
    if (error) {
        console.error('Error:', error);
        return;
    }
    console.log('Resultado:', resultado);
});

// Callback con manejo de errores
function dividirNumeros(a, b, callback) {
    if (b === 0) {
        callback(new Error('División por cero no permitida'));
        return;
    }
    
    setTimeout(() => {
        const resultado = a / b;
        callback(null, resultado);
    }, 100);
}

dividirNumeros(10, 2, (error, resultado) => {
    if (error) {
        console.error('Error:', error.message);
        return;
    }
    console.log('Resultado:', resultado);
});
```

#### Callback Hell (Infierno de Callbacks)
```javascript
// Problema: Callbacks anidados se vuelven difíciles de leer
obtenerUsuario(1, (error, usuario) => {
    if (error) {
        console.error('Error al obtener usuario:', error);
        return;
    }
    
    obtenerPerfil(usuario.id, (error, perfil) => {
        if (error) {
            console.error('Error al obtener perfil:', error);
            return;
        }
        
        obtenerConfiguracion(perfil.configId, (error, config) => {
            if (error) {
                console.error('Error al obtener configuración:', error);
                return;
            }
            
            console.log('Usuario completo:', { usuario, perfil, config });
        });
    });
});

// Solución: Separar en funciones más pequeñas
function procesarUsuarioCompleto(userId) {
    obtenerUsuario(userId, (error, usuario) => {
        if (error) {
            console.error('Error al obtener usuario:', error);
            return;
        }
        procesarPerfil(usuario);
    });
}

function procesarPerfil(usuario) {
    obtenerPerfil(usuario.id, (error, perfil) => {
        if (error) {
            console.error('Error al obtener perfil:', error);
            return;
        }
        procesarConfiguracion(perfil);
    });
}

function procesarConfiguracion(perfil) {
    obtenerConfiguracion(perfil.configId, (error, config) => {
        if (error) {
            console.error('Error al obtener configuración:', error);
            return;
        }
        console.log('Usuario completo:', { usuario, perfil, config });
    });
}
```

### 3. Promesas

Las promesas son objetos que representan el resultado eventual de una operación asíncrona, proporcionando una forma más elegante de manejar código asíncrono.

#### Creación de Promesas
```javascript
// Crear una promesa
const miPromesa = new Promise((resolve, reject) => {
    // Operación asíncrona
    setTimeout(() => {
        const exito = Math.random() > 0.5;
        
        if (exito) {
            resolve('Operación exitosa');
        } else {
            reject(new Error('Operación falló'));
        }
    }, 1000);
});

// Usar la promesa
miPromesa
    .then(resultado => {
        console.log('Éxito:', resultado);
    })
    .catch(error => {
        console.error('Error:', error.message);
    });

// Promesa que siempre se resuelve
const promesaResuelta = Promise.resolve('Valor inmediato');
const promesaRechazada = Promise.reject(new Error('Error inmediato'));
```

#### Encadenamiento de Promesas
```javascript
// Encadenamiento básico
obtenerUsuario(1)
    .then(usuario => {
        console.log('Usuario obtenido:', usuario);
        return obtenerPerfil(usuario.id);
    })
    .then(perfil => {
        console.log('Perfil obtenido:', perfil);
        return obtenerConfiguracion(perfil.configId);
    })
    .then(config => {
        console.log('Configuración obtenida:', config);
        return { usuario, perfil, config };
    })
    .catch(error => {
        console.error('Error en cualquier paso:', error);
    });

// Encadenamiento con transformaciones
fetch('/api/usuarios')
    .then(response => response.json())
    .then(usuarios => usuarios.filter(u => u.activo))
    .then(usuariosActivos => usuariosActivos.map(u => u.nombre))
    .then(nombres => console.log('Usuarios activos:', nombres))
    .catch(error => console.error('Error:', error));
```

#### Métodos Estáticos de Promise
```javascript
// Promise.all - Todas las promesas deben resolverse
const promesas = [
    fetch('/api/usuarios'),
    fetch('/api/productos'),
    fetch('/api/ordenes')
];

Promise.all(promesas)
    .then(responses => {
        const [usuarios, productos, ordenes] = responses;
        return Promise.all([
            usuarios.json(),
            productos.json(),
            ordenes.json()
        ]);
    })
    .then(([usuarios, productos, ordenes]) => {
        console.log('Todos los datos cargados:', { usuarios, productos, ordenes });
    })
    .catch(error => {
        console.error('Error al cargar datos:', error);
    });

// Promise.race - La primera promesa que se resuelve
const promesaRapida = new Promise(resolve => setTimeout(() => resolve('Rápida'), 100));
const promesaLenta = new Promise(resolve => setTimeout(() => resolve('Lenta'), 1000));

Promise.race([promesaRapida, promesaLenta])
    .then(resultado => console.log('Ganadora:', resultado)); // "Rápida"

// Promise.allSettled - Todas las promesas se completan (resuelvan o rechacen)
const promesasMixtas = [
    Promise.resolve('Éxito'),
    Promise.reject(new Error('Fallo')),
    Promise.resolve('Otro éxito')
];

Promise.allSettled(promesasMixtas)
    .then(resultados => {
        resultados.forEach((resultado, index) => {
            if (resultado.status === 'fulfilled') {
                console.log(`Promesa ${index}: Éxito -`, resultado.value);
            } else {
                console.log(`Promesa ${index}: Fallo -`, resultado.reason);
            }
        });
    });
```

### 4. Async/Await

Async/await es una sintaxis más moderna y legible para trabajar con promesas, permitiendo escribir código asíncrono que se ve como código síncrono.

#### Sintaxis Básica
```javascript
// Función async siempre retorna una promesa
async function obtenerDatos() {
    return 'Datos obtenidos';
}

// Equivalente a:
function obtenerDatos() {
    return Promise.resolve('Datos obtenidos');
}

// Usar await para esperar promesas
async function procesarUsuario(userId) {
    try {
        const usuario = await obtenerUsuario(userId);
        const perfil = await obtenerPerfil(usuario.id);
        const config = await obtenerConfiguracion(perfil.configId);
        
        return { usuario, perfil, config };
    } catch (error) {
        console.error('Error al procesar usuario:', error);
        throw error; // Re-lanzar el error
    }
}

// Llamar función async
procesarUsuario(1)
    .then(resultado => console.log('Usuario procesado:', resultado))
    .catch(error => console.error('Error:', error));
```

#### Manejo de Errores con Try/Catch
```javascript
async function operacionRiesgosa() {
    try {
        const resultado1 = await paso1();
        const resultado2 = await paso2(resultado1);
        const resultado3 = await paso3(resultado2);
        
        return resultado3;
    } catch (error) {
        console.error('Error en operación:', error);
        
        // Intentar recuperación
        try {
            return await recuperacion();
        } catch (recuperacionError) {
            console.error('Error en recuperación:', recuperacionError);
            throw new Error('Operación falló completamente');
        }
    }
}

// Manejo de errores específicos
async function manejarErroresEspecificos() {
    try {
        const resultado = await operacionRiesgosa();
        return resultado;
    } catch (error) {
        if (error.name === 'NetworkError') {
            console.log('Error de red, reintentando...');
            return await reintentar();
        } else if (error.name === 'ValidationError') {
            console.log('Error de validación:', error.message);
            return null;
        } else {
            throw error; // Re-lanzar errores desconocidos
        }
    }
}
```

#### Operaciones Paralelas con Async/Await
```javascript
// Operaciones secuenciales (lentas)
async function operacionesSecuenciales() {
    const inicio = Date.now();
    
    const resultado1 = await operacionLenta(1000);
    const resultado2 = await operacionLenta(1000);
    const resultado3 = await operacionLenta(1000);
    
    const duracion = Date.now() - inicio;
    console.log(`Secuencial: ${duracion}ms`);
    
    return [resultado1, resultado2, resultado3];
}

// Operaciones paralelas (rápidas)
async function operacionesParalelas() {
    const inicio = Date.now();
    
    const [resultado1, resultado2, resultado3] = await Promise.all([
        operacionLenta(1000),
        operacionLenta(1000),
        operacionLenta(1000)
    ]);
    
    const duracion = Date.now() - inicio;
    console.log(`Paralelo: ${duracion}ms`);
    
    return [resultado1, resultado2, resultado3];
}

// Combinar secuencial y paralelo
async function operacionCompleja() {
    // Paso 1: Operación inicial
    const config = await obtenerConfiguracion();
    
    // Paso 2: Operaciones paralelas
    const [usuarios, productos, ordenes] = await Promise.all([
        obtenerUsuarios(config.usuariosUrl),
        obtenerProductos(config.productosUrl),
        obtenerOrdenes(config.ordenesUrl)
    ]);
    
    // Paso 3: Procesamiento secuencial
    const usuariosProcesados = await procesarUsuarios(usuarios);
    const productosProcesados = await procesarProductos(productos);
    const ordenesProcesadas = await procesarOrdenes(ordenes);
    
    return {
        usuarios: usuariosProcesados,
        productos: productosProcesados,
        ordenes: ordenesProcesadas
    };
}
```

### 5. Fetch API

Fetch es una API moderna para realizar peticiones HTTP, reemplazando XMLHttpRequest con una interfaz más limpia basada en promesas.

#### Peticiones Básicas
```javascript
// GET request básico
fetch('/api/usuarios')
    .then(response => {
        if (!response.ok) {
            throw new Error(`HTTP error! status: ${response.status}`);
        }
        return response.json();
    })
    .then(data => console.log('Usuarios:', data))
    .catch(error => console.error('Error:', error));

// Con async/await
async function obtenerUsuarios() {
    try {
        const response = await fetch('/api/usuarios');
        
        if (!response.ok) {
            throw new Error(`HTTP error! status: ${response.status}`);
        }
        
        const usuarios = await response.json();
        return usuarios;
    } catch (error) {
        console.error('Error al obtener usuarios:', error);
        throw error;
    }
}
```

#### Diferentes Tipos de Peticiones
```javascript
// POST request
async function crearUsuario(datosUsuario) {
    const response = await fetch('/api/usuarios', {
        method: 'POST',
        headers: {
            'Content-Type': 'application/json',
            'Authorization': `Bearer ${token}`
        },
        body: JSON.stringify(datosUsuario)
    });
    
    if (!response.ok) {
        throw new Error(`Error al crear usuario: ${response.status}`);
    }
    
    return await response.json();
}

// PUT request
async function actualizarUsuario(id, datosActualizados) {
    const response = await fetch(`/api/usuarios/${id}`, {
        method: 'PUT',
        headers: {
            'Content-Type': 'application/json',
            'Authorization': `Bearer ${token}`
        },
        body: JSON.stringify(datosActualizados)
    });
    
    if (!response.ok) {
        throw new Error(`Error al actualizar usuario: ${response.status}`);
    }
    
    return await response.json();
}

// DELETE request
async function eliminarUsuario(id) {
    const response = await fetch(`/api/usuarios/${id}`, {
        method: 'DELETE',
        headers: {
            'Authorization': `Bearer ${token}`
        }
    });
    
    if (!response.ok) {
        throw new Error(`Error al eliminar usuario: ${response.status}`);
    }
    
    return true;
}
```

#### Manejo de Respuestas y Errores
```javascript
// Cliente HTTP reutilizable
class HttpClient {
    constructor(baseURL, defaultHeaders = {}) {
        this.baseURL = baseURL;
        this.defaultHeaders = defaultHeaders;
    }
    
    async request(endpoint, options = {}) {
        const url = `${this.baseURL}${endpoint}`;
        const config = {
            headers: { ...this.defaultHeaders, ...options.headers },
            ...options
        };
        
        try {
            const response = await fetch(url, config);
            
            // Manejar diferentes códigos de estado
            if (response.status === 401) {
                throw new Error('No autorizado');
            } else if (response.status === 404) {
                throw new Error('Recurso no encontrado');
            } else if (response.status >= 500) {
                throw new Error('Error del servidor');
            } else if (!response.ok) {
                throw new Error(`Error HTTP: ${response.status}`);
            }
            
            // Determinar tipo de respuesta
            const contentType = response.headers.get('content-type');
            if (contentType && contentType.includes('application/json')) {
                return await response.json();
            } else {
                return await response.text();
            }
        } catch (error) {
            if (error.name === 'TypeError' && error.message.includes('fetch')) {
                throw new Error('Error de red o CORS');
            }
            throw error;
        }
    }
    
    get(endpoint, options = {}) {
        return this.request(endpoint, { ...options, method: 'GET' });
    }
    
    post(endpoint, data, options = {}) {
        return this.request(endpoint, {
            ...options,
            method: 'POST',
            body: JSON.stringify(data)
        });
    }
    
    put(endpoint, data, options = {}) {
        return this.request(endpoint, {
            ...options,
            method: 'PUT',
            body: JSON.stringify(data)
        });
    }
    
    delete(endpoint, options = {}) {
        return this.request(endpoint, { ...options, method: 'DELETE' });
    }
}

// Uso del cliente HTTP
const api = new HttpClient('https://api.ejemplo.com', {
    'Content-Type': 'application/json'
});

// Ejemplos de uso
api.get('/usuarios')
    .then(usuarios => console.log(usuarios))
    .catch(error => console.error(error));

api.post('/usuarios', { nombre: 'Juan', email: 'juan@ejemplo.com' })
    .then(usuario => console.log('Usuario creado:', usuario))
    .catch(error => console.error(error));
```

### 6. XMLHttpRequest (Legacy)

XMLHttpRequest es la API más antigua para peticiones HTTP, aún útil para compatibilidad con navegadores antiguos.

```javascript
// GET request con XMLHttpRequest
function obtenerDatosXHR(url) {
    return new Promise((resolve, reject) => {
        const xhr = new XMLHttpRequest();
        
        xhr.open('GET', url, true);
        xhr.setRequestHeader('Content-Type', 'application/json');
        
        xhr.onload = function() {
            if (xhr.status >= 200 && xhr.status < 300) {
                try {
                    const data = JSON.parse(xhr.responseText);
                    resolve(data);
                } catch (error) {
                    reject(new Error('Error al parsear respuesta JSON'));
                }
            } else {
                reject(new Error(`HTTP Error: ${xhr.status}`));
            }
        };
        
        xhr.onerror = function() {
            reject(new Error('Error de red'));
        };
        
        xhr.ontimeout = function() {
            reject(new Error('Timeout de la petición'));
        };
        
        xhr.timeout = 10000; // 10 segundos
        xhr.send();
    });
}

// POST request con XMLHttpRequest
function enviarDatosXHR(url, datos) {
    return new Promise((resolve, reject) => {
        const xhr = new XMLHttpRequest();
        
        xhr.open('POST', url, true);
        xhr.setRequestHeader('Content-Type', 'application/json');
        
        xhr.onload = function() {
            if (xhr.status >= 200 && xhr.status < 300) {
                try {
                    const response = JSON.parse(xhr.responseText);
                    resolve(response);
                } catch (error) {
                    reject(new Error('Error al parsear respuesta JSON'));
                }
            } else {
                reject(new Error(`HTTP Error: ${xhr.status}`));
            }
        };
        
        xhr.onerror = function() {
            reject(new Error('Error de red'));
        };
        
        xhr.send(JSON.stringify(datos));
    });
}
```

### 7. Temporizadores y Operaciones Retrasadas

JavaScript proporciona funciones para ejecutar código después de un retraso o en intervalos regulares.

```javascript
// setTimeout - Ejecutar código después de un retraso
const timeoutId = setTimeout(() => {
    console.log('Este mensaje aparece después de 2 segundos');
}, 2000);

// Cancelar timeout
clearTimeout(timeoutId);

// setInterval - Ejecutar código en intervalos regulares
const intervalId = setInterval(() => {
    console.log('Este mensaje aparece cada segundo');
}, 1000);

// Cancelar interval
clearInterval(intervalId);

// Implementar retraso con promesas
function delay(ms) {
    return new Promise(resolve => setTimeout(resolve, ms));
}

// Uso del delay
async function operacionConRetraso() {
    console.log('Inicio');
    await delay(2000);
    console.log('Después de 2 segundos');
    await delay(1000);
    console.log('Después de 1 segundo más');
}

// Implementar timeout para promesas
function timeout(promesa, ms) {
    return Promise.race([
        promesa,
        new Promise((_, reject) => 
            setTimeout(() => reject(new Error('Timeout')), ms)
        )
    ]);
}

// Uso del timeout
const promesaLenta = new Promise(resolve => 
    setTimeout(() => resolve('Completada'), 5000)
);

timeout(promesaLenta, 3000)
    .then(resultado => console.log('Éxito:', resultado))
    .catch(error => console.error('Error:', error.message)); // "Timeout"
```

### 8. Patrones Avanzados de Programación Asíncrona

#### Retry Pattern
```javascript
async function retry(operacion, maxIntentos = 3, delay = 1000) {
    for (let intento = 1; intento <= maxIntentos; intento++) {
        try {
            return await operacion();
        } catch (error) {
            if (intento === maxIntentos) {
                throw error;
            }
            
            console.log(`Intento ${intento} falló, reintentando en ${delay}ms...`);
            await new Promise(resolve => setTimeout(resolve, delay));
        }
    }
}

// Uso del patrón retry
const operacionRiesgosa = () => fetch('/api/datos');
retry(operacionRiesgosa, 3, 2000)
    .then(response => console.log('Éxito después de reintentos'))
    .catch(error => console.error('Falló después de todos los intentos'));
```

#### Circuit Breaker Pattern
```javascript
class CircuitBreaker {
    constructor(operacion, umbral = 5, timeout = 60000) {
        this.operacion = operacion;
        this.umbral = umbral;
        this.timeout = timeout;
        this.estado = 'CLOSED'; // CLOSED, OPEN, HALF_OPEN
        this.fallos = 0;
        this.ultimoFalloTiempo = 0;
    }
    
    async ejecutar(...args) {
        if (this.estado === 'OPEN') {
            if (Date.now() - this.ultimoFalloTiempo > this.timeout) {
                this.estado = 'HALF_OPEN';
            } else {
                throw new Error('Circuit breaker está abierto');
            }
        }
        
        try {
            const resultado = await this.operacion(...args);
            this.onExito();
            return resultado;
        } catch (error) {
            this.onFallo();
            throw error;
        }
    }
    
    onExito() {
        this.fallos = 0;
        this.estado = 'CLOSED';
    }
    
    onFallo() {
        this.fallos++;
        this.ultimoFalloTiempo = Date.now();
        
        if (this.fallos >= this.umbral) {
            this.estado = 'OPEN';
        }
    }
}

// Uso del circuit breaker
const operacionRiesgosa = async () => {
    const response = await fetch('/api/datos');
    if (!response.ok) throw new Error('API error');
    return response.json();
};

const breaker = new CircuitBreaker(operacionRiesgosa);

// Ejecutar con protección
breaker.ejecutar()
    .then(resultado => console.log('Éxito:', resultado))
    .catch(error => console.error('Error:', error.message));
```

## 🧪 Ejercicios Prácticos

### Ejercicio 1: Cliente de API REST
Implementa un cliente completo de API REST con manejo de errores y reintentos.

```javascript
// Tu código aquí
class ApiClient {
    constructor(baseURL, options = {}) {
        this.baseURL = baseURL;
        this.options = {
            timeout: 10000,
            retries: 3,
            ...options
        };
    }
    
    async request(endpoint, config = {}) {
        // Implementa peticiones HTTP con reintentos
    }
    
    async get(endpoint) {
        // Implementa GET requests
    }
    
    async post(endpoint, data) {
        // Implementa POST requests
    }
}
```

### Ejercicio 2: Sistema de Cola de Tareas
Crea un sistema de cola que procese tareas de forma asíncrona con prioridades.

```javascript
// Tu código aquí
class ColaTareas {
    constructor() {
        this.cola = [];
        this.procesando = false;
    }
    
    agregarTarea(tarea, prioridad = 0) {
        // Implementa añadir tarea con prioridad
    }
    
    async procesarCola() {
        // Implementa procesamiento de cola
    }
    
    async ejecutarTarea(tarea) {
        // Implementa ejecución de tarea individual
    }
}
```

### Ejercicio 3: Cache de Promesas
Implementa un sistema de cache para promesas que evite peticiones duplicadas.

```javascript
// Tu código aquí
class CachePromesas {
    constructor() {
        this.cache = new Map();
    }
    
    async obtener(clave, operacion) {
        // Implementa cache de promesas
    }
    
    invalidar(clave) {
        // Implementa invalidación de cache
    }
    
    limpiar() {
        // Implementa limpieza de cache
    }
}
```

### Ejercicio 4: Sistema de Notificaciones Asíncronas
Crea un sistema de notificaciones que maneje diferentes tipos de eventos de forma asíncrona.

```javascript
// Tu código aquí
class SistemaNotificaciones {
    constructor() {
        this.suscriptores = new Map();
        this.cola = [];
        this.procesando = false;
    }
    
    suscribir(tipo, callback) {
        // Implementa suscripción a eventos
    }
    
    async emitir(tipo, datos) {
        // Implementa emisión asíncrona de eventos
    }
    
    async procesarCola() {
        // Implementa procesamiento de cola de notificaciones
    }
}
```

### Ejercicio 5: Gestor de Archivos Asíncrono
Implementa un gestor de archivos que maneje operaciones de lectura/escritura de forma asíncrona.

```javascript
// Tu código aquí
class GestorArchivos {
    constructor() {
        this.archivos = new Map();
        this.operaciones = [];
    }
    
    async leerArchivo(nombre) {
        // Implementa lectura asíncrona
    }
    
    async escribirArchivo(nombre, contenido) {
        // Implementa escritura asíncrona
    }
    
    async eliminarArchivo(nombre) {
        // Implementa eliminación asíncrona
    }
    
    async listarArchivos() {
        // Implementa listado asíncrono
    }
}
```

### Ejercicio 6: Sistema de Autenticación
Crea un sistema de autenticación con manejo de tokens y renovación automática.

```javascript
// Tu código aquí
class SistemaAutenticacion {
    constructor() {
        this.token = null;
        this.refreshToken = null;
        this.expiraEn = null;
    }
    
    async iniciarSesion(credenciales) {
        // Implementa inicio de sesión
    }
    
    async renovarToken() {
        // Implementa renovación de token
    }
    
    async verificarToken() {
        // Implementa verificación de token
    }
    
    async cerrarSesion() {
        // Implementa cierre de sesión
    }
}
```

### Ejercicio 7: Gestor de Descargas
Implementa un gestor de descargas con progreso y manejo de errores.

```javascript
// Tu código aquí
class GestorDescargas {
    constructor() {
        this.descargas = new Map();
        this.maxConcurrentes = 3;
    }
    
    async iniciarDescarga(url, destino) {
        // Implementa inicio de descarga
    }
    
    async pausarDescarga(id) {
        // Implementa pausa de descarga
    }
    
    async reanudarDescarga(id) {
        // Implementa reanudación de descarga
    }
    
    async cancelarDescarga(id) {
        // Implementa cancelación de descarga
    }
}
```

### Ejercicio 8: Sistema de Sincronización
Crea un sistema que sincronice datos entre cliente y servidor de forma asíncrona.

```javascript
// Tu código aquí
class SincronizadorDatos {
    constructor() {
        this.cambiosPendientes = [];
        this.sincronizando = false;
        this.ultimaSincronizacion = null;
    }
    
    async sincronizar() {
        // Implementa sincronización de datos
    }
    
    async enviarCambios(cambios) {
        // Implementa envío de cambios
    }
    
    async recibirCambios() {
        // Implementa recepción de cambios
    }
    
    async resolverConflictos(conflictos) {
        // Implementa resolución de conflictos
    }
}
```

### Ejercicio 9: Sistema de Monitoreo
Implementa un sistema que monitoree el estado de servicios externos de forma asíncrona.

```javascript
// Tu código aquí
class MonitorServicios {
    constructor(servicios) {
        this.servicios = servicios;
        this.estados = new Map();
        this.intervalo = null;
    }
    
    async verificarServicio(servicio) {
        // Implementa verificación de servicio
    }
    
    async verificarTodos() {
        // Implementa verificación de todos los servicios
    }
    
    iniciarMonitoreo(intervalo = 30000) {
        // Implementa monitoreo continuo
    }
    
    detenerMonitoreo() {
        // Implementa detención de monitoreo
    }
}
```

### Ejercicio 10: Sistema de Backup Automático
Crea un sistema que realice backups automáticos de datos de forma asíncrona.

```javascript
// Tu código aquí
class SistemaBackup {
    constructor() {
        this.backups = [];
        this.programado = false;
        this.ultimoBackup = null;
    }
    
    async crearBackup(datos) {
        // Implementa creación de backup
    }
    
    async restaurarBackup(id) {
        // Implementa restauración de backup
    }
    
    async programarBackup(intervalo) {
        // Implementa programación automática
    }
    
    async limpiarBackupsAntiguos(dias = 30) {
        // Implementa limpieza de backups antiguos
    }
}
```

## 🎯 Proyecto Integrador: Cliente de API REST Completo

### Descripción
Crea un cliente de API REST completo que implemente todos los conceptos de programación asíncrona aprendidos en este módulo.

### Requisitos
- Cliente HTTP completo con métodos GET, POST, PUT, DELETE
- Sistema de reintentos automáticos
- Manejo de errores robusto
- Cache de respuestas
- Interceptores de peticiones y respuestas
- Sistema de autenticación con tokens
- Rate limiting y throttling
- Logging y debugging
- Testing completo

### Funcionalidades a Implementar
1. **Cliente HTTP Base**: Métodos HTTP estándar
2. **Sistema de Reintentos**: Reintentos automáticos con backoff exponencial
3. **Manejo de Errores**: Clasificación y manejo de diferentes tipos de errores
4. **Cache Inteligente**: Cache con TTL y invalidación
5. **Interceptores**: Middleware para peticiones y respuestas
6. **Autenticación**: JWT, OAuth, API keys
7. **Rate Limiting**: Control de velocidad de peticiones
8. **Logging**: Sistema de logs estructurado
9. **Testing**: Tests unitarios y de integración
10. **Documentación**: API documentation completa

### Estructura del Proyecto
```
cliente-api-rest/
├── src/
│   ├── client.js
│   ├── interceptors/
│   │   ├── auth.js
│   │   ├── cache.js
│   │   ├── retry.js
│   │   └── logging.js
│   ├── utils/
│   │   ├── cache.js
│   │   ├── retry.js
│   │   └── rateLimiter.js
│   └── index.js
├── tests/
│   ├── unit/
│   └── integration/
├── examples/
├── package.json
└── README.md
```

## 📚 Recursos Adicionales

### Documentación
- [MDN - Promesas](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise)
- [MDN - Async/Await](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Statements/async_function)
- [MDN - Fetch API](https://developer.mozilla.org/en-US/docs/Web/API/Fetch_API)

### Libros Recomendados
- "You Don't Know JS: Async & Performance" por Kyle Simpson
- "JavaScript Async Programming" por Trevor Burnham

### Herramientas de Práctica
- [JSONPlaceholder](https://jsonplaceholder.typicode.com/) - API de prueba
- [HTTPBin](https://httpbin.org/) - Testing de peticiones HTTP
- [Postman](https://www.postman.com/) - Testing de APIs

## 🎯 Evaluación del Módulo

### Criterios de Evaluación
- ✅ Comprensión de programación asíncrona
- ✅ Implementación correcta de promesas y async/await
- ✅ Manejo efectivo de errores asíncronos
- ✅ Uso apropiado de Fetch API
- ✅ Completación del proyecto integrador

### Puntos Clave a Dominar
1. **Callbacks**: Manejo básico y patrones
2. **Promesas**: Creación, encadenamiento, métodos estáticos
3. **Async/Await**: Sintaxis, manejo de errores, operaciones paralelas
4. **Fetch API**: Métodos HTTP, headers, manejo de respuestas
5. **Patrones**: Retry, circuit breaker, cache
6. **Performance**: Operaciones paralelas vs secuenciales

---

**¡Completa todos los ejercicios y el proyecto integrador antes de continuar al siguiente módulo!** 🚀

*El siguiente módulo cubrirá ES6+ y Características Modernas, donde aprenderás las últimas características del lenguaje.*

## 🧭 Navegación del Curso

- **⬅️ Anterior**: [Módulo 3: DOM y Eventos](../junior_3/README.md)
- **➡️ Siguiente**: [Módulo 5: ES6+ y Características Modernas](../midLevel_2/README.md)
- **📚 [Índice Completo](../INDICE_COMPLETO.md)** | **[🧭 Navegación Rápida](../NAVEGACION_RAPIDA.md)**

---
