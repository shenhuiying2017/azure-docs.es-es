---
title: "Introducción a Azure Web App on Linux | Microsoft Docs"
description: "Obtenga información sobre Azure Web App en Linux."
keywords: azure app service, linux, oss
services: app-service
documentationcenter: 
author: naziml
manager: erikre
editor: 
ms.assetid: bc85eff6-bbdf-410a-93dc-0f1222796676
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/16/2017
ms.author: naziml;wesmc
ms.translationtype: HT
ms.sourcegitcommit: 25e4506cc2331ee016b8b365c2e1677424cf4992
ms.openlocfilehash: 67dee77dd4e46d097358d86626a859b7dc7982e7
ms.contentlocale: es-es
ms.lasthandoff: 08/24/2017

---
# <a name="introduction-to-azure-web-app-on-linux"></a>Introducción a Azure Web App en Linux

[!INCLUDE [app-service-linux-preview](../../includes/app-service-linux-preview.md)]

## <a name="overview"></a>Información general
Los clientes pueden usar Web App on Linux para hospedar aplicaciones web de forma nativa en Linux para pilas de aplicaciones admitidas. En la sección siguiente se muestran las pilas de aplicaciones que son compatibles actualmente. 

## <a name="features"></a>Características
Web App on Linux admite actualmente las siguientes pilas de aplicaciones:

* Node.js
    * 4.4.
    * 4.5.
    * 6.2
    * 6.6
    * 6.9
    * 6.10
    * 6.11
    * 8.0
    * 8.1
* PHP
    * 5.6
    * 7.0
* .Net Core
    * 1.0
    * 1.1
* Ruby
    * 2.3

Los clientes pueden implementar sus aplicaciones mediante:

* FTP
* Git local
* GitHub
* Bitbucket

Para el escalado de aplicaciones:

* Los clientes pueden escalar o reducir aplicaciones web verticalmente mediante la modificación del nivel en su plan de App Service.
* Los clientes pueden escalar horizontalmente aplicaciones y ejecutar varias instancias de la aplicación dentro de los confines de su SKU.

Para Kudu, parte de la funcionalidad básica funciona con lo siguiente:

* Entornos
* Implementaciones
* Consola básica
* SSH

Para DevOps:

* Entornos de ensayo
* ACR y DockerHub CI/CD

## <a name="limitations"></a>Limitaciones
Azure Portal solo muestra las características que funcionan actualmente para Web App on Linux y oculta el resto. A medida que se habiliten más características, estas aparecerán en el portal.

Algunas de ellas, como la integración de la red virtual, la autenticación de Azure Active Directory o de terceros o las extensiones de sitio de Kudu, no están aún disponibles. Pero cuando lo estén, actualizaremos nuestra documentación y el blog sobre los cambios.

Esta versión preliminar pública solo está disponible en las siguientes regiones:

* Oeste de EE. UU.
* Este de EE. UU.
* Europa occidental
* Europa del Norte
* Centro-Sur de EE. UU
* Centro-Norte de EE. UU
* Sudeste asiático
* Asia oriental
* Australia Oriental
* Este de Japón
* Sur de Brasil
* Sur de la India

Las aplicaciones web en Linux solo se admiten en planes de App Service dedicados y no tienen un nivel gratuito o compartido. Además, los planes de App Service para aplicaciones web normales y de Linux son mutuamente excluyentes, de modo que no puede crear una aplicación web de Linux en un plan de App Service que no sea de Linux.

Las aplicaciones web de Linux se deben crear en un grupo de recursos que no contenga aplicaciones web que no sean de Linux en la misma región.

## <a name="troubleshooting"></a>Solución de problemas ##

Si la aplicación no se inicia o desea comprobar el registro desde la aplicación, compruebe que el Docker realiza el registro en LogFiles. A este directorio se accede a través del sitio SCM o a través de FTP.
Para registrar `stdout` y `stderr` del contenedor, debe habilitar **Registros de contenedor de Docker** en **Registros de diagnóstico**.

![Habilitación del registro][2]

![Uso de Kudu para ver registros de Docker][1]

Puede acceder al sitio SCM desde **Advanced Tools** (Herramientas avanzadas) en el menú **Herramientas de desarrollo**.

## <a name="next-steps"></a>Pasos siguientes
Consulte los vínculos siguientes para empezar a trabajar con App Service en Linux. Puede publicar preguntas y problemas en [nuestro foro](https://social.msdn.microsoft.com/forums/azure/home?forum=windowsazurewebsitespreview).

* [Creación de aplicaciones web en Azure Web App en Linux](app-service-linux-how-to-create-web-app.md)
* [Uso de una imagen personalizada de Docker para Web App on Linux de Azure](app-service-linux-using-custom-docker-image.md)
* [Uso de la configuración de PM2 para Node.js en Web App on Linux de Azure](app-service-linux-using-nodejs-pm2.md)
* [Uso de .NET Core en Web App on Linux de Azure App Service](app-service-linux-using-dotnetcore.md)
* [Uso de Ruby en Web App on Linux de Azure App Service](app-service-linux-ruby-get-started.md)
* [Preguntas más frecuentes sobre Web App on Linux de Azure App Service](app-service-linux-faq.md)
* [Compatibilidad con SSH para Web App on Linux de Azure](./app-service-linux-ssh-support.md)
* [Configuración de entornos de ensayo en Azure App Service](./web-sites-staged-publishing.md)
* [Implementación continua de Docker Hub con Azure Web App en Linux](./app-service-linux-ci-cd.md)

<!--Image references-->
[1]: ./media/app-service-linux-intro/kudu-docker-logs.png
[2]: ./media/app-service-linux-intro/logging.png
