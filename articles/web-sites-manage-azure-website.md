<properties title="Manage an Azure website" pageTitle="Manage an Azure website" description="Links to resources for managing a Microsoft Azure website." services="web-sites" solutions="web" documentationCenter="" authors="mwasson" videoId="" scriptId="" />

<tags ms.service="web-sites" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="09/16/2014" ms.author="mwasson"></tags>

# Administrar un sitio web de Azure

Este tema contiene vínculos a recursos para administrar un sitio web de Azure. La administración incluye todas las tareas que hacen que un sitio web funcione sin problemas.

A lo largo de la vida de un sitio, llevará a cabo distintas tareas de administración, desde la implementación inicial hasta el funcionamiento normal pasando por el mantenimiento y las actualizaciones.

-   [Antes de implementar su sitio para la producción][Antes de implementar su sitio para la producción]
-   [Cuando el sitio está en funcionamiento][Cuando el sitio está en funcionamiento]
-   [Cuando actualiza el sitio web][Cuando actualiza el sitio web]

Muchas tareas de administración de sitios web se pueden hacer desde el portal de Azure. Si quiere más información, vea [Administrar sitios web a través del portal de administración de Azure][Administrar sitios web a través del portal de administración de Azure].

## Antes de implementar su sitio para la producción

### Elija un nivel

Los sitios web de Azure se ofrecen en cuatro niveles: gratis, compartido, básico y estándar. Para más información sobre las funciones y los precios de cada nivel, vea [Detalles de precios][Detalles de precios].

-   Los [planes de hospedaje web][planes de hospedaje web] le permiten agrupar varios sitios web en el mismo nivel.
-   Siempre puede [cambiar los niveles][cambiar los niveles] después de crear su sitio web.

### Configuración

Use el [portal de administración de Azure][portal de administración de Azure] para establecer varias opciones de configuración. Si quiere detalles, vea [Configuración de sitios web][Configuración de sitios web]. Esta es una lista de comprobación breve:

-   Seleccione **versiones de tiempo de ejecución** para .NET, PHP, Java o Python si es necesario.
-   Habilite **WebSockets** si su sitio web usa el protocolo WebSocket. (Se incluyen las aplicaciones que usan [ASP.NET SignalR][ASP.NET SignalR] o [socket.io][socket.io]).
-   ¿Ejecuta trabajos web continuamente? Si es así, habilite la opción **Siempre activado**.
-   Establezca el **documento predeterminado**, como index.html.

Además de estas opciones de configuración básicas, puede configurar lo siguiente:

-   Cifrado **Capa de sockets seguros (SSL)**. Para usar SSL con un nombre de dominio personalizado, debe obtener un certificado SSL y configurar el sitio web para que lo use. Vea [Habilitación de HTTPS en un sitio web de Azure][Habilitación de HTTPS en un sitio web de Azure].
-   **Nombre de dominio personalizado.** Su sitio web tiene automáticamente un subdominio en azurewebsites.net. Puede asociar un nombre de dominio personalizado, como contoso.com. Vea [Configuración de un nombre de dominio personalizado][Configuración de un nombre de dominio personalizado].

Configuración específica por idioma:

-   **PHP**: [Configuración de PHP en Sitios web Azure][Configuración de PHP en Sitios web Azure].
-   **Python**: [Configuración de Python en Sitios web Azure][Configuración de Python en Sitios web Azure]

## Cuando el sitio está en funcionamiento

Cuando el sitio esté en funcionamiento, querrá asegurarse de que está disponible y de que escala para satisfacer el tráfico de los usuarios. También puede necesitar solucionar errores.

### Supervisión

-   Desde el portal de administración, puede [agregar métricas de rendimiento][agregar métricas de rendimiento], como el uso de la CPU y el número de solicitudes de clientes.
-   Si quiere más detalles, use New Relic para supervisar y administrar el rendimiento. Vea [Administración del rendimiento de la aplicación New Relic en Sitios web Azure][Administración del rendimiento de la aplicación New Relic en Sitios web Azure].
-   [Escale su sitio web][cambiar los niveles] en respuesta al tráfico. En función de su nivel, puede escalar el número de máquinas virtuales y/o el tamaño de las instancias de las máquinas virtuales. En el plan estándar, también puede configurar la escala automática para que el sitio escale automáticamente, bien según una programación fija, o bien en respuesta a la carga.

### Backups

-   Establezca las [copias de seguridad automáticas][copias de seguridad automáticas] en su sitio web. Más información sobre las copias de seguridad en [este vídeo][este vídeo].
-   Conozca las opciones para la [recuperación de bases de datos][recuperación de bases de datos] en Base de datos SQL de Azure.

### Solución de problemas

-   Si algo falla, puede [solucionar los problemas en Visual Studio][solucionar los problemas en Visual Studio] usando registros de diagnóstico y depuración activa en la nube.
-   Fuera de Visual Studio, hay varias formas de recopilar registros de diagnóstico. Vea [Habilitación del registro de diagnóstico para Sitios web Azure][Habilitación del registro de diagnóstico para Sitios web Azure].
-   Para aplicaciones Node.js, vea [Depuración de una aplicación Node.js en Sitios web Azure][Depuración de una aplicación Node.js en Sitios web Azure].

### Restauración de datos

-   [Restaure][Restaure] un sitio web del que hizo copia de seguridad.

## Cuando actualiza el sitio web

Si no ha habilitado las copias de seguridad automáticas, puede crear una [copia de seguridad manual][copias de seguridad automáticas].

Puede usar una [implementación de ensayo][implementación de ensayo]. Esta opción le permite publicar actualizaciones en una implementación provisional que se ejecuta en paralelo a su implementación de producción.

Si usa Visual Studio Online, puede configurar una implementación continua desde el control de código fuente:

-   [Usar el control de versiones de Team Foundation (TFVC)][Usar el control de versiones de Team Foundation (TFVC)]
-   [Usar Git][Usar Git]

<!-- Anchors. -->

  [Antes de implementar su sitio para la producción]: #before-you-deploy-your-site-to-production
  [Cuando el sitio está en funcionamiento]: #while-your-website-is-running
  [Cuando actualiza el sitio web]: #when-you-update-your-website
  [Administrar sitios web a través del portal de administración de Azure]: http://azure.microsoft.com/es-es/documentation/articles/web-sites-manage/
  [Detalles de precios]: http://azure.microsoft.com/es-es/pricing/details/websites/
  [planes de hospedaje web]: http://azure.microsoft.com/es-es/documentation/articles/azure-web-sites-web-hosting-plans-in-depth-overview
  [cambiar los niveles]: http://azure.microsoft.com/es-es/documentation/articles/web-sites-scale/
  [portal de administración de Azure]: https://manage.windowsazure.com/
  [Configuración de sitios web]: http://azure.microsoft.com/es-es/documentation/articles/web-sites-configure/
  [ASP.NET SignalR]: http://www.asp.net/signalr
  [socket.io]: http://azure.microsoft.com/es-es/documentation/articles/web-sites-nodejs-chat-app-socketio/
  [Habilitación de HTTPS en un sitio web de Azure]: http://azure.microsoft.com/es-es/documentation/articles/web-sites-configure-ssl-certificate/
  [Configuración de un nombre de dominio personalizado]: http://azure.microsoft.com/es-es/documentation/articles/web-sites-custom-domain-name/
  [Configuración de PHP en Sitios web Azure]: http://azure.microsoft.com/es-es/documentation/articles/web-sites-php-configure/
  [Configuración de Python en Sitios web Azure]: http://azure.microsoft.com/es-es/documentation/articles/web-sites-python-configure/
  [agregar métricas de rendimiento]: http://azure.microsoft.com/es-es/documentation/articles/web-sites-monitor
  [Administración del rendimiento de la aplicación New Relic en Sitios web Azure]: http://azure.microsoft.com/es-es/documentation/articles/store-new-relic-web-sites-dotnet-application-performance-management/
  [copias de seguridad automáticas]: http://azure.microsoft.com/es-es/documentation/articles/web-sites-backup/
  [este vídeo]: http://azure.microsoft.com/es-es/documentation/videos/azure-websites-automatic-and-easy-backup/
  [recuperación de bases de datos]: http://msdn.microsoft.com/es-es/library/azure/hh852669.aspx
  [solucionar los problemas en Visual Studio]: http://azure.microsoft.com/es-es/documentation/articles/web-sites-dotnet-troubleshoot-visual-studio/#remotedebug
  [Habilitación del registro de diagnóstico para Sitios web Azure]: http://azure.microsoft.com/es-es/documentation/articles/web-sites-enable-diagnostic-log/
  [Depuración de una aplicación Node.js en Sitios web Azure]: http://azure.microsoft.com/es-es/documentation/articles/web-sites-nodejs-debug/
  [Restaure]: http://azure.microsoft.com/es-es/documentation/articles/web-sites-restore/
  [implementación de ensayo]: http://azure.microsoft.com/es-es/documentation/articles/web-sites-staged-publishing/
  [Usar el control de versiones de Team Foundation (TFVC)]: http://azure.microsoft.com/es-es/documentation/articles/cloud-services-continuous-delivery-use-vso/
  [Usar Git]: http://azure.microsoft.com/es-es/documentation/articles/cloud-services-continuous-delivery-use-vso-git/
