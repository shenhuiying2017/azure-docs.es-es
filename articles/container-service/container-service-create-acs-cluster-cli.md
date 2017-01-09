---
title: "Implementación de un clúster de Azure Container Service | Microsoft Docs"
description: "Implementación de un clúster de Azure Container Service mediante la versión preliminar de Azure CLI 2.0"
services: container-service
documentationcenter: 
author: sauryadas
manager: timlt
editor: 
tags: acs, azure-container-service
keywords: 
ms.assetid: 8da267e8-2aeb-4c24-9a7a-65bdca3a82d6
ms.service: container-service
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/01/2016
ms.author: saudas
translationtype: Human Translation
ms.sourcegitcommit: 855f0fe77bd55f6ec0dacad4bc28603ac1c6979c
ms.openlocfilehash: c4a513686433e802f27f78de60e8b7fca21b4634


---
# <a name="using-the-azure-cli-20-preview-to-create-an-azure-container-service-cluster"></a>Using the Azure CLI 2.0 preview to create an Azure Container Service cluster (Uso de la vista previa de la CLI de Azure 2.0 para crear un clúster de Azure Container Service)

Para crear un clúster de Azure Container Service, necesitará:
* Una cuenta de Azure ([obtenga aquí una prueba gratuita](https://azure.microsoft.com/pricing/free-trial/)).
* La [CLI de Azure v. 2.0 (versión preliminar)](https://github.com/Azure/azure-cli#installation) instalada
* para iniciar sesión en la cuenta de Azure (consulte más abajo)

## <a name="log-in-to-your-account"></a>Inicie sesión en su cuenta.
```azurecli
az login 
```
Tendrán que ir a este [vínculo](https://login.microsoftonline.com/common/oauth2/deviceauth) para autenticarse con el código de dispositivo especificado en la CLI.

![comando de tipo](media/container-service-create-acs-cluster-cli/login.png)

![iPhone](media/container-service-create-acs-cluster-cli/login-browser.png)


## <a name="create-a-resource-group"></a>Crear un grupo de recursos
```azurecli
az group create -n acsrg1 -l "westus"
```

![Crear grupo de recursos de imagen](media/container-service-create-acs-cluster-cli/rg-create.png)

## <a name="list-of-available-azure-container-service-cli-commands"></a>Lista de comandos disponibles de CLI de Azure Container Service

```azurecli
az acs -h
```

![Uso de comandos de ACS](media/container-service-create-acs-cluster-cli/acs-command-usage-help.png)

## <a name="create-an-azure-container-service-cluster"></a>Implementación de un clúster de Azure Container Service

*Uso del comando create de ACS en la CLI*

```azurecli
az acs create -h
```
Son obligatorios el nombre del servicio de contenedor, el grupo de recursos creado en el paso anterior y un nombre DNS único. Otras entradas se establecen en los valores predeterminados (consulte la siguiente pantalla con la instantánea de ayuda a continuación), a menos que se sobrescriban con sus respectivos modificadores.
![Imagen de la ayuda del comando create de ACS](media/container-service-create-acs-cluster-cli/acs-command-usage-help.png)

*Cree ACS rápidamente con los valores predeterminados. Si no tiene ninguna clave SSH, utilice el segundo comando. Éste segundo comando create con el modificador --generate-ssh-keys creará uno automáticamente.*

```azurecli
az acs create -n acs-cluster -g acsrg1 -d applink789
```

```azurecli
az acs create -n acs-cluster -g acsrg1 -d applink789 --generate-ssh-keys
```

*Asegúrese de que dns-prefix (modificador -d) es único. Si se produce un error, vuelva a intentarlo con una cadena única.*

Después de escribir el comando anterior, espere unos 10 minutos a que se cree el clúster.

![Imagen del comando create de ACS](media/container-service-create-acs-cluster-cli/cluster-create.png)

## <a name="list-acs-clusters"></a>Lista de clústeres de ACS 

### <a name="under-a-subscription"></a>En una suscripción

```azurecli
az acs list --output table
```

### <a name="in-a-specific-resource-group"></a>En un grupo de recursos específico

```azurecli
az acs list -g acsrg1 --output table
```

![Imagen del comando list de ACS](media/container-service-create-acs-cluster-cli/acs-list.png)


## <a name="display-details-of-a-container-service-cluster"></a>Visualización de los detalles de un clúster del servicio de contenedor

```azurecli
az acs show -g acsrg1 -n acs-cluster --output list
```

![Imagen del comando list de ACS](media/container-service-create-acs-cluster-cli/acs-show.png)


## <a name="scale-the-acs-cluster"></a>Escalado del clúster de ACS
*Se permite la reducción y el escalado horizontales. El parámetro new-agent-count es el nuevo número de agentes en el clúster de ACS.*

```azurecli
az acs scale -g acsrg1 -n acs-cluster --new-agent-count 4
```

![Imagen del comando scale de ACS](media/container-service-create-acs-cluster-cli/acs-scale.png)

## <a name="delete-a-container-service-cluster"></a>Eliminación de un clúster del servicio de contenedor
```azurecli
az acs delete -g acsrg1 -n acs-cluster 
```
*Tenga en cuenta que este comando delete no elimina todos los recursos (red y almacenamiento) creados al crear el servicio de contenedor. Para eliminar todos los recursos, se recomienda crea un único clúster de ACS por grupo de recursos; en ese caso, el grupo de recursos se puede eliminar cuando el clúster de ACS ya no es necesario para asegurarse de que todos los recursos relacionados se eliminen y no se cobra por ellos.*



<!--HONumber=Nov16_HO3-->


