# Introducción paso a paso al framework móvil [Ionic]

📖 **Lea el tutorial: [https://stahe.github.io/ales-ionic-etape-par-etape-oct-2026/](https://stahe.github.io/ales-ionic-etape-par-etape-oct-2026/)**

Este curso enseña a construir una aplicación **móvil** con el framework [Ionic](https://ionicframework.com) 9, combinado con [Angular](https://angular.dev) 22 y [Capacitor](https://capacitorjs.com) 8: una aplicación Android cuyas pantallas se renderizan **en el teléfono** a partir de datos JSON provenientes de un servidor. Es una aplicación web (HTML, CSS, TypeScript) que Capacitor empaqueta como aplicación Android; el mismo código también se ejecuta en un navegador.

Sigue el esquema del curso [Introducción paso a paso al framework móvil [Flutter]](https://stahe.github.io/ales-flutter-etape-par-etape-sept-2026/) (y de los cursos sobre [React](https://stahe.github.io/ales-react-etape-par-etape-sept-2026/), [Vue.js](https://stahe.github.io/ales-vuejs-etape-par-etape-sept-2026/) y [Angular](https://stahe.github.io/ales-angular-etape-par-etape-sept-2026/)): el mismo servidor, las mismas pantallas, escritas al estilo Ionic, para un teléfono inteligente.

| Curso de Flutter | Curso de Ionic |
|---|---|
| el lenguaje Dart | TypeScript |
| el motor de Flutter renderiza cada píxel | componentes web de apariencia nativa (Material Design en Android), mostrados por el WebView del teléfono |
| widgets, un método `build()`, `setState` | componentes Angular, plantillas HTML, signals (`signal`, `computed`, `effect`) |
| `InheritedWidget`, provider (`ChangeNotifier`) | inyección de dependencias de Angular, servicios con signals |
| `Form` y `FormField` | formularios reactivos de Angular |
| go_router, `ShellRoute` + `NavigationBar` | enrutador de Angular, `ion-tabs`, guardias `CanActivateFn` |
| `Future`, `Stream` | `Promise`, `Observable` (RxJS), `resource()` |
| `shared_preferences` | `@capacitor/preferences` |
| un cliente HTTP que gestiona cookies (en el teléfono) | `HttpClient`; en el teléfono, el plugin CapacitorHttp establece y devuelve la cookie |

El servidor, en cambio, permanece sin cambios: es el servidor JSON de la aplicación **RdvMedecins** que ya utilizaron los clientes Flutter, React, Vue.js y Angular.

## El enfoque: muchos ejemplos breves y luego un caso de estudio

El curso se estructura en torno a **25 ejemplos breves**, cada uno centrado en un único concepto, que llevan los mismos números (y muestran las mismas pantallas) que los de los cursos de Flutter, React, Vue.js y Angular. Forman un único proyecto Ionic: basta un solo comando `npm install`, seguido de `npm start`; el ejemplo se selecciona desde el menú de la aplicación (o mediante su URL: http://localhost:8100/ex05).

| Capítulo | Contenido | Ejemplos |
|---|---|---|
| Primeros pasos | un proyecto Ionic + Angular + Capacitor, componentes de Ionic, signals y propiedades calculadas, plantillas (`@if`, `@for`), eventos, todos los tipos de campo, un reducer, validación, formularios reactivos, formateo (pipes, `Intl`) | 01–09 |
| Componentes | entradas (`input`), salidas (`output`, `model`), proyección de contenido (`ng-content`, `ng-template`), un arreglo genérico, ciclo de vida, inyección de dependencias, directivas y animaciones, cuadro de diálogo de confirmación (`AlertController`) | 10–16 |
| Enrutamiento | el enrutador de Angular con Ionic: rutas, parámetros, `ion-tabs`, carga diferida, páginas 404, guardias | 17–18 |
| Operaciones asíncronas y estado compartido | `Promise`, `Observable`, generadores asíncronos, temporizadores, antirrebote, respuestas obsoletas (`switchMap`), `resource()`, servicios con signals, `@capacitor/preferences`, modo oscuro | 19–20 |
| Internacionalización | diccionarios JSON, parámetros, plurales (`Intl.PluralRules`), fechas, importes, calendario localizado (`ion-datetime`) | 21 |
| El servidor: una caja negra | puesta en marcha del servidor JSON, su API, 48 ejemplos con `curl`, qué cambia para un cliente móvil | – |
| Comunicación con el servidor | `HttpClient`, el proxy de desarrollo, la dirección del servidor (emulador, teléfono, web), CapacitorHttp y la cookie del token, una capa de acceso a la API, un modelo de vista, errores del servidor asociados a los campos | 22–25 |

Cada ejemplo se presenta con su código completo, comentado línea por línea, y con capturas de pantalla de su ejecución (generadas automáticamente mediante scripts de [Playwright](https://playwright.dev), en la carpeta `captures/`).

## El servidor: una caja negra

El servidor es el servidor NestJS de las lecciones anteriores, cuyos controladores devuelven **JSON**. Esta lección lo trata como una **caja negra**: se instala, se estudia su API y se consulta con `curl`, pero no es necesario leer su código (que se proporciona y está comentado para los curiosos).

- Todos los errores tienen el mismo formato: `{ "statusCode": 409, "cle": "ERRORS. LOGIN_TAKEN", "params": {...}, "fields": {...} }`: **claves** de traducción, nunca texto plano;
- Autenticación mediante un token JWT en una cookie `httpOnly`: en el navegador, este lo almacena; en el teléfono, lo gestiona el plugin CapacitorHttp;
- un «modo de prueba» para el CAPTCHA, de modo que se pueda consultar la API con `curl` (y generar capturas de pantalla automáticamente).


## El caso de estudio: el cliente Ionic de RdvMedecins

Una aplicación completa para **reservar citas en un consultorio médico**, con **todos** los archivos listados y comentados.

- **Ionic y Angular modernos**: componentes standalone, signals, una aplicación sin Zone.js, el nuevo flujo de control de plantillas, `inject()`, un interceptor HTTP funcional, guardias `CanActivateFn` y una arquitectura en capas (acceso a la API, estado, interfaz de usuario).
- **Tres roles**: `ADMIN` (gestiona médicos y clientes), `DOCTOR` (agenda y cancela citas), `USER` (el paciente: reserva citas para sí mismo y gestiona su cuenta).
- **Confidencialidad**: un paciente nunca recibe los nombres de otros pacientes; el servidor no los envía.
- **Todo el estado de la pantalla está contenido en la URL**: `/agenda?idMedecin=1&jour=2026-10-13&reserver=7`; en un teléfono, el botón Atrás cierra la ventana de reserva; en la web, funcionan F5 y Atrás/Adelante.
- **Validación del lado del servidor**: los formularios muestran bajo cada campo los errores devueltos por la API; bloqueo optimista, nombres duplicados, nombre de usuario ya en uso...
- **Sesión**: se restablece al iniciar (`GET /api/auth/moi`); la expiración se gestiona en un único lugar (respuesta 401, en el interceptor).
- **Adaptado a móviles**: menú lateral desplegable (`ion-menu`), siempre visible en pantallas grandes (`ion-split-pane`), listas en lugar de tablas.
- **Francés / inglés**, incluido el calendario.
- **Despliegue**: la versión web compilada, servida por el propio servidor JSON, y la aplicación Android (APK).

## Contenido del repositorio

```
exemples_ionic/            los 25 ejemplos breves (un único proyecto Ionic)
rdvmedecins-nestjs-json/   el servidor JSON de RdvMedecins (la «caja negra»)
rdvmedecins_ionic/         el cliente Ionic del caso de estudio
```

La carpeta `android/` de cada proyecto no se incluye: se crea con `npx cap add android` (véase el curso).

## Tecnologías

Ionic 9 · Angular 22 · Capacitor 8 · TypeScript 6 · RxJS 7 · @capacitor/app · @capacitor/preferences · ionicons · Playwright (capturas de pantalla) · del lado del servidor: NestJS 10 · TypeORM · MySQL 8 / MariaDB · Passport JWT · svg-captcha

## Requisitos previos

- Los fundamentos de TypeScript y Angular, tratados en los cursos [Introducción al lenguaje TypeScript mediante ejemplos](https://stahe.github.io/typescript-sept-2026/) e [Introducción paso a paso al framework web [Angular]](https://stahe.github.io/angular-etape-par-etape-sept-2026/); no obstante, los conceptos utilizados se repasan a lo largo de los ejemplos.
- Node.js 24 (o 22.22.3 y posteriores, requerido por Angular CLI 22), Visual Studio Code con sus extensiones Angular Language Service e Ionic, Chrome, Android Studio (para el Android SDK y el emulador) o un teléfono Android; un servidor MySQL (p. ej., Laragon en Windows) para el servidor JSON. Las instrucciones de instalación se proporcionan en el curso.

## Autor

Este curso, sus ejemplos, el cliente Ionic y la adaptación del servidor JSON fueron escritos por **Claude**, la IA de [Anthropic](https://www.anthropic.com) (octubre de 2026), a petición de **Serge Tahé**.
