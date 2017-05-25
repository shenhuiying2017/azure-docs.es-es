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
ms.date: 05/10/2017
ms.author: juluk
ms.translationtype: Human Translation
ms.sourcegitcommit: 44eac1ae8676912bc0eb461e7e38569432ad3393
ms.openlocfilehash: e789283ab9b25f634c50b341ca882bbf9c70a225
ms.contentlocale: es-es
ms.lasthandoff: 05/17/2017

---

# <a name="features--tools-for-azure-cloud-shell"></a>Características y herramientas para Azure Cloud Shell
Azure Cloud Shell es una experiencia de shell basado en el explorador para administrar y desarrollar recursos de Azure.

Cloud Shell ofrece una experiencia de shell preconfigurado y accesible desde el explorador para administrar recursos de Azure sin el trabajo añadido de realizar la instalación, el control de versiones y el mantenimiento de una máquina de forma manual.

Cloud Shell aprovisiona máquinas a medida que se solicitan y, por tanto, el estado de la máquina no se conservará entre sesiones. Como Cloud Shell se ha creado para sesiones interactivas, los shells finalizan automáticamente si pasan 10 minutos inactivos.

## <a name="tools"></a>Herramientas
|Categoría   |Nombre   |
|---|---|
|Intérprete de shell de Linux|Bash<br> sh               |
|Herramientas de Azure            |CLI de Azure 2.0 y 1.0     |
|Editores de texto           |vim<br> nano<br> emacs       |
|Control de código fuente         |git                    |
|Herramientas de compilación            |make<br> maven<br> npm<br> pip         |
|Contenedores             |Docker<br> Kubectl<br> DC/OS CLI         |
|Bases de datos              |Cliente de MySQL<br> Cliente de PostgreSql<br> Utilidad sqlcmd      |
|Otros                  |Cliente de iPython |

## <a name="language-support"></a>Compatibilidad con idiomas
|language   |Versión   |
|---|---|
|.NET       |1.01       |
|Go         |1.7        |
|Node.js    |6.9.4      |
|Python     |2.7 y 3.5|

## <a name="secure-automatic-authentication"></a>Protección de la autenticación automática
Cloud Shell autentica de forma segura y automática el acceso a la cuenta para la CLI de Azure 2.0.

## <a name="azure-files-persistence"></a>Persistencia de Azure Files
Puesto que Cloud Shell se asigna para cada solicitud mediante una máquina temporal, los archivos locales fuera de $Home y el estado de la máquina no se conservan entre sesiones.
Para conservar archivos entre sesiones, la primera vez que se inicia Cloud Shell se explica cómo conectar un recurso compartido de archivos de Azure.
Una vez finalizado, Cloud Shell conectará automáticamente su almacenamiento para todas las sesiones futuras.

[Más información acerca de cómo conectar recursos compartidos de archivos de Azure a Cloud Shell](persisting-shell-storage.md).

## <a name="next-steps"></a>Pasos siguientes
[Guía de inicio rápido de Cloud Shell](quickstart.md) 
[Más información sobre CLI de Azure 2.0](https://docs.microsoft.com/cli/azure/)
