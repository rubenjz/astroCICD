# Guía de Despliegue en Vercel

Esta guía te ayudará a desplegar aplicaciones Next.js con TypeScript en Vercel.

## Tabla de Contenidos

1. [Prerrequisitos](#prerrequisitos)
2. [Preparación del Proyecto](#preparación-del-proyecto)
3. [Configuración de Vercel](#configuración-de-vercel)
4. [Variables de Entorno](#variables-de-entorno)
5. [Despliegue desde la CLI](#despliegue-desde-la-cli)
6. [Despliegue desde GitHub](#despliegue-desde-github)
7. [Configuración de Base de Datos](#configuración-de-base-de-datos)
8. [Solución de Problemas Comunes](#solución-de-problemas-comunes)
9. [Mejores Prácticas](#mejores-prácticas)

---

## Prerrequisitos

Antes de comenzar, asegúrate de tener:

- Una cuenta en [Vercel](https://vercel.com)
- Node.js instalado (versión 18 o superior)
- Git instalado y configurado
- Tu proyecto Next.js funcionando correctamente en local

---

## Preparación del Proyecto

### 1. Verificar que el proyecto compile correctamente

Antes de desplegar, asegúrate de que tu proyecto compile sin errores:

```bash
npm run build
```

Si hay errores, corrígelos antes de continuar. Vercel ejecutará este comando durante el despliegue.

### 2. Verificar estructura del proyecto

Asegúrate de que tu proyecto tenga la siguiente estructura básica:

```
tu-proyecto/
├── package.json
├── next.config.js (o next.config.ts)
├── tsconfig.json
├── .env.local (para variables locales)
├── .env.example (opcional, para documentar variables)
└── src/ o app/ (dependiendo de tu estructura)
```

### 3. Archivo `.gitignore`

Asegúrate de que tu `.gitignore` incluya:

```
node_modules/
.next/
.env.local
.env*.local
.vercel
dist/
build/
```

---

## Configuración de Vercel

### Opción 1: Despliegue desde la Interfaz Web

1. **Iniciar sesión en Vercel**
   - Ve a [vercel.com](https://vercel.com)
   - Inicia sesión con GitHub, GitLab o Bitbucket

2. **Importar proyecto**
   - Haz clic en "Add New..." → "Project"
   - Conecta tu repositorio de Git
   - Selecciona el proyecto que deseas desplegar

3. **Configurar el proyecto**
   - **Framework Preset**: Next.js (se detecta automáticamente)
   - **Root Directory**: Dejar en blanco (o especificar si tu proyecto está en un subdirectorio)
   - **Build Command**: `npm run build` (por defecto)
   - **Output Directory**: `.next` (por defecto)
   - **Install Command**: `npm install` (por defecto)

4. **Variables de Entorno**
   - Agrega todas las variables necesarias (ver sección siguiente)

5. **Desplegar**
   - Haz clic en "Deploy"
   - Espera a que termine el proceso
   - Tu aplicación estará disponible en una URL como: `tu-proyecto.vercel.app`

---

## Variables de Entorno

### Configurar Variables en Vercel

1. Ve a tu proyecto en Vercel
2. Navega a **Settings** → **Environment Variables**
3. Agrega cada variable con su valor correspondiente

### Variables Comunes para Next.js

```bash
# Base de datos MongoDB (si usas MongoDB Atlas)
MONGODB_URI=mongodb+srv://usuario:password@cluster.mongodb.net/database

# Variables de autenticación
NEXTAUTH_URL=https://tu-proyecto.vercel.app
NEXTAUTH_SECRET=tu-secret-key-aqui

# API Keys
API_KEY=tu-api-key
NEXT_PUBLIC_API_URL=https://api.ejemplo.com

# Otras variables
NODE_ENV=production
```

### Variables Públicas vs Privadas

- **Variables sin `NEXT_PUBLIC_`**: Solo disponibles en el servidor (API routes, Server Components)
- **Variables con `NEXT_PUBLIC_`**: Disponibles tanto en servidor como en cliente (se exponen en el bundle)

⚠️ **Importante**: Nunca expongas secretos o API keys privadas con el prefijo `NEXT_PUBLIC_`

### Configurar Variables por Entorno

Puedes configurar variables diferentes para:
- **Production**: Producción
- **Preview**: Pull requests y branches
- **Development**: Desarrollo local

---

## Despliegue desde la CLI

### 1. Instalar Vercel CLI

```bash
npm install -g vercel
```

### 2. Iniciar sesión

```bash
vercel login
```

### 3. Desplegar

```bash
# Despliegue de preview (para testing)
vercel

# Despliegue a producción
vercel --prod
```

### 4. Configuración inicial

La primera vez que ejecutes `vercel`, te pedirá:
- **Set up and deploy?**: Yes
- **Which scope?**: Selecciona tu cuenta/organización
- **Link to existing project?**: No (primera vez) o Yes (si ya existe)
- **Project name**: Nombre de tu proyecto
- **Directory**: `.` (directorio actual)
- **Override settings?**: No (usa los defaults)

### Comandos Útiles de Vercel CLI

```bash
# Ver información del proyecto
vercel inspect

# Ver logs en tiempo real
vercel logs

# Ver deployments
vercel ls

# Eliminar un deployment
vercel remove [deployment-url]

# Abrir el proyecto en el navegador
vercel open
```

---

## Despliegue desde GitHub

### Configuración Automática

1. **Conectar repositorio**
   - En Vercel, importa tu proyecto desde GitHub
   - Autoriza el acceso a tu repositorio

2. **Configuración automática**
   - Cada push a `main` o `master` → despliega a producción
   - Cada pull request → crea un preview deployment
   - Cada push a otras branches → crea un preview deployment

### Configuración Manual con `vercel.json`

Crea un archivo `vercel.json` en la raíz de tu proyecto:

```json
{
  "buildCommand": "npm run build",
  "devCommand": "npm run dev",
  "installCommand": "npm install",
  "framework": "nextjs",
  "regions": ["iad1"],
  "env": {
    "NODE_ENV": "production"
  },
  "headers": [
    {
      "source": "/api/(.*)",
      "headers": [
        {
          "key": "Access-Control-Allow-Origin",
          "value": "*"
        }
      ]
    }
  ],
  "rewrites": [
    {
      "source": "/api/:path*",
      "destination": "/api/:path*"
    }
  ]
}
```

---

## Configuración de Base de Datos

### MongoDB Atlas (Recomendado para Producción)

Si tu aplicación usa MongoDB y está configurada para usar una base de datos local, necesitarás:

1. **Crear cuenta en MongoDB Atlas**
   - Ve a [mongodb.com/cloud/atlas](https://www.mongodb.com/cloud/atlas)
   - Crea un cluster gratuito

2. **Obtener connection string**
   - En Atlas, ve a "Connect" → "Connect your application"
   - Copia la connection string

3. **Configurar en Vercel**
   - Agrega la variable `MONGODB_URI` en Vercel
   - Actualiza tu código para usar esta variable:

```typescript
// lib/mongodb.ts
const MONGODB_URI = process.env.MONGODB_URI;

if (!MONGODB_URI) {
  throw new Error('Please define the MONGODB_URI environment variable');
}

export async function connectToDatabase() {
  // Tu lógica de conexión aquí
  // Sin mongoose, usando el driver nativo de MongoDB
}
```

### Whitelist de IPs en MongoDB Atlas

- Para desarrollo: Agrega `0.0.0.0/0` (permite todas las IPs)
- Para producción: Agrega las IPs de Vercel o usa `0.0.0.0/0` con autenticación adecuada

---

## Solución de Problemas Comunes

### Error: Build Failed

**Problema**: El build falla en Vercel

**Soluciones**:
1. Verifica que `npm run build` funcione localmente
2. Revisa los logs en Vercel para ver el error específico
3. Verifica que todas las dependencias estén en `package.json`
4. Asegúrate de que no haya errores de TypeScript o ESLint

### Error: Module not found

**Problema**: No se encuentra un módulo

**Soluciones**:
1. Verifica que todas las dependencias estén en `dependencies` (no solo `devDependencies`)
2. Ejecuta `npm install` localmente y verifica que funcione
3. Limpia `.next` y `node_modules` y vuelve a instalar

### Error: Environment Variable Missing

**Problema**: Variable de entorno no encontrada

**Soluciones**:
1. Verifica que todas las variables estén configuradas en Vercel
2. Asegúrate de que las variables estén disponibles para el entorno correcto (Production/Preview)
3. Reinicia el deployment después de agregar variables

### Error: Timeout en Build

**Problema**: El build tarda demasiado

**Soluciones**:
1. Optimiza tu build eliminando dependencias innecesarias
2. Usa `output: 'standalone'` en `next.config.js` para builds más rápidos
3. Considera usar `experimental.outputFileTracingIncludes` para optimizar

### Error: Function exceeded maximum duration

**Problema**: Las funciones serverless exceden el tiempo límite

**Soluciones**:
1. Optimiza tus API routes
2. Considera usar Edge Functions para operaciones más rápidas
3. Implementa caching donde sea posible
4. Considera usar un plan superior si necesitas más tiempo

---

## Mejores Prácticas

### 1. Optimización del Build

```javascript
// next.config.js
/** @type {import('next').NextConfig} */
const nextConfig = {
  // Optimización para producción
  output: 'standalone', // Reduce el tamaño del build
  compress: true,
  poweredByHeader: false,
  
  // Optimización de imágenes
  images: {
    domains: ['ejemplo.com'],
    formats: ['image/avif', 'image/webp'],
  },
  
  // Variables de entorno públicas
  env: {
    CUSTOM_KEY: process.env.CUSTOM_KEY,
  },
};

module.exports = nextConfig;
```

### 2. Monitoreo y Analytics

- Usa Vercel Analytics para monitorear el rendimiento
- Configura alertas para errores
- Revisa los logs regularmente

### 3. Seguridad

- Nunca commits secretos en el código
- Usa variables de entorno para todos los secretos
- Implementa rate limiting en tus API routes
- Usa HTTPS (Vercel lo proporciona automáticamente)

### 4. Performance

- Usa Server Components cuando sea posible
- Implementa caching apropiado
- Optimiza imágenes con `next/image`
- Usa `next/dynamic` para code splitting

### 5. CI/CD

- Configura tests automáticos antes del deploy
- Usa preview deployments para testing
- Implementa checks de calidad de código

### 6. Estructura de Archivos Recomendada

```
proyecto/
├── .env.example          # Template de variables
├── .gitignore
├── next.config.js
├── package.json
├── tsconfig.json
├── vercel.json           # Configuración de Vercel (opcional)
├── src/
│   ├── app/              # App Router (Next.js 13+)
│   ├── components/
│   ├── lib/
│   └── types/
└── public/
```

---

## Comandos de Referencia Rápida

```bash
# Instalar Vercel CLI
npm install -g vercel

# Login
vercel login

# Deploy preview
vercel

# Deploy producción
vercel --prod

# Ver logs
vercel logs

# Ver deployments
vercel ls

# Abrir proyecto
vercel open

# Ver información del proyecto
vercel inspect
```

---

## Recursos Adicionales

- [Documentación oficial de Vercel](https://vercel.com/docs)
- [Guía de Next.js en Vercel](https://vercel.com/docs/frameworks/nextjs)
- [Variables de entorno en Vercel](https://vercel.com/docs/concepts/projects/environment-variables)
- [Vercel CLI Reference](https://vercel.com/docs/cli)

---

## Checklist Pre-Deploy

Antes de desplegar, verifica:

- [ ] `npm run build` ejecuta sin errores
- [ ] Todas las variables de entorno están configuradas
- [ ] La base de datos está accesible desde internet (si aplica)
- [ ] No hay secretos hardcodeados en el código
- [ ] Las rutas API están funcionando correctamente
- [ ] Los tests pasan (si los tienes)
- [ ] El `.gitignore` está configurado correctamente
- [ ] Las imágenes y assets están optimizados
- [ ] El proyecto funciona correctamente en local

---

¡Feliz despliegue! 🚀
