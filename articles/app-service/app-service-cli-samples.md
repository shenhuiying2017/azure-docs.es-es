---
title: 'Ejemplos de la CLI de Azure: App Service | Microsoft Docs'
description: 'Ejemplos de la CLI de Azure: App Service'
services: app-service
documentationcenter: app-service
author: syntaxc4
manager: erikre
editor: ggailey777
tags: azure-service-management
ms.assetid: 53e6a15a-370a-48df-8618-c6737e26acec
ms.service: app-service
ms.devlang: na
ms.topic: sample
ms.tgt_pltfrm: na
ms.workload: app-service
ms.date: 12/12/2017
ms.author: cfowler
ms.custom: mvc
ms.openlocfilehash: fdc5e03350783fb8c3e30b6c9a40af45a5925ba8
ms.sourcegitcommit: aaba209b9cea87cb983e6f498e7a820616a77471
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/12/2017
---
# <a name="azure-cli-samples"></a>Ejemplos de la CLI de Azure

En la tabla siguiente se incluyen vínculos a scripts de Bash creados con la CLI de Azure.

| | |
|-|-|
|**Creación de la aplicación**||
| [Creación de una aplicación web e implementación de archivos con FTP](./scripts/app-service-cli-deploy-ftp.md?toc=%2fcli%2fazure%2ftoc.json)| Crea una aplicación web de Azure e implementa un archivo mediante FTP. |
| [Creación de una aplicación web e implementación de código desde GitHub](./scripts/app-service-cli-deploy-github.md?toc=%2fcli%2fazure%2ftoc.json)| Crea una aplicación web de Azure e implementa código proveniente de un repositorio público de GitHub. |
| [Creación de una aplicación web con implementación continua desde GitHub](./scripts/app-service-cli-continuous-deployment-github.md?toc=%2fcli%2fazure%2ftoc.json)| Crea una aplicación web de Azure con publicación continua desde un repositorio de GitHub de su propiedad. |
| [Creación de una aplicación web e implementación de código desde un repositorio local de GitHub](./scripts/app-service-cli-deploy-local-git.md?toc=%2fcli%2fazure%2ftoc.json) | Crea una aplicación web de Azure y configura la inserción de código desde un repositorio de Git local. |
| [Creación de una aplicación web e implementación de código en un entorno de ensayo](./scripts/app-service-cli-deploy-staging-environment.md?toc=%2fcli%2fazure%2ftoc.json) | Crea una aplicación web de Azure con una ranura de implementación para cambios en el código de ensayo. |
| [Creación de una aplicación web de ASP.NET Core en un contenedor de Docker](./scripts/app-service-cli-linux-docker-aspnetcore.md?toc=%2fcli%2fazure%2ftoc.json)| Crea una aplicación web de Azure en Linux y carga una imagen de Docker desde Docker Hub. |
|**Configuración de la aplicación**||
| [Asignación de un dominio personalizado a una aplicación web](./scripts/app-service-cli-configure-custom-domain.md?toc=%2fcli%2fazure%2ftoc.json)| Crea una aplicación web de Azure y le asigna un nombre de dominio personalizado. |
| [Enlace de un certificado SSL personalizado a una aplicación web](./scripts/app-service-cli-configure-ssl-certificate.md?toc=%2fcli%2fazure%2ftoc.json)| Crea una aplicación web de Azure y enlaza a ella el certificado SSL de un nombre de dominio personalizado. |
|**Escalado de la aplicación**||
| [Escalado manual de una aplicación web](./scripts/app-service-cli-scale-manual.md?toc=%2fcli%2fazure%2ftoc.json) | Crea una aplicación web de Azure y la escala a través de dos instancias. |
| [Escalado de una aplicación web en todo el mundo con una arquitectura de alta disponibilidad](./scripts/app-service-cli-scale-high-availability.md?toc=%2fcli%2fazure%2ftoc.json) | Crea dos aplicaciones web de Azure en dos regiones geográficas diferentes y hace que estén disponibles a través de un punto de conexión único mediante Azure Traffic Manager. |
|**Conexión de la aplicación a recursos**||
| [Conexión de una aplicación web a SQL Database](./scripts/app-service-cli-app-service-sql.md?toc=%2fcli%2fazure%2ftoc.json)| Crea una aplicación web de Azure y una base de datos SQL y, a continuación, añade la cadena de conexión de base de datos a la configuración de la aplicación. |
| [Conexión de una aplicación web a una cuenta de almacenamiento](./scripts/app-service-cli-app-service-storage.md?toc=%2fcli%2fazure%2ftoc.json)| Crea una aplicación web de Azure y una cuenta de almacenamiento y, a continuación, añade la cadena de conexión de almacenamiento a la configuración de la aplicación. |
| [Conexión de una aplicación web a Redis Cache](./scripts/app-service-cli-app-service-redis.md?toc=%2fcli%2fazure%2ftoc.json) | Crea una aplicación web de Azure y una instancia de Redis Cache y, luego, agrega los detalles de conexión de Redis a la configuración de la aplicación). |
| [Conexión de una aplicación web a Cosmos DB](./scripts/app-service-cli-app-service-documentdb.md?toc=%2fcli%2fazure%2ftoc.json) | Crea una aplicación web de Azure y una instancia de Cosmos DB y, luego, agrega los detalles de conexión de Cosmos DB a la configuración de la aplicación. |
|**Copia de seguridad y restauración de la aplicación**||
| [Back up a web app](./scripts/app-service-cli-backup-onetime.md?toc=%2fcli%2fazure%2ftoc.json) (Copia de seguridad de una aplicación web) | Crea una aplicación web de Azure y una copia de seguridad única para ella. |
| [Create a scheduled backup for a web app](./scripts/app-service-cli-backup-scheduled.md?toc=%2fcli%2fazure%2ftoc.json) (Creación de una copia de seguridad programada para una aplicación web) | Crea una aplicación web de Azure y una copia de seguridad programada para ella. |
| [Restaura una aplicación web desde una copia de seguridad](./scripts/app-service-cli-backup-restore.md?toc=%2fcli%2fazure%2ftoc.json) | Restaura una aplicación web de Azure desde una copia de seguridad. |
|**Supervisión de la aplicación**||
| [Supervisión de una aplicación web con registros de servidor web](./scripts/app-service-cli-monitor.md?toc=%2fcli%2fazure%2ftoc.json) | Crea una aplicación web de Azure, habilita el registro para ella y descarga los registros en el equipo local. |
| | |