---
title: "Preguntas más frecuentes sobre Azure Container Service"
description: "En este artículo se ofrecen respuestas a algunas de las preguntas más frecuentes sobre Azure Container Service."
services: container-service
author: neilpeterson
manager: timlt
ms.service: container-service
ms.topic: article
ms.date: 2/01/2018
ms.author: nepeters
ms.openlocfilehash: 2b78479c257930669729a7781b3893b3e2064bab
ms.sourcegitcommit: eeb5daebf10564ec110a4e83874db0fb9f9f8061
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 02/03/2018
---
# <a name="frequently-asked-questions-about-azure-container-service-aks"></a>Preguntas más frecuentes sobre Azure Container Service (AKS)

En este artículo se abordan las preguntas más frecuentes sobre Azure Container Service (AKS).

## <a name="which-azure-regions-will-have-azure-container-service-aks"></a>¿En qué regiones de Azure estará disponible Azure Container Service (AKS)? 

- Centro de Canadá 
- Este de Canadá 
- Central EE. UU: 
- Este de EE. UU 
- Sudeste de Asia 
- Europa occidental 
- Oeste de EE. UU. 2 

## <a name="when-will-additional-regions-be-added"></a>¿Cuándo se agregarán más regiones? 

Se agregarán más regiones a medida que aumente la demanda.

## <a name="are-security-updates-applied-to-aks-nodes"></a>¿Se aplican las actualizaciones de seguridad a los nodos de AKS? 

Las revisiones de seguridad del sistema operativo se aplican a los nodos del clúster según una programación nocturna, pero no se realiza ningún reinicio. Si resulta necesario, los nodos pueden reiniciarse en Azure Portal o con la CLI de Azure. Al actualizar un clúster, se utiliza la imagen de Ubuntu más reciente y se aplican todas las revisiones de seguridad (con un reinicio).

## <a name="do-you-recommend-customers-use-acs-or-akss"></a>¿Recomienda a los clientes usar ACS o AKS? 

Dado que Azure Container Service (AKS) estará disponible con carácter general en una fecha posterior, se recomienda compilar clústeres de desarrollo y pruebas de prueba de concepto (POC) en AKS, pero clústeres de producción de Kubernetes en ACS.  

## <a name="when-will-acs-be-deprecated"></a>¿Cuándo dejará de utilizarse ACS? 

ACS dejará de utilizarse aproximadamente cuando AKS esté disponible con carácter general. Dispondrá de doce meses después de esa fecha para migrar los clústeres a AKS. Durante el período de doce meses, puede ejecutar todas las operaciones de ACS.

## <a name="does-aks-support-node-autoscaling"></a>¿AKS admite el escalado automático de los nodos? 

El escalado automático de los nodos no se admite, pero está previsto. Puede que desee echar un vistazo a esta [implementación de escalado automático][auto-scaler] de código abierto.

## <a name="why-are-two-resource-groups-created-with-aks"></a>¿Por qué se crean dos grupos de recursos con AKS? 

El segundo grupo de recursos se crea automáticamente para poder eliminar con facilidad todos los recursos asociados con una implementación de AKS.

## <a name="is-azure-key-vault-integrated-with-aks"></a>¿Azure Key Vault se integra con AKS? 

No, pero esta integración está prevista. Mientras tanto, puede probar la solución siguiente de [Hexadite][hexadite]. 

<!-- LINKS - external -->
[auto-scaler]: https://github.com/kubernetes/autoscaler
[hexadite]: https://github.com/Hexadite/acs-keyvault-agent  