## <a name="what-is"></a>Qué son los Servicios móviles

Servicios móviles es una oferta de servicio de Azure diseñada para facilitar la creación de aplicaciones móviles altamente funcionales usando Azure. Servicios móviles reúne un conjunto de servicios de Azure que habilitan las capacidades back-end para sus aplicaciones. Servicios móviles ofrece las siguientes posibilidades en Azure para dar brindar apoyo a sus aplicaciones: 

+ Aprovisionamiento y administración sencillos de las tablas para almacenar datos de aplicaciones. 
+ Integración con los servicios de notificación para entregar notificaciones de inserción a su aplicación.
+ Integración con proveedores de identidad conocidos para la autenticación.
+ Control detallado sobre la autorización del acceso a las tablas.
+ Lógica de negocios personalizada en el servidor.
+ Integración con otros servicios en la nube.
+ Compatibilidad con la escalación de una instancia de servicio móvil.
+ Supervisión y registro del servicio.

Servicios móviles proporciona una biblioteca de clientes para cada plataforma compatible, lo que facilita aún más el desarrollo de aplicaciones en su plataforma que utiliza Servicios móviles.

## <a name="concepts"> </a>Conceptos

A continuación se presentan características y conceptos importantes en los Servicios móviles:

<!--![1][1]-->

+ **.NET backend:** este tipo de servicio móvil se implementa como un proyecto de API web de ASP.NET. El código de back-end se implementa como controladores de WebAPI.
+ **Clave de aplicación:**  un valor único generado por servicios móviles, que se distribuye con su aplicación y se presenta en las solicitudes que genera el cliente. Aunque es útil para limitar el acceso al servicio móvil desde clientes aleatorios, esta clave no es segura y no debería usarse para autenticar a los usuarios de la aplicación.    
+ **Token de acceso:** el token de acceso que generan los servicios móviles cuando se autentica un usuario.
+ **Proveedor de identidades:** un servicio externo, como Facebook, Twitter, Google, Cuenta Microsoft o Azure Active Directory, que se usa para autenticar a los usuarios de su servicio móvil.
+ **Back-end de JavaScript:** este tipo de servicio móvil se implementa como un servicio de node.js. El código del back-end se escribe como JavaScript.
+ **Permisos:** el nivel de autenticación mínimo necesario para invocar una operación de tabla o API personalizada.
+ **Notificación de inserción:** mensaje iniciado por el servicio que se envía a un dispositivo o usuario registrados mediante los centros de notificación de Azure.
+ **Escala:** la capacidad de agregar, por un coste adicional, más potencia de procesamiento, rendimiento y almacenamiento a medida que su aplicación se hace más conocida.
+ **Trabajo programado:** código personalizado que se ejecuta en una programación predeterminada o a petición.
+ **Script del servidor:** código de lógica de negocios personalizado en un servicio móvil de back-end de JavaScript. Este código se registra en una operación de tabla (read, insert, update, delete), una API personalizada o un trabajo programado.
+ **Tabla:** los datos de usuario se almacenan en tablas de una base de datos de SQL. Puede crear estas tablas en el Portal de administración.


<!-- Images. -->


  

<!--HONumber=47-->
