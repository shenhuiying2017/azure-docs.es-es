---
title: "Clonación de aplicaciones web con el Portal de Azure"
description: Aprenda a clonar sus aplicaciones web en nuevas aplicaciones web con el Portal de Azure.
services: app-service\web
documentationcenter: 
author: ahmedelnably
manager: stefsch
editor: 
ms.assetid: 20b0ae4e-67e8-4bae-9d74-8a24dc445cce
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/08/2016
ms.author: aelnably
translationtype: Human Translation
ms.sourcegitcommit: 6b77e338e1c7f0f79ea3c25b0b073296f7de0dcf
ms.openlocfilehash: 9ebfa91c7972ab3c264032ead8376c23c1197a4b


---
# <a name="azure-app-service-app-cloning-using-azure-portal"></a>Clonación de aplicaciones del Servicio de aplicaciones de Azure mediante el Portal de Azure
La característica de clonación de las [aplicaciones web del Servicio de aplicaciones de Azure](http://go.microsoft.com/fwlink/?LinkId=529714) le permite clonar fácilmente aplicaciones web existentes en una aplicación de reciente creación de una región distinta o de la misma. Esto permitirá que los clientes implementen varias aplicaciones en diferentes regiones de una forma rápida y sencilla.

La clonación de aplicaciones actualmente solo se admite para los planes de Servicio de aplicaciones de nivel Premium. La nueva característica cuenta con las mismas limitaciones que la de Copia de seguridad de aplicaciones web; consulte [Hacer copia de seguridad de una aplicación web en el servicio de aplicaciones de Azure](web-sites-backup.md).

[!INCLUDE [app-service-web-to-api-and-mobile](../../includes/app-service-web-to-api-and-mobile.md)]

## <a name="cloning-an-existing-app"></a>Clonación de una aplicación existente
La aplicación web debe ejecutarse en el modo **Premium** para que pueda crear un clon de esta.

1. En el [Portal de Azure](https://portal.azure.com/), abra la hoja de la aplicación web.
2. Haga clic en **Herramientas**. Luego, en la hoja **Herramientas**, haga clic en **Clonar aplicación**.
   
    ![][1]
   
   > [!NOTE]
   > Si la aplicación web aún no está en el modo **Premium** , recibirá un mensaje en el que se indican los modos compatibles para la clonación de aplicaciones. En este momento, tiene la opción de seleccionar **Actualizar**.
   > 
   > 
3. En la hoja **Clonar aplicación** , especifique un nombre de la nueva aplicación web, el grupo de recursos y el plan de servicio de aplicaciones. El usuario también podrá elegir si desea clonar un determinado número de opciones de configuración de aplicaciones web de origen o no.
   
    ![][2]
4. Después de hacer clic **crear** , la plataforma empezará a trabajar en la creación de un clon de la aplicación web de origen.

## <a name="cloning-an-existing-app-to-an-app-service-environment"></a>Clonación de una aplicación existente en un entorno de Servicio de aplicaciones
En la hoja **Clonar aplicación** , el cliente tendrá la posibilidad de elegir un grupo de aplicaciones en un entorno del Servicio de aplicaciones existente.

## <a name="current-restrictions"></a>Restricciones actuales
Esta característica se encuentra actualmente en versión preliminar y estamos trabajando para agregar nuevas funcionalidades con el tiempo. En la siguiente lista, se incluyen las restricciones conocidas sobre la compatibilidad actual de la clonación de aplicaciones en el Portal de Azure:

* No se clona la configuración del Administrador de tráfico de Azure.
* No se clona la configuración de escalado automático.
* No se clona la configuración de programación de copia de seguridad.
* No se clona la configuración de red virtual.
* No se instala automáticamente App Insights en la aplicación web de destino.
* No se clona la configuración de Easy Auth.
* No se clona la extensión Kudu.
* No se clonan las reglas de TiP.
* No se clona el contenido de la base de datos

### <a name="references"></a>Referencias
* [Clonación de aplicaciones web con PowerShell](app-service-web-app-cloning.md)
* [Hacer copia de seguridad de una aplicación web en el Servicio de aplicaciones de Azure](web-sites-backup.md)
* [Creación de un entorno del Servicio de aplicaciones](app-service-web-how-to-create-an-app-service-environment.md)
* [Creación de una aplicación web en un entorno del Servicio de aplicaciones](app-service-web-how-to-create-a-web-app-in-an-ase.md)
* [Introducción al entorno del Servicio de aplicaciones](app-service-app-service-environment-intro.md)

<!--Image references-->
[1]: ./media/app-service-web-app-cloning-portal/CloningBlade.png
[2]: ./media/app-service-web-app-cloning-portal/CloneSettings.png



<!--HONumber=Dec16_HO2-->


