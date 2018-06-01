---
title: Azure Dev Spaces | Microsoft Docs
services: azure-dev-spaces
ms.service: azure-dev-spaces
ms.component: azds-kubernetes
author: ghogen
ms.author: ghogen
ms.date: 05/11/2018
ms.topic: tutorial
description: Desarrollo rápido de Kubernetes con contenedores y microservicios en Azure
keywords: Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, contenedores
manager: douge
ms.openlocfilehash: 344947b7906d15e819e372e0affe4af3c34ba69b
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/16/2018
ms.locfileid: "34198765"
---
# <a name="azure-dev-spaces"></a>Azure Dev Spaces
Azure Dev Spaces le ayuda a desarrollar con velocidad en Kubernetes. Con Azure Dev Spaces, también se incorporan funcionalidades de desarrollo completas, como la depuración de los contenedores de Kubernetes de Azure, además de desarrollar contenedores de manera iterativa en la nube con herramientas conocidas como VS Code, Visual Studio o la línea de comandos. Azure Dev Spaces es especialmente importante en el desarrollo en equipo, en el que el aislamiento de las bifurcaciones de código individuales en sus propios espacios es una parte fundamental del ciclo de vida de desarrollo.

## <a name="how-azure-dev-spaces-simplifies-kubernetes-development"></a>Cómo simplifica Azure Dev Spaces el desarrollo de Kubernetes 

Este enfoque tiene varias ventajas:

* Se obtiene un entorno de desarrollo sin infraestructura que es representativo del entorno de producción, con acceso completo a los recursos en la nube.
* Puede depurar contenedores Node.js y .NET Core directamente en Kubernetes con VS Code o Visual Studio. Todos los demás lenguajes se pueden desarrollar con la interfaz de línea de comandos.
* Compartir una instancia de Kubernetes entre el equipo de desarrollo para ahorrar costos y minimizar así la configuración de la máquina local para los nuevos miembros del equipo.
* Desarrollar el código de forma aislada y realizar pruebas de un extremo a otro con otros componentes sin réplicas ni dependencias de simulación.

[!INCLUDE[](includes/get-started.md)]

![](media/azure-dev-spaces/vscode-overview.png)