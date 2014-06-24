## <a name="what-is"></a>Qué es Servicios móviles

Servicios móviles es una oferta de servicio de Azure diseñada para
facilitar la creación de aplicaciones móviles altamente funcionales
usando Azure. Servicios móviles reúne un conjunto de servicios de Azure
que habilitan las capacidades back-end para sus aplicaciones. Servicios
móviles ofrece las siguientes capacidades back-end en Azure para dar
brindar apoyo a sus aplicaciones:

* Aprovisionamiento y administración sencillos de las tablas para
  almacenar datos de aplicaciones.
* Integración con los servicios de notificación para entregar
  notificaciones de inserción a su aplicación.
* Integración con proveedores de identidad conocidos para la
  autenticación.
* Control detallado sobre la autorización del acceso a las tablas.
* Lógica de negocios personalizada en el servidor.
* Integración con otros servicios en la nube.
* Compatibilidad con la escalación de una instancia de servicio móvil.
* Supervisión y registro del servicio.

Servicios móviles proporciona una biblioteca de clientes para cada
plataforma compatible, lo que facilita aún más el desarrollo de
aplicaciones en su plataforma que utiliza Servicios móviles.
## <a name="concepts"> </a>Conceptos

A continuación se presentan funcionalidades y conceptos importantes en
los Servicios móviles:

<!--![1][1]-->

* **Clave de aplicación:** Un valor único generado por Servicios
  móviles, distribuido con su aplicación y que se presenta en las
  solicitudes generadas por los clientes. Aun cuando es útil para
  limitar el acceso a su servicio móvil desde clientes aleatorios, esta
  clave no es segura y no debe usarse para autenticar a los usuarios de
  su aplicación.

* **Token de autenticación:** El token de acceso que genera Servicios
  móviles después de que un usuario se autentica.

* **Esquema dinámico:** La capacidad de Servicios móviles de agregar
  automáticamente una columna a una tabla basada en los campos del
  objeto JSON enviado en una solicitud de inserción o actualización. El
  esquema dinámico debe deshabilitarse cuando su aplicación se encuentra
  en producción.

* **Proveedor de identidad:** Servicio externo, como Facebook, Twitter,
  Google o Cuenta Microsoft, que se usa para autenticar a los usuarios
  de su servicio móvil.

* **Permiso:** El nivel de autenticación mínimo necesario para invocar
  una operación de tabla.

* **Notificación de inserción:** Mensaje iniciado por el servicio que se
  envía a un dispositivo o usuario registrados.

* **Escala:** La capacidad de agregar, por un costo adicional, más poder
  de procesamiento, rendimiento y almacenamiento a medida que su
  aplicación se hace más popular.

* **Proyecto programado:** Código de script de servidor que se ejecuta
  según un programa predeterminado o a petición.

* **Script de servidor:** Código de JavaScript para lógica de negocios
  personalizada que se almacena en el servidor. Este código se registra
  en una operación de tabla (leer, insertar, actualizar, eliminar) o un
  proyecto programado.

* **Tabla:** los datos de usuario se almacenan en tablas en una Base de
  datos SQL. Puede crear estas tablas en el Portal de administración.

* **Operación de tabla:** Una solicitud HTTP recibida del cliente para
  leer, insertar, actualizar o eliminar datos de la tabla.

<!-- Images. -->

