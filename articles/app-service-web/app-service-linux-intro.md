---
title: "Introducción a App Service en Linux | Microsoft Docs"
description: Aprenda sobre App Service en Linux.
keywords: azure app service, linux, oss
services: app-service
documentationcenter: 
author: naziml
manager: wpickett
editor: 
ms.assetid: bc85eff6-bbdf-410a-93dc-0f1222796676
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/10/2016
ms.author: naziml
translationtype: Human Translation
ms.sourcegitcommit: a3df293a056f4b06281bf8b80717529286d528b8
ms.openlocfilehash: 48f26113d129ebfa613f1b8f2a372d0e7286aea1


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

Los clientes pueden implementar sus aplicaciones mediante:

* FTP
* Git local
* GitHub o Bitbucket

Para el escalado de aplicaciones:

* Los clientes pueden escalar o reducir sus aplicaciones web verticalmente mediante la modificación del nivel en su plan de App Service.
* Los clientes pueden escalar horizontalmente sus aplicaciones y ejecutar su aplicación en varias instancias dentro de los confines de su SKU.

Para Kudu, parte de la funcionalidad básica funciona con lo siguiente:

* Entornos
* Implementaciones
* Consolas básicas

## <a name="limitations"></a>Limitaciones
Azure Portal solo muestra las características que funcionan actualmente para App Service en Linux y oculta el resto. A medida que habilitemos más características, las verá reflejadas en el portal.

Algunas de ellas, como la integración de la red virtual, la autenticación de Azure Active Directory o de terceros o las extensiones de sitio de Kudu, no funcionan por el momento. Pero cuando consigamos que lo hagan, actualizaremos nuestra documentación y el blog sobre los cambios.

Esta versión preliminar pública solo está disponible en las siguientes regiones:

* Oeste de EE. UU.
* Europa occidental 
* Sudeste asiático

Las aplicaciones web en Linux solo se admiten en planes de App Service dedicados y no tienen un nivel gratuito o compartido. Además, los planes de App Service para aplicaciones web normales y de Linux son mutuamente excluyentes, de modo que no puede crear una aplicación web de Linux en un plan de App Service que no sea de Linux.

Las aplicaciones web de Linux se deben crear en un grupo de recursos que no contenga aplicaciones web que no sean de Linux en la misma región.

## <a name="next-steps"></a>Pasos siguientes
Consulte los vínculos siguientes para empezar a trabajar con App Service en Linux. Puede publicar preguntas y problemas en [nuestro foro](https://social.msdn.microsoft.com/forums/azure/home?forum=windowsazurewebsitespreview).

* [Creación de aplicaciones web en App Service en Linux](app-service-linux-how-to-create-a-web-app.md)
* [How to use a custom Docker image for App Service on Linux](app-service-linux-using-custom-docker-image.md) (Uso de una imagen de Docker personalizada para App Service en Linux)
* [Using PM2 Configuration for Node.js in Web Apps on Linux](app-service-linux-using-nodejs-pm2.md) (Uso de la configuración de PM2 para Node.js en Web Apps en Linux)
* [Uso de .NET Core en Azure App Service Web Apps en Linux](app-service-linux-using-dotnetcore.md)



<!--HONumber=Jan17_HO1-->


