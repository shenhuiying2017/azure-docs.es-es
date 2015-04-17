<properties 
	pageTitle="Conectores sociales de aplicaciones de API de Microsoft Azure | Microservicio de aplicaciones de API" 
	description="Aprenda a crear aplicaciones de API de conectores sociales de Microsoft Azure, así como a agregar las aplicaciones de API a la aplicación lógica; microservicios" 
	services="app-service-logic" 
	documentationCenter="" 
	authors="MSFTMan" 
	manager="dwrede" 
	editor=""/>

<tags
	ms.service="app-service-logic" 
	ms.workload="connectors" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="03/24/2015" 
	ms.author="deonhe"/> 

# Conectores sociales en el servicio de aplicaciones de Microsoft Azure


## ¿Qué es un conector social de aplicación de API?
Los conectores sociales son "aplicaciones de API" que pueden conectarse a aplicaciones sociales y realizar varias *Actions* en nombre del usuario autenticado. Casi todos los conectores se pueden configurar también con un *Trigger*. Los desencadenadores son eventos (similares a los eventos de .NET Framework) que pueden configurarse en algunos conectores. Entonces, los desencadenadores pueden iniciar las acciones que se han configurado para el conector (o cualquier otro conector). Por ejemplo, se puede configurar una instancia del conector de Twitter con un desencadenador de *new message* cuando el desencadenador se define como cualquier nuevo tweet en el que se mencione @VisualStudio. Entonces, este desencadenador se puede configurar para que inicie una *Action* para retwittear el tweet. 

Microsoft cuenta con los siguientes conectores sociales:

- Facebook
- Twitter
- Chatter
- Yammer
- Twilio

A continuación, aparece una breve descripción de cada uno de los conectores sociales. 

<table>
<tr>
<th> Nombre</th>
<th> Descripción</th>
<th> Desencadenadores</th>
<th>Acciones</th>

<tr>
<td>Facebook
<td>Un conector de Facebook permite publicar y leer mensajes, comentarios, eventos y estados desde su fuente de Facebook. También puede publicar fotos y vídeos.
<td>Nueva publicación
<td><li>Publicar contenido
	<li>Publicar fotografía 
</tr>

<tr>
<td>Twitter
<td>Un conector de Twitter permite realizar varias operaciones como publicar, recibir o buscar tweets. También puede utilizar un conector de Twitter para estar al tanto de lo que sucede en Twitter y obtener detalles como tweets, líneas de tiempo, amigos y seguidores.
<td>Nuevos tweets
<td><li>Buscar tweets
	<li>Twittear
	<li>Ver las líneas de tiempo de los usuarios
	<li>Retwittear
</tr>

<tr>
<td>Chatter
<td>Un conector de Chatter se puede usar para leer, publicar y buscar mensajes en su fuente de Chatter.
<td>Nuevo mensaje
<td><li>Publicar mensaje
<li>Buscar mensaje
</tr>

<tr>
<td>Yammer
<td>Un conector de Yammer permite publicar y leer mensajes en los grupos de Yammer.
<td>Mensajes nuevos
<td><li>Publicar mensaje
	<li>Twittear
	<li>Ver las líneas de tiempo de los usuarios
	<li>Retwittear
</tr>

<tr>
<td>Twilio
<td>Un conector de Twilio permite enviar y recibir SMS en su cuenta de Twilio. También permite recuperar números de teléfono y datos de uso.
<td>N/D
<td><li>Mensajes: enviar, enumerar, obtener y buscar
	<li>Números de teléfono: comprar, enumerar y obtener números de teléfono locales o gratuitos
</tr>
</table>


Ahora que sabe lo que pueden hacer nuestros conectores sociales, veamos algunos casos de uso sencillos para estos conectores.

##¿Por qué usar conectores?

Los conectores aceleran el desarrollo de aplicaciones e incluso permiten a quienes no son desarrolladores crear aplicaciones de nivel empresarial completamente funcionales sin tener que aprender un lenguaje de programación ni escribir ningún código. 

### Supervisión de los comentarios de los clientes ###
Imagine que su empresa ha publicado recientemente una nueva aplicación y que el equipo desea saber qué opinan los clientes sobre ella en los medios sociales. Una opción sería que cada miembro del equipo comprobara periódicamente los distintos sitios de redes sociales y averiguara las palabras clave que están usando los clientes para describir la aplicación. Sin embargo, es más elegante crear simplemente un conector de Twitter y configurarlo para supervisar Twitter en busca de palabras clave, menciones y hastags específicos. A continuación, se puede utilizar el conector SMTP para enviar un correo electrónico que incluya el contenido de los tweets coincidentes a los miembros del equipo. Puede hacer algo similar mediante la supervisión de Facebook y Yammer y el envío de un SMS a través de un conector de Twilio a su equipo de DevOps si el contenido de las publicaciones de Facebook o Yammer indica que puede haber un problema crítico que afecte a sus clientes. Puede hacer todo esto sin necesidad de escribir una sola línea de código.  

Comencemos. 


##Creación de un conector

Hay dos maneras de crear conectores:

- Mediante el Portal de Microsoft Azure (no se necesitan códigos)
- Mediante API de REST 

### Creación de un conector social en el Portal de Microsoft Azure

1. En el Portal de Azure, elija **Nuevo** > **Web + móvil** > **Azure Marketplace**.
2. **Busque** el conector o selecciónelo en la lista. Una vez seleccionado, se abre una ventana o una hoja. Elija **Crear**. 
3. Introduzca las siguientes propiedades para el conector: 
	<table>
	    <tr><th>Propiedad</th> <th>Descripción</th> </tr>
	    <tr><td>Nombre</td> <td>Escriba un nombre para la aplicación de API. Por ejemplo, puede asignarle el nombre RulesDiscountTaxCode o APIAppValidateXML.</td> </tr>
	    <tr><td>Plan de servicio de la aplicación</td> <td>Muestra el plan de pagos. Puede modificarlo en función de los recursos que necesite.</th> </td>
	    <tr><td>Nivel de precios</td> <td>Propiedad de solo lectura que muestra la categoría de precios correspondiente a la suscripción de Azure.</td> </tr>
	    <tr><td>Grupo de recursos</td> <td>Permite crear un grupo nuevo o usar uno existente. Al usar grupos de recursos, se explica esta propiedad.</td> </tr>
	    <tr><td>Subscription</td> <td>Propiedad de solo lectura que muestra la suscripción actual.</td> </tr>
	    <tr><td>Ubicación</td> <td>La ubicación geográfica en la que se hospeda el servicio de Azure. </td></tr>
        <tr><td>Agregar a Panel de inicio</td> <td>Elija esta opción para agregar la aplicación de la API al Panel de inicio (la página principal).</td></tr>
	</table> 
4. Elija **Crear**. Se creará el conector. La operación puede tardar varios minutos en completarse y la pantalla de inicio se mostrará durante la creación del conector. Use el elemento de menú Notificaciones situado a la izquierda para supervisar el estado del conector.

Ahora que ha creado su primer conector, considere la posibilidad de crear una aplicación web, móvil o lógica con él. 


### Creación de un conector con API de REST

[Creación de conectores con las API de REST](http://go.microsoft.com/fwlink/p/?LinkId=529766)


## Adición de un conector a una aplicación 
Servicio de aplicaciones de Microsoft Azure expone diferentes tipos de aplicación que pueden usar estos conectores. Por ejemplo, puede crear una aplicación *Logic* mediante la combinación de uno o varios de los conectores *logically* en una sola aplicación.

Para utilizar los conectores dentro de su aplicación *Logic*, elija un conector preconfigurado en la lista, agréguelo a su flujo de trabajo de diseño y efectúe los cambios de configuración necesarios. Ya está listo para usarlo. 

Para seguir estos pasos, necesitará una aplicación web, móvil o lógica. Consulte <> para ver los pasos específicos. Una vez que la aplicación esté disponible, agregue los conectores. Este es el procedimiento:

Utilice los pasos siguientes para agregar un conector a una aplicación lógica: 

1. En el Panel de inicio del Portal de Azure (página principal), vaya a la **Marketplace** y busque la aplicación web, móvil o lógica. 

	Si está creando una nueva aplicación, busque la aplicación lógica, la aplicación móvil o la aplicación web. Elija la aplicación y, en la nueva hoja, elija **Crear**. [Creación de una aplicación lógica nueva](app-service-logic-create-a-logic-app.md) muestra los pasos del procedimiento. 

2. Abra la aplicación y elija **Desencadenadores y acciones**. 
3. En **Galería**, elija el conector. Se agregará a la aplicación.
4. Configure el conector:
5. Cada conector tiene propiedades que son específicas para el servicio y el entorno al que se está conectando. Especifique los detalles de las propiedades. Tenga en cuenta que algunas propiedades son opcionales.
6. Haga clic en **Aceptar** para guardar los cambios.


## Seguridad
Los conectores utilizan OAuth o nombres de usuario y contraseñas.

## Información acerca de las aplicaciones web y lógicas
[¿Qué son las aplicaciones lógicas?](app-service-logic-what-are-logic-apps.md) |
[Sitios web y aplicaciones web en el servicio de aplicaciones de Azure](app-service-web-app-azure-portal.md) |


## Más conectores

[Conectores para la integración de BizTalk](app-service-logic-integration-connectors.md) |
[Conectores para empresas](app-service-logic-enterprise-connectors.md) |
[Conectores empresa-empresa (B2B)](app-service-logic-b2b-connectors.md) |
[Conectores de protocolo](app-service-logic-protocol-connectors.md) |
[Conectores para servicios de datos y aplicaciones](app-service-logic-data-connectors.md) |
[Lista de aplicaciones de API y conectores](app-service-logic-connectors-list.md)

<!--HONumber=49-->