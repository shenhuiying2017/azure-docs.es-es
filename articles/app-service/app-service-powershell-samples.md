---
title: 'Ejemplos de Azure PowerShell: App Service | Microsoft Docs'
description: 'Ejemplos de Azure PowerShell: App Service'
services: app-service
documentationcenter: app-service
author: syntaxc4
manager: erikre
editor: ggailey777
tags: azure-service-management
ms.assetid: b48d1137-8c04-46e0-b430-101e07d7e470
ms.service: app-service
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: app-service
ms.date: 03/08/2017
ms.author: cfowler
ms.custom: mvc
ms.openlocfilehash: 3254fdd57cfcd170f22374c1e3b058e6081d8e8e
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/11/2017
---
# <a name="azure-powershell-samples"></a>Ejemplos de Azure PowerShell

En la tabla siguiente se incluyen vínculos a scripts de Bash creados con Azure PowerShell.

| | |
|-|-|
|**Creación de la aplicación**||
| [Creación de una aplicación web con implementación desde GitHub](./scripts/app-service-powershell-deploy-github.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Crea una aplicación web de Azure que extrae código de GitHub. |
| [Creación de una aplicación web con implementación continua desde GitHub](./scripts/app-service-powershell-continuous-deployment-github.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Crea una aplicación web de Azure que implementa continuamente código desde GitHub. |
| [Creación de una aplicación web e implementación de código con FTP](./scripts/app-service-powershell-deploy-ftp.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Crea una aplicación web de Azure y cargue archivos de un directorio local con FTP. |
| [Creación de una aplicación web e implementación de código desde un repositorio local de GitHub](./scripts/app-service-powershell-deploy-local-git.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Crea una aplicación web de Azure y configura la inserción de código desde un repositorio de Git local. |
| [Creación de una aplicación web e implementación de código en un entorno de ensayo](./scripts/app-service-powershell-deploy-staging-environment.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Crea una aplicación web de Azure con una ranura de implementación para cambios en el código de ensayo. |
|**Configuración de la aplicación**||
| [Asignación de un dominio personalizado a una aplicación web](./scripts/app-service-powershell-configure-custom-domain.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Crea una aplicación web de Azure y le asigna un nombre de dominio personalizado. |
| [Enlace de un certificado SSL personalizado a una aplicación web](./scripts/app-service-powershell-configure-ssl-certificate.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Crea una aplicación web de Azure y enlaza a ella el certificado SSL de un nombre de dominio personalizado. |
|**Escalado de la aplicación**||
| [Escalado manual de una aplicación web](./scripts/app-service-powershell-scale-manual.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Crea una aplicación web de Azure y la escala a través de dos instancias. |
| [Escalado de una aplicación web en todo el mundo con una arquitectura de alta disponibilidad](./scripts/app-service-powershell-scale-high-availability.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Crea dos aplicaciones web de Azure en dos regiones geográficas diferentes y hace que estén disponibles a través de un punto de conexión único mediante Azure Traffic Manager. |
|**Conexión de la aplicación a recursos**||
| [Conexión de una aplicación web a una base de datos SQL](./scripts/app-service-powershell-connect-to-sql.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Crea una aplicación web de Azure y una base de datos SQL y, a continuación, añade la cadena de conexión de base de datos a la configuración de la aplicación. |
| [Conexión de una aplicación web a una cuenta de almacenamiento](./scripts/app-service-powershell-connect-to-storage.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Crea una aplicación web de Azure y una cuenta de almacenamiento y, a continuación, añade la cadena de conexión de almacenamiento a la configuración de la aplicación. |
|**Supervisión de la aplicación**||
| [Supervisión de una aplicación web con registros de servidor web](./scripts/app-service-powershell-monitor.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Crea una aplicación web de Azure, habilita el registro para ella y descarga los registros en el equipo local. |
| | |
