# 👤 Proyecto API Stand Tests 

Urban Grocers es una plataforma integral diseñada para la planificación de movilidad urbana y la gestión de logística, permitiendo a los usuarios gestionar viajes y entregas en una interfaz unificada. Sus funcionalidades clave incluyen la configuración de perfiles, un selector modular de tarifas, reserva de vehículos en tiempo real, gestión de pagos y persistencia de datos para asegurar la consistencia del servicio. Para más detalles técnicos, consulte la documentación del proyecto Urban Grocers.

El sistema backend procesa el registro y almacenamiento del cliente, respondiendo a través de una arquitectura cliente-servidor basada en microservicios e integrando bases de datos planas en formato CSV.

---

## 🎯 Objetivo del Proyecto

Validar la estabilidad, lógica de negocio y seguridad del endpoint de creación de usuarios/as mediante la automatización de escenarios positivos y negativos para el parámetro **`firstName`**. El objetivo principal es garantizar que el backend responda con los códigos de estado correctos (`201 Created` / `400 Bad Request`) y asegurar la integridad de la base de datos verificando la inserción exacta del registro en el sistema de archivos del servidor.

---

## 🦹 Alcance de las pruebas

Las pruebas cubren la verificación funcional de la API y la auditoría de persistencia en la base de datos:

📍 **Validación de Parámetros en Registro (`/api/v1/users/`)**
* Análisis de Valores Límite (BVA) y control de fronteras en el campo `firstName` aplicando casos lógicos para longitudes permitidas (2 y 15 caracteres) y longitudes inválidas (1 carácter).
* Validación de la generación correcta y obligatoria del token de autenticación (`authToken`) tras un registro exitoso.

📊 **Verificación de Persistencia en Base de Datos (`user_model.csv`)**
* Consulta directa y descarga en tiempo real del archivo de recursos del sistema (`/api/db/resources/user_model.csv`).
* Validación mediante aserciones de cadena de texto para comprobar que el formato estructurado (`firstName,phone,address,,,,authToken`) se inyecte exactamente una sola vez en la base de datos del servidor, evitando registros duplicados o fantasmas.

💳 **Control de Excepciones y Respuestas HTTP**
* Verificación de códigos de error `400 Bad Request` y correspondencia exacta de los mensajes de alerta devueltos por el backend ante entradas que violen el alfabeto latino o la longitud requerida.

---

## 🟣 Lógica de funcionamiento

* En el estado inicial, para que un usuario sea considerado válido por el sistema, su parámetro `firstName` debe estar compuesto estrictamente por caracteres latinos y poseer una longitud de 2 a 15 caracteres.
* Tras un envío exitoso, el sistema genera de forma síncrona un código de estado `201` y un `authToken` no vacío.
* Inmediatamente después de la confirmación, el servidor escribe en el archivo físico `user_model.csv` una nueva fila con la estructura de datos del usuario vinculada al token emitido.
* Cualquier entrada fuera de los límites lógicos interrumpe la persistencia en la base de datos, deniega el registro, y retorna un JSON con un código de error de negocio interno `400` junto con un mensaje de validación instructivo.

## 🪶 Contenido del Proyecto

Este proyecto contiene el script de automatización avanzada `create_user_test.py`, desarrollado bajo buenas prácticas de ingeniería de calidad para desacoplar el cliente de peticiones de red de las aserciones de prueba y los esquemas lógicos de la base de datos.

### 📁 Estructura de Archivos
```plaintext
qa-project-api-stand-tests/
│
├── .gitignore              # Evita que se carguen carpetas locales de caché y del sistema
├── README.md               # Documentación general y guía técnica del proyecto
├── configuration.py        # Define la URL activa del servidor y las rutas CSV/Endpoints de la API
├── create_user_test.py     # Suite de automatización de pruebas para firstName y validación de DB
├── data.py                 # Almacena los payloads base y diccionarios JSON de prueba
└── sender_stand_request.py # Implementación de métodos HTTP (POST, GET) con la librería requests
```

## 🧩 Tecnologías y Herramientas

* **Lenguaje principal de programación:** `python`
* **Framework para pruebas automatizadas:** `pytest`
* **Librerías de comunicación de red:** `requests`
* **Estructuras de intercambio de datos:** `json`
* **Formatos de persistencia validados:** `csv` (Mapeo directo de la tabla de usuarios del servidor)

### 🏷️ Endpoints y Rutas de Red Monitoreadas
* `URL_SERVICE:` `https://tripleten-services.com`
* `CREATE_USER_PATH:` `/api/v1/users/`
* `USERS_TABLE_PATH:` `/api/db/resources/user_model.csv`
* `DOC_PATH:` `/docs/`
* `LOG_MAIN_PATH:` `/api/logs/main/`
* `PRODUCTS_KITS_PATH:` `/api/v1/products/kits/`

---

## 🪄 Enfoque de testing

El enfoque de este proyecto combina las pruebas de API funcionales de Caja Negra con **pruebas de integración para la validación de persistencia de datos**, rompiendo el aislamiento tradicional del testing de endpoints básicos.

Incluye:
* **Estrategia Dual de Aserción:** Validación simultánea de la respuesta en la capa de transporte (Código HTTP y JSON) y de la inserción en la capa de datos (Búsqueda en la tabla CSV mediante conteo de ocurrencias exactas).
* **Análisis de Valores Límite Avanzado:** Pruebas controladas simulando nombres reales como ` DanaPaolaSantos ` (límite exacto superior de 15 caracteres) o combinaciones mínimas como ` Aa ` (límite inferior de 2 caracteres) para auditar la precisión matemática de las restricciones de los strings.
* **Aislamiento Funcional mediante Helper:** Creación de una función constructora dinámica (`get_user_body`) que clona el payload base y modifica el parámetro necesario en tiempo de ejecución para mantener pruebas limpias e independientes.

## ⚡ Aspectos destacados

* **Garantía Real de Persistencia:** Las pruebas no asumen que el software funciona porque responde `201`; confirman el almacenamiento real en el disco del servidor mediante `get_users_table()`.
* **Seguridad Antiduplicados:** Validación rigurosa que asegura que cada registro se inserte exactamente una sola vez (`count(str_user) == 1`), previniendo fugas de datos o inyecciones masivas.
* **Robustez en Mensajes de Error:** Auditoría del texto exacto del string de error devuelto por la API para asegurar que las excepciones del backend sean amigables e informativas para el usuario.

## 🎯 Objetivo

Asegurar que el flujo de registro de la plataforma funcione de manera impecable, robusta y altamente confiable, garantizando que el backend filtre de forma segura los datos inválidos y registre con total precisión matemática y física a los nuevos usuarios en el sistema.

---

## 🚀 Instrucciones de Ejecución

### Prerrequisitos
Asegúrate de contar con Python 3.x instalado de forma local.

### Instalación de Dependencias
Prepara tu entorno de comandos ejecutando:
```bash
pip install pytest requests
```

### Ejecución de la Suite
* **Ejecutar las pruebas lógicas y de base de datos de forma automática:**
  ```bash
  pytest
  ```
