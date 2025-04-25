# **Runbook: Procedimiento Añadir Cuenta a Producto**

----

### **Introducción**
Este Runbook proporciona instrucciones paso a paso para llevar a cabo una tarea operativa específica dentro del Servicio de Plataforma de Ingeniería. El objetivo es añadir una cuenta a un producto que se creó por fuera de Poseidón.

### **Información del Documento**
- **Título del Runbook:** Añadir cuenta a producto
- **ID del Documento:** []
- **Versión:** 1.1
- **Fecha de Creación:** 31/03/2025
- **Fecha de Última Modificación:** 01/04/2025
- **Autor:** Víctor Márquez Gómez
- **Revisor:** Samuel Carretero Jara
- **Aprobador:** []
### **1. Información General**
- **Descripción de la Tarea:** Añadir cuenta a producto, cuenta creada por fuera de Poseidón conectando manualmente la base de datos. 
- **Servicio/Sistema Afectado:** MongoDB y portal Plataforma Ingeniería.
- **Frecuencia de Ejecución:** A demanda.
- **Duración Estimada:** 20 minutos aproximadamente.
- **Ventana de Mantenimiento:** N/A.
- **Impacto en el Negocio:** No aparece la cuenta en el registro de Poseidón lo que puede afectar al registro de costes.
- **Riesgos:** El entorno donde se desarrolla la actividad es producción.
- **Requisitos Previos:** Tener acceso a las cuentas de AWS de Poseidon, acceso para conectarse a la base de datos, nombre y número de cuenta y nombre de producto.

### **2. Procedimiento Detallado**

**Paso 1:** Conectarse a la base de datos de Poseidón.

**Paso 1.1:** Acceder a la cuenta de AWS de Poseidón.

![Texto alternativo](https://i.postimg.cc/mgFT0Dn4/imagen-3.png)

**Paso 1.2:** Acceder a servicio Cloud9 a través de AWS.

![Texto alternativo](https://i.postimg.cc/TwTGLc32/imagen-2.png)

**Paso 1.3:** Establecer conexión con MongoDB a través del entorno “fparisowner” seleccionando el apartado de “Shared with me” y luego “Open in Cloud9” dentro del entorno.

![Texto alternativo](https://i.postimg.cc/mrYBgnR6/imagen-1.png)

**Paso 1.4:** Utilizar comando con contraseña y, escribir `use poseidon` en una nueva terminal para establecer conexión con la base de datos de Poseidón.

![Texto alternativo](https://i.postimg.cc/g0FGdp9Z/imagen.png)

**Paso 2:** Buscar la cuenta en la base de datos de Poseidón utilizando el siguiente comando.

`db.environment.find({"environment_cloud_account_number":"XXX"}).pretty()`

**Paso 2.1:** Crear backup de la base de datos con el siguiente comando: (Se necesita contraseña) 

`mongoexport --ssl --host 'psdn-source-psdn-metadata.cluster-ctrly2qeqwpv.eu-west-1.docdb.amazonaws.com:27017' --sslCAFile global-bundle.pem --username 'admin1' --password 'XXXX' --db 'poseidon' --ssl --collection='XXXX' --out=XXXX_YearMonthDayHourMinutes.json` 

**Paso 3:** **Actualizar la base de datos** 

**A)** Si la cuenta no existe en Poseidón se debe de insertar la cuenta solicitada rellenando los datos necesarios en el siguiente comando. 

`db.environment.insertOne({"environment_cloud_account_number":"XXXXXX","environment_cloud_account_name":"XXXXX","environment_status":"ACTIVE","environment_cloud_provider":"XXX","environment_web_enabled":"1","environment_operation_enabled":"0","environment_duplicates_control_key":true,"environment_mapfre_entity_name":"XXXXXX","environment_project_name":"XXXXXX","environment_project_short_name":"XXXXXX","environment_lz_version":"v2"})`

**B)** Si la cuenta ya existe en Poseidón pero no está asociada a ningún producto, habrá que actualizar los datos de dicha cuenta utilizando el siguiente comando.

`db.environment.updateOne({"environment_cloud_account_number" : "XXXX"}, {$set:{"environment_project_name" : "XXXX", "environment_project_short_name" : "XXXX" }})`

### **3. Verificación y Validación**

**Criterios de Éxito:** Mensaje de confirmación en Mondo DB

`{ "acknowledged" : true, "matchedCount" : 1, "modifiedCount" : 1 }`

**Procedimiento de Verificación:** Acudimos al paso 2 para buscar dicha cuenta. 

**Procedimiento de Validación:** Ejecutar el siguiente comando para revisar las cuentas asociadas a un determinado producto, para ver si la cuenta se ha asociado correctamente: 

`db.environment.find({"environment_project_short_name":"XXX"}).pretty()`

### **4. Manejo de Excepciones y Rollback**

**Posibles Errores:** Errores humanos, (Error de sintaxis de código, cuenta/producto erróneo…)

**Acciones Correctivas:** Restaurar el backup en caso de modificar erróneamente la base de datos.

**Procedimiento de Rollback:** En caso de error y modificación de base de datos, recuperar el backup de MongoDB.

### **5. Comunicación**

**Partes Interesadas a Notificar:** Notificar a nuestros compañeros de L1 que procedemos a la modificación de la base de datos.

**Método de Comunicación:** Aplicación JIRA (comentarios), comunicación con L1.

**Plantilla de Notificación:** N/A

### **6. Apéndice**

**Referencias:** N/A

**Información de Contacto:** Equipo Técnico

### **7. Historial de Revisiones**

| Versión | Fecha        | Autor           | Descripción de los Cambios                                                                          |
| ------- | ------------ | --------------- | ---------------------------------------------------------------------------------------------------- |
| 1.0     | 31/03/2025   | Víctor Márquez  | Creación del documento                                                                               |
| 1.1     | 01/04/2025   | Samuel Carretero | Revisión y modificación de la descripción de algunos de los campos y adición de capturas de pantalla. |

