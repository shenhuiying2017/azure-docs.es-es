<properties title="Manage an Azure website" pageTitle="Administrar un sitio web de Azure" description="Vincula a recursos para administrar un sitio web de Microsoft Azure." services="web-sites" solutions="web" documentationCenter="" authors="mwasson" videoId="" scriptId="" manager="wpickett" />

<tags ms.service="web-sites" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="09/16/2014" ms.author="mwasson" />

# Administrar un sitio web de Azure

Este tema contiene vínculos a recursos para administrar un sitio web de Azure. La administración incluye todas las tareas que hacen que un sitio web funcione sin problemas. 

A lo largo de la vida de un sitio, llevará a cabo distintas tareas de administración, desde la implementación inicial hasta el funcionamiento normal pasando por el mantenimiento y las actualizaciones.

- [Antes de implementar su sitio para la producción]
- [Cuando el sitio está en funcionamiento]
- [Cuando actualiza el sitio web]

Muchas tareas de administración de sitios web se pueden hacer desde el portal de Azure. Si quiere más información, vea [Administración de sitios web a través del Portal de administración de Azure](http://azure.microsoft.com/es-es/documentation/articles/web-sites-manage/).

## Antes de implementar su sitio para la producción

### Elija un nivel

Los sitios web de Azure se ofrecen en cuatro niveles: gratis, compartido, básico y estándar. Para más información sobre las funciones y los precios de cada nivel, vea [Detalles de precios](http://azure.microsoft.com/es-es/pricing/details/websites/). 

Los - [planes de hospedaje web](http://azure.microsoft.com/es-es/documentation/articles/azure-web-sites-web-hosting-plans-in-depth-overview) le permiten agrupar varios sitios web en el mismo nivel.
- Siempre puede [cambiar los niveles](http://azure.microsoft.com/es-es/documentation/articles/web-sites-scale/) después de crear su sitio web.

### Configuración

Use el [Portal de administración de Azure](https://manage.windowsazure.com/) para establecer varias opciones de configuración. Si quiere detalles, vea [Configuración de sitios web](http://azure.microsoft.com/es-es/documentation/articles/web-sites-configure/). Esta es una lista de comprobación breve:

- Seleccione **versiones de tiempo de ejecución** para .NET, PHP, Java o Python si es necesario.
- Habilite **WebSockets** si su sitio web usa el protocolo WebSocket. (Se incluyen las aplicaciones que usan [ASP.NET SignalR](http://www.asp.net/signalr) o [socket.io](http://azure.microsoft.com/es-es/documentation/articles/web-sites-nodejs-chat-app-socketio/)).
- ¿Ejecuta trabajos web continuamente? Si es así, habilite la opción **Siempre activado**.
- Establezca el **documento predeterminado**, como index.html.

Además de estas opciones de configuración básicas, puede configurar lo siguiente:

Cifrado - **Capa de sockets seguros (SSL)**. Para usar SSL con un nombre de dominio personalizado, debe obtener un certificado SSL y configurar el sitio web para que lo use. Vea [Habilitación de HTTPS en un sitio web de Azure](http://azure.microsoft.com/es-es/documentation/articles/web-sites-configure-ssl-certificate/).
- **Nombre de dominio personalizado.** Su sitio web tiene automáticamente un subdominio en azurewebsites.net. Puede asociar un nombre de dominio personalizado, como contoso.com. Vea [Configuración de un nombre de dominio personalizado](http://azure.microsoft.com/es-es/documentation/articles/web-sites-custom-domain-name/).

Configuración específica por idioma:

- **PHP**: [Configuración de PHP en Sitios web Azure](http://azure.microsoft.com/es-es/documentation/articles/web-sites-php-configure/).
- **Python**: [Configuración de Python con Sitios web Azure](http://azure.microsoft.com/es-es/documentation/articles/web-sites-python-configure/)


## Cuando el sitio está en funcionamiento

Cuando el sitio esté en funcionamiento, querrá asegurarse de que está disponible y de que se escala para satisfacer el tráfico de los usuarios. También puede necesitar solucionar errores.

### Supervisión

- Desde el portal de administración, puede [agregar métricas de rendimiento](http://azure.microsoft.com/es-es/documentation/articles/web-sites-monitor), como el uso de la CPU y el número de solicitudes de clientes.
- Si quiere más detalles, use New Relic para supervisar y administrar el rendimiento. Vea [Administración del rendimiento de la aplicación New Relic en Sitios web Azure](http://azure.microsoft.com/es-es/documentation/articles/store-new-relic-web-sites-dotnet-application-performance-management/).
- [Escale su sitio web](http://azure.microsoft.com/es-es/documentation/articles/web-sites-scale/) en respuesta al tráfico. En función de su nivel, puede escalar el número de máquinas virtuales y/o el tamaño de las instancias de las máquinas virtuales. En el plan estándar, también puede configurar la escala automática para que el sitio escale automáticamente, bien según una programación fija, o bien en respuesta a la carga. 
 
### Copias de seguridad

- Establezca las [copias de seguridad automáticas](http://azure.microsoft.com/es-es/documentation/articles/web-sites-backup/) de su sitio web. Obtenga más información sobre las copias de seguridad en [este vídeo](http://azure.microsoft.com/es-es/documentation/videos/azure-websites-automatic-and-easy-backup/).
- Conozca las opciones para la [recuperación de bases de datos](http://msdn.microsoft.com/es-es/library/azure/hh852669.aspx) en Base de datos SQL de Azure.

### Solución de problemas

- Si algo falla, puede [solucionar los problemas en Visual Studio](http://azure.microsoft.com/es-es/documentation/articles/web-sites-dotnet-troubleshoot-visual-studio/#remotedebug) usando registros de diagnóstico y depuración activa en la nube. 
- Fuera de Visual Studio, hay varias formas de recopilar registros de diagnóstico. Vea [Habilitación del registro de diagnóstico para Sitios web Azure](http://azure.microsoft.com/es-es/documentation/articles/web-sites-enable-diagnostic-log/).
- Para aplicaciones Node.js, vea [Depuración de una aplicación Node.js en Sitios web Azure](http://azure.microsoft.com/es-es/documentation/articles/web-sites-nodejs-debug/).

### Restauración de datos

- [Restaure](http://azure.microsoft.com/es-es/documentation/articles/web-sites-restore/) un sitio web del que hizo copia de seguridad.


## Cuando actualiza el sitio web

Si no ha habilitado las copias de seguridad automáticas, puede crear una [copia de seguridad manual](http://azure.microsoft.com/es-es/documentation/articles/web-sites-backup/).

Puede usar una [implementación de ensayo](http://azure.microsoft.com/es-es/documentation/articles/web-sites-staged-publishing/). Esta opción le permite publicar actualizaciones en una implementación provisional que se ejecuta en paralelo a su implementación de producción. 

Si usa Visual Studio Online, puede configurar una implementación continua desde el control de código fuente:

- [Uso del control de versiones de Team Foundation (TFVC)](http://azure.microsoft.com/es-es/documentation/articles/cloud-services-continuous-delivery-use-vso/).
- [Usar Git](http://azure.microsoft.com/es-es/documentation/articles/cloud-services-continuous-delivery-use-vso-git/)
 

 
<!-- Anchors. -->


[Antes de implementar su sitio para la producción]: #before-you-deploy-your-site-to-production
[Cuando el sitio está en funcionamiento]: #while-your-website-is-running
[Cuando actualiza el sitio web]: #when-you-update-your-website

 
