<properties 
	pageTitle="Creación de una aplicación web de Java en el Servicio de aplicaciones de Azure" 
	description="En este tutorial se muestra cómo implementar una aplicación web de Java en el Servicio de aplicaciones de Azure." 
	services="app-service\web" 
	documentationCenter="java" 
	authors="rmcmurray" 
	manager="wpickett" 
	editor="jimbe"/>

<tags 
	ms.service="app-service-web" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="Java" 
	ms.topic="article" 
	ms.date="03/24/2015" 
	ms.author="robmcm"/>

# Creación de una aplicación web de Java en el Servicio de aplicaciones de Azure

En este tutorial se muestra cómo crear una aplicación web en Microsoft Azure mediante Java, Azure Marketplace o la interfaz de usuario de configuración de [Aplicaciones web de Servicio de aplicaciones](http://go.microsoft.com/fwlink/?LinkId=529714). 

Si no desea usar ninguna de estas técnicas y, por ejemplo, quiere personalizar el contenedor de aplicaciones, consulte [Carga de una aplicación web de Java personalizado en Azure](web-sites-java-custom-upload.md).

> [AZURE.NOTE] Necesita una cuenta de Microsoft Azure para completar este tutorial. Si aún no la tiene, puede <a href="/es-es/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F" target="_blank">activar los beneficios de suscripción a MSDN</a> o bien <a href="/es-es/pricing/free-trial/?WT.mc_id=A261C142F" target="_blank">registrarse para obtener una evaluación gratuita</a>. 

>Si desea empezar a trabajar con el Servicio de aplicaciones de Azure antes de inscribirse para abrir una cuenta de Azure, vaya a [Prueba del Servicio de aplicaciones](http://go.microsoft.com/fwlink/?LinkId=523751), donde podrá crear inmediatamente una aplicación web de inicio de corta duración en el Servicio de aplicaciones. No es necesario proporcionar ninguna tarjeta de crédito ni asumir ningún compromiso.

# Creación de una aplicación web de Java con Azure Marketplace

Esta información muestra cómo usar Azure Marketplace para seleccionar un contenedor de aplicaciones Java, Apache Tomcat o Jetty, para la aplicación web.

A continuación se muestra cuál sería el aspecto de una aplicación web creada mediante Tomcat desde Azure Marketplace:

<!--todo:![Web app using Apache Tomcat](./media/web-sites-java-get-started/tomcat.png)-->

A continuación se muestra cuál sería el aspecto de una aplicación web creada mediante Jetty desde Azure Marketplace:

<!--todo:![Web app using Jetty](./media/web-sites-java-get-started/jetty.png)-->

1. Inicie sesión en el [Portal de Azure](http://go.microsoft.com/fwlink/?LinkId=529715).
2. Haga clic en **Nuevo**, en la parte inferior izquierda de la página.
3. Haga clic en la hoja **Web + Móvil**.
4. Haga clic en **Azure Marketplace** en la parte inferior de la hoja **Web + Móvil**.
5. Haga clic en **Web**.
6. La parte superior de la página **Web** contiene un cuadro de texto de búsqueda. En este cuadro de texto, escriba el servidor de aplicaciones Java deseado, como **Apache Tomcat** o **Jetty**.
4. Haga clic en el servidor de aplicaciones Java deseado.
5. Haga clic en **Crear**.
6. Especifique el nombre de la dirección URL.
6. Seleccione una región. Por ejemplo, **Oeste de EE. UU.**
7. Haga clic en **Crear**.

Se creará la aplicación web en un momento. Para ver la aplicación web, en el Portal de administración de Azure, en la hoja **Aplicaciones web**, haga clic en la aplicación web que acaba de crear y, a continuación, haga clic en la dirección URL de la aplicación web.

Ahora que ha creado la aplicación web con un contenedor de aplicaciones, consulte la sección **Pasos siguientes** para obtener información sobre la carga de la aplicación en la aplicación web.

# Creación de una aplicación web de Java con la interfaz de usuario de configuración de Azure

Esta información muestra cómo usar la interfaz de usuario de configuración de Azure para seleccionar un contenedor de aplicaciones Java, Apache Tomcat o Jetty, para la aplicación web.

1. Inicie sesión en el Portal de administración de Microsoft Azure.
2. Haga clic en **Nuevo**, en la parte inferior izquierda de la página.
3. Haga clic en la hoja **Web + Móvil**.
4. Haga clic en **Azure Marketplace** en la parte inferior de la hoja **Web + Móvil**.
5. Haga clic en **Web**.
6. Haga clic en **Aplicación web**.
7. Haga clic en **Crear**. 
8. Especifique el nombre de la dirección URL.
9. Seleccione una región. Por ejemplo, **Oeste de EE. UU.**
10. Haga clic en **Crear**.
11. Cuando se ha creado la aplicación web, haga clic en **Toda la configuración**.
12. Haga clic en **Configuración de la aplicación**.
13. Haga clic en la versión de Java deseada.
14. Se muestran las opciones para el contenedor web, por ejemplo, Tomcat y Jetty. Seleccione el **contenedor web** deseado. 
15. Haga clic en **Save**. 

En un momento, la aplicación web se convertirá en una aplicación basada en Java. Para confirmar que está basada en Java, haga clic en la dirección URL de la aplicación web. Tenga en cuenta que la página proporcionará el texto que indica que la aplicación nueva es una aplicación web basada en Java.

Ahora que ha creado la aplicación web con un contenedor de aplicaciones, consulte la sección **Pasos siguientes** para obtener información sobre la carga de la aplicación en la aplicación web.

# Pasos siguientes

En este momento, dispone de un servidor de aplicaciones Java ejecutándose como aplicación web de Java en Azure. Para agregar su propia aplicación o página web, consulte [Incorporación de una aplicación a la aplicación web de Javaweb-sites-java-add-app.md).

## ¿Qué ha cambiado?
* Para obtener una guía del cambio de Sitios web al Servicio de aplicaciones, consulte: [Servicio de aplicaciones de Azure y su impacto en los servicios de Azure existentes](http://go.microsoft.com/fwlink/?LinkId=529714)
* Para obtener una guía del cambio del portal antiguo al nuevo, consulte: [Referencia para navegar por el portal de vista previa](http://go.microsoft.com/fwlink/?LinkId=529715)

<!--HONumber=49-->