---
title: "Características de Azure Cloud Shell (versión preliminar) | Microsoft Docs"
description: "Introducción a las características de Azure Cloud Shell"
services: 
documentationcenter: 
author: jluk
manager: timlt
tags: azure-resource-manager
ms.assetid: 
ms.service: azure
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 07/13/2017
ms.author: juluk
ms.translationtype: HT
ms.sourcegitcommit: bde1bc7e140f9eb7bb864c1c0a1387b9da5d4d22
ms.openlocfilehash: 3e8eb5b2fe7d3d072132044e2445eab6fcd82ce1
ms.contentlocale: es-es
ms.lasthandoff: 07/21/2017

---

# <a name="features-and-tools-for-azure-cloud-shell"></a>Características y herramientas para Azure Cloud Shell
Azure Cloud Shell es una experiencia de shell basado en el explorador para administrar y desarrollar recursos de Azure.

Cloud Shell ofrece una experiencia de shell preconfigurado y accesible desde el explorador para administrar recursos de Azure sin el trabajo añadido de realizar la instalación, el control de versiones y el mantenimiento de una máquina de forma manual.

Cloud Shell aprovisiona máquinas a medida que se solicitan y, por tanto, el estado de la máquina no se conservará entre sesiones. Como Cloud Shell se ha creado para sesiones interactivas, los shells finalizan automáticamente después de 20 minutos de inactividad.

## <a name="bash-in-cloud-shell"></a>Bash en Cloud Shell
### <a name="tools"></a>Herramientas
|Categoría   |Nombre   |
|---|---|
|Intérprete de shell de Linux|Bash<br> sh               |
|Herramientas de Azure            |[CLI de Azure 2.0](https://github.com/Azure/azure-cli) y [1.0](https://github.com/Azure/azure-xplat-cli)<br> [AzCopy](https://docs.microsoft.com/azure/storage/storage-use-azcopy)<br> [Batch Shipyard](https://github.com/Azure/batch-shipyard)     |
|Editores de texto           |vim<br> nano<br> emacs       |
|Control de código fuente         |git                    |
|Herramientas de compilación            |make<br> maven<br> npm<br> pip         |
|Contenedores             |[CLI de Docker](https://github.com/docker/cli)/[Docker Machine](https://github.com/docker/machine)<br> [Kubectl](https://kubernetes.io/docs/user-guide/kubectl-overview/)<br> [Draft](https://github.com/Azure/draft)<br> [CLI de DC/OS](https://github.com/dcos/dcos-cli)         |
|Bases de datos              |Cliente de MySQL<br> Cliente de PostgreSql<br> [Utilidad sqlcmd](https://docs.microsoft.com/sql/tools/sqlcmd-utility)      |
|Otros                  |Cliente de iPython<br> [CLI de Cloud Foundry](https://github.com/cloudfoundry/cli)<br> |

### <a name="language-support"></a>Compatibilidad con idiomas
|language   |Versión   |
|---|---|
|.NET       |1.01       |
|Go         |1.7        |
|Java       |1.8        |
|Node.js    |6.9.4      |
|Python     |2.7 y 3.5 (predeterminadas)|

## <a name="secure-automatic-authentication"></a>Protección de la autenticación automática
Cloud Shell autentica de forma segura y automática el acceso a la cuenta para la CLI de Azure 2.0.

## <a name="azure-files-persistence"></a>Persistencia de Azure Files
Dado que Cloud Shell se asigna para cada solicitud mediante una máquina temporal, los archivos fuera de $Home y el estado de la máquina no se conservan entre sesiones.
Para conservar archivos entre sesiones, la primera vez que se inicia Cloud Shell se explica cómo conectar un recurso compartido de archivos de Azure.
Una vez finalizado, Cloud Shell conectará automáticamente su almacenamiento para todas las sesiones futuras.

[Obtenga más información sobre cómo conectar recursos compartidos de archivos de Azure a Cloud Shell](persisting-shell-storage.md).

## <a name="next-steps"></a>Pasos siguientes
[Inicio rápido de Cloud Shell](quickstart.md) <br>
[Más información sobre la CLI de Azure 2.0](https://docs.microsoft.com/cli/azure/) <br>
