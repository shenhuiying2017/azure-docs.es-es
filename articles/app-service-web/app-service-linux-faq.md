---
title: "Preguntas más frecuentes sobre Azure App Service Web Apps en Linux | Microsoft Docs"
description: "Preguntas más frecuentes sobre Azure App Service Web Apps en Linux"
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
ms.date: 02/14/2017
ms.author: aelnably
translationtype: Human Translation
ms.sourcegitcommit: 72b2d9142479f9ba0380c5bd2dd82734e370dee7
ms.openlocfilehash: 148bc76b7f3e09745cbecfa41710a5e949704948
ms.lasthandoff: 03/08/2017


---

# <a name="azure-app-service-web-apps-on-linux-faq"></a>Preguntas más frecuentes sobre Azure App Service Web Apps en Linux

Con el lanzamiento de Azure App Service en Linux (actualmente en versión preliminar), estamos trabajando en incorporar características y realizar mejoras en nuestra plataforma. A continuación encontrará una serie de preguntas más frecuentes que nuestros clientes nos han formulado durante los últimos meses.
Si tiene alguna pregunta, comente el artículo y le responderemos tan pronto como sea posible.

## <a name="built-in-images"></a>Imágenes integradas

**P:** Quiero bifurcar los contenedores de Docker integrados que ofrece la plataforma. ¿Dónde puedo encontrar esos archivos?

**R.:** Encontrará todos los archivos de Docker en [GitHub](https://github.com/azure-app-service). Puede encontrar todos los contenedores de Docker en [Docker Hub](https://hub.docker.com/u/appsvc/).

**P.:** ¿Cuál es el valor previsible para la sección del archivo de inicio cuando se configura la pila en tiempo de ejecución?

**R.:** Para Node.Js, puede especificar el archivo de configuración de PM2 o el archivo de script. Para .Net Core, debe especificar el nombre del archivo DLL compilado. Para Ruby, puede especificar un script de Ruby con el que quiera inicializar la aplicación.

## <a name="management"></a>Administración

**P.:**: Hice clic en el botón de reinicio en Azure Portal, pero no se reinició mi aplicación web. ¿Por qué?

**R.:** Estamos trabajando para habilitar el botón de reinicio en un futuro próximo. En este momento, dispone de dos opciones:
- Agregar o cambiar una configuración de aplicación ficticia. Esto obligará a que la aplicación web se reinicie.
- Detenga y después inicie la aplicación web.

**P.:** ¿Puedo usar Secure Shell (SSH) para conectarme a la máquina virtual de contenedor de la aplicación?

**R**: No. Le proporcionaremos una forma de usar SSH en el contenedor de la aplicación en una próxima versión.

## <a name="continuous-integrationdeployment"></a>Integración e implementación continuas

**P.: ** ¿Mi aplicación web sigue usando una imagen de contenedor de Docker antigua después de actualizar la imagen en DockerHub? ¿Se admite la integración e implementación continuas de contenedores personalizados?

**R.:** Puede actualizar el contenedor deteniendo y, luego, iniciando la aplicación web. También puede cambiar o agregar una configuración de aplicación ficticia para forzar una actualización del contenedor. Tenemos intención de lanzar una característica de integración e implementación continuas para contenedores personalizados en una versión futura.

## <a name="language-support"></a>Compatibilidad con idiomas

**P.:** ¿Se admiten aplicaciones de .NET Core sin compilar?

**R**: No. Debe implementar aplicaciones de .NET Core compiladas con todas las dependencias. Planeamos realizar una implementación completa y una experiencia de compilación en una futura versión.

**P.: ¿**Admite un compositor como un administrador de dependencias para aplicaciones PHP?

**R**: No. Debe implementar las aplicaciones PHP con todas las dependencias. Planeamos una experiencia de implementación completa en una futura versión.

## <a name="custom-containers"></a>Contenedores personalizados

**P.:** Utilizo mi propio contenedor personalizado. Mi aplicación reside en el directorio \home\, pero no puedo encontrar mis archivos al examinar el contenido mediante el [sitio SCM](https://github.com/projectkudu/kudu) o un cliente FTP. ¿Dónde están mis archivos?

**R.:** Montamos un recurso compartido SMB en el directorio \home\. Esto invalida cualquier contenido de dicho directorio.

**P: ** Quiero exponer más de un puerto en mi imagen de contenedor personalizado. ¿Es posible?

**R.:** Actualmente no se admite.

**P:** ¿Puedo traer mi propio almacenamiento?

**R.:** Actualmente no se admite.

**P: ** No puedo examinar el sistema de archivos de mi contenedor personalizado o ejecutar procesos desde el sitio SCM. ¿Por qué ocurre esto?

**R.:** El sitio SCM se ejecuta en un contenedor independiente; no puede comprobar el sistema de archivos o los procesos en ejecución del contenedor de la aplicación.

**P.:** Mi contenedor personalizado escucha a un puerto distinto al puerto 80. ¿Cómo puedo configurar mi aplicación para enrutar las solicitudes hacia ese puerto?

**R.:** Puede especificar un parámetro de la aplicación llamado **PORT** y asignarle el valor del número de puerto esperado.

## <a name="pricing-and-sla"></a>Precios y contrato de nivel de servicio

**P.: ¿** ¿Qué precios tiene usar la versión preliminar pública?

**R.:** Se le cobrará la mitad del número de horas que se ejecute la aplicación, con los precios normales de Azure App Service. Esto significa que hay un descuento del 50 por ciento en los precios normales de Azure App Service.

## <a name="other"></a>Otros

**P: ** ¿Cuáles son los caracteres admitidos en los nombres de configuración de aplicación?

**R.:** Solo puede utilizar A-z, a-z, 0-9 y caracteres de subrayado para la configuración de la aplicación.

**P: ** ¿Dónde puedo solicitar nuevas características?

**R.:** Puede enviar su idea en el [foro de comentarios de Web Apps](https://aka.ms/webapps-uservoice). Agregue [Linux] en el título de la idea.

## <a name="next-steps"></a>Pasos siguientes
* [¿Qué es App Service en Linux?](app-service-linux-intro.md)
* [Creación de aplicaciones web en App Service en Linux](app-service-linux-how-to-create-a-web-app.md)

