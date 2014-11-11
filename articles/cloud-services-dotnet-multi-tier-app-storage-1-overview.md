<properties linkid="develop-net-tutorials-multi-tier-web-site-1-overview" pageTitle="Azure Cloud Service Tutorial: ASP.NET MVC Web Role, Worker Role, Azure Storage Tables, Queues, and Blobs" metaKeywords="Azure tutorial, Azure storage tutorial, Azure multi-tier tutorial, MVC Web Role tutorial, Azure worker role tutorial, Azure blobs tutorial, Azure tables tutorial, Azure queues tutorial" description="Learn how to create a multi-tier app using ASP.NET MVC and Azure. The app runs in a cloud service, with web role and worker roles, and uses Azure storage tables, queues, and blobs." metaCanonical="" services="cloud-services,storage" documentationCenter=".NET" title="Azure Cloud Service Tutorial: ASP.NET MVC Web Role, Worker Role, Azure Storage Tables, Queues, and Blobs" authors="tdykstra,riande" solutions="" manager="wpickett" editor="mollybos" />

<tags ms.service="cloud-services" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="dotnet" ms.topic="article" ms.date="01/01/1900" ms.author="tdykstra,riande" />

# Tutorial del servicio en la nube de Azure Rol web de ASP.NET MVC, rol de trabajo y tablas, colas y blobs de almacenamiento de Azure - 1 de 5

En esta serie de tutoriales se muestra cómo crear e implementar una aplicación web ASP.NET MVC de niveles múltiples que se ejecuta en un servicio en la nube de Azure y usa tablas, colas y blobs de Almacenamiento de Azure. Puede descargar [la aplicación completa][la aplicación completa] de la Galería de código de MSDN o un [e-book][e-book] de una versión anterior de la Galería de e-books de TechNet.

A continuación puede ver un diagrama que muestra cómo interactúan las partes de la aplicación:

![procesamiento de mensajes de correo electrónico][procesamiento de mensajes de correo electrónico]

Esta es una serie de tutoriales de 5 partes. Si desea una introducción más rápida y sencilla a los servicios en la nube, las colas y los blobs, vea [Introducción a Servicios en la nube de Azure y ASP.NET][Introducción a Servicios en la nube de Azure y ASP.NET]. Como alternativa, puede ejecutar una aplicación de niveles múltiples en Websites y WebJobs; para obtener más información, vea [Introducción al SDK de Azure WebJobs][Introducción al SDK de Azure WebJobs].

En esta serie de tutoriales, se abordarán los siguientes temas:

-   Habilitar su equipo para desarrollar contenido de Azure mediante la instalación del SDK de Azure
-   Creación de un proyecto en la nube de Visual Studio con un rol web de ASP.NET MVC y dos roles de trabajo
-   Publicación del proyecto en la nube en un servicio en la nube de Azure
-   Uso del servicio de almacenamiento en cola de Azure para la comunicación entre niveles o entre roles de trabajo
-   Uso del servicio de almacenamiento de tablas de Azure como almacén de datos altamente escalable para datos no relacionales estructurados
-   Uso del servicio de blobs de Azure para almacenar archivos en la nube
-   Visualización y edición de tablas, colas y blobs de Azure mediante el Explorador de servidores de Visual Studio.
-   Uso de SendGrid para enviar correos electrónicos
-   Configuración del seguimiento y visualización de los datos de seguimiento
-   Escalado de una aplicación aumentando el número de instancias de rol de trabajo

## <a name="toc"></a>Tutoriales de la serie

Hay cinco tutoriales en la serie:

1.  **Introducción a la aplicación Servicio de correo electrónico de Azure** (este tutorial). Información detallada sobre la aplicación y su arquitectura. Puede omitir esta acción si solo quiere ver cómo implementar o quiere ver el código, y volver aquí más adelante para entender mejor la arquitectura.
2.  [Configuración e implementación de la aplicación Servicio de correo electrónico de Azure][Configuración e implementación de la aplicación Servicio de correo electrónico de Azure]. Cómo descargar la aplicación de ejemplo, configurarla, probarla localmente, implementarla y probarla en la nube.
3.  [Creación del rol web para la aplicación Servicio de correo electrónico de Azure][Creación del rol web para la aplicación Servicio de correo electrónico de Azure]. Cómo compilar los componentes MVC de la aplicación y probarlos localmente.
4.  [Creación del rol de trabajo A (programador de correo electrónico) para la aplicación Servicio de correo electrónico de Azure][Creación del rol de trabajo A (programador de correo electrónico) para la aplicación Servicio de correo electrónico de Azure]. Cómo desarrollar el componente de back-end que crea elementos de trabajo de cola para enviar correos electrónicos y probarlo localmente
5.  [Creación del rol de trabajo B (remitente de correo electrónico) para la aplicación Servicio de correo electrónico de Azure][Creación del rol de trabajo B (remitente de correo electrónico) para la aplicación Servicio de correo electrónico de Azure]. Cómo desarrollar el componente de back-end que procesa los elementos de trabajo de cola para enviar correos electrónicos y probarlo localmente

## Apartados de este tutorial

-   [Requisitos previos][Requisitos previos]
-   [Información general del front-end][Información general del front-end]
-   [Información general sobre el back-end][Información general sobre el back-end]
-   [Tablas de Azure][Tablas de Azure]
-   [Colas de Azure][Colas de Azure]
-   [Diagrama de datos][Diagrama de datos]
-   [Blobs de Azure][Blobs de Azure]
-   [Servicio en la nube de Azure frente a sitio web de Azure][Servicio en la nube de Azure frente a sitio web de Azure]
-   [Coste][Coste]
-   [Autenticación y autorización][Autenticación y autorización]
-   [Pasos siguientes][Pasos siguientes]

## Requisitos previos

Las instrucciones de estos tutoriales sirven para los productos siguientes:

-   Visual Studio 2013 con Update 2
-   Visual Studio 2013 Express para Web con Update 2

También necesita una suscripción de Azure. Puede crear una [cuenta de evaluación gratuita][cuenta de evaluación gratuita] o [activar los beneficios de suscripción a MSDN][activar los beneficios de suscripción a MSDN].

## <a name="frontend"></a>Información general del front-end

La aplicación es un servicio de listas de correo electrónico. El front-end incluye páginas web que usan los administradores del servicio para administrar las listas de correo electrónico.

(Las capturas de pantalla muestran el estilo de plantilla de Visual Studio 2012; el contenido es el mismo para Visual Studio 2013 pero el estilo es distinto).

![Página de índice de listas de correo][Página de índice de listas de correo]

![Página de índice de suscriptores][Página de índice de suscriptores]

También existe un conjunto de páginas utilizadas por los administradores para crear mensajes que se enviarán a una lista de correo electrónico.

![página de índice de mensajes][página de índice de mensajes]

![página de creación de mensajes][página de creación de mensajes]

Los usuarios del servicio son empresas que ofrecen a sus clientes la oportunidad de registrarse en una lista de correo en los sitios web de dichas empresas. Por ejemplo, un administrador configura una lista para los anuncios del Departamento de Historia de la Universidad Contoso. Cuando un alumno interesado en los anuncios del Departamento de Historia hace clic en un vínculo del sitio web de la Universidad Contoso, la universidad realiza una llamada de servicio web a la aplicación Servicio de correo electrónico de Azure. El método de servicio hace que se envíe un correo electrónico al cliente. Ese correo electrónico contiene un hipervínculo, de tal modo que cuando el destinatario hace clic en el vínculo aparece una página de bienvenida a la lista de anuncios del Departamento de Historia.

![correo electrónico de confirmación][correo electrónico de confirmación]

![página de bienvenida a la lista][página de bienvenida a la lista]

Todo correo electrónico enviado (a excepción de la confirmación de suscripción) incluye un hipervínculo de cancelación de la suscripción. Si el destinatario hace clic en el vínculo, aparece una página web en la que debe confirmar su intención de cancelar la suscripción.

![página para confirmar la cancelación de la suscripción][página para confirmar la cancelación de la suscripción]

Si el destinatario hace clic en el botón **Confirm**, aparece una página de confirmación de que esta persona se eliminó de la lista.

![Página de confirmación de la cancelación de la suscripción][Página de confirmación de la cancelación de la suscripción]

## <a name="backend"></a>Información general sobre el back-end

El front-end almacena las listas de correo electrónico y los mensajes que se enviarán a ellas en tablas de Azure. Cuando un administrador programa el envío de un mensaje, se agrega una fila a la tabla `message` con la fecha programada y otros datos como la línea del asunto. Un rol de trabajo examina periódicamente la tabla `message` en busca de mensajes pendientes de envío (este rol se denominará rol de trabajo A).

Cuando el rol de trabajo A encuentra un mensaje pendiente de envío, realiza las siguientes tareas:

-   Obtiene todas las direcciones de correo electrónico de la lista de correos electrónicos de destino.
-   Incluye en la tabla `message` la información necesaria para enviar cada correo electrónico.
-   Crea un elemento de trabajo de cola para cada correo electrónico pendiente de envío.

Un segundo rol de trabajo (rol de trabajo B) sondea la cola en busca de elementos de trabajo. Cuando encuentra un elemento de trabajo, lo procesa enviando el correo electrónico y, a continuación, lo elimina de la cola. En el siguiente diagrama se muestran estas relaciones.

![procesamiento de mensajes de correo electrónico][procesamiento de mensajes de correo electrónico]

Si se produce un error en el rol de trabajo B y hay que reiniciarlo, no se dejará de enviar ningún correo electrónico, ya que los elementos de trabajo de cola correspondientes a los correos electrónicos no se eliminan hasta que estos se envían. La aplicación también evita el envío de varios correos electrónicos si se produce un error en el rol de trabajo A y este debe reiniciarse.

![prevención de la duplicación de correos electrónicos][prevención de la duplicación de correos electrónicos]

El rol de trabajo B sondea la cola de suscripción en busca de elementos de trabajo incluidos allí por el método de servicio de la API web cuando se producen nuevas suscripciones. Cuando encuentra uno, envía un correo electrónico de confirmación.

![procesamiento de los mensajes de la cola de suscripción][procesamiento de los mensajes de la cola de suscripción]

## <a name="tables"></a>Tablas de Azure

La aplicación Servicio de correo electrónico de Azure almacena los datos en tablas de almacenamiento de Azure. Las tablas de Azure constituyen un almacén de datos NoSQL y no una base de datos relacional como la [Base de datos SQL de Azure][Base de datos SQL de Azure]. Las tablas de Azure son una buena opción en aquellos casos en que la eficiencia y la escalabilidad son más importantes que la normalización y la integridad relacional de los datos. Por ejemplo, en esta aplicación, un rol de trabajo agrega una fila cada vez que se crea un elemento de trabajo de cola y el otro rol recupera y actualiza una fila cada vez que se envía un correo electrónico, lo que podría convertirse en un cuello de botella del rendimiento si se utilizase una base de datos relacional. Además, las tablas de Azure resultan más económicas que SQL Azure. Para obtener más información acerca de las tablas de Azure, vea el [último tutorial de esta serie][Creación del rol de trabajo B (remitente de correo electrónico) para la aplicación Servicio de correo electrónico de Azure].

En la siguiente sección se describen los contenidos de las tablas de Azure utilizadas por la aplicación Servicio de correo electrónico de Azure. Más adelante en esta página, en la sección [Diagrama de datos del Servicio de correo electrónico de Azure][Diagrama de datos], encontrará un diagrama en el que se muestran las tablas y sus relaciones.

### Tabla mailinglist

La tabla `mailinglist` almacena información sobre las listas de correo y los suscriptores de las mismas. (Es recomendable usar solo letras minúsculas a la hora de poner nombre a las tablas de Azure).

En las tablas de Azure, las diferentes filas pueden presentar diferentes esquemas, y esta flexibilidad se usa habitualmente para almacenar en una sola tabla datos que requerirían varias tablas en una base de datos relacional. Por ejemplo, para almacenar los datos de una lista de correo en una base de datos SQL necesitaría tres tablas: una tabla `mailinglist` para almacenar información sobre la lista, una tabla `subscriber` para almacenar la información de los suscriptores y una tabla `mailinglistsubscriber` para asociar la lista de correo con los suscriptores y viceversa. En la tabla NoSQL de esta aplicación, todas esas funciones las realiza una única tabla denominada `mailinglist`.

En las tablas de Azure, cada fila tiene una *clave de partición* y una *clave de fila* que la identifica inequívocamente. La clave de partición divide la tabla de forma lógica en particiones. Dentro de una partición, la clave de fila identifica inequívocamente una fila. No hay índices secundarios, por lo que para garantizar la escalabilidad de la aplicación es importante diseñar las tablas de tal forma que siempre pueda consultar por clave de partición y por clave de fila.

La clave de partición de la tabla `mailinglist` es el nombre de la lista de correo.

La clave de fila de la tabla `mailinglist` puede ser una de estas dos cosas: la constante “mailinglist” o la dirección de correo electrónico del suscriptor. Las filas con la clave de fila “mailinglist” incluyen información sobre la lista de correo. Las filas que utilizan una dirección de correo electrónico como clave de fila contienen información sobre los suscriptores de la lista.

En otras palabras, las filas con la clave de fila “mailinglist” equivalen a una tabla `mailinglist` en una base de datos relacional. Mientras que las filas con una dirección de correo electrónico como clave de fila equivalen a una tabla `subscriber` y a una tabla de asociación `mailinglistsubscriber` en una base de datos relacional.

El hecho de poder utilizar una sola tabla para varios fines de esta manera mejora el rendimiento. En una base de datos relacional sería necesario leer tres tablas y ordenar y relacionar entre sí tres grupos de filas, lo que lleva tiempo. Aquí solo es necesario leer una tabla y las filas de esta se ordenan automáticamente según la clave de partición y la clave de fila.

En la siguiente cuadrícula se muestran las propiedades de las filas que contienen información sobre la lista de correo (clave de fila = “mailinglist”).

| Propiedad        | Tipo de datos | Descripción                                                                                                                                                                                                                                                                                                   |
|------------------|---------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| PartitionKey     | String        | Nombre de la lista: Identificador exclusivo para la lista de correo, por ejemplo: contoso1. Normalmente esta tabla se utiliza para recuperar toda la información de una lista de correo concreta, por lo que utilizar el nombre de la lista resulta eficiente a la hora de realizar la partición de la tabla. |
| RowKey           | String        | Constante “mailinglist”.                                                                                                                                                                                                                                                                                      |
| Descripción      | String        | Descripción de la lista de correo, por ejemplo: "Contoso University History Department announcements".                                                                                                                                                                                                        |
| FromEmailAddress | String        | Dirección que aparece en el campo “De” de los correos electrónicos enviados a esta lista, por ejemplo: "donotreply@contoso.edu".                                                                                                                                                                              |

En la siguiente cuadrícula se muestran las propiedades de las filas que contienen información sobre los suscriptores de la lista (clave de fila = dirección de correo electrónico).

| Propiedad      | Tipo de datos | Descripción                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                               |
|----------------|---------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| PartitionKey   | String        | Nombre de la lista: Nombre (identificador exclusivo) de la lista de correo, por ejemplo: contoso1.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                        |
| RowKey         | String        | Dirección de correo electrónico: Dirección de correo electrónico del suscriptor, por ejemplo: “student1@contoso.edu”.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                     |
| SubscriberGUID | String        | Se genera cuando se agrega la dirección de correo electrónico a la lista. Se utiliza en los vínculos de suscripción y cancelación de suscripción, por lo que resulta difícil suscribir la dirección de correo electrónico de otra persona o cancelar su suscripción.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                      
                                   Algunas consultas a las páginas web de suscripción y cancelación de suscripción solo especifican la propiedad PartitionKey y esta propiedad. Al realizar consultas a una partición sin utilizar la propiedad RowKey, se limita la escalabilidad de la aplicación, ya que estas consultas tardarán más tiempo a medida que aumente el tamaño de la lista de correo. Una opción para mejorar la escalabilidad es agregar filas de búsqueda con el valor de la propiedad SubscriberGUID en la propiedad RowKey. Por ejemplo, para cada dirección de correo electrónico una fila podría presentar el valor “email:student1@domain.com” en la propiedad RowKey y otra fila, el valor “guid:6f32b03b-90ed-41a9-b8ac-c1310c67b66a”. Esto resulta sencillo de implementar porque las transacciones atómicas por lotes de las filas de una partición son fáciles de codificar. Para obtener más información, consulte [Casos reales: Diseño de una estrategia de partición escalable para el almacenamiento de tablas de Azure][Casos reales: Diseño de una estrategia de partición escalable para el almacenamiento de tablas de Azure]  |
| Verified       | Boolean       | Cuando la fila se crea inicialmente para un nuevo suscriptor, el valor es “false”. Solo cambia a “true” cuando el nuevo suscriptor hace clic en el hipervínculo Confirm del correo electrónico de bienvenida, o si un administrador lo establece en “true”. Si se envía un mensaje a la lista mientras el valor de la propiedad Verified es “false” para uno de sus suscriptores, dicho suscriptor no recibe el correo electrónico.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                       |

En la siguiente lista se muestra un ejemplo de la apariencia de los datos en la tabla.

<table border="1">
<tr>
<th width="200" align="right" bgcolor="lightgray">
Partition Key

</th>
<td>
contoso1

</td>
</tr>
<tr>
<th align="right" bgcolor="lightgray">
Row Key

</th>
<td>
mailinglist

</td>
</tr>
<tr>
<th align="right" bgcolor="lightgray">
Descripción

</th>
<td>
Contoso University History Department announcements

</td>
</tr>
<tr>
<th align="right" bgcolor="lightgray">
FromEmailAddress

</th>
<td>
donotreply@contoso.edu

</td>
</tr>
</table>

------------------------------------------------------------------------

<table border="1">
<tr>
<th width="200" align="right" bgcolor="lightgray">
Partition Key

</th>
<td>
contoso1

</td>
</tr>
<tr>
<th align="right" bgcolor="lightgray">
Row Key

</th>
<td>
student1@domain.com

</td>
</tr>
<tr>
<th align="right" bgcolor="lightgray">
SubscriberGUID

</th>
<td>
6f32b03b-90ed-41a9-b8ac-c1310c67b66a

</td>
</tr>
<tr>
<th align="right" bgcolor="lightgray">
Verified

</th>
<td>
true

</td>
</tr>
</table>

------------------------------------------------------------------------

<table border="1">
<tr>
<th width="200" align="right" bgcolor="lightgray">
Partition Key

</th>
<td>
contoso1

</td>
</tr>
<tr>
<th align="right" bgcolor="lightgray">
Row Key

</th>
<td>
student2@domain.com

</td>
</tr>
<tr>
<th align="right" bgcolor="lightgray">
SubscriberGUID

</th>
<td>
01234567-90ed-41a9-b8ac-c1310c67b66a

</td>
</tr>
<tr>
<th align="right" bgcolor="lightgray">
Verified

</th>
<td>
false

</td>
</tr>
</table>

------------------------------------------------------------------------

<table border="1">
<tr>
<th width="200" align="right" bgcolor="lightgray">
Partition Key

</th>
<td>
fabrikam1

</td>
</tr>
<tr>
<th align="right" bgcolor="lightgray">
Row Key

</th>
<td>
mailinglist

</td>
</tr>
<tr>
<th align="right" bgcolor="lightgray">
Descripción

</th>
<td>
Fabrikam Engineering job postings

</td>
</tr>
<tr>
<th align="right" bgcolor="lightgray">
FromEmailAddress

</th>
<td>
donotreply@fabrikam.com

</td>
</tr>
</table>

------------------------------------------------------------------------

<table border="1">
<tr>
<th width="200" align="right" bgcolor="lightgray">
Partition Key

</th>
<td>
fabrikam1

</td>
</tr>
<tr>
<th align="right" bgcolor="lightgray">
Row Key

</th>
<td>
applicant1@domain.com

</td>
</tr>
<tr>
<th align="right" bgcolor="lightgray">
SubscriberGUID

</th>
<td>
76543210-90ed-41a9-b8ac-c1310c67b66a

</td>
</tr>
<tr>
<th align="right" bgcolor="lightgray">
Verified

</th>
<td>
true

</td>
</tr>
</table>
### Tabla message

La tabla `message` almacena información sobre los mensajes que se van a enviar a la lista de correo. Los administradores crean y editan filas en esta tabla mediante páginas web, y los roles de trabajo la utilizan para transmitir información sobre cada correo electrónico del rol de trabajo A al rol de trabajo B.

La clave de partición de la tabla message es la fecha en la que está previsto enviar el correo electrónico, en formato aaaa-mm-dd. De este modo, se optimiza la tabla para la consulta realizada con mayor frecuencia en relación a ella, que selecciona las filas cuyo valor para la propiedad `ScheduledDate` coincide con el día de hoy o una fecha anterior. Sin embargo, se crea un posible cuello de botella del rendimiento, ya que las tablas de almacenamiento de Azure ofrecen un rendimiento máximo de 500 entidades por segundo para una misma partición. Por cada correo electrónico que se va a enviar, la aplicación escribe una fila en la tabla `message`, lee una fila y elimina una fila. Por lo tanto, el tiempo mínimo necesario para procesar un millón de correos electrónicos programados para un único día es de al menos dos horas, con independencia de la cantidad de roles de trabajo que se agreguen para soportar el aumento de las cargas.

La clave de fila de la tabla `message` puede ser una de estas dos cosas: la constante “message” más una clave exclusiva para el mensaje denominada `MessageRef`, o bien el valor `MessageRef` más la dirección de correo electrónico del suscriptor. Las filas con una clave de fila que comienza por “message” incluyen información acerca del mensaje, como la lista de correo a la que se va a enviar y cuándo debe enviarse. Las filas con el valor `MessageRef` y una dirección de correo electrónico como clave de fila contienen toda la información necesaria para enviar un correo electrónico a esa dirección.

Las filas cuya clave de fila comienza por “message” equivalen a una tabla `message` de una base de datos relacional. Las filas cuya clave de fila se compone del valor `MessageRef` más una dirección de correo electrónico equivalen a una vista de consulta de combinación que contenga información de tipo ` mailinglist`, `message` y `subscriber`.

En la siguiente cuadrícula se muestran las propiedades de las filas de la tabla `message` que contienen información sobre el propio mensaje.

<table>
<colgroup>
<col width="33%" />
<col width="33%" />
<col width="33%" />
</colgroup>
<thead>
<tr class="header">
<th align="left">Propiedad</th>
<th align="left">Tipo de datos</th>
<th align="left">Descripción</th>
</tr>
</thead>
<tbody>
<tr class="odd">
<td align="left">PartitionKey</td>
<td align="left">String</td>
<td align="left">Fecha en que se prevé enviar el mensaje, en formato aaaa-mm-dd.</td>
</tr>
<tr class="even">
<td align="left">RowKey</td>
<td align="left">String</td>
<td align="left">Constante “message” más el valor <code data-inline="1">MessageRef</code>. El valor <code data-inline="1">MessageRef</code> es un valor exclusivo que se creó obteniendo el valor <code data-inline="1">Ticks</code> de <code data-inline="1">DateTime.Now</code> cuando se agregó la fila.<br /><br />Nota: las aplicaciones multiproceso de instancias múltiples con un alto volumen deben estar preparadas para soportar excepciones de duplicidad de la propiedad RowKey al usar “Ticks”. No se garantiza que el valor “Ticks” sea exclusivo.</td>
</tr>
<tr class="odd">
<td align="left">ScheduledDate</td>
<td align="left">Date</td>
<td align="left">Fecha en que está previsto enviar el mensaje. (Igual que <code data-inline="1">PartitionKey</code> pero en formato de fecha).</td>
</tr>
<tr class="even">
<td align="left">SubjectLine</td>
<td align="left">String</td>
<td align="left">Línea del asunto del correo electrónico.</td>
</tr>
<tr class="odd">
<td align="left">ListName</td>
<td align="left">String</td>
<td align="left">Lista a la que se va a enviar este mensaje.</td>
</tr>
<tr class="even">
<td align="left">Status</td>
<td align="left">String</td>
<td align="left"><ul>
<li>“Pending”: El rol de trabajo A aún no empezó a crear mensajes de cola para programar correos electrónicos.</li>
<li>“Queuing”: El rol de trabajo A empezó a crear mensajes de cola para programar correos electrónicos.</li>
<li>“Processing”: El rol de trabajo A creó elementos de trabajo de cola para todos los correos electrónicos de la lista, pero aún no se enviaron todos los correos.</li>
<li>“Completed”: El rol de trabajo B acabó de procesar todos los elementos de trabajo de cola (se enviaron todos los correos electrónicos). Las filas completadas se archivan en la tabla <code data-inline="1">messagearchive</code>, como se explica más adelante. Esperamos convertir esta propiedad en una <code data-inline="1">enum</code> en la próxima versión.</li>
</ul></td>
</tr>
</tbody>
</table>

Cuando el rol de trabajo A crea un mensaje de cola para un correo electrónico que va a enviarse a una lista, se agrega una fila de correo electrónico a la tabla `message`. Cuando el rol de trabajo B envía el correo electrónico, mueve la fila de correo electrónico a la tabla `messagearchive` y establece la propiedad `EmailSent` en `true`. Tras archivar todas las filas de correo electrónico de un mensaje con estado “Processing”, el rol de trabajo A establece el estado en “Completed” y mueve la fila `message` a la tabla `messagearchive`.

En la siguiente cuadrícula se muestran las propiedades de las filas de correo electrónico de la tabla `message`.

| Propiedad         | Tipo de datos | Descripción                                                                                                                 |
|-------------------|---------------|-----------------------------------------------------------------------------------------------------------------------------|
| PartitionKey      | String        | Fecha en que se prevé enviar el mensaje, en formato aaaa-mm-dd.                                                             |
| RowKey            | String        | Valor `MessageRef` y dirección de correo electrónico de destino incluida en la fila `subscriber` de la tabla `mailinglist`. |
| MessageRef        | Largo         | Igual que el componente `MessageRef` de la propiedad `RowKey`.                                                              |
| ScheduledDate     | Date          | Fecha programada incluida en la fila `message` de la tabla `message`. (Igual que `PartitionKey` pero en formato de fecha).  |
| SubjectLine       | String        | Línea del asunto de correo electrónico incluida en la fila `message` de la tabla `message`.                                 |
| ListName          | String        | Nombre de la lista de correo incluido en la tabla `mailinglist`.                                                            |
| From EmailAddress | String        | Dirección de correo electrónico del campo “De” incluida en la fila `mailinglist` de la tabla `mailinglist`.                 |
| EmailAddress      | String        | Dirección de correo electrónico incluida en la fila `subscriber` de la tabla `mailinglist`.                                 |
| SubscriberGUID    | String        | GUID del suscriptor incluido en la fila `subscriber` de la tabla `mailinglist`.                                             |
| EmailSent         | Boolean       | “False” indica que el correo electrónico aún no se envió, mientras que “true” indica que ya se envió.                       |

En estas filas hay datos redundantes, lo que suele evitarse en las bases de datos relacionales. Sin embargo, en este caso, algunas de las desventajas que supone la redundancia de datos se compensan con una mayor eficiencia de procesamiento y escalabilidad. Dado que todos los datos necesarios para enviar un correo electrónico se encuentran en una sola de estas filas, el rol de trabajo B solo tiene que leer una fila para enviar un correo electrónico a la hora de procesar un elemento de trabajo de la cola.

Es posible que se pregunte de dónde proviene el cuerpo del correo electrónico. Estas filas no incluyen referencias de blob para los archivos que contienen el cuerpo del correo electrónico, porque ese valor se deriva del valor `MessageRef`. Por ejemplo, si el valor `MessageRef` es “634852858215726983”, los blobs se denominan “634852858215726983.htm” y “634852858215726983.txt”.

En la siguiente lista se muestra un ejemplo de la apariencia de los datos en la tabla.

<table border="1">
<tr>
<th width="200" align="right" bgcolor="lightgray">
Partition Key

</th>
<td>
2012-10-15

</td>
</tr>
<tr>
<th align="right" bgcolor="lightgray">
Row Key

</th>
<td>
message634852858215726983

</td>
</tr>
<tr>
<th align="right" bgcolor="lightgray">
MessageRef

</th>
<td>
634852858215726983

</td>
</tr>
<tr>
<th align="right" bgcolor="lightgray">
ScheduledDate

</th>
<td>
2012-10-15

</td>
</tr>
<tr>
<th align="right" bgcolor="lightgray">
SubjectLine

</th>
<td>
New lecture series

</td>
</tr>
<tr>
<th align="right" bgcolor="lightgray">
ListName

</th>
<td>
contoso1

</td>
</tr>
<tr>
<th align="right" bgcolor="lightgray">
Status

</th>
<td>
Processing

</td>
</tr>
</table>

------------------------------------------------------------------------

<table border="1">
<tr>
<th width="200" align="right" bgcolor="lightgray">
Partition Key

</th>
<td>
2012-10-15

</td>
</tr>
<tr>
<th align="right" bgcolor="lightgray">
Row Key

</th>
<td>
634852858215726983student1@contoso.edu

</td>
</tr>
<tr>
<th align="right" bgcolor="lightgray">
MessageRef

</th>
<td>
634852858215726983

</td>
</tr>
<tr>
<th align="right" bgcolor="lightgray">
ScheduledDate

</th>
<td>
2012-10-15

</td>
</tr>
<tr>
<th align="right" bgcolor="lightgray">
SubjectLine

</th>
<td>
New lecture series

</td>
</tr>
<tr>
<th align="right" bgcolor="lightgray">
ListName

</th>
<td>
contoso1

</td>
</tr>
<tr>
<th align="right" bgcolor="lightgray">
FromEmailAddress

</th>
<td>
donotreply@contoso.edu

</td>
</tr>
<tr>
<th align="right" bgcolor="lightgray">
EmailAddress

</th>
<td>
student1@contoso.edu

</td>
</tr>
<tr>
<th align="right" bgcolor="lightgray">
SubscriberGUID

</th>
<td>
76543210-90ed-41a9-b8ac-c1310c67b66a

</td>
</tr>
<tr>
<th align="right" bgcolor="lightgray">
EmailSent

</th>
<td>
true

</td>
</tr>
</table>

------------------------------------------------------------------------

<table border="1">
<tr>
<th width="200" align="right" bgcolor="lightgray">
Partition Key

</th>
<td>
2012-10-15

</td>
</tr>
<tr>
<th align="right" bgcolor="lightgray">
Row Key

</th>
<td>
634852858215726983student2@contoso.edu

</td>
</tr>
<tr>
<th align="right" bgcolor="lightgray">
MessageRef

</th>
<td>
634852858215726983

</td>
</tr>
<tr>
<th align="right" bgcolor="lightgray">
ScheduledDate

</th>
<td>
2012-10-15

</td>
</tr>
<tr>
<th align="right" bgcolor="lightgray">
SubjectLine

</th>
<td>
New lecture series

</td>
</tr>
<tr>
<th align="right" bgcolor="lightgray">
ListName

</th>
<td>
contoso1

</td>
</tr>
<tr>
<th align="right" bgcolor="lightgray">
FromEmailAddress

</th>
<td>
donotreply@contoso.edu

</td>
</tr>
<tr>
<th align="right" bgcolor="lightgray">
EmailAddress

</th>
<td>
student2@contoso.edu

</td>
</tr>
<tr>
<th align="right" bgcolor="lightgray">
SubscriberGUID

</th>
<td>
12345678-90ed-41a9-b8ac-c1310c679876

</td>
</tr>
<tr>
<th align="right" bgcolor="lightgray">
EmailSent

</th>
<td>
true

</td>
</tr>
</table>

------------------------------------------------------------------------

<table border="1">
<tr>
<th width="200" align="right" bgcolor="lightgray">
Partition Key

</th>
<td>
2012-11-15

</td>
</tr>
<tr>
<th align="right" bgcolor="lightgray">
Row Key

</th>
<td>
message124852858215726999

</td>
</tr>
<tr>
<th align="right" bgcolor="lightgray">
MessageRef

</th>
<td>
124852858215726999

</td>
</tr>
<tr>
<th align="right" bgcolor="lightgray">
ScheduledDate

</th>
<td>
2012-11-15

</td>
</tr>
<tr>
<th align="right" bgcolor="lightgray">
SubjectLine

</th>
<td>
New job postings

</td>
</tr>
<tr>
<th align="right" bgcolor="lightgray">
ListName

</th>
<td>
fabrikam

</td>
</tr>
<tr>
<th align="right" bgcolor="lightgray">
Status

</th>
<td>
Pending

</td>
</tr>
</table>

### tabla messagearchive

Una estrategia para asegurarse de que las consultas se ejecuten con eficiencia, especialmente si tiene que realizar búsquedas en campos distintos de `PartitionKey` y `RowKey`, es limitar el tamaño de la tabla. La consulta del rol de trabajo A que comprueba si se enviaron todos los correos electrónicos correspondientes a un mensaje necesita encontrar filas de correo electrónico en la tabla `message` cuyo valor para `EmailSent` sea “false”. El valor `EmailSent` no se incluye en las propiedades PartitionKey ni RowKey, por lo que esta no sería una consulta eficiente para un mensaje con una gran cantidad de filas de correo electrónico. En consecuencia, la aplicación mueve las filas de correo electrónico a la tabla `messagearchive` a medida que se envían los correos electrónicos. De este modo, la consulta para comprobar si se enviaron todos los correos electrónicos correspondientes a un mensaje solo tiene que consultar las propiedades `PartitionKey` y `RowKey` de la tabla message, porque si encuentra alguna fila de correo electrónico para un mensaje, quiere decir que hay correos electrónicos sin enviar y el mensaje no se puede marcar como `Complete`.

El esquema de filas de la tabla `messagearchive` es idéntico al de la tabla `message`. Dependiendo de lo que desee hacer con estos datos de archivo, puede limitar su tamaño y gastos reduciendo el número de propiedades almacenadas para cada fila y eliminando las filas que tengan más de una cierta antigüedad.

## <a name="queues"></a>Colas de Azure

Las colas de Azure facilitan la comunicación entre los niveles de esta aplicación de niveles múltiples, así como entre los roles de trabajo del nivel de back-end.
Se usan para la comunicación entre el rol de trabajo A y el rol de trabajo B a fin de hacer la aplicación escalable. El rol de trabajo A podría crear una fila en la tabla message para cada correo electrónico, y el rol de trabajo B podría buscar en la tabla filas que indiquen que hay correos electrónicos no enviados, pero no se podrían agregar instancias adicionales del rol de trabajo B para repartir el trabajo. El problema que supone el uso de filas de tabla para coordinar el trabajo entre el rol de trabajo A y el rol de trabajo B es que no existe manera alguna de garantizar que una única instancia de rol de trabajo se ocupe del procesamiento de una fila de tabla concreta. Las colas le ofrecen esta garantía. Cuando una instancia de rol de trabajo procesa un elemento de trabajo de una cola, el servicio de colas se asegura de que ninguna otra instancia de rol de trabajo pueda procesar el mismo elemento de trabajo. Esta función exclusiva de las colas de Azure facilita el uso compartido de una carga de trabajo por parte de varias instancias de un rol de trabajo.

Azure también ofrece el servicio de colas Bus de servicio. Para obtener más información acerca de las colas de Almacenamiento de Azure y las colas de Bus de servicio, vea el [último tutorial de esta serie][Creación del rol de trabajo B (remitente de correo electrónico) para la aplicación Servicio de correo electrónico de Azure].

La aplicación Servicio de correo electrónico de Azure usa dos colas, denominadas `AzureMailQueue` y `AzureMailSubscribeQueue`.

### AzureMailQueue

La cola `AzureMailQueue` coordina el envío de correos electrónicos a las listas de correo electrónico. El rol de trabajo A coloca un elemento de trabajo en la cola por cada correo electrónico pendiente de envío, mientras que el rol de trabajo B procesa dicho elemento de trabajo enviando el correo electrónico.

Los elementos de trabajo de cola contienen una cadena delimitada por comas que consta de la fecha programada del mensaje (clave de partición de la tabla `message`) y los valores `MessageRef` y `EmailAddress` (clave de fila de la tabla `message`), además de una marca que indica si el elemento se creó después de que se produjera un error en el rol de trabajo y se reiniciara, por ejemplo:

      2012-10-15,634852858215726983,student1@contoso.edu,0

El rol de trabajo B usa estos valores para buscar la fila en la tabla `message` que contiene toda la información necesaria para enviar el correo electrónico. Si la marca de reinicio indica que se produjo un reinicio, el rol de trabajo B se asegura de que no se enviara ya el correo electrónico antes de enviarlo.

Cuando se producen picos de tráfico, el servicio en la nube puede configurarse de nuevo para crear varias instancias del rol de trabajo B, de tal forma que cada una de ellas de manera independiente pueda procesar elementos de trabajo de la cola.

### AzureMailSubscribeQueue

La cola `AzureMailSubscribeQueue` coordina el envío de correos electrónicos de confirmación de suscripción. El método de servicio recibe una llamada y coloca un elemento de trabajo en la cola. El rol de trabajo B procesa el elemento de trabajo enviando el correo electrónico de confirmación de suscripción.

Los elementos de trabajo de cola contienen el GUID del suscriptor. Este valor identifica inequívocamente una dirección de correo electrónico y la lista a la que se va a suscribir, que es todo lo que necesita saber el rol de trabajo B para enviar un correo electrónico de confirmación. Como se explicó antes, esto requiere una consulta en un campo distinto de `PartitionKey` o`RowKey`, lo que resulta ineficiente. Para hacer más escalable la aplicación, habría que reestructurar la tabla `mailinglist` para incluir el GUID del suscriptor en la `RowKey`.

## <a name="datadiagram"></a><span class="short-header">Diagrama de datos</span>Diagrama de datos del Servicio de correo electrónico de Azure

En el siguiente diagrama se muestran las tablas y colas, así como sus relaciones.

![diagrama de datos de la aplicación Servicio de correo electrónico de Azure][diagrama de datos de la aplicación Servicio de correo electrónico de Azure]

## <a name="blobs"></a>Blobs de Azure

Blob son las siglas de “binary large object” (objeto binario de gran tamaño). El servicio de blobs de Azure ofrece un mecanismo para cargar y almacenar archivos en la nube. Para obtener más información acerca de los blobs de Azure, vea el [último tutorial de esta serie][Creación del rol de trabajo B (remitente de correo electrónico) para la aplicación Servicio de correo electrónico de Azure].

Los administradores del Servicio de correo electrónico de Azure ponen el cuerpo de los correos electrónicos en formato HTML en un archivo *.htm* y en texto sin formato en un archivo *.txt*. Cuando programan un correo electrónico, cargan estos archivos en la página web **Create Message** y, a continuación, el controlador ASP.NET MVC de la página los almacena en un blob de Azure.

Los blobs se almacenan en contenedores de blobs, al igual que los archivos se almacenan en carpetas. La aplicación Servicio de correo electrónico de Azure utiliza un único contenedor de blobs, llamado **azuremailblobcontainer**. El nombre de los blobs incluidos en el contenedor se obtiene combinando el valor “MessageRef” con la extensión del archivo, por ejemplo: 634852858215726983.htm y 634852858215726983.txt.

Ya que tanto los mensajes HTML como de texto sin formato son básicamente cadenas, podríamos haber diseñado la aplicación para almacenar el cuerpo de los mensajes de correo electrónico en las propiedades de cadena de la tabla `Message` en lugar de en blobs. Sin embargo, existe un límite de 64 K en el tamaño de las propiedades de las filas de tabla, por lo que el uso de blobs evita aplicar ese límite de tamaño al cuerpo de los correos electrónicos. (64 K es el tamaño total máximo que puede tener una propiedad; tras dejar espacio para la sobrecarga de codificación, el tamaño máximo de las cadenas que se almacenan en las propiedades realmente se acerca a los 48 K).

## <a name="wawsvswacs"></a>Servicio en la nube de Azure frente a sitio web de Azure

El Servicio de correo electrónico de Azure está configurado para que el front-end y el back-end se ejecuten en un servicio en la nube de Azure.

![información general sobre la arquitectura de la aplicación][información general sobre la arquitectura de la aplicación]

Una arquitectura alternativa consiste en ejecutar el front-end en un sitio web de Azure.

![arquitectura alternativa de la aplicación][arquitectura alternativa de la aplicación]

Otra alternativa es ejecutar el front-end en un sitio web de Azure y usar la característica WebJobs para ejecutar el back-end en los mismos servidores que ejecutan el front-end. Para obtener más información, consulte [Introducción al SDK de Azure WebJobs][Introducción al SDK de Azure WebJobs].

## <a name="cost"></a>Coste

En esta sección se incluye información general abreviada sobre los costes asociados a la ejecución de la aplicación de ejemplo en Azure; las tarifas que se presentan eran las aplicables cuando se publicó inicialmente este tutorial en diciembre de 2012. Antes de tomar ninguna decisión empresarial basada en los costes, debe comprobar las tarifas actuales en las siguientes páginas web:

-   [Calculadora de precios de Azure][Calculadora de precios de Azure]
-   [SendGrid Azure][SendGrid Azure]

Los costes dependen del número de instancias de rol web y de trabajo que decida mantener. Para optar al [contrato de nivel de servicio que garantiza una disponibilidad del 99,95% de los servicios en la nube de Azure][contrato de nivel de servicio que garantiza una disponibilidad del 99,95% de los servicios en la nube de Azure], debe implementar al menos dos instancias de cada rol. Una de las razones por las que debe disponer de al menos dos instancias de cada rol es que las máquinas virtuales que ejecutan la aplicación se reinician aproximadamente dos veces al mes para implementar las actualizaciones del sistema operativo. (Para obtener más información acerca de las actualizaciones del sistema operativo, consulte el blog sobre el [reinicio de las instancias de rol debido a actualizaciones del sistema operativo][reinicio de las instancias de rol debido a actualizaciones del sistema operativo] [en inglés]).

El trabajo realizado por los dos roles de trabajo de este ejemplo no es crítico en el tiempo, por lo que no es necesario el contrato de nivel de servicio que garantiza una disponibilidad del 99,95%. En consecuencia, es posible ejecutar una única instancia de cada rol de trabajo, siempre y cuando esta pueda asumir toda la carga de trabajo. La instancia de rol web está sujeta a limitación temporal, es decir, los usuarios esperan que el sitio web esté siempre operativo, por lo que cualquier aplicación de producción debería contar con dos instancias del rol web como mínimo.

En la siguiente tabla se muestra cuál sería el coste de la arquitectura predeterminada de la aplicación de ejemplo Servicio de correo electrónico de Azure para una carga de trabajo mínima. Los costes indicados se basan en el uso de una máquina virtual de tamaño extrapequeño (compartida). Al crear un proyecto en la nube de Visual Studio, de manera predeterminada se utiliza una máquina virtual de tamaño pequeño, que resulta unas seis veces más costosa que una de tamaño extrapequeño.

<table border="1">
<tr bgcolor="lightgray">
<th>
Componente o servicio

</th>
<th>
Tarifa

</th>
<th>
Coste mensual

</th>
</tr>
<tr>
<td>
Rol web

</td>
<td>
2 instancias a 0,02 $/hora para las instancias extrapequeñas

</td>
<td>
29,00 $

</td>
</tr>
<tr>
<td>
Rol de trabajo A (programa los correos electrónicos que se van a enviar)

</td>
<td>
1 instancia a 0,02 $/hora para las instancias extrapequeñas

</td>
<td>
14,50 $

</td>
</tr>
<tr>
<td>
Rol de trabajo B (envía los correos electrónicos)

</td>
<td>
1 instancia a 0,02 $/hora para las instancias extrapequeñas

</td>
<td>
14,50 $

</td>
</tr>
<tr>
<td>
Transacciones de almacenamiento de Azure

</td>
<td>
1 millón de transacciones al mes a 0,10 $/millón (Cada consulta se considera una transacción; el rol de trabajo A envía continuamente consultas a las tablas en busca de mensajes pendientes de envío. La aplicación también está configurada para escribir datos diagnósticos en el almacenamiento de Azure, y cada acción de escritura se considera una transacción).

</td>
<td>
0,10 $

</td>
</tr>
<tr>
<td>
Almacenamiento localmente redundante de Azure

</td>
<td>
2,33 $ por 25 GB (Incluye almacenamiento para tablas de aplicaciones y datos diagnósticos).

</td>
<td>
2,33 $

</td>
</tr>
<tr>
<td>
Ancho de banda

</td>
<td>
5 GB de salida son gratuitos

</td>
<td>
Gratuito

</td>
</tr>
<tr>
<td>
SendGrid

</td>
<td>
Los clientes de Azure pueden enviar 25.000 correos electrónicos al mes de manera gratuita

</td>
<td>
Gratuito

</td>
</tr>
<tr>
<td colspan="2">
Total

</td>
<td>
60,43 $

</td>
</tr>
</table>
Como puede ver, las instancias de rol son un componente importante del coste total. Las instancias de rol generan gastos incluso aunque estén paradas; para que no generen gastos debe eliminarlas. Una manera de ahorrar costes sería mover todo el código del rol de trabajo A y del rol de trabajo B a un solo rol de trabajo. En estos tutoriales decidimos implementar dos instancias de rol de trabajo para simplificar el escalado horizontal. El trabajo que realiza el rol de trabajo B se coordina con el servicio de colas de Azure, lo que implica que el rol de trabajo B se puede escalar horizontalmente simplemente aumentando el número de instancias de rol. (El rol de trabajo B es el factor que limita las condiciones de carga elevada). El trabajo que realiza el rol de trabajo A no se coordina mediante colas, por lo que no es posible ejecutar varias instancias de este rol. Si los dos roles de trabajo se combinaran y usted deseara habilitar el escalado horizontal, tendría que implementar un mecanismo para garantizar que las tareas del rol de trabajo A solo se ejecutaran en una instancia. (Un mecanismo de este tipo lo ofrece [CloudFx][CloudFx]. Consulte el [ejemplo de WorkerRole.cs (en inglés)][ejemplo de WorkerRole.cs (en inglés)]).

También es posible mover todo el código de los dos roles de trabajo al rol web para que todas las tareas se ejecuten en este. Sin embargo, no es posible o no se considera fiable realizar tareas en segundo plano en ASP.NET, además de que esta arquitectura complicaría la escalabilidad. Para obtener más información, consulte [The Dangers of Implementing Recurring Background Tasks In ASP.NET][The Dangers of Implementing Recurring Background Tasks In ASP.NET]. Consulte también [How to Combine a Worker and Web Role in Azure][How to Combine a Worker and Web Role in Azure] y [Combining Multiple Azure Worker Roles into an Azure Web Role][Combining Multiple Azure Worker Roles into an Azure Web Role]. Si quisiera seguir esta dirección, una solución mejor sería [ejecutar en un sitio web de Azure y usar la característica WebJobs para tareas de back-end][ejecutar en un sitio web de Azure y usar la característica WebJobs para tareas de back-end].

Otra arquitectura alternativa que reduciría el coste consistiría en utilizar el [bloque de escalado automático de la aplicación][bloque de escalado automático de la aplicación] para implementar automáticamente los roles de trabajo únicamente durante los períodos programados y eliminarlos cuando finalice el trabajo. Para obtener más información sobre la autoescala, consulte [el último tutorial de esta serie][Creación del rol de trabajo B (remitente de correo electrónico) para la aplicación Servicio de correo electrónico de Azure].

En el futuro es posible que Azure ofrezca un mecanismo de notificación de los reinicios programados, lo que le permitiría ejecutar una instancia de rol web adicional únicamente durante el reinicio. No optaría al contrato de nivel de servicio que garantiza una disponibilidad del 99,95%, pero podría reducir los costes a casi la mitad y garantizar que la aplicación web siga estando disponible durante el intervalo de reinicio.

## <a name="auth"></a>Autenticación y autorización

En una aplicación de producción habría que implementar un mecanismo de autenticación y autorización, como [ASP.NET Identity][ASP.NET Identity] para el front-end web de ASP.NET MVC, incluido el método de servicio de la API web de ASP.NET. También existen otras opciones, como el uso de un secreto compartido, para proteger el método de servicio de la API web. La funcionalidad de autenticación y autorización se omitió en la aplicación de ejemplo para facilitar la configuración e implementación de esta.

## <a name="nextsteps"></a>Pasos siguientes

En el [siguiente tutorial][Configuración e implementación de la aplicación Servicio de correo electrónico de Azure], aprenderá a descargar el proyecto de ejemplo, configurar el entorno de implementación, configurar el proyecto para su entorno y probar el proyecto de manera local y en la nube. En los tutoriales del 3 al 5 verá cómo compilar el proyecto desde cero.

Podrá encontrar vínculos a recursos adicionales para trabajar con tablas, colas y blobs de Almacenamiento de Azure en el [último tutorial de la serie][último tutorial de la serie].

<div><a href="/es-es/develop/net/tutorials/multi-tier-web-site/2-download-and-run/" class="site-arrowboxcta download-cta">Tutorial 2</a></div>

  [la aplicación completa]: http://code.msdn.microsoft.com/Windows-Azure-Multi-Tier-eadceb36
  [e-book]: http://social.technet.microsoft.com/wiki/contents/articles/11608.e-book-gallery-for-microsoft-technologies.aspx#ASPNETMultiTierWindowsAzureApplicationUsingStorageTablesQueuesandBlobs
  [procesamiento de mensajes de correo electrónico]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-overview/mtas-worker-roles-a-and-b.png
  [Introducción a Servicios en la nube de Azure y ASP.NET]: /es-es/documentation/articles/cloud-services-dotnet-get-started/
  [Introducción al SDK de Azure WebJobs]: /es-es/documentation/articles/websites-dotnet-webjobs-sdk-get-started/
  [Configuración e implementación de la aplicación Servicio de correo electrónico de Azure]: /es-es/develop/net/tutorials/multi-tier-web-site/2-download-and-run/
  [Creación del rol web para la aplicación Servicio de correo electrónico de Azure]: /es-es/develop/net/tutorials/multi-tier-web-site/3-web-role/
  [Requisitos previos]: #prerequisites
  [Información general del front-end]: #frontend
  [Información general sobre el back-end]: #backend
  [Tablas de Azure]: #tables
  [Colas de Azure]: #queues
  [Diagrama de datos]: #datadiagram
  [Blobs de Azure]: #blobs
  [Servicio en la nube de Azure frente a sitio web de Azure]: #wawsvswacs
  [Coste]: #cost
  [Autenticación y autorización]: #auth
  [Pasos siguientes]: #nextsteps
  [cuenta de evaluación gratuita]: /es-es/pricing/free-trial/
  [activar los beneficios de suscripción a MSDN]: /es-es/pricing/member-offers/msdn-benefits/
  [Página de índice de listas de correo]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-overview/mtas-mailing-list-index-page.png
  [Página de índice de suscriptores]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-overview/mtas-subscribers-index-page.png
  [página de índice de mensajes]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-overview/mtas-message-index-page.png
  [página de creación de mensajes]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-overview/mtas-message-create-page.png
  [correo electrónico de confirmación]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-overview/mtas-subscribe-email.png
  [página de bienvenida a la lista]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-overview/mtas-subscribe-confirmation-page.png
  [página para confirmar la cancelación de la suscripción]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-overview/mtas-unsubscribe-query-page.png
  [Página de confirmación de la cancelación de la suscripción]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-overview/mtas-unsubscribe-confirmation-page.png
  [prevención de la duplicación de correos electrónicos]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-overview/mtas-message-processing.png
  [procesamiento de los mensajes de la cola de suscripción]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-overview/mtas-subscribe-diagram.png
  [Base de datos SQL de Azure]: http://msdn.microsoft.com/es-es/library/windowsazure/ee336279.aspx
  [Casos reales: Diseño de una estrategia de partición escalable para el almacenamiento de tablas de Azure]: http://msdn.microsoft.com/es-es/library/windowsazure/hh508997.aspx
  [diagrama de datos de la aplicación Servicio de correo electrónico de Azure]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-overview/mtas-datadiagram.png
  [información general sobre la arquitectura de la aplicación]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-overview/mtas-architecture-overview.png
  [arquitectura alternativa de la aplicación]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-overview/mtas-alternative-architecture.png
  [Calculadora de precios de Azure]: http://www.windowsazure.com/es-es/pricing/calculator/
  [SendGrid Azure]: http://sendgrid.com/windowsazure.html
  [contrato de nivel de servicio que garantiza una disponibilidad del 99,95% de los servicios en la nube de Azure]: https://www.windowsazure.com/es-es/support/legal/sla/ "contrato de nivel de servicio"
  [reinicio de las instancias de rol debido a actualizaciones del sistema operativo]: http://blogs.msdn.com/b/kwill/archive/2012/09/19/role-instance-restarts-due-to-os-upgrades.aspx
  [CloudFx]: http://nuget.org/packages/Microsoft.Experience.CloudFx "CloudFX"
  [The Dangers of Implementing Recurring Background Tasks In ASP.NET]: http://haacked.com/archive/2011/10/16/the-dangers-of-implementing-recurring-background-tasks-in-asp-net.aspx
  [How to Combine a Worker and Web Role in Azure]: http://www.31a2ba2a-b718-11dc-8314-0800200c9a66.com/2010/12/how-to-combine-worker-and-web-role-in.html
  [Combining Multiple Azure Worker Roles into an Azure Web Role]: http://www.31a2ba2a-b718-11dc-8314-0800200c9a66.com/2012/02/combining-multiple-azure-worker-roles.html
  [ejecutar en un sitio web de Azure y usar la característica WebJobs para tareas de back-end]: http://go.microsoft.com/fwlink/?LinkId=390226
  [bloque de escalado automático de la aplicación]: /es-es/develop/net/how-to-guides/autoscaling/
  [ASP.NET Identity]: http://asp.net/identity
  [último tutorial de la serie]: /es-es/develop/net/tutorials/multi-tier-web-site/5-worker-role-b/#nextsteps
