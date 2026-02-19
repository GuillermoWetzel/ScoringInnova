# Innova Trials - Plataforma de Evaluación de Sitios de Investigación Clínica

## 📋 Descripción del Proyecto

Innova Trials es una plataforma web diseñada para evaluar y calificar sitios de investigación clínica. Permite a organizaciones de investigación clínica valorar centros de investigación mediante criterios ponderados (infraestructura, personal, sistemas de calidad, etc.), gestionar registros de sitios, generar tokens de evaluación y rastrear la actividad del sistema.

La plataforma cuenta con dos interfaces principales:
- **Panel de Administración**: Para gestionar sitios, configurar preguntas de evaluación, revisar puntuaciones y exportar resultados
- **Portal del Cliente**: Donde los sitios de investigación acceden mediante un token único para completar cuestionarios de autoevaluación

El sistema de puntuación evalúa sitios en múltiples categorías con preguntas ponderadas. Los sitios reciben calificaciones que determinan su estado (Aprobado, Rechazado, A Considerar).

## 🏗️ Arquitectura del Sistema

### Frontend
- **Framework**: React 18 con TypeScript, empaquetado con Vite
- **Enrutamiento**: Wouter (router ligero del lado del cliente)
- **Gestión de Estado**: Zustand con middleware `persist` para estado de autenticación (localStorage)
- **Obtención de Datos**: TanStack React Query para gestión del estado del servidor
- **Componentes UI**: shadcn/ui (estilo New York) construido sobre primitivas Radix UI
- **Estilos**: Tailwind CSS v4 con variables CSS para tematización
- **Gráficos**: Recharts para visualización de puntuaciones y datos
- **Formularios**: React Hook Form con validación Zod vía @hookform/resolvers

### Backend
- **Framework**: Express 5 ejecutándose en Node.js con TypeScript (tsx para desarrollo)
- **Patrón API**: API RESTful JSON bajo prefijo `/api/`
- **Autenticación**: Sistema simple basado en tokens — admins con usuario/contraseña, sitios con email + token único
- **Build**: Script personalizado usando esbuild para servidor y Vite para cliente

### Base de Datos
- **Motor**: PostgreSQL (requerido vía variable de entorno `DATABASE_URL`)
- **ORM**: Drizzle ORM con `drizzle-zod` para integración esquema-validación
- **Ubicación del Esquema**: `shared/schema.ts` — compartido entre cliente y servidor
- **Migraciones**: Drizzle Kit con comando `npm run db:push`
- **Tablas Principales**:
  - `admin_users` — cuentas de administrador
  - `sites` — sitios de investigación con datos de contacto, ubicación, respuestas de evaluación (JSONB), puntuaciones
  - `questions` — preguntas de evaluación con categoría, peso, tipo, flag de descalificación
  - `activity_log` — registro de auditoría de acciones del sistema
  - `chat_logs` — registro de conversaciones del chatbot AI

## 🚀 Stack Tecnológico

### Dependencias Principales
- **Runtime**: Node.js con TypeScript
- **Base de Datos**: PostgreSQL + Drizzle ORM
- **Backend**: Express 5, Passport.js
- **Frontend**: React 18, Wouter, Zustand, TanStack React Query
- **UI**: Radix UI, Tailwind CSS v4, shadcn/ui, Lucide Icons
- **Validación**: Zod
- **Herramientas de Build**: Vite, esbuild, tsx
- **Servicios Externos**:
  - Resend (envío de emails transaccionales)
  - OpenAI (vía Replit AI Integrations para chatbot)

### Herramientas de Desarrollo
- TypeScript 5.6.3
- Drizzle Kit (migraciones de base de datos)
- Plugins de Vite: Replit Cartographer, Dev Banner, Runtime Error Modal

## 📁 Estructura del Proyecto

```
ScoringInnova/
├── client/                    # Aplicación React frontend
│   ├── src/
│   │   ├── components/       # Componentes compartidos y shadcn/ui
│   │   ├── hooks/            # Hooks personalizados de React
│   │   ├── lib/              # Cliente API, store, tipos, utilidades
│   │   └── pages/            # Páginas de rutas
│   │       ├── admin/        # Páginas del panel de administración
│   │       └── site/         # Páginas del portal del cliente
│   ├── index.html
│   └── public/               # Recursos estáticos
├── server/                    # Backend Express
│   ├── index.ts              # Punto de entrada del servidor
│   ├── routes.ts             # Definiciones de rutas API
│   ├── storage.ts            # Capa de almacenamiento de base de datos
│   ├── db.ts                 # Configuración Drizzle + pg pool
│   ├── email.ts              # Servicio de envío de emails
│   ├── report-engine.ts      # Motor de generación de reportes PDF
│   └── replit_integrations/  # Integraciones Replit (chatbot AI)
├── shared/                    # Código compartido cliente/servidor
│   └── schema.ts             # Esquema de base de datos Drizzle
├── migrations/                # Archivos de migración Drizzle
├── script/                    # Scripts de build personalizados
├── package.json
├── vite.config.ts
├── drizzle.config.ts
└── tsconfig.json
```

## ⚙️ Instalación y Configuración

### Prerrequisitos
- Node.js (versión 18 o superior)
- PostgreSQL
- npm o yarn

### Variables de Entorno Requeridas
```bash
DATABASE_URL=postgresql://user:password@localhost:5432/dbname
RESEND_API_KEY=your_resend_api_key  # Opcional, para emails
AI_INTEGRATIONS_OPENAI_API_KEY=xxx  # Auto-gestionado por Replit
AI_INTEGRATIONS_OPENAI_BASE_URL=xxx # Auto-gestionado por Replit
```

### Pasos de Instalación

1. **Clonar el repositorio**
```bash
git clone https://github.com/GuillermoWetzel/ScoringInnova.git
cd ScoringInnova
```

2. **Instalar dependencias**
```bash
npm install
```

3. **Configurar base de datos**
```bash
# Sincronizar esquema con la base de datos
npm run db:push
```

4. **Iniciar en modo desarrollo**
```bash
# Terminal 1: Servidor backend
npm run dev

# Terminal 2: Cliente frontend (en otra terminal)
npm run dev:client
```

5. **Build para producción**
```bash
npm run build
npm start
```

## ✨ Características Principales

### Gestión de Sitios
- Registro y administración de sitios de investigación
- Generación automática de tokens de acceso únicos
- Envío de emails automatizados (tokens, confirmaciones, cambios de estado)
- Seguimiento del flujo de estados: Pending → TokenSent → InProcess → Completed → Approved/Rejected/ToConsider

### Sistema de Evaluación
- Cuestionarios configurables con preguntas categorizadas
- Tipos de preguntas: Sí/No, Texto, Selección múltiple
- Sistema de ponderación por pregunta
- Preguntas "knock-out" (descalificación automática)
- Cálculo automático de puntuaciones

### Panel de Administración
- Dashboard con métricas en tiempo real
- Gestión de sitios (crear, editar, eliminar, cambiar estados)
- Configuración de preguntas de evaluación
- Visualización de puntuaciones con gráficos
- Exportación de resultados a PDF
- Registro de actividad (audit log)
- Visualización de conversaciones del chatbot

### Portal del Cliente
- Acceso mediante email + token único
- Interfaz de evaluación paso a paso
- Guardado automático de progreso
- Visualización de resultados
- Descarga de reporte de evaluación

### Chatbot AI
- Asistente virtual disponible en todas las páginas
- Respuestas en streaming con SSE (Server-Sent Events)
- Persistencia de conversaciones en base de datos
- Panel de administración para revisar logs de chat
- Modelo GPT-4o-mini con contexto de investigación clínica

### Reportes y Analítica
- Generación de reportes PDF con jsPDF
- Visualización de métricas (tiempo promedio de respuesta, tasas de aprobación)
- Gráficos interactivos con Recharts
- Estadísticas por categoría de evaluación

## 🔧 Modificaciones Más Relevantes

### 1. Sistema de Autenticación
- Implementación de autenticación dual: administradores (usuario/contraseña) y sitios (email/token)
- Gestión de estado de sesión con Zustand y persistencia en localStorage
- Tokens únicos generados automáticamente para cada sitio

### 2. Motor de Puntuación
- Sistema de evaluación ponderada con múltiples categorías
- Cálculo automático de puntuaciones basado en respuestas
- Soporte para preguntas "knock-out" que descalifican automáticamente
- Umbrales configurables para determinar aprobación/rechazo

### 3. Integración de Chatbot AI
- Chatbot flotante con integración de OpenAI (vía Replit AI Integrations)
- Streaming de respuestas en tiempo real con SSE
- Persistencia completa de conversaciones con sessionId
- Panel administrativo para análisis de interacciones

### 4. Sistema de Notificaciones por Email
- Integración con Resend para envío de emails transaccionales
- Emails HTML branded y personalizados
- Notificaciones automáticas: generación de tokens, confirmación de evaluación, cambios de estado
- Inicialización lazy con manejo graceful de errores

### 5. Generación de Reportes PDF
- Motor de reportes con jsPDF y jspdf-autotable
- Exportación de evaluaciones completas con puntuaciones detalladas
- Gráficos y tablas incluidos en reportes PDF

### 6. Arquitectura Compartida
- Esquema de base de datos compartido entre frontend y backend (`shared/schema.ts`)
- Type safety completo en toda la aplicación
- Validación con Zod derivada directamente del esquema de Drizzle

### 7. UI/UX Moderna
- Implementación completa de shadcn/ui con componentes Radix UI
- Diseño responsive con Tailwind CSS v4
- Tema personalizable con variables CSS
- Animaciones suaves con Framer Motion
- Iconografía consistente con Lucide React

### 8. Métricas y Analítica
- Dashboard con métricas en tiempo real
- Cálculo de tiempo promedio de respuesta (tokenSentAt → evaluatedAt)
- Visualización de distribución de estados
- Estadísticas por categoría de evaluación

## 📝 Scripts Disponibles

```bash
npm run dev           # Inicia servidor backend en modo desarrollo
npm run dev:client    # Inicia cliente frontend en modo desarrollo
npm run build         # Build de producción (cliente + servidor)
npm start             # Inicia servidor en modo producción
npm run check         # Verifica tipos de TypeScript
npm run db:push       # Sincroniza esquema de base de datos
```

## 🔒 Seguridad

- Validación de datos con Zod en cliente y servidor
- Sanitización de entradas de usuario
- Tokens únicos de acceso de un solo uso
- Registro de auditoría completo (activity_log)
- Variables de entorno para credenciales sensibles

## 📄 Licencia

MIT

## 👥 Contribución

Las contribuciones son bienvenidas. Por favor, abre un issue primero para discutir los cambios que te gustaría realizar.

---

**Desarrollado para optimizar el proceso de evaluación de sitios de investigación clínica** ⚕️
