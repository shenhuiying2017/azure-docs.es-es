---
title: "Características de Bash en Azure Cloud Shell (versión preliminar) | Microsoft Docs"
description: "Introducción a las características de Bash en Azure Cloud Shell"
services: Azure
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
ms.date: 09/25/2017
ms.author: juluk
ms.openlocfilehash: 158b0b65ba53630050275c910f2a7acfc39b8759
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="features--tools-for-bash-in-azure-cloud-shell"></a>Características y herramientas para Bash en Azure Cloud Shell

[!include [features-introblock](../../includes/cloud-shell-features-introblock.md)]

> [!TIP]
> También hay disponible un artículo sobre Características y herramientas en [PowerShell](features-powershell.md).

Bash in Cloud Shell se ejecuta en `Ubuntu 16.04 LTS`.

## <a name="features"></a>Características

### <a name="secure-automatic-authentication"></a>Protección de la autenticación automática

Bash en Cloud Shell autentica de forma segura y automática el acceso a la cuenta para la CLI de Azure 2.0.

### <a name="ssh-into-azure-linux-virtual-machines"></a>SSH en máquinas virtuales Linux de Azure

La creación de una máquina virtual Linux desde la CLI de Azure 2.0 puede crear una clave SSH predeterminada y colocarla en el directorio `$Home`. Colocar claves SSH en `$Home` permite conexiones SSH directas a máquinas virtuales Linux de Azure directamente desde Cloud Shell. Las claves se mantienen en acc_<user>.img en el recurso compartido de archivos. Use los procedimientos recomendados cuando use o comparta el acceso a las claves o al recurso compartido de archivos.

### <a name="home-persistence-across-sessions"></a>Persistencia de $Home entre sesiones

Para conservar archivos entre sesiones, la primera vez que se inicia Cloud Shell se explica cómo conectar un recurso compartido de archivos de Azure.
Una vez finalizado, Cloud Shell conectará automáticamente su almacenamiento (montado como `$Home\clouddrive`) para todas las sesiones futuras.
En Bash en Cloud Shell, además, el directorio `$Home` se conserva como .img en el recurso compartido de archivos de Azure.
Los archivos fuera de `$Home` y el estado de la máquina no se conservan entre sesiones.

[Obtenga más información sobre la persistencia de los archivos en Bash en Cloud Shell](persisting-shell-storage.md).

## <a name="tools"></a>Herramientas

|Categoría   |Nombre   |
|---|---|
|Intérprete de shell de Linux|Bash<br> sh               |
|Herramientas de Azure            |[CLI de Azure 2.0](https://github.com/Azure/azure-cli) y [1.0](https://github.com/Azure/azure-xplat-cli)<br> [AzCopy](https://docs.microsoft.com/azure/storage/storage-use-azcopy)<br> [Batch Shipyard](https://github.com/Azure/batch-shipyard) <br>    |
|Editores de texto           |vim<br> nano<br> emacs       |
|Control de código fuente         |git                    |
|Herramientas de compilación            |make<br> maven<br> npm<br> pip         |
|Contenedores             |[CLI de Docker](https://github.com/docker/cli)/[Docker Machine](https://github.com/docker/machine)<br> [Kubectl](https://kubernetes.io/docs/user-guide/kubectl-overview/)<br> [Helm](https://github.com/kubernetes/helm)<br> [CLI de DC/OS](https://github.com/dcos/dcos-cli)         |
|Bases de datos              |Cliente de MySQL<br> Cliente de PostgreSql<br> [Utilidad sqlcmd](https://docs.microsoft.com/sql/tools/sqlcmd-utility)<br> [mssql-scripter](https://github.com/Microsoft/sql-xplat-cli) |
|Otros                  |Cliente de iPython<br> [CLI de Cloud Foundry](https://github.com/cloudfoundry/cli)<br> [Terraform](https://www.terraform.io/docs/providers/azurerm/)<br> |

## <a name="language-support"></a>Compatibilidad con idiomas

|language   |Versión   |
|---|---|
|.NET       |1.01       |
|Go         |1.7        |
|Java       |1.8        |
|Node.js    |6.9.4      |
|PowerShell  |[6.0 (versión beta)](https://github.com/PowerShell/powershell/releases)       |
|Python     |2.7 y 3.5 (predeterminadas)|

## <a name="next-steps"></a>Pasos siguientes
[Guía de inicio rápido de Bash en Cloud Shell](quickstart.md) <br>
[Más información sobre la CLI de Azure 2.0](https://docs.microsoft.com/cli/azure/)
