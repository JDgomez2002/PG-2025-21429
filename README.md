# Implementación de infraestructura cloud con políticas de seguridad para un sistema integrador de aplicaciones y modelos de inteligencia artificial de orientación vocacional

**Proyecto de Graduación 2025**  
**Estudiante:** José Daniel Gómez Cabrera  
**Carnet:** 21429  
**Universidad del Valle de Guatemala**

---

## 📋 Descripción del Proyecto

Este proyecto implementa una infraestructura cloud serverless con políticas de seguridad avanzadas para **Mirai**, un sistema integrador de aplicaciones y modelos de inteligencia artificial diseñado para la orientación vocacional de estudiantes en Guatemala y Latinoamérica.

El sistema proporciona una plataforma completa que permite a los estudiantes explorar carreras, interactuar con contenido educativo, participar en foros de discusión, realizar evaluaciones vocacionales y recibir recomendaciones personalizadas basadas en inteligencia artificial.

## 🏗️ Arquitectura

### Infraestructura AWS

El sistema está desplegado en **AWS** utilizando una arquitectura serverless y microservicios dentro de una **VPC (Virtual Private Cloud)** en la región **us-east-2 (Ohio)**.

#### Componentes Principales

1. **Edge y Frontend Services**

   - **Web Application Firewall (WAF)**: Protección contra amenazas web
   - **CloudFront CDN**: Distribución global de contenido
   - **API Gateway**: Punto de entrada para todas las peticiones API

2. **Capa de Autorización**

   - **Lambda Authorizer (JWT)**: Validación de tokens JWT para autenticación de usuarios
   - **Webhook Authorizer (Signed)**: Validación de webhooks firmados desde Clerk

3. **Backend Serverless (VPC - Private Subnets)**

   - **36 Lambda Functions** distribuidas en:
     - **Lambda Functions**: Endpoints principales (`/users`, `/careers`, `/explore`, `/cards`, `/feed`, `/interactions`, `/testimonies`, `/forums`, `/comments`, `/answers`)
     - **Auth Lambda Functions**: Operaciones de autenticación (`/create`, `/update`, `/delete`)
   - **Security Groups**: Control de acceso a nivel de red

4. **Microservicio de Recomendaciones**

   - **EC2 Instance**: Microservicio dedicado para recomendaciones basadas en IA
   - **VPC Link**: Conexión privada desde API Gateway
   - Endpoints: `/recommendations` y `/proxy+`

5. **Base de Datos**

   - **DocumentDB (MongoDB)**: Base de datos NoSQL para almacenamiento de datos
   - **Security Groups**: Aislamiento de red para la base de datos

6. **Autenticación Externa**
   - **Clerk**: Servicio de autenticación y gestión de usuarios
   - Integración mediante webhooks firmados

### Características de Seguridad

- **Encriptación en Reposo (AES-256-CBC)**: Datos personales (PII) encriptados antes de almacenarse en MongoDB
- **Encriptación en Tránsito (AES-256-GCM)**: Cifrado adicional para datos sensibles en tráfico de red
- **VPC con Subnets Privadas**: Aislamiento de recursos backend
- **Security Groups**: Control granular de acceso a nivel de red
- **WAF**: Protección contra ataques comunes (OWASP Top 10)
- **JWT Validation**: Autenticación basada en tokens

## 🚀 Funcionalidades

### 🔐 Autenticación y Gestión de Usuarios

- **Registro de Usuarios** (`/auth/register`)

  - Creación de cuentas con integración Clerk
  - Validación de datos y prevención de duplicados
  - Encriptación de datos personales (PII)

- **Actualización de Usuario** (`/auth/updateUser`)

  - Modificación de información de perfil
  - Encriptación automática de campos sensibles

- **Eliminación de Usuario** (`/auth/delete`)

  - Eliminación segura de cuentas de usuario

- **Obtener Usuario** (`/users/getUser`)

  - Consulta de perfil de usuario con desencriptación automática

- **Obtener Usuarios** (`/users/getUsers`)

  - Listado de usuarios con filtros y paginación

- **Editar Usuario** (`/users/editUser`)
  - Edición de información de usuario

### 🎓 Carreras

- **Obtener Carreras** (`/careers/getCareers`)

  - Listado de carreras disponibles
  - Filtros por facultad, duración y nombre
  - Proyección optimizada de campos

- **Obtener Carrera** (`/careers/getCareer`)

  - Detalles completos de una carrera específica
  - Información de insights y estadísticas

- **Editar Insights de Carrera** (`/careers/editCareerInsights`)
  - Actualización de información y estadísticas de carreras

### 🔍 Exploración de Contenido

- **Feed de Contenido** (`/explore/getFeed`)

  - Feed paginado de contenido
  - Filtrado por tipos: `career`, `alumni_story`, `what_if`, `short_question`
  - Ordenamiento basado en prioridad

- **Gestión de Tarjetas (Cards)**

  - **Crear Tarjeta** (`/explore/cards/newCard`): Creación de contenido con metadatos
  - **Obtener Tarjeta** (`/explore/cards/getCard`): Consulta de tarjeta por ID
  - **Editar Tarjeta** (`/explore/cards/editCard`): Modificación de contenido
  - **Eliminar Tarjeta** (`/explore/cards/deleteCard`): Eliminación de contenido

- **Testimonios** (`/explore/getTestimonies`)

  - Obtención de testimonios de estudiantes y egresados

- **Interacciones**

  - **Nueva Interacción** (`/explore/interactions/newInteraction`): Registro de interacciones (`view`, `tap`, `save`, `share`)
  - **Obtener Interacciones** (`/explore/interactions/getInteractions`): Consulta de historial de interacciones
  - Tracking de duración y metadatos

- **Likes** (`/explore/likes/handleLikes`)
  - Gestión de likes en contenido

### 💬 Foros y Comentarios

- **Foros**

  - **Crear Foro** (`/forums/newForum`): Creación de nuevos foros de discusión
  - **Obtener Foros** (`/forums/getForums`): Listado de foros con desencriptación
  - **Obtener Foro** (`/forums/getForum`): Detalles de un foro específico
  - **Editar Foro** (`/forums/editForum`): Modificación de foros
  - **Eliminar Foro** (`/forums/deleteForum`): Eliminación de foros

- **Comentarios**

  - **Nuevo Comentario** (`/forums/comments/newComment`): Creación de comentarios
  - **Editar Comentario** (`/forums/comments/editComment`): Modificación de comentarios
  - **Eliminar Comentario** (`/forums/comments/deleteComment`): Eliminación de comentarios

- **Respuestas a Comentarios**
  - **Nueva Respuesta** (`/forums/comments/answers/newAnswer`): Respuestas a comentarios
  - **Editar Respuesta** (`/forums/comments/answers/editAnswer`): Modificación de respuestas
  - **Eliminar Respuesta** (`/forums/comments/answers/deleteAnswer`): Eliminación de respuestas

### 📊 Cuestionarios y Resultados

- **Resultados de Quiz** (`/quiz/results/getResults`)

  - Obtención de resultados de evaluaciones vocacionales
  - Análisis de respuestas y recomendaciones

- **Eliminar Resultados** (`/quiz/results/deleteResults`)
  - Eliminación de resultados de quiz

### 📈 Analíticas

- **Obtener Analíticas** (`/analytics/getAnalytics`)
  - Estadísticas de estudiantes
  - Análisis de completitud de cuestionarios
  - Métricas de crecimiento mensual
  - Estadísticas de resultados de quiz

### 💾 Contenido Guardado

- **Guardar Item** (`/users/saved/saveItem`): Guardar contenido para lectura posterior
- **Obtener Items Guardados** (`/users/saved/getSavedItems`): Consulta de contenido guardado
- **Desguardar Item** (`/users/saved/unsaveItem`): Eliminar de contenido guardado

## 🛠️ Stack Tecnológico

### Backend

- **Runtime**: Node.js (ES Modules)
- **Base de Datos**: MongoDB (DocumentDB en AWS)
- **ODM**: Mongoose
- **Autenticación**: JWT con Clerk
- **Arquitectura**: Serverless (AWS Lambda)

### Infraestructura

- **Cloud Provider**: AWS (con soporte multi-cloud)
- **Compute**: AWS Lambda (36 funciones)
- **API Gateway**: AWS API Gateway
- **CDN**: CloudFront
- **WAF**: AWS WAF
- **Networking**: VPC con subnets privadas
- **Base de Datos**: AWS DocumentDB
- **Microservicios**: EC2 para recomendaciones
- **IaC**: Terraform (soporte para AWS, Azure, GCP)

### Seguridad

- **Encriptación en Reposo**: AES-256-CBC
- **Encriptación en Tránsito**: AES-256-GCM (TLS 1.3 style)
- **Autenticación**: JWT tokens con validación de clave pública
- **Webhooks**: Firma criptográfica para webhooks de Clerk

### DevOps

- **Infrastructure as Code**: Terraform
- **Testing**: K6 para pruebas de rendimiento
- **Monitoreo**: CloudWatch Logs

## 📁 Estructura del Proyecto

```
PG-2025-21429/
├── src/
│   ├── features/              # Funciones Lambda por funcionalidad
│   │   ├── analytics/         # Analíticas y estadísticas
│   │   ├── auth/              # Autenticación
│   │   ├── careers/           # Gestión de carreras
│   │   ├── explore/           # Exploración de contenido
│   │   ├── forums/            # Foros y comentarios
│   │   ├── quiz/              # Cuestionarios vocacionales
│   │   └── users/             # Gestión de usuarios
│   ├── middleware/            # Middleware de autorización
│   │   ├── auth/              # Lambda Authorizer (JWT)
│   │   └── webhook-auth/      # Webhook Authorizer
│   ├── utils/                 # Utilidades compartidas
│   │   ├── repose.crypto.js   # Encriptación en reposo
│   │   └── traffic.crypto.js  # Encriptación en tránsito
│   ├── terraform/             # Infraestructura como código
│   │   ├── main.tf
│   │   ├── variables.tf
│   │   ├── outputs.tf
│   │   └── modules/           # Módulos para AWS, Azure, GCP
│   ├── testing/               # Pruebas de rendimiento
│   │   └── performance/       # Tests de carga, estrés, spike, soak
│   └── public/                # Documentación y resultados
│       └── tests/             # Reportes de pruebas
├── docs/                      # Documentación del proyecto
│   └── informe_final.pdf      # Informe final de graduación
├── demo/                      # Demostraciones
│   └── demo.mp4
└── README.md                  # Este archivo
```

## 🔒 Seguridad

### Encriptación de Datos Personales (PII)

El sistema implementa encriptación **AES-256-CBC** para los siguientes campos:

- `first_name`
- `last_name`
- `username`
- `email`
- `image_url`

**Configuración:**

```bash
ENCRYPTION_KEY=tu_clave_generada_en_base64
```

### Encriptación de Tráfico

Implementación de **AES-256-GCM** (estilo TLS 1.3) para datos en tránsito:

- Autenticación y encriptación (AEAD)
- Protección contra manipulación de datos
- Formato: `nonce:authTag:encryptedData`

**Configuración:**

```bash
TRAFFIC_ENCRYPTION_KEY=tu_clave_generada_en_base64
```

### Políticas de Seguridad

- ✅ Datos encriptados en reposo y en tránsito
- ✅ VPC con subnets privadas
- ✅ Security Groups para control de acceso
- ✅ WAF para protección contra amenazas web
- ✅ Validación JWT con clave pública
- ✅ Webhooks firmados criptográficamente
- ✅ Variables de entorno para secretos

## 🚀 Despliegue

### Prerrequisitos

1. **Terraform** (>= 1.0)
2. **AWS CLI** configurado con credenciales
3. **Node.js** (para desarrollo local)
4. **MongoDB/DocumentDB** (instancia configurada)

### Despliegue con Terraform

1. **Configurar variables:**

   ```bash
   cd src/terraform
   cp terraform.tfvars.example terraform.tfvars
   # Editar terraform.tfvars con tus valores
   ```

2. **Inicializar Terraform:**

   ```bash
   terraform init
   ```

3. **Revisar plan de despliegue:**

   ```bash
   terraform plan
   ```

4. **Desplegar:**
   ```bash
   terraform apply
   ```

El sistema desplegará automáticamente:

- **34 funciones Lambda** de features
- **2 funciones Lambda** de autorización
- **Total: 36 funciones**

### Despliegue Manual de Funciones Lambda

Para cada función Lambda:

```bash
cd src/features/[feature-name]
zip -r function.zip .
# Subir function.zip a AWS Lambda
```

## 🧪 Testing

El proyecto incluye pruebas de rendimiento utilizando **K6**:

### Tipos de Pruebas

1. **Load Testing**: Pruebas de carga con usuarios constantes

   - `get-careers`: 50 usuarios
   - `get-feed`: 40 usuarios
   - `get-forums`: Ramp-up de 10 a 45 usuarios
   - `get-profile`: 50 usuarios

2. **Stress Testing**: Pruebas de estrés hasta límites del sistema

   - `edit-comment`: Hasta 6,000 usuarios
   - `get-career`: Hasta 6,000 usuarios

3. **Spike Testing**: Pruebas de picos de tráfico

   - `get-careers`: De 20 a 100 usuarios
   - `get-profile`: De 10 a 100 usuarios
   - `get-quiz-results`: De 10 a 100 usuarios

4. **Soak Testing**: Pruebas de resistencia a largo plazo
   - `get-careers`: Pruebas de duración extendida

Los reportes de pruebas están disponibles en `src/public/tests/`.

## 📊 Endpoints API

### Autenticación

- `POST /auth/register` - Registrar nuevo usuario
- `PUT /auth/updateUser` - Actualizar usuario
- `DELETE /auth/delete` - Eliminar usuario

### Usuarios

- `GET /users/getUser` - Obtener perfil de usuario
- `GET /users/getUsers` - Listar usuarios
- `PUT /users/editUser` - Editar usuario

### Carreras

- `GET /careers/getCareers` - Listar carreras
- `GET /careers/getCareer` - Obtener carrera específica
- `PUT /careers/editCareerInsights` - Editar insights de carrera

### Exploración

- `GET /explore/getFeed` - Obtener feed de contenido
- `POST /explore/cards/newCard` - Crear tarjeta
- `GET /explore/cards/getCard` - Obtener tarjeta
- `PUT /explore/cards/editCard` - Editar tarjeta
- `DELETE /explore/cards/deleteCard` - Eliminar tarjeta
- `GET /explore/getTestimonies` - Obtener testimonios
- `POST /explore/interactions/newInteraction` - Registrar interacción
- `GET /explore/interactions/getInteractions` - Obtener interacciones
- `POST /explore/likes/handleLikes` - Gestionar likes

### Foros

- `POST /forums/newForum` - Crear foro
- `GET /forums/getForums` - Listar foros
- `GET /forums/getForum` - Obtener foro específico
- `PUT /forums/editForum` - Editar foro
- `DELETE /forums/deleteForum` - Eliminar foro

### Comentarios

- `POST /forums/comments/newComment` - Crear comentario
- `PUT /forums/comments/editComment` - Editar comentario
- `DELETE /forums/comments/deleteComment` - Eliminar comentario
- `POST /forums/comments/answers/newAnswer` - Crear respuesta
- `PUT /forums/comments/answers/editAnswer` - Editar respuesta
- `DELETE /forums/comments/answers/deleteAnswer` - Eliminar respuesta

### Quiz

- `GET /quiz/results/getResults` - Obtener resultados de quiz
- `DELETE /quiz/results/deleteResults` - Eliminar resultados

### Analíticas

- `GET /analytics/getAnalytics` - Obtener estadísticas del sistema

### Contenido Guardado

- `POST /users/saved/saveItem` - Guardar item
- `GET /users/saved/getSavedItems` - Obtener items guardados
- `DELETE /users/saved/unsaveItem` - Desguardar item

### Recomendaciones

- `GET /recommendations` - Obtener recomendaciones personalizadas (Microservicio EC2)

## 📚 Documentación Adicional

- **Infraestructura**: Ver `src/terraform/README.md`
- **Encriptación en Reposo**: Ver `src/utils/ENCRYPTION.md`
- **Encriptación en Tránsito**: Ver `src/utils/TRAFFIC_ENCRYPTION.md`
- **Informe Final**: Ver `docs/informe_final.pdf`

## 🎯 Objetivos del Proyecto

1. **Orientación Vocacional**: Proporcionar herramientas para ayudar a estudiantes a descubrir su vocación
2. **Escalabilidad**: Arquitectura serverless que escala automáticamente
3. **Seguridad**: Implementación de múltiples capas de seguridad
4. **Integración IA**: Sistema de recomendaciones basado en inteligencia artificial
5. **Accesibilidad**: Plataforma accesible para estudiantes de Guatemala y Latinoamérica

## 👨‍💻 Autor

**José Daniel Gómez Cabrera**  
Carnet: 21429  
Universidad del Valle de Guatemala  
Guatemala, Guatemala

## 📄 Licencia

Este proyecto es parte de un Trabajo de Graduación de la Universidad del Valle de Guatemala.

---

**Nota**: Este README es un documento vivo. Por favor, actualízalo conforme el proyecto evolucione.
