---
title: "Preguntas más frecuentes sobre Azure App Service Web Apps en Linux | Microsoft Docs"
description: "Preguntas más frecuentes sobre Azure App Service Web Apps en Linux."
keywords: "azure app service, web app, preguntas más frecuentes, linux, oss"
services: app-service
documentationCenter: 
authors: aelnably
manager: wpickett
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
ms.sourcegitcommit: d4a5260dde1994bbf4ef4467eddd2fb80a2199b0
ms.openlocfilehash: 22c6c22ba3123555d279fac087a6be2f99bde8d7


---

# <a name="azure-app-service-web-apps-on-linux-faq"></a>Preguntas más frecuentes sobre Azure App Service Web Apps en Linux #

Con el lanzamiento de Azure App Service en Linux (actualmente en versión preliminar), estamos trabajando en incorporar características y realizar mejoras en nuestra plataforma. A continuación encontrará una serie de preguntas más frecuentes que nuestros clientes nos han formulado durante los últimos meses.
Si tiene alguna pregunta, comente el artículo y le responderemos tan pronto como sea posible.

## <a name="built-in-images"></a>Imágenes integradas ##

**P:** Quiero bifurcar los contenedores de Docker integrados que ofrece la plataforma. ¿Dónde puedo encontrar esos archivos?

**R:** Puede encontrar todos los archivos de Docker en https://github.com/azure-app-service. Puede encontrar todos los contenedores de Docker en https://hub.docker.com/u/appsvc/.

## <a name="management"></a>Administración ##

**P:** Presiono el botón de reinicio en el portal pero mi aplicación web no se reinicia, ¿por qué?

.**R:** Estamos trabajando para habilitar el botón de restablecimiento próximamente; ahora tiene dos opciones.
1. Agregar o cambiar una configuración de aplicación ficticia, lo que exigirá el reinicio de la aplicación web. 
2. Detener y después iniciar la aplicación web.

**P:** ¿Puedo usar SSH en la VM?

**R:** No, próximamente le proporcionaremos una forma de usar SSH en el contenedor de la aplicación.

## <a name="continous-integration--deployment"></a>Integración e implementación continuas ##

**P: ** ¿Mi aplicación web sigue usando una imagen de contenedor de Docker antigua después de actualizar la imagen en DockerHub? ¿Se admite la integración e implementación continuas de contenedores personalizados?

**R:** Puede actualizar el contenedor, ya sea deteniendo y luego iniciando la aplicación web, ya cambiando o agregando una configuración de aplicación ficticia para exigir una actualización del contenedor. Próximamente tendremos una característica de integración e implementación continuas para contenedores personalizados.

## <a name="language-support"></a>Compatibilidad con lenguajes ##

**P:** ¿Se admiten aplicaciones de .net core sin compilar?

**R:** No, debe implementar la aplicación de .net core compilada con todas las dependencias. Próximamente disfrutará de una experiencia de implementación y compilación completa.

## <a name="custom-containers"></a>Contenedores personalizados ##

**P:** Utilizo mi propio contenedor personalizado. Mi aplicación reside en el directorio \home\, pero no puedo encontrar mis archivos al examinar el contenido mediante el sitio SCM o un cliente ftp. ¿Dónde están mis archivos?

**R:** Se monta un recurso compartido SMB para el directorio \home\, lo que invalida cualquier contenido allí existente.

**P: ** Quiero exponer más de un puerto en mi imagen de contenedor personalizado. ¿Es posible?

**R:** Actualmente no se admite.

**P:** ¿Puedo traer mi propio almacenamiento?

**R:** Actualmente esto no se admite; estamos pensando en proporcionar esta compatibilidad próximamente.

**P: ** No puedo examinar el sistema de archivos de mi contenedor personalizado o ejecutar procesos desde el sitio SCM. ¿Por qué ocurre esto?

**R:** El sitio SCM se ejecuta en un contenedor independiente; no puede comprobar el sistema de archivos o los procesos en ejecución del contenedor de la aplicación.

## <a name="pricing-and-sla"></a>Precios y contrato de nivel de servicio ##

**P: ** ¿Cuál es el precio mientras se encuentra en versión preliminar pública?

**R:** Se le cobrará la mitad del número de horas que se ejecuta la aplicación, con el precio normal de Azure App Service; esto significa un 50 % de descuento sobre el precio normal de Azure App Service.

## <a name="other"></a>Otros ##

**P: ** ¿Cuáles son los caracteres admitidos en los nombres de configuración de aplicación?

**R:** Solo puede utilizar A-z, a-z, 0-9 y caracteres de subrayado para la configuración de la aplicación.

**P: ** ¿Dónde puedo solicitar nuevas características?

**R:** Puede enviar su idea aquí: https://aka.ms/webapps-uservoice. Agregue [Linux] en el título de la idea.

## <a name="next-steps"></a>Pasos siguientes
* [Introducción a App Service en Linux](./app-service-linux-intro.md) 
* [¿Qué es App Service en Linux?](app-service-linux-intro.md)
* [Creación de aplicaciones web en App Service en Linux](./app-service-linux-how-to-create-a-web-app.md)



<!--HONumber=Feb17_HO3-->


