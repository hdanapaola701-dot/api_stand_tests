# 🛒 Proyecto Urban Grocers - Pruebas Automatizadas de API 

Urban Grocers es una aplicación orientada al comercio electrónico (*e-grocery*) y servicio de entrega de comestibles a domicilio. La plataforma permite a los usuarios crear cuentas, configurar perfiles, consultar la disponibilidad de inventario en almacenes urbanos, gestionar carritos de compra y organizar productos mediante la creación de kits temáticos personalizados.

El sistema backend procesa flujos transaccionales y la logística de preparación de pedidos (*picking*), respondiendo a través de una arquitectura cliente-servidor basada en microservicios y respuestas HTTP.

---

## 🎯 Objetivo del Proyecto

La tarea principal de este proyecto consiste en **automatizar de forma íntegra las pruebas de la lista de comprobación (*checklist*) diseñada para el campo `name` en la solicitud de creación de un kit de productos**, con el fin de validar los límites lógicos, tipos de datos y restricciones del backend. Posteriormente, el código se despliega y carga en un repositorio de GitHub para su correspondiente control de versiones y envío formal a revisión técnica de calidad.

---

## 🦹 Alcance de las pruebas

Las pruebas cubren la verificación exhaustiva de los servicios REST API del servidor, incluyendo:

📍 **Gestión de Kits de Usuario (`/api/v1/kits`)**
* Creación de kits con nombres válidos e inválidos (análisis de valores límite y particiones de equivalencia de strings).
* Validación de estructuras JSON en solicitudes `POST` y persistencia de kits vinculados a usuarios específicos.
* Verificación del comportamiento del servidor ante la omisión de campos obligatorios en el cuerpo de la petición.

Selector **Órdenes y Carritos de Compra (`/api/v1/orders`)**
* Creación, edición y adición dinámica de productos a carritos activos por identificador (`id`) y cantidad.
* Validación del cálculo económico total del carrito en función del catálogo de precios del backend.

🔐 **Sincronización de Almacenes (`/api/v1/warehouses`)**
* Consulta de disponibilidad de inventario en los diferentes almacenes centrales distribuidos por zonas.
* Validación de la asignación automática de tiempos de preparación y costos de envío según el peso y tipo de orden.

💳 **Validación de Códigos de Respuesta HTTP**
* Verificación de la consistencia de respuestas esperadas para escenarios positivos (`200 OK`, `201 Created`).
* Evaluación de control de excepciones y fronteras para escenarios negativos (`400 Bad Request`, `404 Not Found`).

💬 **Integridad de Datos del Cliente (`/api/v1/users`)**
* Flujo de registro de nuevos perfiles de usuario y adición de tokens de autenticación en los encabezados (*headers*) de las llamadas.

---

## 🟣 Lógica de funcionamiento

* En el estado inicial, las peticiones que carecen de un token de autenticación válido (`Authorization`) son rechazadas automáticamente por el servidor con un código `401 Unauthorized`.
* La creación de un kit está restringida por la base de datos a un formato de nombre específico; strings vacíos o con longitudes que excedan los límites provocan fallas controladas en el backend.
* El sistema calcula dinámicamente los costos de despacho en el momento en que se consulta el endpoint de la orden, basándose en la disponibilidad de stock en los almacenes más cercanos.
* Cualquier modificación en la cantidad de productos de una orden actualiza síncronamente el cuerpo de la respuesta en formato JSON.
* La base de datos mantiene la persistencia local de los kits, asegurando que un usuario solo pueda visualizar o editar las colecciones asociadas a su identificador único.

## 🪶 Contenido del Proyecto

Este proyecto contiene el desarrollo y la implementación del script automatizado `create_kit_test.py` encargado de ejecutar las aserciones de la lista de comprobación para el campo `name`. La estructura está desacoplada bajo buenas prácticas de ingeniería de software para aislar la conectividad de red de los datos de prueba.

### 📁 Estructura de Archivos
```plaintext
qa-project-Urban-Grocers-es/
│
├── README.md               # Documentación general y guía técnica del proyecto
├── UrbanRoutesPage.py      # Implementación de clases de páginas y localizadores (Patrón POM)
├── configuration.py        # Define las variables de entorno, puertos y rutas base de la API
├── data.py                 # Almacena los diccionarios JSON, payloads y headers de prueba
├── helpers.py              # Método auxiliar para la recuperación dinámica del código SMS
├── sender_stand_request.py # Implementación de métodos HTTP (POST, GET, PUT) con requests
└── create_kit_test.py      # Suite con los 9 casos de prueba que automatiza la lista de comprobación
```

## 🧩 Tecnologías y Herramientas

* **Lenguaje principal de programación:** `python`
* **Framework para pruebas automatizadas:** `pytest`
* **Librerías de comunicación de red:** `requests`
* **Pruebas y validaciones manuales de API:** `postman`
* **Estructuras de intercambio de datos:** `json`
* **Gestión de proyectos y seguimiento de defectos:** `jira`
* **Control de versiones y almacenamiento de código:** `git` / `github`
* **Análisis de red e inspección del backend:** `devtools` (Consola del navegador para análisis de logs y llamadas de red)

### 🏷️ Métodos y Parámetros HTTP Validados
* `POST /api/v1/users` para el registro inicial de credenciales.
* `POST /api/v1/kits` para la inyección de colecciones de productos.
* `GET /api/v1/warehouses` para la inspección y lectura de inventarios.

---

## 🪄 Enfoque de testing

El enfoque principal del proyecto está centrado en la **automatización funcional de Caja Negra a nivel de API**, traduciendo una lista de comprobación de diseño manual a código ejecutable mediante Pytest. 

Incluye:
* **Pruebas de Frontera y Tipos de Datos:** Validación de las longitudes límites de caracteres del campo `name` (1, 511, 512 caracteres), inyección de caracteres especiales, manejo de espacios intermedios y control de tipos erróneos (valores numéricos en lugar de strings).
* **Gestión Dinámica de Dependencias:** El script implementa un flujo automatizado para registrar un usuario de prueba en cada ciclo, capturar dinámicamente su token único de autorización (`authToken`) desde la respuesta del servidor e inyectarlo en los *Headers* HTTP antes de disparar la validación del kit.
* **Aislamiento de Infraestructura:** Separación modular de la lógica web en un cliente de peticiones independiente (`sender_stand_request.py`) para asegurar que la suite sea escalable ante futuros cambios en los endpoints.

## ⚡ Aspectos destacados

* **Validación rigurosa de respuestas:** Comprobación dual que audita tanto el código de estado HTTP (`status_code`) como el contenido del objeto JSON devuelto.
* **Manejo eficiente de asincronía:** Scripts que realizan peticiones de forma secuencial y limpia, asegurando que la creación del usuario ocurra y devuelva su ID antes de intentar inyectar un kit.
* **Cero dependencias estáticas:** Uso de métodos que modifican los payloads dinámicamente en tiempo de ejecución, evitando la colisión de datos duplicados en el servidor.
* **Alta mantenibilidad:** Centralización completa de rutas y configuraciones de red, lo que permite migrar las pruebas de un entorno de *Staging* a *Producción* modificando una sola línea.

## 🎯 Objetivo

Garantizar que la API de Urban Grocers sea robusta, segura y confiable, asegurando que la lógica transaccional del backend responda con total precisión matemática y devuelva mensajes de error limpios y controlados ante cualquier entrada inválida del cliente.

---

## 🚀 Instrucciones de Ejecución

### Prerrequisitos
Antes de iniciar, asegúrate de contar con:
1. Python 3.x instalado en tu entorno local.
2. Acceso al servidor activo o contenedor del backend de Urban Grocers.

### Instalación de Dependencias
Prepara el entorno instalando las librerías necesarias con el siguiente comando en tu terminal:
```bash
pip install requests pytest
```

### Ejecución de la Suite
* **Ejecutar las pruebas automáticas de la API:**
  ```bash
  pytest create_kit_test.py
  ```
* **Ejecutar con salida detallada (Verbose):**
  ```bash
  pytest -v create_kit_test.py
  ```

---

## 🧠 Retos Técnicos y Soluciones

* 🧩 **Tokens Dinámicos Obligatorios:** Los endpoints de creación de kits fallaban si el token de usuario expiraba o cambiaba. Se solucionó creando una función inicial que registra un usuario aleatorio en cada ejecución, recupera el token del cuerpo de la respuesta y lo inyecta automáticamente en los cabezales (`headers`) de los siguientes tests.
* 🛑 **Fronteras en Strings Complejos:** La validación de caracteres especiales u orientales provocaba inconsistencias de codificación (`UTF-8`). Se solucionó forzando la conversión explícita del diccionario a JSON string con el método `json.dumps(payload)` antes de transmitir la petición.
* ⌛ **Manejo de Respuestas Vacías (Null Responses):** Al enviar peticiones de error, el servidor respondía sin cuerpo JSON, rompiendo los métodos de aserción estándar de Python. Se implementó una cláusula condicional previa que valida si la longitud del texto de respuesta es mayor a cero antes de intentar decodificar el objeto JSON.


## 🎯 Conclusión del Proyecto & Lecciones Aprendidas

Este proyecto de *Urban Grocers* consolidó mi entendimiento sobre la arquitectura cliente-servidor y el ciclo de vida de los datos en el backend. Validar APIs me enseñó que la calidad del software va mucho más allá de lo que el usuario ve en la pantalla. 

Dominar las aserciones sobre respuestas JSON con **Python Requests** y cruzar esa información mediante consultas **SQL** directo en la base de datos me otorgó una perspectiva técnica profunda sobre la persistencia y seguridad de la información. Estas competencias en backend son el pilar que me prepara para auditar con total seguridad sistemas multimedia complejos como el envío y recepción de telemetría o datos de usuario en **servicios de streaming y videojuegos**. 🐌🚀
