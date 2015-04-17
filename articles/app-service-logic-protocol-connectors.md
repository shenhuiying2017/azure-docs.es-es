<properties 
	pageTitle="Conectores de protocolos de aplicaciones de API de Microsoft Azure | Microservicio de aplicaciones de API" 
	description="Aprenda a crear aplicaciones de API del conector de protocolos de Microsoft Azure, así como a agregar la aplicación de API a la aplicación lógica; microservicios" 
	services="app-service-logic" 
	documentationCenter="" 
	authors="Deonhe" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="app-service-logic" 
	ms.workload="integration" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="03/24/2015" 
	ms.author="deonhe"/> 


# Conectores de protocolos en el servicio de aplicaciones de Microsoft Azure


## ¿Qué es un conector de protocolos?
Los conectores de protocolos son aplicaciones de API* usadas para crear aplicaciones que se comunican con una amplia variedad de protocolos, como HTTP, SMTP, FTP, etc. 

Puede encontrar los conectores de protocolos en el Portal de Microsoft Azure; para ello, haga clic en **Nuevo** > **Web + móvil** > **Azure Marketplace** > especifique la palabra clave **protocolo** > presione Intro.

Los siguientes conectores de protocolos están disponibles en Azure Marketplace:

- SFTP
- POP3
- FTP
- HTTP
- SMTP
- Archivo

A continuación, aparece una breve descripción de cada uno de los conectores de protocolos. 

<table>
<tr>
<th> Nombre</th>
<th> Descripción</th>
<th> Desencadenadores</th>
<th> Acciones</th>

<tr>
<td>SFTP
<td>Un conector SFTP permite descargar archivos desde un servidor SFTP, así como cargarlos en él.
<td>Archivo nuevo disponible en la carpeta SFTP

<td>Archivos: cargar, eliminar, enumerar y obtener 

</tr>

<tr>
<td>POP3
<td>Un conector POP3 permite descargar el correo electrónico desde un servidor POP3.
<td>Llegada de un nuevo mensaje
<td>N/D
</tr>

<tr>
<td>FTP
<td>Un conector FTP permite descargar archivos desde un servidor FTP, así como cargarlos en él.
<td>Archivo nuevo disponible en la carpeta FTP
<td>Archivo: cargar, obtener, eliminar y enumerar
</tr>

<tr>
<td>HTTP
<td>Un conector HTTP permite enviar datos a los servidores HTTP mediante el protocolo HTTP o HTTPS.
<td>None
<td>Publicar, obtener, eliminar, poner
</tr>

<tr>
<td>SMTP
<td>Un conector SMTP permite enviar un correo electrónico desde un servidor SMTP.
<td>N/D
<td>Enviar correo electrónico
</tr>

<tr>
<td>Archivo
<td>Un conector de archivo permite conectarse al servidor de archivos local y realizar acciones tales como cargar, obtener, eliminar y enumerar archivos. También hay un desencadenador para recuperar archivos.
<td>Nuevo archivo agregado a un dispositivo
<td>Archivo: cargar, obtener, eliminar y enumerar
</tr>


</table>

Ahora que sabe lo que pueden hacer nuestros conectores de protocolos, veamos algunos casos de uso sencillos para estos conectores.

### Supervisión de los comentarios de los clientes ###
Imagine que su empresa ha publicado recientemente una nueva aplicación y que el equipo desea saber qué opinan los clientes sobre ella en los medios sociales. Una opción sería que cada miembro del equipo comprobara periódicamente los distintos sitios de redes sociales y averiguara las palabras clave que están usando los clientes para describir la aplicación. Sin embargo, es más elegante crear simplemente un conector de Twitter y configurarlo para supervisar Twitter en busca de palabras clave, menciones y hastags específicos. A continuación, se puede utilizar el conector SMTP para crear un correo electrónico que incluya el contenido de los tweets coincidentes y, acto seguido, enviarlo a los miembros del equipo. Después, se puede usar un conector HTTP para publicar esos tweets en un sitio web interno de la empresa. Puede hacer todo esto sin necesidad de escribir una sola línea de código.  

Comencemos. 

## Creación de un conector

Se puede crear un conector usando el Portal de Azure o mediante las API de REST [Creación de conectores con API de REST](http://go.microsoft.com/fwlink/p/?LinkId=529766)

### Creación de un conector en el Portal de Azure

Veamos cómo se crea paso a paso un conector SMTP mediante Azure Marketplace.

1. Inicie sesión en el [Portal](https://manage.windowsazure.com) de Microsoft Azure.
2. Elija **Nuevo** > **Web + móvil** > **Azure Marketplace.**
3. En el cuadro **Buscar**, escriba el **protocolo** y, a continuación, presione **Intro**. También puede seleccionar el valor adecuado en la lista. Una vez seleccionado, se abre una ventana o una hoja. Elija **Crear**. 
4. Especifique las siguientes propiedades:

<table>
<tr><th>Propiedad</th> <th>Descripción</th> </tr>
<tr><td>Nombre</td> <td>Descripción</td> </tr>
<tr><td>Configuración del paquete</td> <td>Utilice la propiedad de configuración del paquete para especificar todos los detalles de autenticación, por ejemplo, el nombre de usuario, la contraseña, el número de puerto, la dirección del servidor, etc., correspondientes al servidor SMTP. </td> </tr>
<tr><td>Plan de servicio de la aplicación</td> <td>Muestra el plan de pagos. Puede modificarlo en función de los recursos que necesite.</th> </td>
<tr><td>Nivel de precios</td> <td>Propiedad de solo lectura que muestra la categoría de precios correspondiente a la suscripción de Azure.</td> </tr>
<tr><td>Grupo de recursos</td> <td>Permite crear un grupo nuevo o usar uno existente. Al usar grupos de recursos, se explica esta propiedad.</td> </tr>
<tr><td>Subscription</td> <td>Propiedad de solo lectura que muestra la suscripción actual.</td> </tr>
<tr><td>Ubicación</td> <td>La ubicación geográfica en la que se hospeda el servicio de Azure.</th> </td>
<tr><td>Agregar a Panel de inicio</td> <td>Elija esta opción para agregar la aplicación de la API al Panel de inicio (la página principal).</td></tr>
</table> 

### Creación de un conector con las API de REST
[Creación de conectores con las API de REST](http://go.microsoft.com/fwlink/p/?LinkId=529766)

## Incorporación de un conector a la aplicación 
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
[¿Qué son las aplicaciones lógicas??](app-service-logic-what-are-logic-apps.md) |
[Sitios web y aplicaciones web en el servicio de aplicaciones de Azure](app-service-web-app-azure-portal.md) |


## Más conectores

[Conectores para la integración de BizTalk](app-service-logic-integration-connectors.md) |
[Conectores para empresas](app-service-logic-enterprise-connectors.md) |
[Conectores empresa-empresa (B2B)](app-service-logic-b2b-connectors.md) |
[Conectores sociales](app-service-logic-social-connectors.md) |
[Conectores para servicios de datos y aplicaciones](app-service-logic-data-connectors.md) |
[Lista de aplicaciones de API y conectores](app-service-logic-connectors-list.md)

<!--HONumber=49-->