<properties 
	pageTitle="Conectores de datos de aplicaciones de API de Microsoft Azure | Microservicio de aplicaciones de API" 
	description="Aprenda a crear aplicaciones de API de conector de datos de Microsoft Azure y agregue la aplicación de API microservicios de la aplicación lógica" 
	services="app-service\logic" 
	documentationCenter="" 
	authors="MSFTMan" 
	manager="dwrede" 
	editor=""/>

<tags
	ms.service="app-service-logic" 
	ms.workload="data" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="03/31/2015" 
	ms.author="deonhe"/>


# Conectores de datos del Servicio de aplicaciones de Microsoft Azure


## ¿Qué es un conector de aplicación de API de aplicaciones y servicios de datos
Los conectores de aplicaciones y servicios de datos son "aplicaciones de API" que se pueden conectar a una amplia gama de aplicaciones de servicios de datos y realizar varias *acciones* en nombre del usuario autenticado. La mayoría de estos conectores también puede configurarse con un *desencadenador*. Los desencadenadores son eventos (similares a los eventos de .NET Framework) que pueden configurarse en algunos conectores para iniciar un flujo de trabajo.

Por ejemplo, se puede configurar una instancia del conector Dropbox con un desencandenador de *nuevo archivo* donde dicho desencadenador se ejecutará cada vez que un nuevo archivo se agregue a la cuenta de Dropbox que se está supervisando. A continuación, este desencadenador se puede configurar para iniciar una *acción*  que *obtendrá* la operación en el archivo y lo cargará en una lista de SharePoint local.


A continuación figura una breve descripción de cada uno de los conectores de aplicaciones y servicios de datos que están disponibles en la galería de Azure:

<table>
<tr>
<th> Nombre</th>
<th> Descripción</th>
<th> Desencadenadores</th>
<th>Acciones</th>

<tr>
<td>Servicios multimedia de Azure
<td>El conector Servicios multimedia de Azure permite crear flujos de trabajo multimedia de extremo a extremo con codificación, empaquetado y distribución flexibles y escalables. También puede cargar, almacenar, codificar y hacer streaming de contenido de audio o vídeo para entrega de streaming en vivo y a petición a una amplia gama de extremos de TV, PC y dispositivos móviles.
<td>Ninguna
<td>Ninguna 
</tr>

<tr>
<td>Bus de servicio
<td>El conector de Bus de servicio de Azure le permite enviar mensajes desde entidades de Bus de servicio, como colas y temas, y recibir mensajes de entidades de Bus de servicio, como colas y suscripciones.
<td>Mensajes nuevos
<td>Enviar mensaje
</tr>
<tr>
<td>Tabla de almacenamiento de Azure
<td>Conector de tabla de almacenamiento de Azure le permite conectarse a la tabla de almacenamiento de Azure y realizar diversas acciones, como obtener entidades, consultar entidades, insertar entidades, actualizar entidades y eliminar entidades. También cuenta con un desencadenador para recuperar datos.
<td>Nueva entidad
<td><li>Obtener entidad
	<li>Insertar entidad
	<li>Eliminar entidad
	<li>Actualizar entidad
	<li>Consultar

</tr>

<tr>
<td>Box
<td>El conector Box le permite conectarse a Box y realizar diversas acciones en los archivos. 
<td>Nuevos archivos agregados
<td><li>Cargar archivo
<li>Obtener archivo
<li>Eliminar archivo
<li>Enumerar archivos
</tr>

<tr>
<td>DB2
<td>El conector de DB2 le permite conectarse a una base de datos DB2 de IBM, de forma local y en una máquina virtual de Azure con un sistema operativo Windows. Puede asignar las operaciones API web y API de OData a comandos de lenguaje de consulta estructurado de Informix. Cuando se utiliza de forma local, puede usarse la VPN o Azure ExpressRoute. Este conector también incluye el cliente de Microsoft para DRDA para conectarse a un servidor de Informix a través de una red TCP/IP.
<td>Ninguna
<td><li>Seleccionar la tabla
<li>Insertar
<li>Actualizar
<li>Eliminar
<li>Instrucción personalizada
</tr>

<tr>
<td>Dropbox
<td>El conector Dropbox le permite conectarse a Dropbox y realizar diversas acciones como cargar archivos, obtener archivos, eliminar archivos y enumerar archivos. También cuenta con un desencadenador para recuperar archivos.
<td>Nuevos archivos agregados
<td><li>Cargar archivo
<li>Obtener archivo
<li>Eliminar archivo
<li>Enumerar archivos
</tr>

<tr>
<td>HDInsight
<td>Un conector HDInsight le permite crear un clúster de Hadoop en Azure y enviar trabajos de Hadoop diferentes como Hive, Pig, MapReduce y MapReduce de streaming. Mediante este conector puede hacer girar un clúster, enviar un trabajo y esperar a que el trabajo finalice.
<td>Ninguna
<td><li>Crear clúster
		<li>Esperar a que se cree el clúster
		<li>Enviar trabajo de Pig
		<li>Enviar trabajo de Hive
		<li>Enviar trabajo de MapReduce
		<li>Esperar la finalización del trabajo
		<li>Eliminar clúster
<li>Enviar trabajo de MapReduce
<li>Enviar trabajo de MapReduce de streaming
</tr>

<tr>
<td>Informix
<td>El conector de Informix se conecta a una base de datos Informix de IBM, de forma local y en una máquina virtual de Azure con un sistema operativo Windows. Puede asignar las operaciones API web y API de OData a comandos de lenguaje de consulta estructurado de Informix. Cuando se utiliza de forma local, puede usarse la VPN o Azure ExpressRoute. Este conector también incluye el cliente de Microsoft para DRDA para conectarse a un servidor de Informix a través de una red TCP/IP.
<td>Ninguna
<td><li>Seleccionar la tabla
<li>Insertar
<li>Actualizar
<li>Eliminar
<li>Instrucción personalizada
</tr>

<tr>
<td>Microsoft SQL
<td>Un conector de Microsoft SQL le permite crear y modificar entradas de Microsoft SQL Server y tablas de Base de datos SQL de Azure.
<td>Datos de sondeo
<td><li>Insertar en tabla
	<li>Actualizar tabla
	<li>Seleccionar en tabla
	<li>Eliminar de tabla
	<li>Llamar a procedimiento almacenado
</tr>

<tr>
<td>MongoDB
<td>Un conector MongoDB le permite crear, actualizar, eliminar y obtener documentos de una colección de MongoDB.
<td>Nuevo documento
<td>	<li>Agregar documento
		<li>Actualizar documento
		<li>Obtener documentos
		<li>Aplicar upsert a documento
		<li>Eliminar documento
</tr>


<tr>
<td>MQ
<td>El conector de MQ se conecta a la versión 8 de IBM WebSphere MQ Server, en local y en una máquina virtual de Azure con un sistema operativo Windows. Cuando se utiliza de forma local, puede usarse la VPN o Azure ExpressRoute. El conector también incluye el cliente de Microsoft para MQ.<br/><br/><strong>Nota</strong>: actualmente no se puede utilizar con aplicaciones lógicas.
<td>Ninguna
<td>Ninguna
</tr>

<tr>
<td>Office 365
<td>Un conector Office 365 le permite enviar y recibir correos electrónicos y administrar su calendario y contactos.
<td>Nuevo mensaje
<td>	<li>Enviar correo
		<li>Responder al correo
		<li>Enviar evento
		<li>Agregar contacto
</tr>

<tr>
<td>OneDrive
<td>El conector OneDrive le permite conectarse a su cuenta de almacenamiento personal de Microsoft OneDrive y realizar diversas acciones, como cargar, obtener, eliminar y enumerar archivos.
<td>Nuevo archivo
<td><li>Archivos: cargar, eliminar, enumerar y descargar
</tr>

<tr>
<td>Oracle
<td>Un conector Base de datos de Oracle le permite crear y modificar entradas de una tabla de base de datos de Oracle.
<td>Nuevos datos, basados en consulta
<td><li>Tabla: insertar en, actualizar, seleccionar de y eliminar de
<li>Llamar a procedimiento almacenado
</tr>

<tr>
<td>SharePoint Online
<td>Un conector Microsoft SharePoint le permite crear y modificar documentos y enumerar elementos de SharePoint Server o Microsoft SharePoint Online.
<td><li>Nuevo documento
<li>Nuevo elemento de lista
<td><li>Biblioteca de documentos: cargar, eliminar y obtener
<li>Lista: insertar elemento

</tr>

<tr>
<td>SharePoint Server
<td>Un conector SharePoint le permite administrar documentos y enumerar elementos de SharePoint Server. Se admiten credenciales predeterminadas, autenticación de Windows y autenticación basada en formularios. Deberá proporcionar una cadena de conexión del Bus de servicio e instalar el agente de escucha local para poder conectarse al servidor.
<td><li>Nuevo documento
<li>Nuevo elemento de lista
<td><li>Biblioteca de documentos: cargar, eliminar y obtener
<li>Lista: insertar elemento
</tr>
</table>


## ¿Por qué usar conectores?

Los conectores aceleran el desarrollo de aplicaciones e incluso permiten a quienes no son desarrolladores crear aplicaciones de nivel empresarial completamente funcionales sin tener que aprender un lenguaje de programación ni escribir ningún código.

Ahora que tiene una idea de lo que nuestros conectores de aplicación + servicios de datos pueden hacer, veamos algunos casos de uso sencillo para estos conectores.

### Supervisión de Dropbox y actualización de SharePoint
Imagine que su compañía es una empresa de construcción que recibe archivos muy grandes que contienen planos. Estos archivos suelen ser demasiado grandes para administrarse a través de correo electrónico por lo que su compañía configura una cuenta de Dropbox y solicita a sus clientes que coloquen los planos en Dropbox. A continuación, podría pedir a cada empleado que comprobara constantemente las carpetas de Dropbox para ver si hay proyectos nuevos y, a continuación, cargarlos en SharePoint Server. Sin embargo, ¡está convencido de que debe haber una manera mejor! Afortunadamente, ya ha descubierto que Microsoft ha publicado recientemente su conector de aplicación + servicios de datos para Dropbox, Sharepoint y otros servicios de datos. Puede crear fácilmente conectores para Dropbox y SharePoint, agregarlos a una aplicación lógica y configurarlos para cargar cada archivo nuevo de la cuenta de Dropbox a la lista de SharePoint. Puesto que el conector Dropbox tiene un desencadenador de *nuevo mensaje*, puede usarlo para notificar a la aplicación lógica que hay un nuevo archivo disponible. A continuación, el conector Dropbox puede descargar el archivo. Después, el conector Sharepoint se puede configurar para cargar el archivo a una lista de SharePoint mediante la acción de *carga* de SharePoint. Puede hacer todo esto sin necesidad de escribir una sola línea de código.

Comencemos.

## Creación de un conector
Las aplicaciones de API del conector se pueden crear mediante el Portal de Azure

### Creación de un conector SharePoint en el Portal de Microsoft Azure

1. En el Portal de Azure, elija **NUEVO** > **Web + móvil** > **Azure Marketplace**.
2. **Busque** el conector o selecciónelo de la lista. Una vez seleccionado, se abrirá una ventana o una hoja. Seleccione **Crear**. 
3. Introduzca las siguientes propiedades para el conector: <table> <tr><th>Propiedad</th> <th>Descripción</th> </tr> <tr><td>Nombre</td> <td>Escriba un nombre para la aplicación de API. Por ejemplo, puede asignarle el nombre RulesDiscountTaxCode o APIAppValidateXML</td> </tr> <tr><td>Plan de servicio de aplicación</td><td>Muestra el plan de pagos. Puede cambiarlo si necesita más o menos recursos</th> </td> <tr><td>Nivel de precios</td> <td>Propiedad de solo lectura que muestra la categoría de precios dentro de la suscripción de Azure.</td> </tr> <tr><td>Grupo de recursos</td> <td>Cree uno nuevo o utilice un grupo ya existente. Uso de grupos de recursos explica esta propiedad</td> </tr> <tr><td>Suscripción</td> <td>Propiedad de solo lectura que muestra la suscripción actual</td> </tr> <tr><td>Ubicación</td> <td>Ubicación geográfica que hospeda el servicio de Azure </td></tr> <tr><td>Agregar al Panel de inicio</td> <td>Seleccione esta opción para agregar la aplicación de la API a su Panel de inicio (la página principal)</td></tr> </table> 
4. Seleccione **Crear**. Se creará el conector. La operación puede tardar varios minutos en completarse y la pantalla de inicio se mostrará durante la creación del conector. Use el elemento de menú Notificaciones situado a la izquierda para supervisar el estado del conector.

Ahora que ha creado su primer conector, considere la posibilidad de crear una aplicación web, móvil o lógica con él.


### Acceso a un conector con API de REST

[Acceso a conectores con API de REST](http://go.microsoft.com/fwlink/p/?LinkId=529766)

## Adición de un conector a una aplicación 
Servicio de aplicaciones de Microsoft Azure expone diferentes tipos de aplicación que pueden usar estos conectores. Por ejemplo, puede crear una aplicación *lógica* mediante la combinación de uno o varios de los conectores *lógicos* en una sola aplicación.

Para utilizar los conectores dentro de su aplicación *lógica*, elija un conector preconfigurado en la lista, agréguelo a su flujo de trabajo de diseño y efectúe los cambios de configuración necesarios. Ya está listo para usarlo.

Para seguir estos pasos, necesitará una aplicación web, móvil o lógica. Consulte <> para ver los pasos específicos. Una vez que la aplicación esté disponible, agregue los conectores. Este es el procedimiento:

Use los pasos siguientes para agregar un conector a una aplicación lógica:

1. En el panel de inicio del Portal de Azure (página principal), vaya a **Marketplace** y busque las aplicaciones lógicas, móviles o web. 

	Si está creando una nueva aplicación, busque la aplicación lógica, la aplicación móvil o la aplicación web. Seleccione la aplicación en la nueva hoja, seleccione **Crear**. En [Creación de una aplicación lógica](app-service-logic-create-a-logic-app.md) se muestran los pasos.

2. Abra su aplicación y seleccione **Desencadenadores y acciones**.
3. En **Galería**, elija el conector. Se agregará a la aplicación.
4. Configure el conector:
5. Cada conector tiene propiedades que son específicas para el servicio y el entorno al que se está conectando. Especifique los detalles de las propiedades. Tenga en cuenta que algunas propiedades son opcionales.
6. Seleccione **Aceptar** para guardar los cambios.


## Seguridad
Los conectores utilizan OAuth o nombres de usuario y contraseñas.


## Lea acerca de las aplicaciones lógicas y las aplicaciones web
[¿Qué son las aplicaciones lógicas?](app-service-logic-what-are-logic-apps.md) | [Sitios web y aplicaciones web en el Servicio de aplicaciones de Azure](../app-service-web/app-service-web-app-azure-portal.md) |



## Más conectores
[Conectores de integración de BizTalk](app-service-logic-integration-connectors.md) | [Conectores Enterprise](app-service-logic-enterprise-connectors.md) | [Conectores de negocio a negocio](app-service-logic-b2b-connectors.md) | [Conectores sociales](app-service-logic-social-connectors.md) | [Conectores de protocolo](app-service-logic-protocol-connectors.md) | [Conectores de aplicaciones y servicios de datos](app-service-logic-data-connectors.md) | [Lista de aplicaciones de API y conectores](app-service-logic-connectors-list.md)<br/><br/> [¿Qué son los conectores y las aplicaciones de la API de BizTalk?](app-service-logic-what-are-biztalk-api-apps.md)

<!--HONumber=54--> 