---
title: "Configuración de aplicaciones web en Azure App Service"
description: "Cómo configurar una aplicación web en Azure App Service"
services: app-service\web
documentationcenter: 
author: cephalin
manager: erikre
editor: 
ms.assetid: 9af8a367-7d39-4399-9941-b80cbc5f39a0
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/25/2017
ms.author: cephalin
ms.openlocfilehash: 9ec501d0a4e1c6165b83b5b590b87b0baa284423
ms.sourcegitcommit: 9ea2edae5dbb4a104322135bef957ba6e9aeecde
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/03/2018
---
# <a name="configure-web-apps-in-azure-app-service"></a>Configuración de aplicaciones web en Azure App Service

En este tema se explica cómo configurar una aplicación web con el [Azure Portal].

[!INCLUDE [app-service-web-to-api-and-mobile](../../includes/app-service-web-to-api-and-mobile.md)]

## <a name="application-settings"></a>Configuración de la aplicación
1. En el [Azure Portal], abra la hoja de la aplicación web.
3. Haga clic en **Configuración de la aplicación**.

![Configuración de la aplicación][configure01]

La hoja **Configuración de la aplicación** tiene configuraciones agrupadas en varias categorías.

### <a name="general-settings"></a>Configuración general
**Versiones del marco**. Configure estas opciones si su aplicación utiliza cualquiera de estos marcos: 

* **.NET Framework**: configure la versión de .NET Framework. 
* **PHP**: defina la versión de PHP, o bien seleccione **DESACTIVADO** para deshabilitar PHP. 
* **Java**: seleccione la versión de Java o **DESACTIVADO** para deshabilitar Java. Utilice la opción **Contenedor web** para elegir entre las versiones Tomcat y Jetty.
* **Python**: seleccione la versión de Python o seleccione **DESACTIVADO** para deshabilitar Python.

Por razones técnicas, si se habilita Java para la aplicación, se deshabilitan las opciones .NET, PHP y Python.

<a name="platform"></a>
**Plataforma**. Seleccione si su aplicación web se ejecuta en un entorno de 32 o 64 bits. El entorno de 64 bits requiere el modo básico o estándar. Los modos libre y compartido siempre se ejecutan en un entorno de 32 bits.

[!INCLUDE [app-service-dev-test-note](../../includes/app-service-dev-test-note.md)]

**Sockets web**. Seleccione **ACTIVADO** para habilitar el protocolo WebSocket; por ejemplo, si el sitio web utiliza [ASP.NET SignalR] o [socket.io](https://socket.io/).

<a name="alwayson"></a>
**AlwaysOn**. De forma predeterminada, las aplicaciones web se descargan si están inactivas durante algún tiempo. Esto permite que el sistema conserve recursos. En el modo básico o estándar puede habilitar **Siempre disponible** para mantener el sitio cargado continuamente. Si su aplicación ejecuta WebJobs continuos o WebJobs activados mediante una expresión CRON, debe habilitar **AlwaysOn** o los trabajos web no se ejecutarán de forma fiable.

**Versión de canalización administrada**. Configura el [modo de canalización]IIS. Deje este valor en Integrado (el valor predeterminado) a no ser que tenga una aplicación web heredada que requiera una versión anterior de IIS.

**Intercambio automático**. Si habilita el Intercambio automático de una ranura de implementación, el servicio de aplicación intercambiará automáticamente la aplicación web en producción cuando inserte una actualización para esa zona. Para obtener más información, consulte [Implementación en ranuras de ensayo para las aplicaciones web en Azure App Service](web-sites-staged-publishing.md).

### <a name="debugging"></a>Depuración
**Depuración remota**. Habilita la depuración remota. Cuando esté habilitada, puede usar la depuración remota en Visual Studio para conectarse directamente a su aplicación web de Azure. La depuración remota permanecerá habilitada durante 48 horas. 

### <a name="app-settings"></a>Configuración de la aplicación
Esta sección contiene las parejas de nombre y valor que la aplicación web cargará al inicio. 

* En las aplicaciones .NET, estas configuraciones se insertarán en la sección de la configuración de .NET `AppSettings` en tiempo de ejecución y reemplazará la configuración existente. 
* Las aplicaciones PHP, Python, Java y Node pueden acceder a estas configuraciones como variables de entorno en tiempo de ejecución. En cada configuración de aplicación se crean dos variables de entorno; una con el nombre especificado en el entrada de configuración de la aplicación y otra con el prefijo APPSETTING_. Ambas contienen el mismo valor.

### <a name="connection-strings"></a>Cadenas de conexión
Cadenas de conexión para los recursos vinculados. 

En las aplicaciones .NET, estas cadenas de conexión se insertan en la sección `connectionStrings` de la configuración de .NET en tiempo de ejecución y reemplazan las entradas existentes en las que la clave sea igual que el nombre de la base de datos vinculada. 

En las aplicaciones PHP, Python, Java y Node, estas configuraciones estarán disponibles como variables de entorno en tiempo de ejecución, con el tipo de conexión como prefijo. Los prefijos de variable de entorno son los siguientes: 

* SQL Server: `SQLCONNSTR_`
* MySQL: `MYSQLCONNSTR_`
* SQL Database: `SQLAZURECONNSTR_`
* Personalizado: `CUSTOMCONNSTR_`

Por ejemplo, si una cadena de conexión de MySQL recibió el nombre de  `connectionstring1`, se obtendrá acceso a ella a través de la variable de entorno `MYSQLCONNSTR_connectionString1`.

### <a name="default-documents"></a>Documentos predeterminados
El documento predeterminado es la página web que se muestra en la dirección URL raíz de un sitio web.  Se usa el primer archivo coincidente en la lista. 

Es posible que las aplicaciones utilicen módulos que enruten en base a la URL, en lugar de ofrecer funcionalidad a contenido estático, en cuyo caso no existe realmente un documento predeterminado.    

### <a name="handler-mappings"></a>Asignaciones de controlador
Utilice esta zona para agregar procesadores de script personalizados para controlar solicitudes de extensiones de archivo específicas. 

* **Extensión**. La extensión de archivo que se va a gestionar, por ejemplo, *.php o handler.fcgi. 
* **Ruta de acceso del procesador de script**. La ruta absoluta del procesador de script. El procesador de script procesará las solicitudes a archivos que coincidan con esta extensión de archivo. Utilice la ruta de acceso `D:\home\site\wwwroot` para hacer referencia al directorio raíz de la aplicación.
* **Argumentos adicionales**. Argumentos opcionales de la línea de comandos para el procesador de script 

### <a name="virtual-applications-and-directories"></a>Directorios y aplicaciones virtuales
Para configurar las aplicaciones y los directorios virtuales, especifique cada directorio virtual y su ruta de acceso física correspondiente en relación con la raíz del sitio web. De manera opcional, puede activar la casilla **Aplicación** para marcar un directorio virtual como una aplicación.

## <a name="enabling-diagnostic-logs"></a>Habilitación de registros de diagnóstico
Para habilitar registros de diagnóstico:

1. En la hoja de la aplicación web, haga clic en **Toda la configuración**.
2. Haga clic en **Registros de diagnóstico**. 

Opciones para escribir registros de diagnóstico de una aplicación web que admita el registro: 

* **Registro de aplicaciones**. Escribe registros de aplicación en el sistema de archivos. El registro dura un período de 12 horas. 

**Nivel**. Cuando el registro de aplicaciones está habilitado, esta opción especifica la cantidad de información que se registrará (Error, Advertencia, Información o Detalle).

**Registro del servidor web**. Los registros se guardan con formato de archivo de registro W3C extendido. 

**Mensajes de error detallados**. Guarda archivos .htm de mensajes de error detallados. Los archivos se guardan en /LogFiles/DetailedErrors. 

**Seguimiento de solicitudes erróneas**. Registra solicitudes erróneas en archivos XML. Los archivos se guardan en /LogFiles/W3SVC*xxx*, donde xxx es un identificador único. Esta carpeta contiene un archivo XSL y uno o varios archivos XML. Asegúrese de descargar el archivo XSL porque proporciona funcionalidad para dar formato y filtrar los contenidos de los archivos XML.

Para ver los archivos de registro, debe crear las credenciales FTP, de la forma siguiente:

1. En la hoja de la aplicación web, haga clic en **Toda la configuración**.
2. Haga clic en **Credenciales de implementación**.
3. Escriba un nombre de usuario y una contraseña.
4. Haga clic en **Save**(Guardar).

![Configurar credenciales de implementación][configure03]

El nombre de usuario de FTP completo es "app\nombreusuario", donde *app* es el nombre de su aplicación web. El nombre de usuario se indica en la tarjeta única de la aplicación web, en **Essentials**

![Credenciales de implementación de FTP][configure02]

## <a name="other-configuration-tasks"></a>Otras tareas de configuración
### <a name="ssl"></a>SSL
En modo básico o estándar puede cargar certificados SSL para un dominio personalizado. Para más información, consulte [Habilitación de HTTPS para una aplicación web]. 

Para ver los certificados cargados, haga clic en **Toda la configuración** > **Dominios personalizados y SSL**.

### <a name="domain-names"></a>Nombres de dominio
Agregue nombres de dominio personalizados para su aplicación web. Para más información, consulte [Configuración de un nombre de dominio personalizado para una aplicación web en Azure App Service].

Para ver los nombres de dominios, haga clic en **Toda la configuración** > **Dominios personalizados y SSL**.

### <a name="deployments"></a>Implementaciones
* Configure la implementación continua. Consulte el artículo sobre el [uso de Git para implementar Web Apps en Azure App Service](app-service-deploy-local-git.md).
* Ranuras de implementación. Consulte [Configuración de entornos de ensayo para Web Apps en Azure App Service].

Para ver las ranuras de implementación, haga clic en **Toda la configuración** > **Ranuras de implementación**.

### <a name="monitoring"></a>Supervisión
En modo estándar o básico, pruebe la disponibilidad de los puntos de conexión HTTP o HTTPS desde ubicaciones geodistribuidas. Una prueba de supervisión da error si el código de respuesta HTTP es un error (4xx o 5xx) o si la respuesta se retrasa más de 30 segundos. Un extremo se considera disponible si sus pruebas de supervisión se realizan correctamente desde todas las ubicaciones especificadas. 

Para obtener más información, consulte [Supervisión de estado de extremo web].

> [!NOTE]
> Si desea empezar a trabajar con Azure App Service antes de inscribirse para abrir una cuenta de Azure, vaya a [Prueba de App Service], donde podrá crear inmediatamente una aplicación web de inicio de corta duración en App Service. No es necesario proporcionar ninguna tarjeta de crédito ni asumir ningún compromiso.
> 
> 

## <a name="next-steps"></a>pasos siguientes
* [Configuración de un nombre de dominio personalizado en Azure App Service]
* [Habilitación de HTTPS para una aplicación en Azure App Service]
* [Escalación de una aplicación web en Azure App Service]
* [Aspectos básicos de supervisión para Web Apps en Azure App Service]

<!-- URL List -->

[ASP.NET SignalR]: http://www.asp.net/signalr
[Azure Portal]: https://portal.azure.com/
[Configuración de un nombre de dominio personalizado en Azure App Service]: ./app-service-web-tutorial-custom-domain.md
[Configuración de entornos de ensayo para Web Apps en Azure App Service]: ./web-sites-staged-publishing.md
[Habilitación de HTTPS para una aplicación en Azure App Service]: ./app-service-web-tutorial-custom-ssl.md
[Supervisión de estado de extremo web]: http://go.microsoft.com/fwLink/?LinkID=279906
[Aspectos básicos de supervisión para Web Apps en Azure App Service]: ./web-sites-monitor.md
[modo de canalización]: http://www.iis.net/learn/get-started/introduction-to-iis/introduction-to-iis-architecture#Application
[Escalación de una aplicación web en Azure App Service]: ./web-sites-scale.md
[Prueba de App Service]: https://azure.microsoft.com/try/app-service/

<!-- IMG List -->

[configure01]: ./media/web-sites-configure/configure01.png
[configure02]: ./media/web-sites-configure/configure02.png
[configure03]: ./media/web-sites-configure/configure03.png
