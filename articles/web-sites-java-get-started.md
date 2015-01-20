<properties urlDisplayName="Get started with Azure" pageTitle="Introducción a los Sitios web de Microsoft Azure mediante Java" metaKeywords="" description="En este tutorial se muestra cómo implementar un sitio web de Java en Microsoft Azure." metaCanonical="" services="web-sites" documentationCenter="Java" title="Get started with Azure and Java" videoId="" scriptId="" authors="robmcm" solutions="" manager="wpickett" editor="mollybos" />

<tags ms.service="web-sites" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="Java" ms.topic="article" ms.date="09/25/2014" ms.author="robmcm" />

# Introducción a sitios web de Azure y Java

En este tutorial se muestra cómo crear un sitio web en Microsoft Azure mediante Java, la galería de aplicaciones de Azure o la interfaz de usuario de configuración del sitio web de Azure. 

Si no desea usar ninguna de estas técnicas y, por ejemplo, quiere personalizar el contenedor de aplicaciones, consulte [Carga de un sitio web de Java personalizado en Azure].(../web-sites-java-custom-upload).

> [WACOM.NOTE] Necesita una cuenta de Microsoft Azure para completar este tutorial. Si no tiene una cuenta, puede <a href="/es-es/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F" target="_blank">activar los beneficios de suscriptor MSDN</a> o <a href="/es-es/pricing/free-trial/?WT.mc_id=A261C142F" target="_blank">suscribirse para una prueba gratuita</a>. 
> 
> Si desea empezar a trabajar con sitios web de Azure antes de contratar una cuenta, vaya a <a href="https://trywebsites.azurewebsites.net/?language=java">https://trywebsites.azurewebsites.net,</a>donde puede crear inmediatamente un sitio de inicio ASP.NET en sitios web de Azure de forma gratuita. No se requiere ninguna tarjeta de crédito, sin compromisos.

# Creación de un sitio web de Java con la galería de aplicaciones de Azure

En esta información se muestra cómo usar la galería de aplicaciones de Azure para seleccionar un contenedor de aplicaciones Java, Apache Tomcat o Jetty, para el sitio web.

A continuación se muestra cuál sería el aspecto de un sitio web creado mediante Tomcat desde la galería de aplicaciones:

![Web site using Apache Tomcat](./media/web-sites-java-get-started/tomcat.png)

A continuación se muestra cuál sería el aspecto de un sitio web creado mediante Jetty desde la galería de aplicaciones:

![Web site using Jetty](./media/web-sites-java-get-started/jetty.png)

1. Inicie sesión en el Portal de administración de Microsoft Azure.
2. Haga clic sucesivamente en **Nuevo**, **Proceso**, **Sitio web** y, a continuación, en **De la galería**.
3. En la lista de aplicaciones, seleccione uno de los servidores de aplicaciones Java, como **Apache Tomcat** o **Jetty**.
4. Haga clic en **Next**.
5. Especifique el nombre de la dirección URL.
6. Seleccione una región. Por ejemplo, **Oeste de EE. UU.**
7. Haga clic en **Complete**.

Se creará el sitio web en un momento. Para ver el sitio web, en el Portal de administración de Azure, en la vista **Sitios web**, espere a que el estado se muestre como **Ejecutándose** y, a continuación, haga clic en la dirección URL para el sitio web.

Ahora que ha creado el sitio web con un contenedor de aplicaciones, consulte la sección **Pasos siguientes** para obtener información sobre la carga de la aplicación en el sitio web.

# Creación de un sitio web de Java con la interfaz de usuario de configuración de Azure

Esta información muestra cómo usar la interfaz de usuario de configuración de Azure para seleccionar un contenedor de aplicaciones Java, Apache Tomcat o Jetty, para el sitio web.

1. Inicie sesión en el Portal de administración de Microsoft Azure.
2. Haga clic sucesivamente en **Nuevo**, **Proceso**, **Sitio web** y, a continuación, en **Creación rápida**.
3. Especifique el nombre de la dirección URL.
4. Seleccione una región. Por ejemplo, **Oeste de EE. UU.**
5. Haga clic en **Complete**. Se creará el sitio web en un momento. Para ver el sitio web, en el Portal de administración de Azure, en la vista **Sitios web**, espere a que el estado se muestre como **Ejecutándose** y, a continuación, haga clic en la dirección URL para el sitio web.
6. Dentro del Portal de administración de Azure, en la vista **Sitios web**, haga clic en el nombre del sitio web para abrir el 
panel.
7. Haga clic en **Configure**.
8. En la sección **General**, habilite **Java** haciendo clic en la versión disponible.
9. Se muestran las opciones para el contenedor web, por ejemplo, Tomcat y Jetty. Haga clic en el contenedor web que desee usar. 
10. Haga clic en **Save**. 

En un momento, el sito web se convertirá en un sitio basado en Java. Para confirmar que es un sitio basado en Java, en el Portal de administración de Azure, en la vista **Sitios web**, espere a que el estado se muestre como **Ejecutándose** y, a continuación, haga clic en la dirección URL para el sitio web. Tenga en cuenta que la página proporcionará el texto que indica que el sitio nuevo es un sitio web basado en Java.

Ahora que ha creado el sitio web con un contenedor de aplicaciones, consulte la sección **Pasos siguientes** para obtener información sobre la carga de la aplicación en el sitio web.

# Pasos siguientes

En este momento, dispone de un servidor de aplicaciones Java ejecutándose como sitio web de Java en Azure. Para agregar su propia aplicación o página web, consulte [Incorporación de una aplicación al sitio web de Java].(../web-sites-java-add-app).

<!--HONumber=35.2-->
