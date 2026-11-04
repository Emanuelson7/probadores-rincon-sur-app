# FitFlow · Probadores Rincón Sur — DSY1105 Evaluación Parcial 2

App Android (MVP) para que el personal de **Rincón Sur** (tiendas Jumbo Chicureo, Costanera, Chamisero y las que
cree el administrador) vea en tiempo real qué probadores están libres u ocupados, cuánto tiempo llevan ocupados
y cuántos clientes esperan, con alertas cuando un probador supera el tiempo límite.

**Integrantes:** _(escribir nombre y apellido de ambos estudiantes)_
**Equipo / Sección:** _(completar)_

## Funcionalidades
- **Inicio de sesión solo con cuenta de la app** (administrador o asistente), con formularios validados:
  íconos, mensajes por campo y botón que se activa cuando los datos son válidos. La app abre directo en el login.
- **Registro** con nombre, **RUT chileno validado (módulo 11)** y formateado automático, correo institucional,
  cargo, tienda donde trabaja, contraseña con **indicador de fortaleza** y aceptación obligatoria de términos.
- **Dos roles**:
  - **Administrador**: ve la lista de todas las tiendas, crea tiendas nuevas (nombre y cantidad de probadores),
    agrega o elimina probadores y marca cada uno como *operativo*, *en reparación* o *no disponible* con un motivo.
  - **Asistente**: entra directo a su tienda y puede **cambiar de tienda** (ícono ⇄) según dónde vaya a trabajar;
    la app recuerda la última tienda en la que trabajó.
- **Multi-tienda**: cada tienda tiene su propia cantidad de probadores, su tiempo límite y su fila de espera.
- Panel de probadores con estado por color (libre / ocupado / uso prolongado / fuera de servicio) y cronómetro.
  Los probadores en reparación o no disponibles se ven en gris, con su motivo, y no se pueden ocupar.
- Contador de clientes en fila de espera.
- Detalle del probador: ocupar, liberar, reporte de observación y foto (prenda abandonada).
- Configuración del tiempo límite de uso (validado, 1–120 min) por tienda.
- Notificación local cuando un probador supera el tiempo límite.
- Persistencia local con Room (SQLite) y **backend con microservicios Spring Boot + API REST + SQLite**, integrado
  con **Retrofit**. La app muestra siempre los datos de Room y se sincroniza con el servidor (cada 5 s en el panel);
  si el backend está apagado, sigue funcionando con los datos locales.
- Diseño FitFlow (Material 3, colores índigo). La pantalla de bienvenida (onboarding) está en el código pero no
  se usa al abrir: para activarla, cambiar `startDestination` a `Rutas.ONBOARDING` en `AppNavegacion.kt`.

## Recursos nativos
1. **Notificaciones** (permiso `POST_NOTIFICATIONS` en Android 13+).
2. **Cámara** (permiso `CAMERA` en tiempo de ejecución; la foto se guarda en almacenamiento interno).

## Arquitectura (MVVM)
```
com.duoc.probadores
├── model            Modelos de datos: entidades Room (tiendas, probadores, usuarios...), estados y resúmenes
├── data
│   ├── local        Room: DAOs y base de datos (versión 4, con migraciones)
│   ├── remote       Retrofit: DTOs y APIs hacia los microservicios (auth y tiendas)
│   └── repository   Interfaz + implementación (servidor + Room; los ViewModels solo conocen la interfaz)
├── domain           Reglas de negocio y validaciones (sin Android): estados, RUT, contraseña, roles
├── ui               Pantallas (Compose) + ViewModels, por carpeta
│   ├── login, registro, onboarding
│   ├── tiendas      Lista de tiendas (elegir dónde trabajar / crear tienda)
│   ├── panel, detalle, configuracion
│   ├── administrar  Agregar/quitar probadores y cambiar su estado de servicio
│   ├── components   Componentes reutilizables (campos, botones, indicadores)
│   ├── navigation   Rutas y NavHost
│   └── theme        Colores y tema Material 3
└── util             Notificaciones, fotos, seguridad, reloj
```
Tecnologías (app): Kotlin, Jetpack Compose, Material Design 3, MVVM, Room (SQLite), Retrofit, Navigation Compose,
Coroutines/Flow.

Backend (carpeta `backend/`): Java 17, Spring Boot 3, API REST, SQLite, JUnit 5 y Mockito. Ver `backend/README.md`.

## Cómo ejecutar
**Backend (opcional, pero necesario para la integración con Retrofit):** en dos terminales,
`cd backend/auth-service && mvn spring-boot:run` y `cd backend/tiendas-service && mvn spring-boot:run`
(detalles en `backend/README.md`). Sin el backend la app funciona igual, solo con datos locales.

**App:**
1. Abrir la carpeta del proyecto con **Android Studio** (Koala o superior, JDK 17).
2. Esperar el *Gradle Sync*.
3. Ejecutar en un emulador o dispositivo con Android 8.0+ (API 26). El emulador llega al backend por `10.0.2.2`.
4. Cuentas de prueba (todas con la clave `Clave123`):

   | Rol | Correo | Tienda inicial |
   |---|---|---|
   | Administrador | `admin@rincon.cl` | todas |
   | Asistente | `asistente.chicureo@rincon.cl` | Jumbo Chicureo (8 probadores) |
   | Asistente | `asistente.costanera@rincon.cl` | Jumbo Costanera (12 probadores, el 4 en reparación) |
   | Asistente | `asistente.chamisero@rincon.cl` | Jumbo Chamisero (6 probadores, el 2 no disponible) |

   También se puede crear una cuenta nueva desde "Regístrate" (el cargo *Jefe/a de Tienda (Administrador)*
   crea un administrador).
5. Para probar la alerta: Configuración → tiempo límite = 1 minuto → ocupar un probador y esperar.
6. Si la app se instaló antes de los últimos cambios y algo falla con los datos, desinstalarla del emulador
   (o *Wipe Data* en Device Manager) y volver a ejecutar.

Pruebas unitarias de la app: `./gradlew test`. Pruebas del backend: `mvn test` dentro de cada servicio.

## Trabajo en equipo
- Planificación en Trello (columnas: Por hacer / En progreso / Hecho).
- Ramas por funcionalidad y commits técnicos, por ejemplo: `feat: agrega validación de clave en Validador`.

