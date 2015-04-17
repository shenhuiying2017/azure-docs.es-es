<properties 
	pageTitle="Conectores de datos de aplicaciones de API de Microsoft Azure | Microservicio de aplicaciones de API" 
	description="Aprenda a crear aplicaciones de API de conector de datos de Microsoft Azure y agregue la aplicación de API microservicios de la aplicación lógica" 
	services="app-service-logic" 
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
	ms.date="03/24/2015" 
	ms.author="deonhe"/>


# Conectores de datos del Servicio de aplicaciones de Microsoft Azure


## ¿Qué es un conector de aplicación de API Agregar datos + servicios
Los conectores de aplicaciones y servicios de datos son "aplicaciones de API" que se pueden conectar a una amplia gama de aplicaciones de servicios de datos y realizar varias *Actions* en nombre del usuario autenticado. La mayoría de estos conectores también pueden configurarse con un *Trigger*. Los desencadenadores son eventos (similares a los eventos de .NET Framework) que pueden configurarse en algunos conectores para iniciar un flujo de trabajo. Por ejemplo, se puede configurar una instancia del conector Dropbox con un desencadenador *new file* donde dicho desencadenador se ejecutará cada vez que un nuevo archivo se agregue a la cuenta de Dropbox que se está supervisando. A continuación, este desencadenador se puede configurar para iniciar una *Action* que ejecutará la operación *Get* en el archivo y lo cargará en una lista de SharePoint local.


A continuación figura una breve descripción de cada uno de los conectores de aplicaciones y servicios de datos que están disponibles en la galería de Azure. 

<table>
<tr>
<th> Nombre</th>
<th> Descripción</th>
<th> Desencadenadores</th>
<th>Acciones</th>

<tr>
<td>Servicios multimedia de Azure
<td>El conector Servicios multimedia de Azure permite crear flujos de trabajo multimedia de extremo a extremo con codificación, empaquetado y distribución flexibles y escalables. También puede cargar, almacenar, codificar y hacer streaming de contenido de audio o vídeo para entrega de streaming en vivo y a petición a una amplia gama de extremos de TV, PC y dispositivos móviles.
<td>None
<td>None 
</tr>

<tr>
<td>Servicios móviles de Azure
<td>Un conector de Servicios móviles de Azure le permite crear y modificar los datos y llamar a las API personalizadas.
<td>None
<td><li>Consultar
<li>Insertar
<li>Eliminar
<li>Actualizar
<li>Llamada API personalizada

</tr>

<tr>
<td>Bus de servicio
<td>El conector de Bus de servicio de Azure le permite enviar mensajes desde entidades de Bus de servicio, como colas y temas, y recibir mensajes de entidades de Bus de servicio, como colas y suscripciones.
<td>Mensajes nuevos
<td>Enviar mensaje
</tr>

<tr>
<td>Blob de almacenamiento de Azure
<td>Blob de almacenamiento de Azure le permite conectarse al almacenamiento de blobs y realizar varias acciones.
<td>Nuevo archivo agregado
<td><li>Cargar <li>Obtener <li>Eliminar <li>Enumerar <li>Copiar <li>Instantánea
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
<td>N/D
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
<td>Microsoft SQL
<td>Un conector de Microsoft SQL le permite crear y modificar entradas de Microsoft SQL Server y tablas de Base de datos SQL de Azure.
<td>N/D
<td><li>Mensajes: enviar, enumerar, obtener y buscar
	<li>Números de teléfono: comprar, enumerar y obtener números de teléfono locales o gratuitos
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
<td>Office 365
<td>Un conector Office 365 le permite enviar y recibir correos electrónicos y administrar su calendario y contactos.
<td>Nuevo mensaje
<td>	<li>Enviar correo
		<li>Responder a correo
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
<td><li>Tabla: Insertar en, actualizar, seleccionar de y eliminar de
<li>Llamar a procedimiento almacenado
</tr>

<tr>
<td>SharePoint Online
<td>Un conector Microsoft SharePoint le permite crear y modificar documentos y enumerar elementos de SharePoint Server o Microsoft SharePoint Online.
<td><li>Nuevo documento
<li>Nuevo elemento de lista
<td><li>Biblioteca de documentos: cargar, eliminar y obtener
<li>Enumerar: insertar elemento

</tr>

<tr>
<td>SharePoint Server
<td>Un conector SharePoint le permite administrar documentos y enumerar elementos de SharePoint Server. Se admiten credenciales predeterminadas, autenticación de Windows y autenticación basada en formularios. Deberá proporcionar una cadena de conexión del Bus de servicio e instalar el agente de escucha local para poder conectarse al servidor.
<td><li>Nuevo documento
<li>Nuevo elemento de lista
<td><li>Biblioteca de documentos: cargar, eliminar y obtener
<li>Enumerar: insertar elemento
</tr>
</table>


##¿Por qué usar conectores?

Los conectores aceleran el desarrollo de aplicaciones e incluso permiten a quienes no son desarrolladores crear aplicaciones de nivel empresarial completamente funcionales sin tener que aprender un lenguaje de programación ni escribir ningún código.

Ahora que tiene una idea de lo que nuestros conectores de aplicación + servicios de datos pueden hacer, veamos algunos casos de uso sencillo para estos conectores. 

### Supervisión de Dropbox y actualización de SharePoint ###
Imagine que su compañía es una empresa de construcción que recibe archivos muy grandes que contienen planos. Estos archivos suelen ser demasiado grandes para administrarse a través de correo electrónico por lo que su compañía configura una cuenta de Dropbox y solicita a sus clientes que coloquen los planos en Dropbox. A continuación, podría pedir a cada empleado que comprobara constantemente las carpetas de Dropbox para ver si hay proyectos nuevos y, a continuación, cargarlos en SharePoint Server. Sin embargo, ¡está convencido de que debe haber una manera mejor! Afortunadamente, ya ha descubierto que Microsoft ha publicado recientemente su conector de aplicación + servicios de datos para Dropbox, Sharepoint y otros servicios de datos. Puede crear fácilmente conectores para Dropbox y SharePoint, agregarlos a una aplicación lógica y configurarlos para cargar cada archivo nuevo  desde su cuenta de Dropbox a la lista de SharePoint. Puesto que el conector Dropbox tiene un desencadenador *new message*, puede usarlo para notificar a la aplicación lógica que hay un nuevo archivo disponible. A continuación, el conector Dropbox puede descargar el archivo. Después, el conector Sharepoint se puede configurar para cargar el archivo a una lista de SharePoint mediante la acción *upload* de SharePoint. Puede hacer todo esto sin necesidad de escribir una sola línea de código.  

Comencemos. 

##Creación de un conector

Hay dos maneras de crear conectores:

- Mediante el Portal de Microsoft Azure (no se necesitan códigos)
- Mediante API de REST. 

### Creación de un conector SharePoint en el Portal de Microsoft Azure

1. En el Portal de Azure, elija **Nuevo** > **Web + móvil** > **Azure Marketplace**.
2. **Busque** el conector o selecciónelo en la lista. Una vez seleccionado, se abrirá una ventana o una hoja. Seleccione **Crear**. 
3. Introduzca las siguientes propiedades para el conector: 
	<table>
	    <tr><th>Propiedad</th> <th>Descripción</th> </tr>
	    <tr><td>Nombre</td> <td>Escriba un nombre para la aplicación de API. Por ejemplo, puede asignarle el nombre RulesDiscountTaxCode o APIAppValidateXML.</td> </tr>
	    <tr><td>Plan de servicio de aplicación</td> <td>Muestra el plan de pagos. Puede modificarlo en función de los recursos que necesite.</th> </td>
	    <tr><td>Nivel de precios</td> <td>Propiedad de solo lectura que muestra la categoría de precios correspondiente a la suscripción de Azure.</td> </tr>
	    <tr><td>Grupos de recursos</td> <td>Cree uno nuevo o use un grupo existente. Al usar grupos de recursos, se explica esta propiedad.</td> </tr>
	    <tr><td>Subscription</td> <td>Propiedad de solo lectura que muestra la suscripción actual</td> </tr>
	    <tr><td>Ubicación</td> <td>La ubicación geográfica en la que se hospeda el servicio de Azure. </td></tr>
        <tr><td>Agregar al panel de inicio</td> <td>Elija esta opción para agregar la aplicación de la API al Panel de inicio (la página principal).</td></tr>
	</table> 
4. Seleccione **Crear**. Se creará el conector. La operación puede tardar varios minutos en completarse y la pantalla de inicio se mostrará durante la creación del conector. Use el elemento de menú Notificaciones situado a la izquierda para supervisar el estado del conector.

Ahora que ha creado su primer conector, considere la posibilidad de crear una aplicación web, móvil o lógica con él. 


### Creación de un conector con API de REST

[Creación de conectores con las API de REST](http://go.microsoft.com/fwlink/p/?LinkId=529766)

## Adición de un conector a una aplicación 
Servicio de aplicaciones de Microsoft Azure expone diferentes tipos de aplicación que pueden usar estos conectores. Por ejemplo, puede crear una aplicación *Logic* mediante la combinación de uno o varios de los conectores *logically* en una sola aplicación.

Para utilizar los conectores dentro de su aplicación *Logic*, elija un conector preconfigurado en la lista, agréguelo a su flujo de trabajo de diseño y efectúe los cambios de configuración necesarios. Ya está listo para usarlo. 

Para seguir estos pasos, necesitará una aplicación web, móvil o lógica. Consulte <> para ver los pasos específicos. Una vez que la aplicación esté disponible, agregue los conectores. Este es el procedimiento:

Use los pasos siguientes para agregar un conector a una aplicación lógica: 

1. En el Panel de inicio del portal de Azure (página principal), vaya a **Marketplace** y busque su  aplicación lógica, móvil o web. 

	Si está creando una nueva aplicación, busque la aplicación lógica, la aplicación móvil o la aplicación web. Seleccione la aplicación y, en la nueva hoja, seleccione **Crear**. [Creación de una aplicación lógica](app-service-logic-create-a-logic-app.md)  enumera los pasos. 

2. Abra la aplicación y seleccione **Desencadenadores y acciones **. 
3. En **Galería**, elija el conector. Se agregará a la aplicación.
4. Configure el conector:
5. Cada conector tiene propiedades que son específicas para el servicio y el entorno al que se está conectando. Especifique los detalles de las propiedades. Tenga en cuenta que algunas propiedades son opcionales.
6. Seleccione **Aceptar** para guardar los cambios.


## Seguridad
Los conectores utilizan OAuth o nombres de usuario y contraseñas. 


## Obtener información acerca de aplicaciones lógicas y aplicaciones web
[¿Qué son las aplicaciones lógicas?](app-service-logic-what-are-logic-apps.md) |
[Sitios Web y aplicaciones web en el Servicio de aplicaciones de Azure](app-service-web-app-azure-portal.md) |



## Más conectores

[Conectores de integración de BizTalk](app-service-logic-integration-connectors.md) |
[Conectores empresariales](app-service-logic-enterprise-connectors.md) |
[Conectores negocio a negocio](app-service-logic-b2b-connectors.md) |
[Conectores sociales](app-service-logic-social-connectors.md) |
[Conectores de protocolos](app-service-logic-protocol-connectors.md) |
[Conectores de aplicación y servicios de datos](app-service-logic-data-connectors.md) |
[Lista de conectores y aplicaciones de API](app-service-logic-connectors-list.md)

<!--HONumber=49-->