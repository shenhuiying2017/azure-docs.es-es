---
title: "Preguntas más frecuentes sobre Web App on Linux de Azure App Service | Microsoft Docs"
description: "Este artículo trata sobre las preguntas más frecuentes sobre Web App on Linux de Azure App Service."
keywords: "azure app service, web app, preguntas más frecuentes, linux, oss"
services: app-service
documentationCenter: 
authors: ahmedelnably
manager: erikre
editor: 
ms.assetid: 
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/04/2017
ms.author: aelnably;wesmc
ms.translationtype: Human Translation
ms.sourcegitcommit: ef1e603ea7759af76db595d95171cdbe1c995598
ms.openlocfilehash: 3fb0f6b26f36ef4044c9733ace05c4f59909ddda
ms.contentlocale: es-es
ms.lasthandoff: 06/16/2017


---

# <a name="azure-app-service-web-app-on-linux-faq"></a>Preguntas más frecuentes sobre Web App on Linux de Azure App Service

[!INCLUDE [app-service-linux-preview](../../includes/app-service-linux-preview.md)]


Con el lanzamiento de Web App en Linux, estamos trabajando en incorporar características y realizar mejoras en nuestra plataforma. A continuación encontrará una serie de preguntas más frecuentes que nuestros clientes nos han formulado durante los últimos meses.
Si tiene alguna pregunta, comente el artículo y le responderemos en cuanto sea posible.

## <a name="built-in-images"></a>Imágenes integradas

**P:** Quiero bifurcar los contenedores de Docker integrados que ofrece la plataforma. ¿Dónde puedo encontrar esos archivos?

**R.:** Encontrará todos los archivos de Docker en [GitHub](https://github.com/azure-app-service). Puede encontrar todos los contenedores de Docker en [Docker Hub](https://hub.docker.com/u/appsvc/).

**P.:** ¿Cuál es el valor previsible para la sección del archivo de inicio cuando se configura la pila en tiempo de ejecución?

**R.:** Para Node.Js, puede especificar el archivo de configuración de PM2 o el archivo de script. Para .Net Core, debe especificar el nombre del archivo DLL compilado. Para Ruby, puede especificar un script de Ruby con el que quiera inicializar la aplicación.

## <a name="management"></a>Administración

**P.**: ¿Qué ocurre al hacer clic en botón de reinicio de Azure Portal?

**R.**: Es el equivalente del reinicio de Docker.

**P.:** ¿Puedo usar Secure Shell (SSH) para conectarme a la máquina virtual de contenedor de la aplicación?

**R.**: Sí, puede hacerlo a través del sitio SCM. Consulte el artículo siguiente para obtener más información: [Compatibilidad con SSH para Web App en Linux](./app-service-linux-ssh-support.md)

## <a name="continuous-integrationdeployment"></a>Integración e implementación continuas

**P:** ¿Mi aplicación web sigue usando una imagen de contenedor de Docker antigua después de actualizar la imagen en DockerHub? ¿Se admite la integración e implementación continuas de contenedores personalizados?

**R.**: Para configurar la integración e implementación continua de las imágenes de Docker Hub consulte el siguiente artículo: [Implementación continua de Docker Hub con Web App en Linux](./app-service-linux-ci-cd.md). Para registros privados, puede actualizar el contenedor deteniendo y, luego, iniciando la aplicación web. También puede cambiar o agregar una configuración de aplicación ficticia para forzar una actualización del contenedor.

**P.**: ¿Los entornos de ensayo son compatibles?

**R:** Sí.

**P:** ¿Puedo usar **Web Deploy** para implementar mi aplicación web?

**R:** Sí, tiene que establecer una configuración de aplicación denominada `UseWebDeployScm` en `false`.

## <a name="language-support"></a>Compatibilidad con idiomas

**P.:** ¿Se admiten aplicaciones de .NET Core sin compilar?

**R:** Sí.

**P.:** ¿Admite un compositor como un administrador de dependencias para aplicaciones PHP?

**R:** Sí. Durante una implementación de Git, Kudu debe detectar que va a implementar una aplicación PHP (gracias a la presencia de un archivo composer.json) y que se va a desencadenar una instalación de compositor automáticamente.

## <a name="custom-containers"></a>Contenedores personalizados

**P.:** Utilizo mi propio contenedor personalizado. Mi aplicación reside en el directorio `\home\`, pero no puedo encontrar mis archivos al examinar el contenido mediante el [sitio SCM](https://github.com/projectkudu/kudu) o un cliente FTP. ¿Dónde están mis archivos?

**R.**: Montamos un recurso compartido SMB en el directorio `\home\`. Esto invalida cualquier contenido de dicho directorio.

**P:** ¿Cuál es el formato de la dirección URL del servidor del Registro privado?

**R.**: Tiene que escribir la dirección URL completa del registro, incluidos `http://` o `https://`.

**P:** ¿Cuál es el formato del nombre de la imagen en la opción del Registro privado?

**R:** Debe agregar el nombre completo de la imagen, incluida la dirección URL del Registro privado (p. ej. myacr.azurecr.io/dotnet:latest)

**P:** Quiero exponer más de un puerto en mi imagen de contenedor personalizado. ¿Es posible?

**R.:** Actualmente no se admite.

**P:** ¿Puedo traer mi propio almacenamiento?

**R.:** Actualmente no se admite.

**P:** No puedo examinar el sistema de archivos de mi contenedor personalizado o ejecutar procesos desde el sitio SCM. ¿Por qué ocurre esto?

**R.:** El sitio SCM se ejecuta en un contenedor independiente; no puede comprobar el sistema de archivos o los procesos en ejecución del contenedor de la aplicación.

**P.:** Mi contenedor personalizado escucha a un puerto distinto al puerto 80. ¿Cómo puedo configurar mi aplicación para enrutar las solicitudes hacia ese puerto?

**R.**: Ofrecemos detección de puertos automática, pero también puede especificar un parámetro de la aplicación llamado **PORT** y asignarle el valor del número de puerto esperado.

**P:** ¿Es necesario implementar HTTPS en mi contenedor personalizado?

**R:** No, la plataforma controla la terminación HTTPS en los front-end compartidos.

## <a name="pricing-and-sla"></a>Precios y contrato de nivel de servicio

**P.:** ¿Qué precios tiene usar la versión preliminar pública?

**R.**: Se le cobrará la mitad del número de horas que se ejecute la aplicación, con los precios normales de Azure App Service. Esto significa que hay un descuento del 50 por ciento en los precios normales de Azure App Service.

## <a name="other"></a>Otros

**P:** ¿Cuáles son los caracteres admitidos en los nombres de configuración de aplicación?

**R.:** Solo puede utilizar A-z, a-z, 0-9 y caracteres de subrayado para la configuración de la aplicación.

**P:** ¿Dónde puedo solicitar nuevas características?

**R.:** Puede enviar su idea en el [foro de comentarios de Web Apps](https://aka.ms/webapps-uservoice). Agregue [Linux] en el título de la idea.

## <a name="next-steps"></a>Pasos siguientes
* [¿Qué es Web App on Linux de Azure?](app-service-linux-intro.md)
* [Creación de aplicaciones web en Web App on Linux de Azure](app-service-linux-how-to-create-web-app.md)
* [Compatibilidad con SSH para Web App on Linux de Azure](./app-service-linux-ssh-support.md)
* [Configuración de entornos de ensayo en Azure App Service](./web-sites-staged-publishing.md)
* [Implementación continua de Docker Hub con Azure Web App en Linux](./app-service-linux-ci-cd.md)

