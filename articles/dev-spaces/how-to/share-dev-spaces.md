---
title: Uso compartido de Azure Dev Spaces | Microsoft Docs
titleSuffix: Azure Dev Spaces
services: azure-dev-spaces
ms.service: azure-dev-spaces
ms.component: azds-kubernetes
author: ghogen
ms.author: ghogen
ms.date: 05/11/2018
ms.topic: article
description: Desarrollo rápido de Kubernetes con contenedores y microservicios en Azure
keywords: Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, contenedores
manager: douge
ms.openlocfilehash: 140a487a0a605e0f03122868af0d2d12bcdba35b
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/16/2018
ms.locfileid: "34197664"
---
# <a name="share-azure-dev-spaces"></a>Uso compartido de Azure Dev Spaces

Con Azure Dev Spaces, puede compartir el espacio de desarrollo con otras personas de su equipo. Cada programador puede trabajar en su propio espacio sin riesgo de interrumpir a los demás. Además, trabajar en conjunto en un solo espacio puede permitirle probar código de un extremo a otro sin tener que crear objetos ficticios ni simular dependencias. Consulte la guía de [información sobre el desarrollo en equipo](../get-started-nodejs.md#learn-about-team-development) para obtener más información.

Para configurar un entorno para varios desarrolladores:
1. Cree una instancia de Dev Space en Azure. Elija [.NET Core y VS Code](../get-started-netcore.md), [.NET Core y Visual Studio](../get-started-netcore-visualstudio.md) o [Node.js y VS Code](../get-started-nodejs.md). Debe tener acceso de propietario o colaborador a la suscripción de Azure seleccionada.
1. Configure el **grupo de recursos** del espacio de Azure Dev Spaces para [conceder acceso de colaborador](/azure/active-directory/role-based-access-control-configure) a cada miembro del equipo. Puede comprobar el grupo de recursos de un entorno mediante la ejecución de este comando: `azds resource list`
1. Pida a los miembros del equipo que **seleccionen el entorno** para desarrollar en él.
     * **Línea de comandos o VS Code**: para ver los espacios de Azure Dev Spaces existentes a los que tiene acceso: `azds resource list`. Para seleccionar un entorno: `azds resource select`.
     * **IDE de Visual Studio**: abra un proyecto en Visual Studio y seleccione **Azure Dev Spaces** en la lista desplegable de configuración de inicio. En el cuadro de diálogo que se abre, seleccione un entorno de desarrollo existente.

![Desplegable de configuración de inicio de Visual Studio](../media/get-started-netcore-visualstudio/LaunchSettings.png)