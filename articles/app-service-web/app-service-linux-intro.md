---
title: "Introducción a App Service en Linux | Microsoft Docs"
description: Aprenda sobre App Service en Linux.
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
translationtype: Human Translation
ms.sourcegitcommit: 72b2d9142479f9ba0380c5bd2dd82734e370dee7
ms.openlocfilehash: f5bc2d4d52e10af6b8393e78a53c4bd983596cda
ms.lasthandoff: 03/08/2017


---
# <a name="introduction-to-app-service-on-linux"></a>Introducción a App Service en Linux
Azure App Service en Linux se encuentra actualmente en versión preliminar y admite la ejecución de aplicaciones web de forma nativa en Linux.

## <a name="overview"></a>Información general
Los clientes pueden usar App Service en Linux para hospedar aplicaciones web de forma nativa en Linux para pilas de aplicaciones admitidas. En la sección siguiente se muestran las pilas de aplicaciones que son compatibles actualmente. 

## <a name="features"></a>Características
App Service en Linux admite actualmente las siguientes pilas de aplicaciones:

* Node.js
* PHP
* .Net Core
* Ruby

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
* Consolas básicas

## <a name="limitations"></a>Limitaciones
Azure Portal solo muestra las características que funcionan actualmente para App Service en Linux y oculta el resto. A medida que se habiliten más características, estas aparecerán en el portal.

Algunas de ellas, como la integración de la red virtual, la autenticación de Azure Active Directory o de terceros o las extensiones de sitio de Kudu, no están terminadas. Pero cuando se terminen, actualizaremos nuestra documentación y el blog sobre los cambios.

Esta versión preliminar pública solo está disponible en las siguientes regiones:

* Oeste de EE. UU.
* Europa occidental 
* Sudeste asiático

Las aplicaciones web en Linux solo se admiten en planes de App Service dedicados y no tienen un nivel gratuito o compartido. Además, los planes de App Service para aplicaciones web normales y de Linux son mutuamente excluyentes, de modo que no puede crear una aplicación web de Linux en un plan de App Service que no sea de Linux.

Las aplicaciones web de Linux se deben crear en un grupo de recursos que no contenga aplicaciones web que no sean de Linux en la misma región.

Web Apps en Linux Web todavía no admite la implementación de aplicaciones de .NET Core a partir de código fuente sin compilar. Primero, tendrá que publicar o compilar la aplicación de .NET Core localmente y, luego, insertar los bits del sitio publicado en la aplicación.

## <a name="next-steps"></a>Pasos siguientes
Consulte los vínculos siguientes para empezar a trabajar con App Service en Linux. Puede publicar preguntas y problemas en [nuestro foro](https://social.msdn.microsoft.com/forums/azure/home?forum=windowsazurewebsitespreview).

* [Creación de aplicaciones web en App Service en Linux](app-service-linux-how-to-create-a-web-app.md)
* [How to use a custom Docker image for App Service on Linux](app-service-linux-using-custom-docker-image.md) (Uso de una imagen de Docker personalizada para App Service en Linux)
* [Using PM2 Configuration for Node.js in Web Apps on Linux](app-service-linux-using-nodejs-pm2.md) (Uso de la configuración de PM2 para Node.js en Web Apps en Linux)
* [Uso de .NET Core en Azure App Service Web Apps en Linux](app-service-linux-using-dotnetcore.md)
* [Uso de Ruby en Azure App Service Web Apps en Linux](app-service-linux-using-ruby.md)
* [Preguntas más frecuentes sobre Azure App Service Web Apps en Linux](app-service-linux-faq.md)


