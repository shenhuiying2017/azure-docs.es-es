---
title: "Preguntas más frecuentes sobre Azure App Service en Linux | Microsoft Docs"
description: "Peguntas más frecuentes sobre Azure App Service en Linux."
keywords: "azure app service, web app, preguntas más frecuentes, linux, oss"
services: app-service
documentationCenter: 
author: ahmedelnably
manager: cfowler
editor: 
ms.assetid: 
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/04/2017
ms.author: aelnably;wesmc
ms.openlocfilehash: b22d5f3497c388192764aa6b4ee8c95fec568bd8
ms.sourcegitcommit: b07d06ea51a20e32fdc61980667e801cb5db7333
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/08/2017
---
# <a name="azure-app-service-on-linux-faq"></a>Peguntas más frecuentes sobre Azure App Service en Linux

Con el lanzamiento de App Service en Linux, estamos trabajando en incorporar características y realizar mejoras en nuestra plataforma. Este artículo proporciona respuestas a preguntas que nuestros clientes nos han formulado recientemente.

Si tiene alguna pregunta, comente el artículo y le responderemos en cuanto sea posible.

## <a name="built-in-images"></a>Imágenes integradas

**Quiero bifurcar los contenedores de Docker integrados que ofrece la plataforma. ¿Dónde puedo encontrar esos archivos?**

Puede encontrar todos los archivos de Docker en [GitHub](https://github.com/azure-app-service). Puede encontrar todos los contenedores de Docker en [Docker Hub](https://hub.docker.com/u/appsvc/).

**¿Cuáles son los valores previsibles para la sección del archivo de inicio cuando se configura la pila en tiempo de ejecución?**

Para Node.js, puede especificar el archivo de configuración de PM2 o el archivo de script. Para .Net Core, debe especificar el nombre del archivo DLL compilado. Para Ruby, puede especificar un script de Ruby con el que quiera inicializar la aplicación.

## <a name="management"></a>Administración

**¿Qué ocurre al hacer clic en botón de reinicio de Azure Portal?**

Esta acción es igual a un reinicio de Docker.

**¿Puedo usar Secure Shell (SSH) para conectarme a la máquina virtual del contenedor de la aplicación?**

Sí, puede hacerlo a través del sitio de administración de control de código fuente (SCM).

**¿Cómo se puede crear un plan de App Service en Linux a través de un SDK o una plantilla de Azure Resource Manager?**

Debe establecer el campo **reservado** del servicio de aplicación en *true*.

## <a name="continuous-integration-and-deployment"></a>Integración e implementación continuas

**Mi aplicación web sigue usando una imagen de contenedor de Docker antigua después de actualizar la imagen en Docker Hub. ¿Se admite la integración e implementación continuas de contenedores personalizados?**

Para configurar la integración e implementación continua de las imágenes de Azure Container Registry o Docker Hub consulte el siguiente artículo: [Implementación continua con Web App for Containers](./app-service-linux-ci-cd.md). Para registros privados, puede actualizar el contenedor deteniendo y, luego, iniciando la aplicación web. También puede cambiar o agregar una configuración de aplicación ficticia para forzar una actualización del contenedor.

**¿Los entornos de ensayo son compatibles?**

Sí.

**¿Puedo usar *Web Deploy* para implementar mi aplicación web?**

Sí, es necesario establecer un valor de la aplicación llamado `WEBSITE_WEBDEPLOY_USE_SCM` en *false*.

**Se produce un error en la implementación de GIT de la aplicación cuando se usa la aplicación web de Linux. ¿Cómo puedo solucionar el problema?**

Si se produce un error en la implementación de GIT en la aplicación web de Linux, puede elegir las opciones alternativas siguientes para implementar el código de aplicación:

- Use la característica Entrega continua (versión preliminar): puede almacenar el código fuente de la aplicación en un repositorio de GIT de Team Services o en un repositorio GitHub para usar Azure Continuous Delivery. Para más detalles, consulte el artículo sobre cómo [configurar Entrega continua para una aplicación web de Linux](https://blogs.msdn.microsoft.com/devops/2017/05/10/use-azure-portal-to-setup-continuous-delivery-for-web-app-on-linux/).

- Use la [API ZIP deploy](https://github.com/projectkudu/kudu/wiki/Deploying-from-a-zip-file): para usar esta API, [use SSH en la aplicación web](https://docs.microsoft.com/azure/app-service/containers/app-service-linux-ssh-support#making-a-client-connection) y vaya a la carpeta en la que desea implementar el código. Ejecute lo siguiente:

   ```
   curl -X POST -u <user> --data-binary @<zipfile> https://{your-sitename}.scm.azurewebsites.net/api/zipdeploy
   ```

   Si recibe un error que indica que no se encuentra el comando `curl`, asegúrese de que instalar curl mediante `apt-get install curl` antes de ejecutar el comando `curl` anterior.

## <a name="language-support"></a>Compatibilidad con idiomas

**Quiero usar protocolos WebSocket en mi aplicación Node.js. ¿Hay alguna opción o configuración especial que deba establecer?**

Sí, deshabilite `perMessageDeflate` en el código de Node.js en el servidor. Por ejemplo, si usa socket.io, haga lo siguiente:
```
var io = require('socket.io')(server,{
  perMessageDeflate :false
});
```

**¿Se admiten aplicaciones de .NET Core sin compilar?**

Sí.

**¿Admite un compositor como un administrador de dependencias para aplicaciones PHP?**

Sí. Durante una implementación de Git, Kudu debe detectar que va a implementar una aplicación PHP (gracias a la presencia de un archivo composer.lock) y Kudu desencadenará una instalación del compositor automáticamente.

## <a name="custom-containers"></a>Contenedores personalizados

**Utilizo mi propio contenedor personalizado. Deseo que la plataforma monte un recurso compartido de SMB en el directorio `/home/`.**

Puede hacerlo estableciendo el valor `WEBSITES_ENABLE_APP_SERVICE_STORAGE` de la aplicación en *true* o mediante la eliminación completa del valor de la aplicación. Tenga en cuenta que de este modo se evitará que el contenedor se reinicie cuando el almacenamiento de la plataforma cambie. 

>[!NOTE]
>Si el valor `WEBSITES_ENABLE_APP_SERVICE_STORAGE` es *false*, el directorio `/home/` no se compartirá entre instancias de escala y los archivos que se escriben no se conservarán al reiniciar.

**Mi contenedor personalizado tarda mucho tiempo en iniciarse y la plataforma reinicia el contenedor antes de que finalice el inicio.**

Puede configurar el tiempo que va a esperar la plataforma antes de reiniciar el contenedor. Para ello, establezca el valor `WEBSITES_CONTAINER_START_TIME_LIMIT` de la aplicación en el valor que desee. El valor predeterminado es de 230 segundos y el valor máximo es de 600 segundos.

**¿Cuál es el formato de la dirección URL del servidor de registro privado?**

Debe escribir la dirección URL completa del registro, incluidos `http://` o `https://`.

**¿Cuál es el formato del nombre de la imagen en la opción del registro privado?**

Agregue el nombre de la imagen completo, incluida la dirección URL de registro privado (por ejemplo, myacr.azurecr.io/dotnet:latest). Los nombres de imágenes que usan un puerto personalizado [no se pueden especificar a través del portal](https://feedback.azure.com/forums/169385-web-apps/suggestions/31304650). Para establecer `docker-custom-image-name`, use la [`az`herramienta de línea de comandos](https://docs.microsoft.com/cli/azure/webapp/config/container?view=azure-cli-latest#az_webapp_config_container_set).

**¿Se puede exponer más de un puerto en mi imagen de contenedor personalizado?**

Actualmente no se admite exponer más de un puerto.

**¿Puedo traer mi propio almacenamiento?**

Actualmente no se admite traer su propio almacenamiento.

**¿Por qué no puedo examinar el sistema de archivos de mi contenedor personalizado o ejecutar procesos desde el sitio de SCM?**

El sitio de SCM se ejecuta en un contenedor independiente. no puede comprobar el sistema de archivos o los procesos en ejecución del contenedor de la aplicación.

**Mi contenedor personalizado escucha en un puerto distinto al puerto 80. ¿Cómo puedo configurar mi aplicación para enrutar las solicitudes hacia ese puerto?**

Disponemos de detección automática de puerto. También puede especificar un valor de la aplicación llamado *WEBSITES_PORT* y asignarle el valor del número de puerto esperado. Anteriormente, la plataforma utilizaba el valor de aplicación *PORT*. Tenemos previsto para dejar de utilizar este valor de la aplicación y usar *WEBSITES_PORT* exclusivamente.

**¿Es necesario implementar HTTPS en mi contenedor personalizado?**

No, la plataforma controla la terminación HTTPS en los front-end compartidos.

## <a name="pricing-and-sla"></a>Precios y contrato de nivel de servicio

**¿Cuál es el precio ahora que el servicio está disponible con carácter general?**

Se le cobrará el número de horas que se ejecute la aplicación, con los precios normales de Azure App Service.

## <a name="other-questions"></a>Otras preguntas

**¿Cuáles son los caracteres admitidos en los nombres de configuración de la aplicación?**

Puede usar solo letras (A-Z, a-z), números (0-9) y el carácter de subrayado (_) para la configuración de la aplicación.

**¿Dónde puedo solicitar nuevas características?**

Puede enviar su idea en el [foro de comentarios de Web Apps](https://aka.ms/webapps-uservoice). Agregue [Linux] en el título de la idea.

## <a name="next-steps"></a>Pasos siguientes

* [¿Qué es Azure App Service en Linux?](app-service-linux-intro.md)
* [Configuración de entornos de ensayo en Azure App Service](../../app-service/web-sites-staged-publishing.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json)
* [Implementación continua con Web App for Containers](./app-service-linux-ci-cd.md)
