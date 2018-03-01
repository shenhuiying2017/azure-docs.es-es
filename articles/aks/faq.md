---
title: "Preguntas más frecuentes sobre Azure Container Service"
description: "En este artículo se ofrecen respuestas a algunas de las preguntas más frecuentes sobre Azure Container Service."
services: container-service
author: neilpeterson
manager: timlt
ms.service: container-service
ms.topic: article
ms.date: 2/14/2018
ms.author: nepeters
ms.openlocfilehash: 59dceded1e72e6e0e3d1a2bb25ca63bd023a9d21
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 02/21/2018
---
# <a name="frequently-asked-questions-about-azure-container-service-aks"></a>Preguntas más frecuentes sobre Azure Container Service (AKS)

En este artículo se abordan las preguntas más frecuentes sobre Azure Container Service (AKS).

## <a name="which-azure-regions-provide-the-azure-container-service-aks-today"></a>¿Qué regiones de Azure proporcionan actualmente Azure Container Service (AKS)?

- Centro de Canadá 
- Este de Canadá 
- Central EE. UU: 
- Este de EE. UU 
- Sudeste de Asia 
- Europa occidental 
- Oeste de EE. UU. 2 

## <a name="when-will-additional-regions-be-added"></a>¿Cuándo se agregarán más regiones? 

Se agregarán más regiones a medida que aumente la demanda.

## <a name="are-security-updates-applied-to-aks-agent-nodes"></a>¿Se aplican las actualizaciones de seguridad a los nodos de agente de AKS? 

Azure aplica automáticamente revisiones de seguridad a los nodos del clúster según una programación nocturna. Sin embargo, es responsabilidad suya asegurarse de que los nodos se reinician si es necesario. Para realizar el reinicio de los nodos, cuenta con varias opciones:

- Manualmente, mediante Azure Portal o la CLI de Azure. 
- Mediante la actualización del clúster de AKS. Las actualizaciones del clúster [acordonan y purgan los nodos](https://kubernetes.io/docs/tasks/administer-cluster/safely-drain-node/) y luego los ponen de nuevo en activo con la imagen de Ubuntu más reciente. Puede actualizar la imagen de sistema operativo en los nodos sin cambiar las versiones de Kubernetes mediante la especificación de la versión actual del clúster en `az aks upgrade`.
- Mediante [Kured](https://github.com/weaveworks/kured), un demonio de reinicio de código abierto para Kubernetes. Kured se ejecuta como un elemento [DaemonSet](https://kubernetes.io/docs/concepts/workloads/controllers/daemonset/) y supervisa cada nodo para comprobar la presencia de un archivo que indique que hace falta un reinicio. A continuación, organiza esos reinicios en el clúster, siguiendo el mismo proceso de acordonamiento y purga descrito anteriormente.

## <a name="do-you-recommend-customers-use-acs-or-aks"></a>¿Recomienda que los clientes usen ACS o AKS? 

Mientras que AKS siga en versión preliminar, se recomienda crear clústeres de producción con ACS Kubernetes o [acs-engine](https://github.com/azure/acs-engine). Puede usar AKS para las implementaciones de prueba de concepto y los entornos de desarrollo y pruebas.

## <a name="when-will-acs-be-deprecated"></a>¿Cuándo dejará de utilizarse ACS? 

ACS dejará de utilizarse aproximadamente cuando AKS esté disponible con carácter general. Dispondrá de doce meses después de esa fecha para migrar los clústeres a AKS. Durante el período de doce meses, puede ejecutar todas las operaciones de ACS.

## <a name="does-aks-support-node-autoscaling"></a>¿AKS admite el escalado automático de los nodos? 

El escalado automático de los nodos no se admite, pero está previsto. Puede que desee echar un vistazo a esta [implementación de escalado automático][auto-scaler] de código abierto.

## <a name="does-aks-support-kubernetes-role-based-access-control-rbac"></a>¿Admite AKS el control de acceso basado en roles (RBAC) de Kubernetes?

No, RBAC no se admite actualmente en AKS pero pronto estará disponible.   

## <a name="can-i-deploy-aks-into-my-existing-virtual-network"></a>¿Puedo implementar AKS en mi red virtual existente?

No, esta opción no está disponible, aunque lo estará pronto.

## <a name="is-azure-key-vault-integrated-with-aks"></a>¿Azure Key Vault se integra con AKS? 

No, pero esta integración está prevista. Mientras tanto, puede probar la solución siguiente de [Hexadite][hexadite]. 

## <a name="can-i-run-windows-server-containers-on-aks"></a>¿Puedo ejecutar contenedores de Windows Server en AKS?

No, AKS no proporciona actualmente nodos de agente basados en Windows Server, así que no puede ejecutar contenedores de Windows Server. Si necesita ejecutar contenedores de Windows Server en Kubernetes en Azure, consulte la [documentación de acs-engine](https://github.com/Azure/acs-engine/blob/master/docs/kubernetes/windows.md).

## <a name="why-are-two-resource-groups-created-with-aks"></a>¿Por qué se crean dos grupos de recursos con AKS? 

Cada implementación de AKS abarca dos grupos de recursos. El primero lo crea el usuario y solo contiene el recurso de AKS. El proveedor de recursos de AKS crea automáticamente el segundo durante la implementación, con un nombre como *MC_myResourceGRoup_myAKSCluster_eastus*. El segundo grupo de recursos contiene todos los recursos de infraestructura asociados con el clúster, como máquinas virtuales, redes y almacenamiento. Se crea para simplificar la limpieza de recursos. 

Si va a crear recursos que se usarán con el clúster de AKS, como cuentas de almacenamiento o direcciones IP públicas reservadas, debe colocarlos en el grupo de recursos generado automáticamente.

<!-- LINKS - external -->
[auto-scaler]: https://github.com/kubernetes/autoscaler
[hexadite]: https://github.com/Hexadite/acs-keyvault-agent  