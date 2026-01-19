# Guía de GitHub Actions - Pipelines CI/CD

Esta guía te ayudará a crear y configurar pipelines de CI/CD usando GitHub Actions para proyectos [Astro](https://astro.build/) con TypeScript.

## Tabla de Contenidos

1. [Introducción a GitHub Actions](#introducción-a-github-actions)
2. [Instalación de Astro](#instalación-de-astro)
3. [Conceptos Básicos](#conceptos-básicos)
4. [Estructura de un Workflow](#estructura-de-un-workflow)
5. [Crear tu Primer Pipeline](#crear-tu-primer-pipeline)
6. [Workflows Comunes](#workflows-comunes)
7. [Integración con Vercel](#integración-con-vercel)
8. [Testing y Linting](#testing-y-linting)
9. [Variables de Entorno y Secretos](#variables-de-entorno-y-secretos)
10. [Matrices y Estrategias](#matrices-y-estrategias)
11. [Mejores Prácticas](#mejores-prácticas)
12. [Solución de Problemas](#solución-de-problemas)

---

## Introducción a GitHub Actions

GitHub Actions es una plataforma de CI/CD integrada en GitHub que te permite automatizar tareas como:
- Ejecutar tests automáticamente
- Hacer builds de tu aplicación
- Desplegar a producción
- Ejecutar linting y formateo
- Crear releases automáticos

### Ventajas

- ✅ Integrado directamente con GitHub
- ✅ Gratis para repositorios públicos
- ✅ 2000 minutos/mes gratis para repositorios privados
- ✅ Fácil de configurar con archivos YAML
- ✅ Gran ecosistema de acciones pre-construidas

### Sobre Astro

[Astro](https://astro.build/) es un framework web JavaScript optimizado para construir sitios web rápidos y basados en contenido. Características relevantes para CI/CD:

- **Server-First**: Renderiza componentes en el servidor, generando HTML estático por defecto
- **Build Output**: Genera archivos estáticos en la carpeta `dist/` (no `.next/` como Next.js)
- **Zero JavaScript por defecto**: Solo envía el JavaScript necesario, optimizando el tamaño del build
- **Soporte multi-framework**: Puede usar componentes de React, Vue, Svelte, etc.
- **Content Collections**: Sistema de gestión de contenido con validación TypeScript
- **Deployment Adapters**: Soporte oficial para Vercel, Netlify, Cloudflare, AWS y más

---

## Instalación de Astro

### Prerrequisitos

Antes de instalar Astro, asegúrate de tener:

- **Node.js**: Versión 18.14.1 o superior (recomendado: 20.x o superior)
- **npm**: Versión 9 o superior (viene incluido con Node.js)
- **Git**: Para control de versiones

Verifica tus versiones:

```bash
node --version  # Debe ser >= 18.14.1
npm --version   # Debe ser >= 9.0.0
```

### Método 1: Crear un Nuevo Proyecto Astro (Recomendado)

La forma más fácil de crear un nuevo proyecto Astro es usando el comando `create-astro`:

```bash
npm create astro@latest
```

Este comando te guiará a través de un asistente interactivo que te permitirá:

1. **Elegir el nombre del proyecto**: Se creará una carpeta con ese nombre
2. **Seleccionar una plantilla**: Starter templates, themes, o proyecto vacío
3. **Instalar dependencias**: Automáticamente ejecuta `npm install`
4. **Configurar TypeScript**: Opción para configurar TypeScript
5. **Agregar integraciones**: Opción de agregar integraciones como React, Vue, Tailwind, etc.

#### Opciones Avanzadas del Asistente

```bash
# Crear proyecto con opciones específicas
npm create astro@latest mi-proyecto -- --template minimal --typescript strict --install --git
```

Opciones disponibles:
- `--template <nombre>`: Usar una plantilla específica
- `--typescript <strict|relaxed|strictest>`: Nivel de TypeScript
- `--install`: Instalar dependencias automáticamente
- `--git`: Inicializar repositorio Git
- `--yes`: Usar valores por defecto sin preguntar

### Método 2: Instalación Manual

Si prefieres configurar todo manualmente:

```bash
# Crear directorio del proyecto
mkdir mi-proyecto-astro
cd mi-proyecto-astro

# Inicializar package.json
npm init -y

# Instalar Astro
npm install astro

# Crear estructura básica de carpetas
mkdir -p src/pages src/components src/layouts public

# Crear archivo de configuración básico
touch astro.config.mjs
```

### Estructura de Archivos Inicial

Después de la instalación, tu proyecto tendrá esta estructura:

```
mi-proyecto-astro/
├── public/              # Archivos estáticos (imágenes, fuentes, etc.)
├── src/
│   ├── pages/          # Páginas y rutas de tu sitio
│   │   └── index.astro # Página principal
│   ├── components/     # Componentes reutilizables
│   └── layouts/        # Layouts de página
├── astro.config.mjs    # Configuración de Astro
├── package.json        # Dependencias y scripts
├── tsconfig.json       # Configuración de TypeScript (si se selecciona)
└── .gitignore          # Archivos ignorados por Git
```

### Configuración Inicial

#### 1. Verificar `package.json`

Tu `package.json` debería incluir estos scripts:

```json
{
  "name": "mi-proyecto-astro",
  "type": "module",
  "version": "1.0.0",
  "scripts": {
    "dev": "astro dev",
    "start": "astro dev",
    "build": "astro build",
    "preview": "astro preview",
    "astro": "astro"
  },
  "dependencies": {
    "astro": "^4.0.0"
  }
}
```

#### 2. Configurar `astro.config.mjs`

Archivo de configuración básico:

```javascript
import { defineConfig } from 'astro/config';

// https://astro.build/config
export default defineConfig({
  // Configuración básica
  output: 'static', // 'static' o 'server' para SSR
  site: 'https://ejemplo.com', // URL de tu sitio (opcional)
});
```

#### 3. Crear tu Primera Página

Crea `src/pages/index.astro`:

```astro
---
// Frontmatter (JavaScript/TypeScript)
const title = "Bienvenido a Astro";
---

<html lang="es">
  <head>
    <meta charset="UTF-8" />
    <meta name="viewport" content="width=device-width" />
    <title>{title}</title>
  </head>
  <body>
    <h1>{title}</h1>
    <p>¡Tu sitio Astro está funcionando!</p>
  </body>
</html>
```

### Verificar la Instalación

Ejecuta el servidor de desarrollo para verificar que todo funciona:

```bash
npm run dev
```

Abre tu navegador en `http://localhost:4321` (puerto por defecto de Astro).

### Instalación de Integraciones Comunes

#### Integración con React

```bash
npx astro add react
```

#### Integración con Vue

```bash
npx astro add vue
```

#### Integración con Tailwind CSS

```bash
npx astro add tailwind
```

#### Integración con Vercel (para despliegue)

```bash
npx astro add vercel
```

### Comandos Útiles

```bash
# Desarrollo
npm run dev          # Inicia servidor de desarrollo
npm run build        # Construye para producción
npm run preview      # Previsualiza el build localmente

# Verificación
npx astro check      # Verifica tipos y errores
npx astro sync       # Sincroniza tipos de Content Collections
```

### Solución de Problemas Comunes

#### Error: "Cannot find module 'astro'"

**Solución**: Instala las dependencias:
```bash
npm install
```

#### Error: "Command not found: astro"

**Solución**: Usa `npx` o agrega `node_modules/.bin` a tu PATH:
```bash
npx astro --version
```

#### Error de permisos en npm

**Solución**: Usa `sudo` (macOS/Linux) o ejecuta PowerShell como administrador (Windows):
```bash
sudo npm create astro@latest
```

### Próximos Pasos

Una vez instalado Astro:

1. ✅ Explora la [documentación oficial](https://docs.astro.build/)
2. ✅ Crea tu primera página en `src/pages/`
3. ✅ Agrega componentes en `src/components/`
4. ✅ Configura tu adaptador de despliegue (Vercel, Netlify, etc.)
5. ✅ Configura GitHub Actions para CI/CD (sigue esta guía)

---

## Conceptos Básicos

### Workflow
Un archivo YAML que define un proceso automatizado. Se guarda en `.github/workflows/`

### Job
Una colección de pasos que se ejecutan en el mismo runner. Los jobs pueden ejecutarse en paralelo o secuencialmente.

### Step
Una tarea individual que ejecuta un comando o acción.

### Action
Una aplicación reutilizable que realiza una tarea específica (ej: checkout código, setup Node.js).

### Runner
Una máquina virtual que ejecuta tus workflows. GitHub proporciona runners con Ubuntu, Windows y macOS.

### Event
Un evento específico que dispara el workflow (ej: push, pull_request, workflow_dispatch).

---

## Estructura de un Workflow

Un archivo de workflow básico tiene esta estructura:

```yaml
name: Nombre del Workflow

on:
  # Eventos que disparan el workflow
  push:
    branches: [ main ]
  pull_request:
    branches: [ main ]

jobs:
  nombre-del-job:
    runs-on: ubuntu-latest
    
    steps:
      - name: Paso 1
        uses: actions/checkout@v3
      
      - name: Paso 2
        run: echo "Hola mundo"
```

### Componentes Principales

1. **`name`**: Nombre descriptivo del workflow
2. **`on`**: Eventos que activan el workflow
3. **`jobs`**: Trabajos que se ejecutan
4. **`runs-on`**: Sistema operativo del runner
5. **`steps`**: Pasos individuales del job

---

## Crear tu Primer Pipeline

### Paso 1: Crear el Directorio

Crea la carpeta `.github/workflows` en la raíz de tu proyecto:

```bash
mkdir -p .github/workflows
```

### Paso 2: Crear tu Primer Workflow

Crea un archivo llamado `ci.yml` en `.github/workflows/`:

```yaml
name: CI Pipeline

on:
  push:
    branches: [ main, develop ]
  pull_request:
    branches: [ main, develop ]

jobs:
  build:
    name: Build and Test
    runs-on: ubuntu-latest
    
    steps:
      - name: Checkout código
        uses: actions/checkout@v4
      
      - name: Setup Node.js
        uses: actions/setup-node@v4
        with:
          node-version: '20'
          cache: 'npm'
      
      - name: Instalar dependencias
        run: npm ci
      
      - name: Ejecutar linter
        run: npm run lint
      
      - name: Verificar tipos TypeScript
        run: npx astro check
      
      - name: Ejecutar tests
        run: npm run test
      
      - name: Build del proyecto
        run: npm run build
```

> **Nota**: Astro incluye el comando `astro check` que verifica tipos TypeScript, errores de build y problemas de accesibilidad. Es recomendable incluirlo en tu pipeline.

### Paso 3: Commit y Push

```bash
git add .github/workflows/ci.yml
git commit -m "Add CI pipeline"
git push
```

### Paso 4: Verificar

Ve a tu repositorio en GitHub → pestaña **Actions** → verás tu workflow ejecutándose.

---

## Workflows Comunes

### 1. Pipeline Completo para Astro

```yaml
name: Astro CI/CD

on:
  push:
    branches: [ main, develop ]
  pull_request:
    branches: [ main ]

jobs:
  lint-and-test:
    name: Lint and Test
    runs-on: ubuntu-latest
    
    steps:
      - name: Checkout código
        uses: actions/checkout@v4
      
      - name: Setup Node.js
        uses: actions/setup-node@v4
        with:
          node-version: '20'
          cache: 'npm'
      
      - name: Instalar dependencias
        run: npm ci
      
      - name: Ejecutar ESLint
        run: npm run lint
      
      - name: Verificar tipos TypeScript
        run: npx tsc --noEmit
      
      - name: Ejecutar tests
        run: npm run test -- --coverage
      
      - name: Upload coverage
        uses: codecov/codecov-action@v3
        with:
          files: ./coverage/lcov.info
          fail_ci_if_error: false

  build:
    name: Build Application
    runs-on: ubuntu-latest
    needs: lint-and-test
    
    steps:
      - name: Checkout código
        uses: actions/checkout@v4
      
      - name: Setup Node.js
        uses: actions/setup-node@v4
        with:
          node-version: '20'
          cache: 'npm'
      
      - name: Instalar dependencias
        run: npm ci
      
      - name: Build del proyecto
        run: npm run build
        env:
          NODE_ENV: production
      
      - name: Upload build artifacts
        uses: actions/upload-artifact@v3
        with:
          name: build-files
          path: dist
          retention-days: 7
```

### 2. Pipeline con Testing en Múltiples Versiones de Node

```yaml
name: Test Multiple Node Versions

on:
  push:
    branches: [ main ]
  pull_request:
    branches: [ main ]

jobs:
  test:
    name: Test Node ${{ matrix.node-version }}
    runs-on: ubuntu-latest
    
    strategy:
      matrix:
        node-version: [18.x, 20.x, 22.x]
    
    steps:
      - name: Checkout código
        uses: actions/checkout@v4
      
      - name: Setup Node.js ${{ matrix.node-version }}
        uses: actions/setup-node@v4
        with:
          node-version: ${{ matrix.node-version }}
          cache: 'npm'
      
      - name: Instalar dependencias
        run: npm ci
      
      - name: Ejecutar tests
        run: npm test
```

### 3. Pipeline con Deploy Automático

```yaml
name: Build and Deploy

on:
  push:
    branches: [ main ]
  workflow_dispatch:

jobs:
  deploy:
    name: Deploy to Production
    runs-on: ubuntu-latest
    
    steps:
      - name: Checkout código
        uses: actions/checkout@v4
      
      - name: Setup Node.js
        uses: actions/setup-node@v4
        with:
          node-version: '20'
          cache: 'npm'
      
      - name: Instalar dependencias
        run: npm ci
      
      - name: Build
        run: npm run build
        env:
          NODE_ENV: production
      
      - name: Deploy to Vercel
        uses: amondnet/vercel-action@v25
        with:
          vercel-token: ${{ secrets.VERCEL_TOKEN }}
          vercel-org-id: ${{ secrets.VERCEL_ORG_ID }}
          vercel-project-id: ${{ secrets.VERCEL_PROJECT_ID }}
          vercel-args: '--prod'
```

### 4. Pipeline con Notificaciones

```yaml
name: CI with Notifications

on:
  push:
    branches: [ main ]
  pull_request:
    branches: [ main ]

jobs:
  build:
    runs-on: ubuntu-latest
    
    steps:
      - name: Checkout código
        uses: actions/checkout@v4
      
      - name: Setup Node.js
        uses: actions/setup-node@v4
        with:
          node-version: '20'
          cache: 'npm'
      
      - name: Instalar y build
        run: |
          npm ci
          npm run build
      
      - name: Notificar éxito
        if: success()
        uses: 8398a7/action-slack@v3
        with:
          status: ${{ job.status }}
          text: 'Build exitoso! 🎉'
          webhook_url: ${{ secrets.SLACK_WEBHOOK }}
      
      - name: Notificar fallo
        if: failure()
        uses: 8398a7/action-slack@v3
        with:
          status: ${{ job.status }}
          text: 'Build falló! ❌'
          webhook_url: ${{ secrets.SLACK_WEBHOOK }}
```

---

## Integración con Vercel

Astro tiene soporte oficial para Vercel a través del adaptador `@astrojs/vercel`. Asegúrate de tenerlo configurado en tu `astro.config.mjs`:

```javascript
import { defineConfig } from 'astro/config';
import vercel from '@astrojs/vercel/static'; // o 'serverless' para SSR

export default defineConfig({
  output: 'static', // o 'server' para SSR
  adapter: vercel(),
});
```

### Opción 1: Deploy Automático con Vercel Action

```yaml
name: Deploy to Vercel

on:
  push:
    branches: [ main ]
  pull_request:
    branches: [ main ]

jobs:
  deploy:
    runs-on: ubuntu-latest
    
    steps:
      - name: Checkout código
        uses: actions/checkout@v4
      
      - name: Setup Node.js
        uses: actions/setup-node@v4
        with:
          node-version: '20'
          cache: 'npm'
      
      - name: Instalar dependencias
        run: npm ci
      
      - name: Deploy to Vercel (Preview)
        if: github.event_name == 'pull_request'
        uses: amondnet/vercel-action@v25
        with:
          vercel-token: ${{ secrets.VERCEL_TOKEN }}
          vercel-org-id: ${{ secrets.VERCEL_ORG_ID }}
          vercel-project-id: ${{ secrets.VERCEL_PROJECT_ID }}
          vercel-args: '--yes'
      
      - name: Deploy to Vercel (Production)
        if: github.ref == 'refs/heads/main'
        uses: amondnet/vercel-action@v25
        with:
          vercel-token: ${{ secrets.VERCEL_TOKEN }}
          vercel-org-id: ${{ secrets.VERCEL_ORG_ID }}
          vercel-project-id: ${{ secrets.VERCEL_PROJECT_ID }}
          vercel-args: '--prod --yes'
```

### Opción 2: Usar Vercel CLI

```yaml
name: Deploy with Vercel CLI

on:
  push:
    branches: [ main ]

jobs:
  deploy:
    runs-on: ubuntu-latest
    
    steps:
      - name: Checkout código
        uses: actions/checkout@v4
      
      - name: Setup Node.js
        uses: actions/setup-node@v4
        with:
          node-version: '20'
          cache: 'npm'
      
      - name: Instalar Vercel CLI
        run: npm install --global vercel@latest
      
      - name: Pull Vercel Environment Information
        run: vercel pull --yes --environment=production --token=${{ secrets.VERCEL_TOKEN }}
      
      - name: Build Project Artifacts
        run: vercel build --prod --token=${{ secrets.VERCEL_TOKEN }}
      
      - name: Deploy Project Artifacts to Vercel
        run: vercel deploy --prebuilt --prod --token=${{ secrets.VERCEL_TOKEN }}
```

### Configurar Secrets de Vercel

1. Ve a tu proyecto en Vercel
2. Settings → General → Scroll hasta "Vercel CLI"
3. Copia:
   - **Vercel Token**
   - **Org ID**
   - **Project ID**
4. En GitHub: Settings → Secrets and variables → Actions
5. Agrega los tres secrets

---

## Comandos Específicos de Astro

Astro proporciona varios comandos útiles para CI/CD:

### `astro check`
Verifica tipos TypeScript, errores de build y problemas de accesibilidad. Es el comando más importante para incluir en tu pipeline:

```yaml
- name: Verificar Astro
  run: npx astro check
```

### `astro build`
Construye tu sitio para producción. Genera archivos estáticos en `dist/`:

```yaml
- name: Build Astro
  run: npm run build
  # Equivale a: npx astro build
```

### `astro sync`
Sincroniza tipos de TypeScript para Content Collections (útil si usas Content Collections):

```yaml
- name: Sincronizar tipos
  run: npx astro sync
```

### Estructura de Archivos de Astro

```
proyecto-astro/
├── .github/
│   └── workflows/
│       └── ci.yml
├── src/
│   ├── pages/          # Rutas de la aplicación
│   ├── components/     # Componentes Astro
│   ├── layouts/        # Layouts reutilizables
│   └── content/        # Content Collections (opcional)
├── public/             # Archivos estáticos
├── dist/              # Output del build (generado)
├── astro.config.mjs   # Configuración de Astro
├── package.json
└── tsconfig.json
```

---

## Testing y Linting

### Pipeline Completo con Tests

```yaml
name: Test Suite

on:
  push:
    branches: [ main, develop ]
  pull_request:
    branches: [ main ]

jobs:
  test:
    name: Run Tests
    runs-on: ubuntu-latest
    
    steps:
      - name: Checkout código
        uses: actions/checkout@v4
      
      - name: Setup Node.js
        uses: actions/setup-node@v4
        with:
          node-version: '20'
          cache: 'npm'
      
      - name: Instalar dependencias
        run: npm ci
      
      - name: Ejecutar linter
        run: npm run lint
        continue-on-error: false
      
      - name: Verificar tipos TypeScript y Astro
        run: npx astro check
      
      - name: Verificar tipos TypeScript (opcional)
        run: npx tsc --noEmit
      
      - name: Ejecutar tests unitarios
        run: npm run test:unit
      
      - name: Ejecutar tests de integración
        run: npm run test:integration
        env:
          MONGODB_URI: ${{ secrets.MONGODB_URI_TEST }}
      
      - name: Generar coverage
        run: npm run test:coverage
      
      - name: Upload coverage a Codecov
        uses: codecov/codecov-action@v3
        with:
          file: ./coverage/lcov.info
          flags: unittests
          name: codecov-umbrella
```

### Pipeline con ESLint y Prettier

```yaml
name: Code Quality

on:
  push:
    branches: [ main ]
  pull_request:
    branches: [ main ]

jobs:
  lint:
    name: Lint Code
    runs-on: ubuntu-latest
    
    steps:
      - name: Checkout código
        uses: actions/checkout@v4
      
      - name: Setup Node.js
        uses: actions/setup-node@v4
        with:
          node-version: '20'
          cache: 'npm'
      
      - name: Instalar dependencias
        run: npm ci
      
      - name: Ejecutar ESLint
        run: npm run lint
      
      - name: Verificar formato con Prettier
        run: npx prettier --check "**/*.{ts,tsx,js,jsx,json,md,astro}"
      
      - name: Verificar tipos TypeScript y Astro
        run: npx astro check
      
      - name: Verificar tipos TypeScript (opcional)
        run: npx tsc --noEmit
```

---

## Variables de Entorno y Secretos

### Usar Variables de Entorno

```yaml
jobs:
  build:
    runs-on: ubuntu-latest
    
    env:
      NODE_ENV: production
      API_URL: https://api.ejemplo.com
    
    steps:
      - name: Build
        run: npm run build
        env:
          CUSTOM_VAR: valor-personalizado
```

### Usar Secretos

Los secretos se configuran en: **Settings → Secrets and variables → Actions**

```yaml
jobs:
  deploy:
    runs-on: ubuntu-latest
    
    steps:
      - name: Deploy
        run: echo "Deploying..."
        env:
          API_KEY: ${{ secrets.API_KEY }}
          DATABASE_URL: ${{ secrets.DATABASE_URL }}
          VERCEL_TOKEN: ${{ secrets.VERCEL_TOKEN }}
```

### Secretos por Entorno

```yaml
jobs:
  deploy-staging:
    runs-on: ubuntu-latest
    if: github.ref == 'refs/heads/develop'
    steps:
      - name: Deploy
        env:
          API_URL: ${{ secrets.STAGING_API_URL }}
  
  deploy-production:
    runs-on: ubuntu-latest
    if: github.ref == 'refs/heads/main'
    steps:
      - name: Deploy
        env:
          API_URL: ${{ secrets.PRODUCTION_API_URL }}
```

---

## Matrices y Estrategias

### Testing en Múltiples Entornos

```yaml
jobs:
  test:
    runs-on: ${{ matrix.os }}
    strategy:
      fail-fast: false
      matrix:
        os: [ubuntu-latest, windows-latest, macos-latest]
        node-version: [18.x, 20.x, 22.x]
    
    steps:
      - name: Checkout código
        uses: actions/checkout@v4
      
      - name: Setup Node.js ${{ matrix.node-version }}
        uses: actions/setup-node@v4
        with:
          node-version: ${{ matrix.node-version }}
          cache: 'npm'
      
      - name: Instalar y test
        run: |
          npm ci
          npm test
```

### Build para Múltiples Plataformas

```yaml
jobs:
  build:
    runs-on: ubuntu-latest
    strategy:
      matrix:
        include:
          - os: ubuntu-latest
            target: linux
          - os: windows-latest
            target: win32
          - os: macos-latest
            target: darwin
    
    steps:
      - name: Checkout código
        uses: actions/checkout@v4
      
      - name: Setup Node.js
        uses: actions/setup-node@v4
        with:
          node-version: '20'
      
      - name: Build para ${{ matrix.target }}
        run: npm run build:${{ matrix.target }}
```

---

## Mejores Prácticas

### 1. Usar Versiones Específicas de Actions

❌ **Mal**:
```yaml
uses: actions/checkout@main
```

✅ **Bien**:
```yaml
uses: actions/checkout@v4
```

### 2. Caché de Dependencias

```yaml
- name: Setup Node.js
  uses: actions/setup-node@v4
  with:
    node-version: '20'
    cache: 'npm'  # Cache automático de node_modules
```

### 3. Usar `npm ci` en lugar de `npm install`

```yaml
- name: Instalar dependencias
  run: npm ci  # Más rápido y determinístico
```

### 4. Fail Fast en Matrices

```yaml
strategy:
  fail-fast: true  # Detiene otros jobs si uno falla
  matrix:
    node-version: [18, 20, 22]
```

### 5. Condiciones para Ejecutar Jobs

```yaml
jobs:
  deploy:
    if: github.ref == 'refs/heads/main'  # Solo en main
    runs-on: ubuntu-latest
```

### 6. Timeouts

```yaml
jobs:
  build:
    runs-on: ubuntu-latest
    timeout-minutes: 30  # Evita jobs infinitos
```

### 7. Reutilizar Workflows

Crea workflows reutilizables en `.github/workflows/reusable-workflow.yml`:

```yaml
name: Reusable Build

on:
  workflow_call:
    inputs:
      node-version:
        required: true
        type: string

jobs:
  build:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - uses: actions/setup-node@v4
        with:
          node-version: ${{ inputs.node-version }}
      - run: npm ci
      - run: npm run build
```

Usar el workflow reutilizable:

```yaml
name: Use Reusable Workflow

on:
  push:
    branches: [ main ]

jobs:
  call-workflow:
    uses: ./.github/workflows/reusable-workflow.yml
    with:
      node-version: '20'
```

### 8. Organizar Jobs con Dependencias

```yaml
jobs:
  lint:
    runs-on: ubuntu-latest
    steps:
      - run: npm run lint
  
  test:
    needs: lint  # Espera a que lint termine
    runs-on: ubuntu-latest
    steps:
      - run: npm test
  
  build:
    needs: [lint, test]  # Espera a ambos
    runs-on: ubuntu-latest
    steps:
      - run: npm run build
```

---

## Solución de Problemas

### Problema: Workflow no se ejecuta

**Soluciones**:
1. Verifica que el archivo esté en `.github/workflows/`
2. Verifica la sintaxis YAML (usa un validador)
3. Verifica que el evento esté correctamente configurado
4. Revisa la pestaña Actions en GitHub

### Problema: "Permission denied" o errores de permisos

**Soluciones**:
```yaml
permissions:
  contents: read
  pull-requests: write
  issues: write
```

### Problema: Secrets no disponibles

**Soluciones**:
1. Verifica que los secrets estén configurados en GitHub
2. Verifica el nombre exacto del secret (case-sensitive)
3. Los secrets solo están disponibles en workflows, no en forks

### Problema: Build funciona localmente pero falla en CI

**Soluciones**:
1. Verifica que todas las dependencias estén en `package.json`
2. Usa `npm ci` en lugar de `npm install`
3. Verifica variables de entorno
4. Revisa los logs completos en GitHub Actions

### Problema: Timeout en jobs largos

**Soluciones**:
```yaml
jobs:
  build:
    timeout-minutes: 60  # Aumenta el timeout
```

### Problema: Cache no funciona

**Soluciones**:
1. Verifica que uses `actions/setup-node@v4` con `cache: 'npm'`
2. Limpia el cache si es necesario:
```yaml
- name: Clear npm cache
  run: npm cache clean --force
```

---

## Ejemplo Completo: Pipeline Profesional

```yaml
name: CI/CD Pipeline

on:
  push:
    branches: [ main, develop ]
  pull_request:
    branches: [ main ]
  workflow_dispatch:

env:
  NODE_VERSION: '20'

jobs:
  # Job 1: Linting y verificación de código
  lint:
    name: Lint and Type Check
    runs-on: ubuntu-latest
    
    steps:
      - name: Checkout código
        uses: actions/checkout@v4
      
      - name: Setup Node.js
        uses: actions/setup-node@v4
        with:
          node-version: ${{ env.NODE_VERSION }}
          cache: 'npm'
      
      - name: Instalar dependencias
        run: npm ci
      
      - name: Ejecutar ESLint
        run: npm run lint
      
      - name: Verificar tipos TypeScript y Astro
        run: npx astro check
  
  # Job 2: Testing
  test:
    name: Run Tests
    runs-on: ubuntu-latest
    needs: lint
    
    strategy:
      matrix:
        node-version: [18.x, 20.x, 22.x]
    
    steps:
      - name: Checkout código
        uses: actions/checkout@v4
      
      - name: Setup Node.js ${{ matrix.node-version }}
        uses: actions/setup-node@v4
        with:
          node-version: ${{ matrix.node-version }}
          cache: 'npm'
      
      - name: Instalar dependencias
        run: npm ci
      
      - name: Ejecutar tests
        run: npm test -- --coverage
        env:
          MONGODB_URI: ${{ secrets.MONGODB_URI_TEST }}
      
      - name: Upload coverage
        uses: codecov/codecov-action@v3
        with:
          files: ./coverage/lcov.info
  
  # Job 3: Build
  build:
    name: Build Application
    runs-on: ubuntu-latest
    needs: test
    
    steps:
      - name: Checkout código
        uses: actions/checkout@v4
      
      - name: Setup Node.js
        uses: actions/setup-node@v4
        with:
          node-version: ${{ env.NODE_VERSION }}
          cache: 'npm'
      
      - name: Instalar dependencias
        run: npm ci
      
      - name: Build
        run: npm run build
        env:
          NODE_ENV: production
      
      - name: Upload build artifacts
        uses: actions/upload-artifact@v3
        with:
          name: build-files
          path: dist
          retention-days: 7
  
  # Job 4: Deploy (solo en main)
  deploy:
    name: Deploy to Vercel
    runs-on: ubuntu-latest
    needs: build
    if: github.ref == 'refs/heads/main'
    
    steps:
      - name: Checkout código
        uses: actions/checkout@v4
      
      - name: Setup Node.js
        uses: actions/setup-node@v4
        with:
          node-version: ${{ env.NODE_VERSION }}
          cache: 'npm'
      
      - name: Instalar dependencias
        run: npm ci
      
      - name: Deploy to Vercel
        uses: amondnet/vercel-action@v25
        with:
          vercel-token: ${{ secrets.VERCEL_TOKEN }}
          vercel-org-id: ${{ secrets.VERCEL_ORG_ID }}
          vercel-project-id: ${{ secrets.VERCEL_PROJECT_ID }}
          vercel-args: '--prod'
```

---

## Recursos Adicionales

### GitHub Actions
- [Documentación oficial de GitHub Actions](https://docs.github.com/en/actions)
- [Marketplace de Actions](https://github.com/marketplace?type=actions)
- [Sintaxis YAML para GitHub Actions](https://docs.github.com/en/actions/using-workflows/workflow-syntax-for-github-actions)
- [Mejores prácticas de GitHub Actions](https://docs.github.com/en/actions/using-workflows/workflow-syntax-for-github-actions)

### Astro
- [Documentación oficial de Astro](https://docs.astro.build/)
- [Guía de despliegue de Astro](https://docs.astro.build/en/guides/deploy/)
- [Adaptador de Vercel para Astro](https://docs.astro.build/en/guides/integrations-guide/vercel/)
- [Astro Check - Verificación de tipos y errores](https://docs.astro.build/en/reference/cli-reference/#astro-check)

---

## Checklist para Crear un Pipeline

- [ ] Crear directorio `.github/workflows/`
- [ ] Definir eventos que disparan el workflow
- [ ] Configurar Node.js con caché
- [ ] Agregar paso de linting
- [ ] Agregar paso de verificación con `astro check`
- [ ] Agregar paso de testing
- [ ] Agregar paso de build (`npm run build`)
- [ ] Verificar que el build genera archivos en `dist/`
- [ ] Configurar variables de entorno necesarias
- [ ] Configurar secrets en GitHub (Vercel tokens si aplica)
- [ ] Verificar configuración del adaptador de Vercel en `astro.config.mjs`
- [ ] Probar el workflow localmente (si es posible)
- [ ] Hacer commit y push
- [ ] Verificar ejecución en GitHub Actions
- [ ] Revisar logs si hay errores
- [ ] Optimizar tiempos de ejecución

---

¡Feliz automatización! 🚀
