# 🛒 Proyecto Urban Grocers - Pruebas de API & Validación de Base de Datos (Sprint 4)

El propósito de este proyecto es asegurar la calidad, integridad y correcto funcionamiento de los servicios backend de la plataforma de comercio electrónico Urban Grocers, combinando pruebas manuales y automatizadas sobre sus endpoints críticos y bases de datos.

---

## 🎯 Objetivo del Proyecto
Validar la lógica de negocio del servidor, asegurar el correcto intercambio de datos (JSON) mediante solicitudes HTTP y confirmar que la persistencia en las tablas de la base de datos sea exacta ante las acciones del usuario, mitigando errores en el procesamiento de órdenes.

---

## 🛠️ Tecnologías y Herramientas Utilizadas

* **Python 3.x**: Lenguaje para el desarrollo de scripts de automatización.
* **Pytest**: Framework para estructurar y ejecutar las pruebas automatizadas de backend.
* **Requests**: Librería de Python utilizada para enviar solicitudes HTTP y procesar respuestas.
* **Postman**: Herramienta utilizada para el diseño de colecciones, pruebas de API manuales y análisis de respuestas de endpoints.
* **PostgreSQL / SQL**: Lenguaje de consultas estructuradas utilizado para verificar la consistencia de datos directamente en las tablas del servidor.

### 🔌 Protocolos y Formatos Soportados
* **HTTP/1.1**: Métodos `GET`, `POST`, `PUT`, `DELETE`.
* **REST API**: Arquitectura de comunicación del backend.
* **JSON**: Formato estándar para el envío y recepción de payloads.

---

## 🧪 Cobertura de Pruebas Realizadas

El proyecto abarca pruebas integrales divididas en dos capas analíticas:

### 1. Testing de APIs (Postman & Python Requests)
* **Validación de Endpoints:** Verificación de códigos de estado (`200 OK`, `201 Created`, `400 Bad Request`).
* **Pruebas de Contratos:** Comprobación de que la estructura JSON de respuesta contenga las llaves y tipos de datos correctos.
* **Flujos de Usuario:** Automatización de la creación de un nuevo usuario, autenticación de token, adición de productos al carrito y simulación de Checkout.

### 2. Validación de Base de Datos (SQL)
* **Integridad de Datos:** Creación de consultas con filtros complejos (`WHERE`, `LIKE`, `IN`) para auditar la información.
* **Consistencia Backend-BD:** Comprobación de que un producto añadido a través de la API se registre de forma exacta en las tablas correspondientes con sus respectivos IDs y cantidades utilizando funciones de agregación (`COUNT`, `SUM`).

---

## 📁 Estructura del Proyecto de Automatización
```plaintext
qa-project-Urban-Grocers-app-es/
│
├── configuration.py  # URLs base y configuración de entornos de prueba
├── data.py           # Payloads JSON, diccionarios y encabezados (Headers)
├── sender_stand_request.py  # Métodos HTTP parametrizados con la librería Requests
├── create_user_test.py      # Casos de prueba automatizados y aserciones de API
└── README.md         # Documentación general del proyecto
```

---

## 🚀 Instrucciones de Ejecución (Automatización)

### Prerrequisitos
Asegúrate de contar con Python 3.x instalado en tu sistema.

### Instalar Dependencias
```bash
pip install requests pytest
```

### Ejecutar Suite de Pruebas de API
```bash
pytest create_user_test.py -v
```

---

## 🧠 Retos Técnicos y Soluciones

* 🧩 **Manejo de Tokens Dinámicos:** Los endpoints de simulación de compra requerían un token de autorización temporal generado únicamente al crear un usuario.
  * **Solución:** Diseñe un flujo lógico donde el script de prueba captura la respuesta de la creación del usuario, extrae la propiedad del token dinámicamente y la inyecta automáticamente en los headers de las siguientes peticiones.
* 🗄️ **Estructura de Datos Compleja en SQL:** Validar la cantidad total de órdenes requería extraer datos cruzados de múltiples registros dinámicos.
  * **Solución:** Consolidé el uso de agrupaciones (`GROUP BY`) y operadores lógicos robustos en Postgres para cruzar los resultados manuales contra las peticiones HTTP de la API, garantizando que el backend y la base de datos se mantuvieran sincronizados.

---

## 🎯 Conclusión del Proyecto & Lecciones Aprendidas

Este proyecto de *Urban Grocers* consolidó mi entendimiento sobre la arquitectura cliente-servidor y el ciclo de vida de los datos en el backend. Validar APIs me enseñó que la calidad del software va mucho más allá de lo que el usuario ve en la pantalla. 

Dominar las aserciones sobre respuestas JSON con **Python Requests** y cruzar esa información mediante consultas **SQL** directo en la base de datos me otorgó una perspectiva técnica profunda sobre la persistencia y seguridad de la información. Estas competencias en backend son el pilar que me prepara para auditar con total seguridad sistemas multimedia complejos como el envío y recepción de telemetría o datos de usuario en **servicios de streaming y videojuegos**. 🐌🚀
