---
title: "Características de Azure Cloud Shell (versión preliminar) | Microsoft Docs"
description: "Introducción a las características de Azure Cloud Shell"
services: 
documentationcenter: 
author: jluk
manager: timlt
tags: azure-resource-manager
ms.assetid: 
ms.service: 
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 06/27/2017
ms.author: juluk
ms.translationtype: Human Translation
ms.sourcegitcommit: 857267f46f6a2d545fc402ebf3a12f21c62ecd21
ms.openlocfilehash: 2b434ccb9218a0a2731642da6125031592ea8ce6
ms.contentlocale: es-es
ms.lasthandoff: 06/28/2017

---

# <a name="features--tools-for-azure-cloud-shell"></a>Características y herramientas para Azure Cloud Shell
Azure Cloud Shell es una experiencia de shell basado en el explorador para administrar y desarrollar recursos de Azure.

Cloud Shell ofrece una experiencia de shell preconfigurado y accesible desde el explorador para administrar recursos de Azure sin el trabajo añadido de realizar la instalación, el control de versiones y el mantenimiento de una máquina de forma manual.

Cloud Shell aprovisiona máquinas a medida que se solicitan y, por tanto, el estado de la máquina no se conservará entre sesiones. Como Cloud Shell se ha creado para sesiones interactivas, los shells finalizan automáticamente si pasan 10 minutos inactivos.

## <a name="bash-in-cloud-shell"></a>Bash en Cloud Shell
### <a name="tools"></a>Herramientas
|Categoría   |Nombre   |
|---|---|
|Intérprete de shell de Linux|Bash<br> sh               |
|Herramientas de Azure            |CLI de Azure 2.0 y 1.0<br> AzCopy     |
|Editores de texto           |vim<br> nano<br> emacs       |
|Control de código fuente         |git                    |
|Herramientas de compilación            |make<br> maven<br> npm<br> pip         |
|Contenedores             |Docker CLI/Docker Machine<br> Kubectl<br> DC/OS CLI         |
|Bases de datos              |Cliente de MySQL<br> Cliente de PostgreSql<br> Utilidad sqlcmd      |
|Otros                  |Cliente de iPython<br> CLI de Cloud Foundry<br> |

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
Puesto que Cloud Shell se asigna para cada solicitud mediante una máquina temporal, los archivos locales fuera de $Home y el estado de la máquina no se conservan entre sesiones.
Para conservar archivos entre sesiones, la primera vez que se inicia Cloud Shell se explica cómo conectar un recurso compartido de archivos de Azure.
Una vez finalizado, Cloud Shell conectará automáticamente su almacenamiento para todas las sesiones futuras.

[Obtenga más información sobre cómo conectar recursos compartidos de archivos de Azure a Cloud Shell](persisting-shell-storage.md).

## <a name="next-steps"></a>Pasos siguientes
[Inicio rápido de Cloud Shell](quickstart.md) <br>
[Más información sobre la CLI de Azure 2.0](https://docs.microsoft.com/cli/azure/) <br>
