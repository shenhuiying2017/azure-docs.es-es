---
title: Uso de kubectl con Azure Dev Spaces | Microsoft Docs
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
ms.openlocfilehash: 38a433a14ab977fb56a8331a057d27241f1d9783
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/16/2018
ms.locfileid: "34198748"
---
# <a name="use-kubectl-with-an-azure-dev-space"></a>Uso de kubectl con una instancia de Azure Dev Spaces

Puede tener acceso al clúster de Kubernetes dentro de un espacio de Azure Dev Space y utilizar herramientas de Kubernetes existentes, como `kubectl`.

La ejecución de `azds resource create` o `azds resource select` agregará automáticamente un contexto de configuración de `kubectl` para usted, por lo que kubectl ya debe estar conectado a su clúster de Kubernetes en Azure Dev Spaces. Ejemplos:
- Confirme el contexto actual: `kubectl config current-context`
- Enumere todos los contextos disponibles: `kubectl config get-contexts`. 
- Cambie el contexto: `kubectl config use-context <context-name>`
- Vea el panel de Kubernetes: ejecute `kubectl proxy` y luego abra el explorador en la dirección que emite este comando (adjunte `/ui` a la dirección URL para navegar al panel de Kubernetes).
- Enumere los servicios en ejecución en el espacio predeterminado de Azure Dev Spaces denominado *default*: `kubectl get services --namespace=default`

