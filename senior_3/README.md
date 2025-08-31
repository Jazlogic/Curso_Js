# Módulo 9: Performance y Optimización

## Descripción del Módulo
Este módulo cubre técnicas avanzadas de optimización de performance en JavaScript, incluyendo análisis de bundle, Core Web Vitals, lazy loading, code splitting, optimización de re-renders, virtualización y optimización de imágenes.

## Objetivos de Aprendizaje
- Analizar y optimizar bundles de JavaScript
- Implementar Core Web Vitals para mejor UX
- Aplicar técnicas de lazy loading y code splitting
- Optimizar re-renders y virtualización
- Implementar estrategias de caching avanzadas

## Contenido del Módulo

### 1. Análisis de Bundle
**Concepto**: Analizar el tamaño y composición del bundle para optimizarlo.

**Webpack Bundle Analyzer**:
```javascript
// webpack.config.js
const { BundleAnalyzerPlugin } = require('webpack-bundle-analyzer');

module.exports = {
  plugins: [
    new BundleAnalyzerPlugin({
      analyzerMode: 'static',
      openAnalyzer: false,
      reportFilename: 'bundle-report.html'
    })
  ]
};

// package.json
{
  "scripts": {
    "analyze": "webpack --profile --json > stats.json && webpack-bundle-analyzer stats.json"
  }
}
```

**Análisis de Dependencias**:
```javascript
// bundle-analysis.js
import { analyze } from 'webpack-bundle-analyzer';

const analyzeBundle = (stats) => {
  const modules = stats.modules;
  const dependencies = {};
  
  modules.forEach(module => {
    const size = module.size;
    const name = module.name;
    
    if (dependencies[name]) {
      dependencies[name] += size;
    } else {
      dependencies[name] = size;
    }
  });
  
  return Object.entries(dependencies)
    .sort(([,a], [,b]) => b - a)
    .slice(0, 10);
};
```

### 2. Core Web Vitals
**Concepto**: Métricas clave para medir la experiencia del usuario.

**Largest Contentful Paint (LCP)**:
```javascript
// performance/lcp.js
const measureLCP = () => {
  const observer = new PerformanceObserver((list) => {
    const entries = list.getEntries();
    const lastEntry = entries[entries.length - 1];
    
    console.log('LCP:', lastEntry.startTime);
    
    if (lastEntry.startTime > 2500) {
      // LCP es lento, implementar optimizaciones
      optimizeLCP();
    }
  });
  
  observer.observe({ entryTypes: ['largest-contentful-paint'] });
};

const optimizeLCP = () => {
  // Optimizar imágenes críticas
  const criticalImages = document.querySelectorAll('img[data-critical]');
  criticalImages.forEach(img => {
    img.loading = 'eager';
    img.fetchPriority = 'high';
  });
};
```

**First Input Delay (FID)**:
```javascript
// performance/fid.js
const measureFID = () => {
  const observer = new PerformanceObserver((list) => {
    list.getEntries().forEach(entry => {
      const fid = entry.processingStart - entry.startTime;
      
      if (fid > 100) {
        console.warn('FID alto:', fid);
        optimizeFID();
      }
    });
  });
  
  observer.observe({ entryTypes: ['first-input'] });
};

const optimizeFID = () => {
  // Dividir tareas largas
  const longTasks = document.querySelectorAll('[data-long-task]');
  longTasks.forEach(task => {
    if ('requestIdleCallback' in window) {
      requestIdleCallback(() => executeTask(task));
    } else {
      setTimeout(() => executeTask(task), 0);
    }
  });
};
```

### 3. Lazy Loading y Code Splitting
**Concepto**: Cargar código solo cuando sea necesario.

**Dynamic Imports**:
```javascript
// lazy-loading.js
const loadComponent = async (componentName) => {
  try {
    const module = await import(`./components/${componentName}.js`);
    return module.default;
  } catch (error) {
    console.error('Error loading component:', error);
    return null;
  }
};

// Uso en React
const LazyComponent = React.lazy(() => import('./HeavyComponent'));

const App = () => (
  <Suspense fallback={<div>Loading...</div>}>
    <LazyComponent />
  </Suspense>
);
```

**Route-based Code Splitting**:
```javascript
// routing/lazy-routes.js
const routes = [
  {
    path: '/',
    component: () => import('./pages/Home'),
    exact: true
  },
  {
    path: '/users',
    component: () => import('./pages/Users'),
    exact: true
  },
  {
    path: '/admin',
    component: () => import('./pages/Admin'),
    exact: true,
    preload: true // Precargar ruta importante
  }
];

const preloadRoute = (route) => {
  if (route.preload) {
    route.component();
  }
};
```

### 4. Optimización de Re-renders
**Concepto**: Minimizar re-renders innecesarios en React.

**React.memo y useMemo**:
```javascript
// optimization/memoization.js
import React, { useMemo, useCallback } from 'react';

const ExpensiveComponent = React.memo(({ data, onUpdate }) => {
  const processedData = useMemo(() => {
    return data.map(item => ({
      ...item,
      processed: item.value * 2
    }));
  }, [data]);

  const handleUpdate = useCallback((id, value) => {
    onUpdate(id, value);
  }, [onUpdate]);

  return (
    <div>
      {processedData.map(item => (
        <div key={item.id}>
          {item.name}: {item.processed}
          <button onClick={() => handleUpdate(item.id, item.processed)}>
            Update
          </button>
        </div>
      ))}
    </div>
  );
});
```

**useCallback Optimization**:
```javascript
// optimization/callbacks.js
const ParentComponent = () => {
  const [count, setCount] = useState(0);
  const [items, setItems] = useState([]);

  const handleItemUpdate = useCallback((id, value) => {
    setItems(prev => prev.map(item => 
      item.id === id ? { ...item, value } : item
    ));
  }, []);

  const handleAddItem = useCallback(() => {
    setItems(prev => [...prev, { id: Date.now(), value: 0 }]);
  }, []);

  return (
    <div>
      <button onClick={() => setCount(c => c + 1)}>
        Count: {count}
      </button>
      <button onClick={handleAddItem}>Add Item</button>
      <ExpensiveComponent 
        data={items} 
        onUpdate={handleItemUpdate} 
      />
    </div>
  );
};
```

### 5. Virtualización
**Concepto**: Renderizar solo elementos visibles para listas grandes.

**Virtual List Implementation**:
```javascript
// virtualization/virtual-list.js
class VirtualList {
  constructor(container, itemHeight, totalItems) {
    this.container = container;
    this.itemHeight = itemHeight;
    this.totalItems = totalItems;
    this.scrollTop = 0;
    this.visibleItems = [];
    
    this.setupContainer();
    this.bindEvents();
  }

  setupContainer() {
    this.container.style.position = 'relative';
    this.container.style.height = `${this.totalItems * this.itemHeight}px`;
    this.container.style.overflow = 'auto';
  }

  bindEvents() {
    this.container.addEventListener('scroll', () => {
      this.scrollTop = this.container.scrollTop;
      this.updateVisibleItems();
    });
  }

  updateVisibleItems() {
    const startIndex = Math.floor(this.scrollTop / this.itemHeight);
    const endIndex = Math.min(
      startIndex + Math.ceil(this.container.clientHeight / this.itemHeight),
      this.totalItems
    );

    this.renderVisibleItems(startIndex, endIndex);
  }

  renderVisibleItems(start, end) {
    // Limpiar items existentes
    this.container.innerHTML = '';
    
    for (let i = start; i < end; i++) {
      const item = this.createItem(i);
      item.style.position = 'absolute';
      item.style.top = `${i * this.itemHeight}px`;
      this.container.appendChild(item);
    }
  }

  createItem(index) {
    const div = document.createElement('div');
    div.textContent = `Item ${index}`;
    div.style.height = `${this.itemHeight}px`;
    return div;
  }
}
```

### 6. Optimización de Imágenes
**Concepto**: Cargar y optimizar imágenes de manera eficiente.

**Lazy Loading de Imágenes**:
```javascript
// images/lazy-loading.js
class ImageLazyLoader {
  constructor() {
    this.images = document.querySelectorAll('img[data-src]');
    this.observer = this.createObserver();
    this.observeImages();
  }

  createObserver() {
    return new IntersectionObserver((entries) => {
      entries.forEach(entry => {
        if (entry.isIntersecting) {
          this.loadImage(entry.target);
          this.observer.unobserve(entry.target);
        }
      });
    }, {
      rootMargin: '50px'
    });
  }

  observeImages() {
    this.images.forEach(img => this.observer.observe(img));
  }

  loadImage(img) {
    const src = img.dataset.src;
    const srcset = img.dataset.srcset;
    
    if (src) img.src = src;
    if (srcset) img.srcset = srcset;
    
    img.classList.remove('lazy');
    img.classList.add('loaded');
  }
}
```

**Responsive Images**:
```javascript
// images/responsive.js
const createResponsiveImage = (container, imageData) => {
  const picture = document.createElement('picture');
  
  // WebP para navegadores modernos
  const webpSource = document.createElement('source');
  webpSource.srcset = imageData.webp;
  webpSource.type = 'image/webp';
  picture.appendChild(webpSource);
  
  // Fallback JPEG
  const img = document.createElement('img');
  img.src = imageData.jpeg;
  img.alt = imageData.alt;
  img.loading = 'lazy';
  
  picture.appendChild(img);
  container.appendChild(picture);
};

// Uso
const imageData = {
  webp: '/images/hero.webp',
  jpeg: '/images/hero.jpg',
  alt: 'Hero image'
};

createResponsiveImage(document.querySelector('.hero'), imageData);
```

### 7. Service Workers y Caching
**Concepto**: Implementar caching inteligente para mejor performance.

**Service Worker Registration**:
```javascript
// sw/registration.js
const registerServiceWorker = async () => {
  if ('serviceWorker' in navigator) {
    try {
      const registration = await navigator.serviceWorker.register('/sw.js');
      console.log('SW registered:', registration);
      
      // Verificar actualizaciones
      registration.addEventListener('updatefound', () => {
        const newWorker = registration.installing;
        newWorker.addEventListener('statechange', () => {
          if (newWorker.state === 'installed' && navigator.serviceWorker.controller) {
            // Nueva versión disponible
            showUpdateNotification();
          }
        });
      });
    } catch (error) {
      console.error('SW registration failed:', error);
    }
  }
};
```

**Caching Strategies**:
```javascript
// sw/caching-strategies.js
const CACHE_NAME = 'app-v1';
const STATIC_CACHE = 'static-v1';
const DYNAMIC_CACHE = 'dynamic-v1';

// Cache First para assets estáticos
const cacheFirst = async (request) => {
  const cachedResponse = await caches.match(request);
  if (cachedResponse) {
    return cachedResponse;
  }
  
  try {
    const networkResponse = await fetch(request);
    const cache = await caches.open(STATIC_CACHE);
    cache.put(request, networkResponse.clone());
    return networkResponse;
  } catch (error) {
    return new Response('Offline content');
  }
};

// Network First para APIs
const networkFirst = async (request) => {
  try {
    const networkResponse = await fetch(request);
    const cache = await caches.open(DYNAMIC_CACHE);
    cache.put(request, networkResponse.clone());
    return networkResponse;
  } catch (error) {
    const cachedResponse = await caches.match(request);
    return cachedResponse || new Response('Offline content');
  }
};
```

### 8. Performance Monitoring
**Concepto**: Monitorear performance en tiempo real.

**Performance Observer**:
```javascript
// monitoring/performance.js
class PerformanceMonitor {
  constructor() {
    this.metrics = {};
    this.setupObservers();
  }

  setupObservers() {
    // Observar métricas de navegación
    const navigationObserver = new PerformanceObserver((list) => {
      list.getEntries().forEach(entry => {
        this.metrics.navigation = {
          loadTime: entry.loadEventEnd - entry.loadEventStart,
          domContentLoaded: entry.domContentLoadedEventEnd - entry.domContentLoadedEventStart,
          firstPaint: entry.responseEnd - entry.fetchStart
        };
      });
    });
    
    navigationObserver.observe({ entryTypes: ['navigation'] });

    // Observar métricas de recursos
    const resourceObserver = new PerformanceObserver((list) => {
      list.getEntries().forEach(entry => {
        if (!this.metrics.resources) this.metrics.resources = [];
        this.metrics.resources.push({
          name: entry.name,
          duration: entry.duration,
          size: entry.transferSize
        });
      });
    });
    
    resourceObserver.observe({ entryTypes: ['resource'] });
  }

  getMetrics() {
    return this.metrics;
  }

  sendMetrics() {
    // Enviar métricas a servicio de analytics
    if (navigator.sendBeacon) {
      navigator.sendBeacon('/api/metrics', JSON.stringify(this.metrics));
    }
  }
}
```

### 9. Bundle Optimization
**Concepto**: Optimizar el tamaño y carga del bundle.

**Tree Shaking**:
```javascript
// optimization/tree-shaking.js
// Solo importar lo que se usa
import { useState, useEffect } from 'react';
// En lugar de: import React from 'react';

// Usar named exports para mejor tree shaking
export const formatDate = (date) => {
  return new Intl.DateTimeFormat('es-ES').format(date);
};

export const formatCurrency = (amount) => {
  return new Intl.NumberFormat('es-ES', {
    style: 'currency',
    currency: 'EUR'
  }).format(amount);
};

// No exportar funciones no utilizadas
// export const unusedFunction = () => {}; // ❌
```

**Dynamic Imports con Preloading**:
```javascript
// optimization/preloading.js
const preloadComponent = (componentPath) => {
  const link = document.createElement('link');
  link.rel = 'prefetch';
  link.href = componentPath;
  document.head.appendChild(link);
};

const preloadCriticalComponents = () => {
  // Precargar componentes críticos
  preloadComponent('/components/Header.js');
  preloadComponent('/components/Navigation.js');
};

// Precargar en idle time
if ('requestIdleCallback' in window) {
  requestIdleCallback(preloadCriticalComponents);
} else {
  setTimeout(preloadCriticalComponents, 1000);
}
```

### 10. Memory Management
**Concepto**: Gestionar memoria eficientemente para evitar leaks.

**Memory Leak Prevention**:
```javascript
// memory/leak-prevention.js
class MemoryManager {
  constructor() {
    this.listeners = new Map();
    this.timers = new Set();
    this.observers = new Set();
  }

  addEventListener(element, event, handler) {
    element.addEventListener(event, handler);
    
    if (!this.listeners.has(element)) {
      this.listeners.set(element, []);
    }
    this.listeners.get(element).push({ event, handler });
  }

  addTimer(timerId) {
    this.timers.add(timerId);
  }

  addObserver(observer) {
    this.observers.add(observer);
  }

  cleanup() {
    // Limpiar event listeners
    this.listeners.forEach((handlers, element) => {
      handlers.forEach(({ event, handler }) => {
        element.removeEventListener(event, handler);
      });
    });
    this.listeners.clear();

    // Limpiar timers
    this.timers.forEach(timerId => clearTimeout(timerId));
    this.timers.clear();

    // Limpiar observers
    this.observers.forEach(observer => observer.disconnect());
    this.observers.clear();
  }
}

// Uso en componentes
const memoryManager = new MemoryManager();

// Agregar listeners
memoryManager.addEventListener(button, 'click', handleClick);
memoryManager.addTimer(setTimeout(() => {}, 1000));
memoryManager.addObserver(new IntersectionObserver(() => {}));

// Limpiar al desmontar
onUnmount(() => {
  memoryManager.cleanup();
});
```

## Ejercicios Prácticos

### Ejercicio 1: Bundle Analysis
Analiza y optimiza el bundle de una aplicación React usando webpack-bundle-analyzer.

### Ejercicio 2: Core Web Vitals Implementation
Implementa métricas de Core Web Vitals en una aplicación web.

### Ejercicio 3: Lazy Loading System
Crea un sistema de lazy loading para componentes y rutas.

### Ejercicio 4: Virtual List
Implementa una lista virtualizada para manejar grandes cantidades de datos.

### Ejercicio 5: Image Optimization
Crea un sistema de optimización de imágenes con lazy loading y formatos modernos.

### Ejercicio 6: Service Worker Caching
Implementa estrategias de caching con Service Workers.

### Ejercicio 7: Performance Monitoring
Crea un sistema de monitoreo de performance en tiempo real.

### Ejercicio 8: Memory Management
Implementa un sistema de gestión de memoria para prevenir leaks.

### Ejercicio 9: Bundle Optimization
Optimiza el bundle de una aplicación usando tree shaking y code splitting.

### Ejercicio 10: Performance Budget
Implementa un sistema de performance budget para mantener métricas óptimas.

## Proyecto Integrador: Aplicación Web Ultra-Optimizada

### Descripción
Desarrolla una aplicación web que implemente todas las técnicas de optimización aprendidas, logrando excelentes métricas de Core Web Vitals.

### Características Requeridas
- **Bundle Optimizado**: Análisis y optimización del bundle
- **Core Web Vitals**: LCP < 2.5s, FID < 100ms, CLS < 0.1
- **Lazy Loading**: Carga diferida de componentes y rutas
- **Virtualización**: Listas virtualizadas para datos grandes
- **Service Worker**: Caching inteligente y offline support
- **Performance Monitoring**: Métricas en tiempo real

### Estructura del Proyecto
```
app-ultra-optimizada/
├── src/
│   ├── components/
│   ├── pages/
│   ├── hooks/
│   ├── utils/
│   └── sw/
├── public/
├── webpack.config.js
├── package.json
└── README.md
```

### Funcionalidades
1. **Dashboard de Performance**: Métricas en tiempo real
2. **Lazy Loading**: Carga diferida de componentes
3. **Virtual Lists**: Manejo eficiente de datos grandes
4. **Image Optimization**: Lazy loading y formatos modernos
5. **Service Worker**: Caching y offline support
6. **Performance Budget**: Alertas cuando se exceden límites

## Evaluación del Módulo

### Criterios de Evaluación
- **Bundle Optimization**: Análisis y optimización efectiva
- **Core Web Vitals**: Implementación de métricas clave
- **Lazy Loading**: Sistema de carga diferida funcional
- **Virtualization**: Listas virtualizadas eficientes
- **Performance Monitoring**: Sistema de monitoreo completo

### Puntos Clave
- Análisis de bundle con herramientas modernas
- Implementación de Core Web Vitals
- Lazy loading y code splitting
- Virtualización para listas grandes
- Gestión de memoria y performance

## Recursos Adicionales

### Documentación
- [Webpack Bundle Analyzer](https://github.com/webpack-contrib/webpack-bundle-analyzer)
- [Core Web Vitals](https://web.dev/vitals/)
- [React Performance](https://reactjs.org/docs/optimizing-performance.html)

### Herramientas
- **Webpack Bundle Analyzer**: Análisis de bundle
- **Lighthouse**: Performance auditing
- **React DevTools Profiler**: Profiling de React
- **Chrome DevTools**: Performance analysis

### Próximos Pasos
- **Módulo 10**: DevOps y Deployment

---

**Nota**: Este módulo establece las bases para optimización profesional de performance en JavaScript, preparando al estudiante para aplicaciones web de alta calidad.

## 🧭 Navegación del Curso

- **⬅️ Anterior**: [Módulo 8: Testing Avanzado y E2E](../senior_2/README.md)
- **➡️ Siguiente**: [Módulo 10: DevOps y Deployment](../senior_4/README.md)
- **📚 [Índice Completo](../INDICE_COMPLETO.md)** | **[🧭 Navegación Rápida](../NAVEGACION_RAPIDA.md)**

---
