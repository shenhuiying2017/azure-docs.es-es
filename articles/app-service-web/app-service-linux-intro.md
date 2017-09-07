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
ms.sourcegitcommit: 1c730c65194e169121e3ad1d1423963ee3ced8da
ms.openlocfilehash: bbe2e0bafe48b39f12c5a8a46511e9275926d4b6
ms.contentlocale: es-es
ms.lasthandoff: 08/30/2017

---
# <a name="introduction-to-azure-web-app-on-linux"></a>Introducción a Azure Web App en Linux

[!INCLUDE [app-service-linux-preview](../../includes/app-service-linux-preview.md)]

## <a name="overview"></a>Información general
[Aplicación web](https://docs.microsoft.com/en-us/azure/app-service-web/app-service-web-overview) es una plataforma de procesos completamente administrada que se ha optimizado para el hospedaje de sitios y aplicaciones web. Los clientes pueden usar Web App on Linux para hospedar aplicaciones web de forma nativa en Linux para pilas de aplicaciones admitidas. En las secciones siguientes se muestran las pilas de aplicaciones que son compatibles actualmente. 

### <a name="languages"></a>Idiomas

|Node.js|PHP|.NET Core|Ruby|
|:------------------:|:---:|:---------:|:----:|
|4.4, 4.5|5.6|1.0-1.1|2.3|
|6.2, 6.6, 6.9-6.11|7.0|||
|8.0-8.1||||

### <a name="deployments"></a>Implementaciones

* FTP
* Git local
* GitHub
* Bitbucket

### <a name="devops"></a>DevOps

* Entornos de ensayo
* [Azure Container Registry](https://docs.microsoft.com/en-us/azure/container-registry/container-registry-intro) y DockerHub CI/CD

### <a name="console-publishing-and-debugging"></a>Consola, publicación y depuración

* Entornos
* Implementaciones
* Consola básica
* SSH

### <a name="scaling"></a>Escalado

* Los clientes pueden escalar o reducir aplicaciones web verticalmente mediante la modificación del nivel en su [plan de App Service](https://docs.microsoft.com/en-us/azure/app-service/azure-web-sites-web-hosting-plans-in-depth-overview?toc=%2fazure%2fapp-service-web%2ftoc.json).

## <a name="limitations"></a>Limitaciones
Azure Portal solo muestra las características que funcionan actualmente para Aplicación web en Linux. A medida que se habiliten más características, estas aparecerán en el portal.

Algunas de ellas, como la integración de la red virtual, la autenticación de Azure Active Directory o de terceros o las extensiones de sitio de Kudu, no están aún disponibles. Pero cuando lo estén, actualizaremos nuestra documentación y el blog sobre los cambios.

Esta versión preliminar pública solo está disponible en las siguientes regiones:

|América|Asia Pacífico|Europa|
|:----------------:|:--------------:|:------------:|
|Sur de Brasil|Australia Oriental|Europa del Norte|
|Este de EE. UU.|Asia oriental|Europa occidental|
|Centro-Norte de EE. UU|Este de Japón||
|Centro-Sur de EE. UU|Sur de la India||
|Oeste de EE. UU.|Sudeste asiático||

Compruebe el [panel de estado de Azure](https://azure.microsoft.com/status) para ver cuándo estarán disponibles las regiones adicionales.

Aplicación web en Linux solo se admite en los planes de App Service [Básico, Estándar y Premium](https://azure.microsoft.com/en-us/pricing/details/app-service/plans/) y no tiene un nivel [Gratis o Compartido](https://azure.microsoft.com/en-us/pricing/details/app-service/plans/). También existen las siguientes restricciones importantes para Aplicación web en Linux:

* No se puede crear Aplicación web en Linux en un plan de App Service que ya hospeda Web Apps que no sean de Linux.
* Al crear Aplicación web en Linux en un grupo de recursos que contenga Web Apps que no sean de Linux, debe crear un plan de App Service en una región distinta a la del plan de App Service existente.

## <a name="troubleshooting"></a>Solución de problemas ##

Si la aplicación no se inicia o desea comprobar el registro desde la aplicación, compruebe que el Docker realiza el registro en LogFiles. A este directorio se accede a través del sitio SCM o a través de FTP.
Para registrar `stdout` y `stderr` del contenedor, debe habilitar **Registros de contenedor de Docker** en **Registros de diagnóstico**.

![Habilitación del registro][2]

![Uso de Kudu para ver registros de Docker][1]

Puede acceder al sitio SCM desde **Advanced Tools** (Herramientas avanzadas) en el menú **Herramientas de desarrollo**.

## <a name="next-steps"></a>Pasos siguientes
Consulte los vínculos siguientes para empezar a trabajar con App Service en Linux. Puede publicar preguntas y problemas en [nuestro foro](https://social.msdn.microsoft.com/forums/azure/home?forum=windowsazurewebsitespreview).

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

