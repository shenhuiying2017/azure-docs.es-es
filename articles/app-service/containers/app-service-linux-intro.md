---
title: "Introducción a Azure Web Apps for Containers | Microsoft Docs"
description: "Obtenga información acerca de Azure Web Apps for Containers."
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
ms.sourcegitcommit: 12c20264b14a477643a4bbc1469a8d1c0941c6e6
ms.openlocfilehash: f46f59a69d13a4e702eccded810cf3c7a8b30904
ms.contentlocale: es-es
ms.lasthandoff: 09/07/2017

---
# <a name="introduction-to-azure-web-apps-for-containers"></a>Introducción a Azure Web Apps for Containers

[Aplicación web](../../app-service-web/app-service-web-overview.md) es una plataforma de procesos completamente administrada que se ha optimizado para el hospedaje de sitios y aplicaciones web. Los clientes pueden usar Web Apps for Containers para hospedar aplicaciones web de forma nativa en Linux para pilas de aplicaciones admitidas. En las secciones siguientes se muestran las pilas de aplicaciones que son compatibles actualmente.

## <a name="languages"></a>Idiomas

|Node.js|PHP|.NET Core|Ruby|
|:------------------:|:---:|:---------:|:----:|
|4.4, 4.5|5.6|1.0-1.1|2.3|
|6.2, 6.6, 6.9-6.11|7.0|||
|8.0-8.1||||

## <a name="deployments"></a>Implementaciones

* FTP
* Git local
* GitHub
* Bitbucket

## <a name="devops"></a>DevOps

* Entornos de ensayo
* [Azure Container Registry](https://docs.microsoft.com/azure/container-registry/container-registry-intro) y DockerHub CI/CD

## <a name="console-publishing-and-debugging"></a>Consola, publicación y depuración

* Entornos
* Implementaciones
* Consola básica
* SSH

## <a name="scaling"></a>Escalado

* Los clientes pueden escalar o reducir aplicaciones web verticalmente mediante la modificación del nivel en su [plan de App Service](https://docs.microsoft.com/azure/app-service/azure-web-sites-web-hosting-plans-in-depth-overview?toc=%2fazure%2fapp-service-web%2ftoc.json).

## <a name="locations"></a>Ubicaciones

Consulte el [panel de estado de Azure](https://azure.microsoft.com/status).

## <a name="limitations"></a>Limitaciones

Azure Portal solo muestra las características que funcionan actualmente para Web Apps for Containers. A medida que se habiliten más características, estas aparecerán en el portal.

Algunas de ellas, como la integración de la red virtual, la autenticación de Azure Active Directory o de terceros o las extensiones de sitio de Kudu, no están aún disponibles. Pero cuando lo estén, actualizaremos nuestra documentación y el blog sobre los cambios.

Web Apps for Containers solo se admite en los planes de App Service [Básico, Estándar y Premium](https://azure.microsoft.com/pricing/details/app-service/plans/) y no tiene un nivel [Gratis o Compartido](https://azure.microsoft.com/pricing/details/app-service/plans/). También existen las siguientes restricciones importantes para Web Apps for Containers:

* No se puede crear Web Apps for Containers en un plan de App Service que ya hospeda Web Apps que no sean de Linux.
* Al crear Web Apps for Containers en un grupo de recursos que contenga Web Apps que no sean de Linux, debe crear un plan de App Service en una región distinta a la del plan de App Service existente.

## <a name="troubleshooting"></a>Solución de problemas

Si la aplicación no se inicia o desea comprobar el registro desde la aplicación, compruebe que el Docker realiza el registro en LogFiles. A este directorio se accede a través del sitio SCM o a través de FTP.
Para registrar `stdout` y `stderr` del contenedor, debe habilitar **Registros de contenedor de Docker** en **Registros de diagnóstico**.

![Habilitación del registro][2]

![Uso de Kudu para ver registros de Docker][1]

Puede acceder al sitio SCM desde **Advanced Tools** (Herramientas avanzadas) en el menú **Herramientas de desarrollo**.

## <a name="next-steps"></a>Pasos siguientes

Consulte los vínculos siguientes para empezar a trabajar con App Service en Linux. Puede publicar preguntas y problemas en [nuestro foro](https://social.msdn.microsoft.com/forums/azure/home?forum=windowsazurewebsitespreview).

* [How to use a custom Docker image for Azure Web Apps for Containers](quickstart-custom-docker-image.md) (Uso de una imagen personalizada de Docker para Azure Web App for Containers)
* [Using .NET Core in Azure App Service Web Apps for Containers](quickstart-dotnetcore.md) (Uso de .NET Core en Web Apps for Containers de Azure App Service)
* [Using Ruby in Azure App Service Web Apps for Containers](quickstart-ruby.md) (Uso de Ruby en Web Apps for Containers de Azure App Service)
* [Preguntas más frecuentes sobre Web Apps for Containers de Azure App Service](app-service-linux-faq.md)
* [Compatibilidad con SSH para Web Apps for Containers](app-service-linux-ssh-support.md)
* [Configuración de entornos de ensayo en Azure App Service](../../app-service-web/web-sites-staged-publishing.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json)
* [Docker Hub Continuous Deployment with Azure Web Apps for Containers](./app-service-linux-ci-cd.md) (Implementación continua de Docker Hub con Azure Web Apps for Containers)

<!--Image references-->
[1]: ./media/app-service-linux-intro/kudu-docker-logs.png
[2]: ./media/app-service-linux-intro/logging.png

