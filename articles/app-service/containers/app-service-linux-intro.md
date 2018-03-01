---
title: "Introducción a App Service en Linux | Microsoft Docs"
description: "Información acerca de Azure App Service en Linux."
keywords: azure app service, linux, oss
services: app-service
documentationcenter: 
author: naziml
manager: cfowler
editor: 
ms.assetid: bc85eff6-bbdf-410a-93dc-0f1222796676
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: overview
ms.date: 02/16/2017
ms.author: wesmc
ms.custom: mvc
ms.openlocfilehash: 465e3d2c0c2b7c5d811441ede20adb9da2dbe847
ms.sourcegitcommit: 12fa5f8018d4f34077d5bab323ce7c919e51ce47
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 02/23/2018
---
# <a name="introduction-to-azure-app-service-on-linux"></a>Introducción a Azure App Service en Linux

[Aplicación web](../app-service-web-overview.md) es una plataforma de procesos completamente administrada que se ha optimizado para el hospedaje de sitios y aplicaciones web. Los clientes pueden usar App Service en Linux para hospedar aplicaciones web de forma nativa en Linux para pilas de aplicaciones admitidas. En las secciones siguientes se muestran las pilas de aplicaciones que son compatibles actualmente.

## <a name="languages"></a>Idiomas

App Service en Linux es compatible con un toda una serie de imágenes integradas para aumentar la productividad del desarrollador. Si el tiempo de ejecución que la aplicación requiere no es compatible con las imágenes integradas, se ofrecen instrucciones sobre cómo [generar su propia imagen de Docker](tutorial-custom-docker-image.md) para implementar en Web App for Containers.

| Idioma | Versiones compatibles |
|---|---|
| Node.js | 4.4, 4.5, 6.2, 6.6, 6.9-6.11, 8.0, 8.1 |
| PHP | 5.6, 7.0 |
| .NET Core | 1.0, 1.1, 2.0 |
| Ruby | 2.3 |

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

App Service en Linux solo se admite en los planes de App Service [Básico y Estándar](https://azure.microsoft.com/pricing/details/app-service/plans/) y no tiene un nivel [Gratis o Compartido](https://azure.microsoft.com/pricing/details/app-service/plans/). También existen las siguientes restricciones importantes para App Service en Linux:

* No se puede crear Web App for Containers en un plan de App Service que ya hospeda Web Apps que no sean de Linux.
* Al crear Web Apps for Containers en un grupo de recursos que contenga Web Apps que no sean de Linux, debe crear un plan de App Service en un grupo de recursos distinto al del plan de App Service existente.

## <a name="troubleshooting"></a>solución de problemas

Si la aplicación no se inicia o desea comprobar el registro desde la aplicación, compruebe que el Docker realiza el registro en LogFiles. A este directorio se accede a través del sitio SCM o a través de FTP.
Para registrar `stdout` y `stderr` del contenedor, debe habilitar **Registros de contenedor de Docker** en **Registros de diagnóstico**.

![Habilitación del registro][2]

![Uso de Kudu para ver registros de Docker][1]

Puede acceder al sitio SCM desde **Advanced Tools** (Herramientas avanzadas) en el menú **Herramientas de desarrollo**.

## <a name="next-steps"></a>pasos siguientes

Consulte los vínculos siguientes para empezar a trabajar con App Service en Linux. Puede publicar preguntas y problemas en [nuestro foro](https://social.msdn.microsoft.com/forums/azure/home?forum=windowsazurewebsitespreview).

* [Uso de una imagen personalizada de Docker para Web App for Containers](quickstart-docker-go.md)
* [Uso de .NET Core en Azure App Service en Linux](quickstart-dotnetcore.md)
* [Uso de Ruby en Azure App Service en Linux](quickstart-ruby.md)
* [Preguntas más frecuentes sobre Web App for Containers de Azure App Service ](app-service-linux-faq.md)
* [Compatibilidad con SSH para Azure App Service en Linux](app-service-linux-ssh-support.md)
* [Configuración de entornos de ensayo en Azure App Service](../../app-service/web-sites-staged-publishing.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json)
* [Implementación continua de Docker Hub con Web App for Containers](./app-service-linux-ci-cd.md)

<!--Image references-->
[1]: ./media/app-service-linux-intro/kudu-docker-logs.png
[2]: ./media/app-service-linux-intro/logging.png
