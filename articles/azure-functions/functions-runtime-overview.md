---
title: "Introducción al Sistema en ejecución de Azure Functions | Microsoft Docs"
description: "Introducción al Sistema en ejecución de Azure Functions (versión preliminar)"
services: functions
documentationcenter: 
author: apwestgarth
manager: stefsch
editor: 
ms.assetid: 
ms.service: functions
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: multiple
ms.topic: article
ms.date: 05/08/2017
ms.author: anwestg
ms.openlocfilehash: cb98d5f2aaa526555820c15ba5a32fb7e78ffc5a
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="azure-functions-runtime-overview"></a>Introducción al Sistema en ejecución de Azure Functions

El Sistema en ejecución de Azure Functions proporciona una nueva forma de aprovechar la simplicidad y flexibilidad del modelo de programación de Azure Functions de forma local. Basado en las mismas raíces de código abierto que Azure Functions, el Sistema en ejecución de Azure Functions se implementa de forma local para proporcionar una experiencia de desarrollo prácticamente idéntica al servicio en la nube.

![Portal del Sistema en ejecución de Azure Functions (versión preliminar)][1]

El Sistema en ejecución de Azure Functions proporciona una forma de experimentar Azure Functions antes de pasarse a la nube. De esta manera, se puede llevar a la nube los recursos de código que cree cuando migre.  El sistema en tiempo de ejecución también le ofrece nuevas opciones, como el uso de la capacidad de proceso adicional de sus equipos locales para ejecutar procesos por lotes durante la noche. También puede usar dispositivos dentro de su organización para enviar datos de forma condicional a otros sistemas, tanto locales como en la nube.

El Sistema en ejecución de Azure Functions consta de dos partes:
* Rol de administración del Sistema en ejecución de Azure Functions
* Rol de trabajo del Sistema en ejecución de Azure Functions

## <a name="azure-functions-management-role"></a>Rol de administración de Azure Functions

El rol de administración de Azure Functions proporciona un host para la administración de Functions de forma local. Este rol realiza las siguientes tareas:

* Hospedaje del Portal de administración de Azure Functions, que es el mismo que se ve en [Azure Portal](https://portal.azure.com). Esto le permite desarrollar las funciones de la misma manera que lo haría en Azure Portal.
* Distribución de funciones entre varios trabajadores de Functions.
* Proporciona un punto de conexión de publicación para que pueda publicar sus funciones directamente desde Microsoft Visual Studio.

## <a name="azure-functions-worker-role"></a>Rol de trabajo de Azure Functions

Los roles de trabajo de Azure Functions se implementan en los contenedores de Windows y son donde se ejecuta el código de la función.  Puede implementar varios roles de trabajo en toda la organización; se trata de una manera clave en que los clientes pueden hacer uso de la capacidad de proceso que sobra.

## <a name="minimum-requirements"></a>Requisitos mínimos

Para empezar a trabajar con el Sistema en ejecución de Azure Functions, debe tener una máquina con **Windows Server 2016 o Windows 10 Creators Update** y acceso a una instancia de **SQL Server**.

## <a name="next-steps"></a>Pasos siguientes

Instale la [versión preliminar del Sistema en ejecución de Azure Functions](https://aka.ms/azafr)

<!--Image references-->
[1]: ./media/functions-runtime-overview/AzureFunctionsRuntime_Portal.png
