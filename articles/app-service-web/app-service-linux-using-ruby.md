---
title: Uso de Ruby en Azure App Service Web Apps en Linux | Microsoft Docs
description: Uso de Ruby en Azure App Service Web Apps en Linux.
keywords: "azure app service, web app, preguntas más frecuentes, linux, oss, ruby"
services: app-service
documentationCenter: 
authors: aelnably
manager: erikre
editor: 
ms.assetid: 
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/16/2017
ms.author: aelnably;wesmc
translationtype: Human Translation
ms.sourcegitcommit: bb4c7ea7adfe1326ae8259782b5de2762c8c2bf5
ms.openlocfilehash: 8427034e5229000faf134bcd5b7174b0f7046321
ms.lasthandoff: 02/17/2017


---

# <a name="using-ruby-in-web-apps-on-linux"></a>Uso de Ruby en Web Apps en Linux #

Con la última actualización de nuestro back-end, se introdujo la compatibilidad con Ruby v.2.3. Al establecer la configuración de la aplicación web de Linux, puede cambiar la pila de la aplicación.

## <a name="using-the-azure-portal"></a>Uso del portal de Azure ##

En el menú nuevo de [Azure Portal](https://portal.azure.com), puede elegir crear una aplicación web en Linux desde la opción Web y móvil como se muestra en la siguiente imagen:

![Inicio de creación de una aplicación web en Azure Portal][1]

Aparecerá la hoja de **creación**, como se muestra en la siguiente imagen:

![Hoja de creación][2]

1. Asigne un nombre a la aplicación web.
2. Elija un grupo de recursos existente o cree uno. (Consulte las regiones disponibles en la [sección de limitaciones](app-service-linux-intro.md)).
3. Seleccione un plan de Azure App Service existente o cree uno. (Consulte las notas del plan de App Service en la [sección de limitaciones](app-service-linux-intro.md)).
4. Seleccione Ruby entre las pilas en tiempo de ejecución integradas.

Una vez que se crea la aplicación web de Ruby, puede implementarla mediante Git o FTP.

## <a name="next-steps"></a>Pasos siguientes
* [¿Qué es App Service en Linux?](app-service-linux-intro.md)
* [Creación de aplicaciones web en App Service en Linux](app-service-linux-how-to-create-a-web-app.md)
* [Implementación de Git local en Azure App Service](app-service-deploy-local-git.md)
* [Preguntas más frecuentes sobre Azure App Service Web Apps en Linux](app-service-linux-faq.md)

<!--Image references-->
[1]: ./media/app-service-linux-using-ruby/New-Linux.png
[2]: ./media/app-service-linux-using-ruby/Ruby-UX.png
