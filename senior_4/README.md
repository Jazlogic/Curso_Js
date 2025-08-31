# 🎯 Módulo 10: DevOps y Deployment

## 🧭 Navegación del Curso

- **⬅️ Anterior**: [Módulo 9: Testing Avanzado y E2E](../senior_3/README.md)
- **➡️ Siguiente**: [🏠 Página Principal](../README.md)
- **📚 [Índice Completo](../INDICE_COMPLETO.md)** | **[🧭 Navegación Rápida](../NAVEGACION_RAPIDA.md)**

---

# Módulo 10: DevOps y Deployment

## Descripción del Módulo
Este módulo cubre prácticas de DevOps para aplicaciones JavaScript, incluyendo CI/CD con GitHub Actions, Docker, deployment en la nube, monitoreo, observabilidad y estrategias de rollback.

## Objetivos de Aprendizaje
- Implementar pipelines de CI/CD con GitHub Actions
- Containerizar aplicaciones con Docker
- Desplegar en la nube (AWS, Azure, GCP)
- Implementar monitoreo y observabilidad
- Gestionar estrategias de rollback y deployment

## Contenido del Módulo

### 1. CI/CD con GitHub Actions
**Concepto**: Automatizar el proceso de integración y deployment continuo.

**Workflow Básico**:
```yaml
# .github/workflows/ci-cd.yml
name: CI/CD Pipeline

on:
  push:
    branches: [ main, develop ]
  pull_request:
    branches: [ main ]

jobs:
  test:
    runs-on: ubuntu-latest
    
    steps:
    - uses: actions/checkout@v3
    
    - name: Setup Node.js
      uses: actions/setup-node@v3
      with:
        node-version: '18'
        cache: 'npm'
    
    - name: Install dependencies
      run: npm ci
    
    - name: Run tests
      run: npm test
    
    - name: Run linting
      run: npm run lint
    
    - name: Build application
      run: npm run build
    
    - name: Upload build artifacts
      uses: actions/upload-artifact@v3
      with:
        name: build-files
        path: dist/
```

**Deployment Automático**:
```yaml
# .github/workflows/deploy.yml
name: Deploy to Production

on:
  push:
    branches: [ main ]

jobs:
  deploy:
    runs-on: ubuntu-latest
    needs: test
    
    steps:
    - uses: actions/checkout@v3
    
    - name: Download build artifacts
      uses: actions/download-artifact@v3
      with:
        name: build-files
        path: dist/
    
    - name: Deploy to AWS S3
      uses: aws-actions/configure-aws-credentials@v1
      with:
        aws-access-key-id: ${{ secrets.AWS_ACCESS_KEY_ID }}
        aws-secret-access-key: ${{ secrets.AWS_SECRET_ACCESS_KEY }}
        aws-region: us-east-1
    
    - name: Sync to S3
      run: |
        aws s3 sync dist/ s3://my-app-bucket --delete
    
    - name: Invalidate CloudFront
      run: |
        aws cloudfront create-invalidation \
          --distribution-id ${{ secrets.CLOUDFRONT_DISTRIBUTION_ID }} \
          --paths "/*"
```

### 2. Docker y Containerización
**Concepto**: Empaquetar aplicaciones en contenedores para deployment consistente.

**Dockerfile para Aplicación Node.js**:
```dockerfile
# Dockerfile
FROM node:18-alpine AS builder

WORKDIR /app

# Copiar package files
COPY package*.json ./

# Instalar dependencias
RUN npm ci --only=production

# Copiar código fuente
COPY . .

# Construir aplicación
RUN npm run build

# Stage de producción
FROM nginx:alpine

# Copiar archivos construidos
COPY --from=builder /app/dist /usr/share/nginx/html

# Copiar configuración de nginx
COPY nginx.conf /etc/nginx/nginx.conf

# Exponer puerto
EXPOSE 80

# Comando de inicio
CMD ["nginx", "-g", "daemon off;"]
```

**Docker Compose para Desarrollo**:
```yaml
# docker-compose.yml
version: '3.8'

services:
  app:
    build: .
    ports:
      - "3000:80"
    environment:
      - NODE_ENV=development
    volumes:
      - ./src:/app/src
      - ./public:/app/public
    depends_on:
      - db
  
  db:
    image: postgres:15-alpine
    environment:
      POSTGRES_DB: myapp
      POSTGRES_USER: user
      POSTGRES_PASSWORD: password
    ports:
      - "5432:5432"
    volumes:
      - postgres_data:/var/lib/postgresql/data
  
  redis:
    image: redis:7-alpine
    ports:
      - "6379:6379"
    volumes:
      - redis_data:/data

volumes:
  postgres_data:
  redis_data:
```

### 3. Deployment en la Nube
**Concepto**: Desplegar aplicaciones en servicios cloud para escalabilidad.

**AWS S3 + CloudFront**:
```javascript
// scripts/deploy-aws.js
import AWS from 'aws-sdk';
import fs from 'fs';
import path from 'path';

const s3 = new AWS.S3();
const cloudfront = new AWS.CloudFront();

const deployToS3 = async (bucketName, sourcePath) => {
  const files = getAllFiles(sourcePath);
  
  for (const file of files) {
    const relativePath = path.relative(sourcePath, file);
    const fileContent = fs.readFileSync(file);
    
    await s3.upload({
      Bucket: bucketName,
      Key: relativePath,
      Body: fileContent,
      ContentType: getContentType(file),
      CacheControl: getCacheControl(relativePath)
    }).promise();
    
    console.log(`Uploaded: ${relativePath}`);
  }
};

const invalidateCloudFront = async (distributionId) => {
  await cloudfront.createInvalidation({
    DistributionId: distributionId,
    InvalidationBatch: {
      Paths: {
        Quantity: 1,
        Items: ['/*']
      },
      CallerReference: Date.now().toString()
    }
  }).promise();
  
  console.log('CloudFront invalidation created');
};

const getContentType = (file) => {
  const ext = path.extname(file);
  const types = {
    '.html': 'text/html',
    '.css': 'text/css',
    '.js': 'application/javascript',
    '.json': 'application/json',
    '.png': 'image/png',
    '.jpg': 'image/jpeg',
    '.svg': 'image/svg+xml'
  };
  return types[ext] || 'application/octet-stream';
};

const getCacheControl = (file) => {
  if (file.includes('index.html')) {
    return 'no-cache, no-store, must-revalidate';
  }
  if (file.match(/\.(css|js)$/)) {
    return 'public, max-age=31536000, immutable';
  }
  return 'public, max-age=86400';
};
```

**Azure App Service**:
```yaml
# .github/workflows/azure-deploy.yml
name: Deploy to Azure

on:
  push:
    branches: [ main ]

jobs:
  deploy:
    runs-on: ubuntu-latest
    
    steps:
    - uses: actions/checkout@v3
    
    - name: Setup Node.js
      uses: actions/setup-node@v3
      with:
        node-version: '18'
    
    - name: Install dependencies
      run: npm ci
    
    - name: Build application
      run: npm run build
    
    - name: Deploy to Azure
      uses: azure/webapps-deploy@v2
      with:
        app-name: 'my-app'
        publish-profile: ${{ secrets.AZURE_WEBAPP_PUBLISH_PROFILE }}
        package: .
```

### 4. Monitoreo y Observabilidad
**Concepto**: Monitorear aplicaciones en producción para detectar problemas.

**Application Performance Monitoring (APM)**:
```javascript
// monitoring/apm.js
import { performance } from 'perf_hooks';

class APMMonitor {
  constructor() {
    this.metrics = new Map();
    this.traces = [];
    this.setupPerformanceObserver();
  }

  setupPerformanceObserver() {
    if ('PerformanceObserver' in global) {
      const observer = new PerformanceObserver((list) => {
        list.getEntries().forEach(entry => {
          this.recordMetric(entry.name, entry.duration);
        });
      });
      
      observer.observe({ entryTypes: ['measure'] });
    }
  }

  startTrace(name) {
    const trace = {
      name,
      startTime: performance.now(),
      tags: {}
    };
    
    this.traces.push(trace);
    return trace;
  }

  endTrace(trace, tags = {}) {
    trace.endTime = performance.now();
    trace.duration = trace.endTime - trace.startTime;
    trace.tags = { ...trace.tags, ...tags };
    
    this.recordMetric(trace.name, trace.duration);
  }

  recordMetric(name, value) {
    if (!this.metrics.has(name)) {
      this.metrics.set(name, []);
    }
    
    this.metrics.get(name).push({
      value,
      timestamp: Date.now()
    });
  }

  getMetrics() {
    const result = {};
    
    for (const [name, values] of this.metrics) {
      const recent = values.slice(-100); // Últimos 100 valores
      result[name] = {
        count: recent.length,
        average: recent.reduce((sum, v) => sum + v.value, 0) / recent.length,
        min: Math.min(...recent.map(v => v.value)),
        max: Math.max(...recent.map(v => v.value)),
        p95: this.calculatePercentile(recent.map(v => v.value), 95)
      };
    }
    
    return result;
  }

  calculatePercentile(values, percentile) {
    const sorted = values.sort((a, b) => a - b);
    const index = Math.ceil((percentile / 100) * sorted.length) - 1;
    return sorted[index];
  }

  sendMetrics() {
    const metrics = this.getMetrics();
    
    // Enviar a servicio de monitoreo
    fetch('/api/metrics', {
      method: 'POST',
      headers: { 'Content-Type': 'application/json' },
      body: JSON.stringify(metrics)
    }).catch(console.error);
  }
}

export const apm = new APMMonitor();
```

**Health Checks**:
```javascript
// monitoring/health.js
class HealthChecker {
  constructor() {
    this.checks = new Map();
    this.status = 'healthy';
  }

  addCheck(name, checkFunction) {
    this.checks.set(name, checkFunction);
  }

  async runChecks() {
    const results = {};
    let overallStatus = 'healthy';
    
    for (const [name, check] of this.checks) {
      try {
        const result = await check();
        results[name] = {
          status: 'healthy',
          responseTime: result.responseTime,
          details: result.details
        };
      } catch (error) {
        results[name] = {
          status: 'unhealthy',
          error: error.message,
          timestamp: new Date().toISOString()
        };
        overallStatus = 'unhealthy';
      }
    }
    
    this.status = overallStatus;
    return {
      status: overallStatus,
      timestamp: new Date().toISOString(),
      checks: results
    };
  }

  getHealthEndpoint() {
    return async (req, res) => {
      const health = await this.runChecks();
      const statusCode = health.status === 'healthy' ? 200 : 503;
      
      res.status(statusCode).json(health);
    };
  }
}

// Configurar health checks
const healthChecker = new HealthChecker();

healthChecker.addCheck('database', async () => {
  const start = Date.now();
  await db.query('SELECT 1');
  return {
    responseTime: Date.now() - start,
    details: 'Database connection successful'
  };
});

healthChecker.addCheck('redis', async () => {
  const start = Date.now();
  await redis.ping();
  return {
    responseTime: Date.now() - start,
    details: 'Redis connection successful'
  };
});

healthChecker.addCheck('external-api', async () => {
  const start = Date.now();
  const response = await fetch('https://api.external.com/health');
  
  if (!response.ok) {
    throw new Error(`External API returned ${response.status}`);
  }
  
  return {
    responseTime: Date.now() - start,
    details: 'External API is responding'
  };
});
```

### 5. Logging y Tracing
**Concepto**: Implementar logging estructurado y tracing distribuido.

**Structured Logging**:
```javascript
// logging/logger.js
class Logger {
  constructor(options = {}) {
    this.level = options.level || 'info';
    this.service = options.service || 'unknown';
    this.environment = options.environment || 'development';
    
    this.levels = {
      error: 0,
      warn: 1,
      info: 2,
      debug: 3
    };
  }

  log(level, message, meta = {}) {
    if (this.levels[level] <= this.levels[this.level]) {
      const logEntry = {
        timestamp: new Date().toISOString(),
        level: level.toUpperCase(),
        message,
        service: this.service,
        environment: this.environment,
        ...meta
      };
      
      if (this.environment === 'production') {
        console.log(JSON.stringify(logEntry));
      } else {
        console.log(`[${logEntry.timestamp}] ${logEntry.level}: ${logEntry.message}`, meta);
      }
    }
  }

  error(message, meta = {}) {
    this.log('error', message, meta);
  }

  warn(message, meta = {}) {
    this.log('warn', message, meta);
  }

  info(message, meta = {}) {
    this.log('info', message, meta);
  }

  debug(message, meta = {}) {
    this.log('debug', message, meta);
  }

  // Logging de requests HTTP
  logRequest(req, res, next) {
    const start = Date.now();
    
    res.on('finish', () => {
      const duration = Date.now() - start;
      
      this.info('HTTP Request', {
        method: req.method,
        url: req.url,
        statusCode: res.statusCode,
        duration,
        userAgent: req.get('User-Agent'),
        ip: req.ip
      });
    });
    
    next();
  }
}

export const logger = new Logger({
  service: 'my-app',
  environment: process.env.NODE_ENV
});
```

**Distributed Tracing**:
```javascript
// tracing/tracer.js
class Tracer {
  constructor() {
    this.traces = new Map();
  }

  startSpan(name, parentId = null) {
    const spanId = this.generateId();
    const traceId = parentId || this.generateId();
    
    const span = {
      id: spanId,
      traceId,
      parentId,
      name,
      startTime: Date.now(),
      tags: {},
      logs: []
    };
    
    this.traces.set(spanId, span);
    return spanId;
  }

  addTag(spanId, key, value) {
    const span = this.traces.get(spanId);
    if (span) {
      span.tags[key] = value;
    }
  }

  addLog(spanId, message, data = {}) {
    const span = this.traces.get(spanId);
    if (span) {
      span.logs.push({
        timestamp: Date.now(),
        message,
        data
      });
    }
  }

  endSpan(spanId) {
    const span = this.traces.get(spanId);
    if (span) {
      span.endTime = Date.now();
      span.duration = span.endTime - span.startTime;
      
      // Enviar span a sistema de tracing
      this.sendSpan(span);
      
      this.traces.delete(spanId);
    }
  }

  generateId() {
    return Math.random().toString(36).substr(2, 9);
  }

  sendSpan(span) {
    // Enviar a sistema de tracing (Jaeger, Zipkin, etc.)
    fetch('/api/traces', {
      method: 'POST',
      headers: { 'Content-Type': 'application/json' },
      body: JSON.stringify(span)
    }).catch(console.error);
  }
}

export const tracer = new Tracer();
```

### 6. Estrategias de Rollback
**Concepto**: Implementar estrategias para revertir deployments problemáticos.

**Blue-Green Deployment**:
```javascript
// deployment/blue-green.js
class BlueGreenDeployment {
  constructor(config) {
    this.config = config;
    this.currentEnvironment = 'blue';
    this.healthCheckUrl = config.healthCheckUrl;
    this.rollbackThreshold = config.rollbackThreshold || 3;
    this.failureCount = 0;
  }

  async deploy(newVersion) {
    try {
      // 1. Deploy a ambiente inactivo
      const targetEnv = this.currentEnvironment === 'blue' ? 'green' : 'blue';
      await this.deployToEnvironment(targetEnv, newVersion);
      
      // 2. Health checks
      const isHealthy = await this.runHealthChecks(targetEnv);
      
      if (isHealthy) {
        // 3. Switch traffic
        await this.switchTraffic(targetEnv);
        this.currentEnvironment = targetEnv;
        this.failureCount = 0;
        
        console.log(`Successfully switched to ${targetEnv} environment`);
      } else {
        // 4. Rollback automático
        await this.rollback(targetEnv);
        throw new Error('Health checks failed, rolled back');
      }
      
    } catch (error) {
      console.error('Deployment failed:', error);
      await this.rollback(this.currentEnvironment);
      throw error;
    }
  }

  async deployToEnvironment(environment, version) {
    console.log(`Deploying version ${version} to ${environment} environment`);
    
    // Implementar lógica de deployment específica
    if (environment === 'blue') {
      await this.deployToBlue(version);
    } else {
      await this.deployToGreen(version);
    }
  }

  async runHealthChecks(environment) {
    const healthUrl = this.getHealthUrl(environment);
    let healthyChecks = 0;
    const totalChecks = 5;
    
    for (let i = 0; i < totalChecks; i++) {
      try {
        const response = await fetch(healthUrl);
        if (response.ok) {
          healthyChecks++;
        }
      } catch (error) {
        console.error(`Health check ${i + 1} failed:`, error);
      }
      
      // Esperar entre checks
      await new Promise(resolve => setTimeout(resolve, 2000));
    }
    
    return healthyChecks >= totalChecks * 0.8; // 80% de éxito
  }

  async switchTraffic(environment) {
    console.log(`Switching traffic to ${environment} environment`);
    
    // Implementar switch de tráfico (Load Balancer, DNS, etc.)
    if (this.config.loadBalancer) {
      await this.updateLoadBalancer(environment);
    }
    
    if (this.config.dns) {
      await this.updateDNS(environment);
    }
  }

  async rollback(environment) {
    console.log(`Rolling back ${environment} environment`);
    
    // Revertir a versión anterior
    const previousVersion = await this.getPreviousVersion();
    await this.deployToEnvironment(environment, previousVersion);
    
    // Switch traffic de vuelta si es necesario
    if (environment === this.currentEnvironment) {
      const otherEnv = environment === 'blue' ? 'green' : 'blue';
      await this.switchTraffic(otherEnv);
      this.currentEnvironment = otherEnv;
    }
  }

  getHealthUrl(environment) {
    return environment === 'blue' 
      ? this.config.blueHealthUrl 
      : this.config.greenHealthUrl;
  }
}
```

### 7. Infrastructure as Code (Terraform)
**Concepto**: Definir infraestructura como código para reproducibilidad.

**Terraform para AWS**:
```hcl
# infrastructure/main.tf
terraform {
  required_version = ">= 1.0"
  required_providers {
    aws = {
      source  = "hashicorp/aws"
      version = "~> 5.0"
    }
  }
}

provider "aws" {
  region = var.aws_region
}

# VPC
resource "aws_vpc" "main" {
  cidr_block           = var.vpc_cidr
  enable_dns_hostnames = true
  enable_dns_support   = true

  tags = {
    Name = "${var.project_name}-vpc"
  }
}

# Subnets públicas
resource "aws_subnet" "public" {
  count             = length(var.public_subnets)
  vpc_id            = aws_vpc.main.id
  cidr_block        = var.public_subnets[count.index]
  availability_zone = var.availability_zones[count.index]

  tags = {
    Name = "${var.project_name}-public-${count.index + 1}"
  }
}

# Application Load Balancer
resource "aws_lb" "main" {
  name               = "${var.project_name}-alb"
  internal           = false
  load_balancer_type = "application"
  security_groups    = [aws_security_group.alb.id]
  subnets            = aws_subnet.public[*].id

  tags = {
    Name = "${var.project_name}-alb"
  }
}

# ECS Cluster
resource "aws_ecs_cluster" "main" {
  name = "${var.project_name}-cluster"

  setting {
    name  = "containerInsights"
    value = "enabled"
  }
}

# ECS Service
resource "aws_ecs_service" "main" {
  name            = "${var.project_name}-service"
  cluster         = aws_ecs_cluster.main.id
  task_definition = aws_ecs_task_definition.main.arn
  desired_count   = var.app_count
  launch_type     = "FARGATE"

  network_configuration {
    security_groups  = [aws_security_group.ecs_tasks.id]
    subnets          = aws_subnet.public[*].id
    assign_public_ip = true
  }

  load_balancer {
    target_group_arn = aws_lb_target_group.main.arn
    container_name   = var.app_name
    container_port   = var.app_port
  }

  depends_on = [aws_lb_listener.main]
}

# CloudWatch Log Group
resource "aws_cloudwatch_log_group" "main" {
  name              = "/ecs/${var.project_name}"
  retention_in_days = 30
}
```

### 8. Monitoring y Alerting
**Concepto**: Configurar alertas automáticas para problemas críticos.

**CloudWatch Alarms**:
```javascript
// monitoring/cloudwatch.js
import AWS from 'aws-sdk';

const cloudwatch = new AWS.CloudWatch();

class CloudWatchMonitor {
  async createAlarm(alarmConfig) {
    const params = {
      AlarmName: alarmConfig.name,
      ComparisonOperator: alarmConfig.operator,
      EvaluationPeriods: alarmConfig.evaluationPeriods || 1,
      MetricName: alarmConfig.metricName,
      Namespace: alarmConfig.namespace,
      Period: alarmConfig.period || 300,
      Statistic: alarmConfig.statistic || 'Average',
      Threshold: alarmConfig.threshold,
      ActionsEnabled: true,
      AlarmActions: alarmConfig.actions || [],
      AlarmDescription: alarmConfig.description
    };

    try {
      await cloudwatch.putMetricAlarm(params).promise();
      console.log(`Alarm ${alarmConfig.name} created successfully`);
    } catch (error) {
      console.error('Error creating alarm:', error);
      throw error;
    }
  }

  async createHighCPUAlarm(instanceId) {
    await this.createAlarm({
      name: `HighCPU-${instanceId}`,
      metricName: 'CPUUtilization',
      namespace: 'AWS/EC2',
      operator: 'GreaterThanThreshold',
      threshold: 80,
      period: 300,
      evaluationPeriods: 2,
      description: 'CPU usage is above 80% for 10 minutes',
      actions: [process.env.SNS_TOPIC_ARN]
    });
  }

  async createHighMemoryAlarm(instanceId) {
    await this.createAlarm({
      name: `HighMemory-${instanceId}`,
      metricName: 'MemoryUtilization',
      namespace: 'AWS/EC2',
      operator: 'GreaterThanThreshold',
      threshold: 85,
      period: 300,
      evaluationPeriods: 2,
      description: 'Memory usage is above 85% for 10 minutes',
      actions: [process.env.SNS_TOPIC_ARN]
    });
  }

  async createErrorRateAlarm(loadBalancerName) {
    await this.createAlarm({
      name: `HighErrorRate-${loadBalancerName}`,
      metricName: 'HTTPCode_ELB_5XX_Count',
      namespace: 'AWS/ApplicationELB',
      operator: 'GreaterThanThreshold',
      threshold: 10,
      period: 300,
      evaluationPeriods: 1,
      description: 'Error rate is above 10 requests per 5 minutes',
      actions: [process.env.SNS_TOPIC_ARN]
    });
  }
}

export const cloudWatchMonitor = new CloudWatchMonitor();
```

### 9. Security y Compliance
**Concepto**: Implementar medidas de seguridad para aplicaciones en producción.

**Security Headers**:
```javascript
// security/headers.js
import helmet from 'helmet';

const securityHeaders = helmet({
  contentSecurityPolicy: {
    directives: {
      defaultSrc: ["'self'"],
      styleSrc: ["'self'", "'unsafe-inline'", "https://fonts.googleapis.com"],
      fontSrc: ["'self'", "https://fonts.gstatic.com"],
      imgSrc: ["'self'", "data:", "https:"],
      scriptSrc: ["'self'"],
      connectSrc: ["'self'", "https://api.external.com"]
    }
  },
  hsts: {
    maxAge: 31536000,
    includeSubDomains: true,
    preload: true
  },
  noSniff: true,
  referrerPolicy: { policy: "strict-origin-when-cross-origin" },
  frameguard: { action: "deny" },
  xssFilter: true
});

export default securityHeaders;
```

**Rate Limiting**:
```javascript
// security/rate-limiting.js
import rateLimit from 'express-rate-limit';

const createRateLimiter = (options = {}) => {
  return rateLimit({
    windowMs: options.windowMs || 15 * 60 * 1000, // 15 minutos
    max: options.max || 100, // máximo 100 requests por ventana
    message: {
      error: 'Too many requests from this IP, please try again later.'
    },
    standardHeaders: true,
    legacyHeaders: false,
    handler: (req, res) => {
      res.status(429).json({
        error: 'Rate limit exceeded',
        retryAfter: Math.ceil(options.windowMs / 1000)
      });
    }
  });
};

// Rate limiters específicos
export const generalLimiter = createRateLimiter();
export const authLimiter = createRateLimiter({
  windowMs: 15 * 60 * 1000, // 15 minutos
  max: 5 // máximo 5 intentos de login
});
export const apiLimiter = createRateLimiter({
  windowMs: 60 * 1000, // 1 minuto
  max: 30 // máximo 30 requests por minuto
});
```

### 10. Disaster Recovery
**Concepto**: Planificar y implementar estrategias de recuperación ante desastres.

**Backup Strategy**:
```javascript
// recovery/backup.js
class BackupManager {
  constructor(config) {
    this.config = config;
    this.s3 = new AWS.S3();
  }

  async createBackup() {
    const timestamp = new Date().toISOString().replace(/[:.]/g, '-');
    const backupName = `backup-${timestamp}`;
    
    try {
      // 1. Backup de base de datos
      const dbBackup = await this.backupDatabase();
      
      // 2. Backup de archivos
      const filesBackup = await this.backupFiles();
      
      // 3. Backup de configuración
      const configBackup = await this.backupConfiguration();
      
      // 4. Crear archivo de metadatos
      const metadata = {
        timestamp: new Date().toISOString(),
        version: process.env.APP_VERSION,
        database: dbBackup,
        files: filesBackup,
        configuration: configBackup
      };
      
      // 5. Subir a S3
      await this.uploadToS3(backupName, metadata);
      
      console.log(`Backup ${backupName} created successfully`);
      return backupName;
      
    } catch (error) {
      console.error('Backup failed:', error);
      throw error;
    }
  }

  async restoreBackup(backupName) {
    try {
      console.log(`Starting restoration from backup: ${backupName}`);
      
      // 1. Descargar backup
      const backup = await this.downloadFromS3(backupName);
      
      // 2. Restaurar base de datos
      await this.restoreDatabase(backup.database);
      
      // 3. Restaurar archivos
      await this.restoreFiles(backup.files);
      
      // 4. Restaurar configuración
      await this.restoreConfiguration(backup.configuration);
      
      console.log(`Restoration from ${backupName} completed successfully`);
      
    } catch (error) {
      console.error('Restoration failed:', error);
      throw error;
    }
  }

  async backupDatabase() {
    // Implementar backup de base de datos
    const backupPath = `/tmp/db-backup-${Date.now()}.sql`;
    
    // Ejemplo para PostgreSQL
    const { exec } = require('child_process');
    const command = `pg_dump -h ${this.config.db.host} -U ${this.config.db.user} -d ${this.config.db.name} > ${backupPath}`;
    
    return new Promise((resolve, reject) => {
      exec(command, (error, stdout, stderr) => {
        if (error) {
          reject(error);
        } else {
          resolve(backupPath);
        }
      });
    });
  }

  async uploadToS3(key, data) {
    const params = {
      Bucket: this.config.backup.bucket,
      Key: key,
      Body: JSON.stringify(data, null, 2),
      ContentType: 'application/json'
    };
    
    await this.s3.upload(params).promise();
  }

  async downloadFromS3(key) {
    const params = {
      Bucket: this.config.backup.bucket,
      Key: key
    };
    
    const response = await this.s3.getObject(params).promise();
    return JSON.parse(response.Body.toString());
  }
}
```

## Ejercicios Prácticos

### Ejercicio 1: CI/CD Pipeline
Implementa un pipeline completo de CI/CD con GitHub Actions para una aplicación JavaScript.

### Ejercicio 2: Docker Containerization
Containeriza una aplicación Node.js con Docker y Docker Compose.

### Ejercicio 3: Cloud Deployment
Despliega una aplicación en AWS S3 + CloudFront o Azure App Service.

### Ejercicio 4: Infrastructure as Code
Define infraestructura completa con Terraform para una aplicación web.

### Ejercicio 5: Monitoring Setup
Configura monitoreo y alertas con CloudWatch o similar.

### Ejercicio 6: Logging Implementation
Implementa logging estructurado y distributed tracing.

### Ejercicio 7: Security Implementation
Implementa medidas de seguridad (headers, rate limiting, etc.).

### Ejercicio 8: Health Checks
Crea un sistema completo de health checks para la aplicación.

### Ejercicio 9: Backup Strategy
Implementa una estrategia de backup y disaster recovery.

### Ejercicio 10: Blue-Green Deployment
Implementa estrategia de blue-green deployment con rollback automático.

## Proyecto Integrador: Pipeline Completo de CI/CD

### Descripción
Desarrolla un pipeline completo de CI/CD que incluya testing, building, deployment, monitoring y rollback automático.

### Características Requeridas
- **CI/CD Pipeline**: GitHub Actions completo
- **Containerización**: Docker y Docker Compose
- **Cloud Deployment**: AWS, Azure o GCP
- **Monitoring**: APM, logging y alertas
- **Security**: Headers, rate limiting, health checks
- **Disaster Recovery**: Backup y rollback automático

### Estructura del Proyecto
```
pipeline-cicd-completo/
├── .github/
│   └── workflows/
├── infrastructure/
├── docker/
├── scripts/
├── monitoring/
├── security/
├── package.json
└── README.md
```

### Funcionalidades
1. **CI/CD Pipeline**: Testing, building y deployment automático
2. **Containerización**: Docker para desarrollo y producción
3. **Infrastructure as Code**: Terraform para infraestructura
4. **Monitoring**: APM, logging y alertas en tiempo real
5. **Security**: Implementación de medidas de seguridad
6. **Disaster Recovery**: Backup automático y rollback

## Evaluación del Módulo

### Criterios de Evaluación
- **CI/CD**: Pipeline completo y funcional
- **Containerización**: Docker implementado correctamente
- **Cloud Deployment**: Deployment exitoso en la nube
- **Monitoring**: Sistema de monitoreo operativo
- **Security**: Medidas de seguridad implementadas

### Puntos Clave
- CI/CD con GitHub Actions
- Containerización con Docker
- Deployment en la nube
- Monitoring y observabilidad
- Security y disaster recovery

## Recursos Adicionales

### Documentación
- [GitHub Actions](https://docs.github.com/en/actions)
- [Docker Documentation](https://docs.docker.com/)
- [Terraform Documentation](https://www.terraform.io/docs)
- [AWS Documentation](https://docs.aws.amazon.com/)

### Herramientas
- **GitHub Actions**: CI/CD
- **Docker**: Containerización
- **Terraform**: Infrastructure as Code
- **AWS/Azure/GCP**: Cloud platforms
- **CloudWatch**: Monitoring

---

**Nota**: Este módulo establece las bases para DevOps profesional en JavaScript, preparando al estudiante para deployment y operaciones en producción.

## 🧭 Navegación del Curso

- **⬅️ Anterior**: [Módulo 9: Performance y Optimización](../senior_3/README.md)
- **➡️ Siguiente**: [🏠 Página Principal](../README.md)
- **📚 [Índice Completo](../INDICE_COMPLETO.md)** | **[🧭 Navegación Rápida](../NAVEGACION_RAPIDA.md)**

---
