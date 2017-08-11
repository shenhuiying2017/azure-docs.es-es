---
title: "Tutorial de Azure Container Instances: implementación de aplicación | Microsoft Docs"
description: "Tutorial de Azure Container Instances: implementación de aplicación"
services: container-instances
documentationcenter: 
author: seanmck
manager: timlt
editor: 
tags: 
keywords: 
ms.assetid: 
ms.service: container-instances
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/19/2017
ms.author: seanmck
ms.translationtype: HT
ms.sourcegitcommit: fff84ee45818e4699df380e1536f71b2a4003c71
ms.openlocfilehash: e48477a79e28db2c40c99fd46d9c5b84506a4279
ms.contentlocale: es-es
ms.lasthandoff: 08/01/2017

---

# <a name="deploy-a-container-to-azure-container-instances"></a>Implementación de un contenedor en Azure Container Instances

Esta es la tercera parte de un tutorial de tres. En las secciones anteriores, [se creó una imagen de contenedor](container-instances-tutorial-prepare-app.md) y [se insertó en una instancia de Azure Container Registry](container-instances-tutorial-prepare-acr.md). En esta sección se completa el tutorial con la implementación del contenedor en Azure Container Instances. Los pasos completados incluyen:

> [!div class="checklist"]
> * Definición de un grupo de contenedores mediante una plantilla de Azure Resource Manager
> * Implementación del grupo de contenedores mediante la CLI de Azure
> * Visualización de los registros de contenedores

## <a name="deploy-the-container-using-the-azure-cli"></a>Implementación del contenedor mediante la CLI de Azure

La CLI de Azure permite la implementación de un contenedor en Azure Container Instances en solo un comando. Como la imagen de contenedor se hospeda en la instancia privada de Azure Container Registry, debe incluir las credenciales que se necesitan para acceder a ella. En caso de ser necesario, puede consultarlas tal como se indica a continuación.

Servidor de inicio de sesión en el registro de contenedor (actualice con su nombre de registro):

```azurecli-interactive
az acr show --name <acrName> --query loginServer
```

Contraseña del registro de contenedor:

```azurecli-interactive
az acr credential show --name <acrName> --query passwords[0].value
```

Para implementar la imagen de contenedor desde el registro de contenedor con una solicitud de recurso de 1 núcleo de CPU y 1 GB de memoria, ejecute el comando siguiente:

```azurecli-interactive
az container create --name aci-tutorial-app --image <acrLoginServer>/aci-tutorial-app:v1 --cpu 1 --memory 1 --registry-login-server <acrLoginServer> --registry-username <acrName> --registry-password <acrPassword> --ip-address public -g myResourceGroup
```

En cuestión de segundos, recibirá una respuesta inicial de Azure Resource Manager. Para ver el estado de la implementación, use:

```azurecli-interactive
az container show --name aci-tutorial-app --resource-group myResourceGroup
```

La salida incluye la dirección IP pública que puede usar para acceder a la aplicación en el explorador.

```json
...
"ipAddress": {
      "ip": "13.88.176.27",
      "ports": [
        {
          "port": 80,
          "protocol": "TCP"
        }
      ]
    }
...
```


## <a name="view-the-application-and-container-logs"></a>Visualización de los registros de contenedor y aplicación

Una vez que la implementación se complete correctamente, puede abrir el explorador en la dirección IP que se muestran en la salida de `az container show`.

![Aplicación Hola mundo en el explorador][aci-app-browser]

También puede ver la salida de registro del contenedor:

```azurecli-interactive
az container logs --name aci-tutorial-app -g myResourceGroup
```

Salida:

```bash
listening on port 80
::ffff:10.240.0.4 - - [21/Jul/2017:06:00:02 +0000] "GET / HTTP/1.1" 200 1663 "-" "Mozilla/5.0 (Macintosh; Intel Mac OS X 10_12_5) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/59.0.3071.115 Safari/537.36"
::ffff:10.240.0.4 - - [21/Jul/2017:06:00:02 +0000] "GET /favicon.ico HTTP/1.1" 404 150 "http://13.88.176.27/" "Mozilla/5.0 (Macintosh; Intel Mac OS X 10_12_5) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/59.0.3071.115 Safari/537.36"
```

## <a name="next-steps"></a>Pasos siguientes

En este tutorial se completó el proceso de implementar los contenedores en Azure Container Instances. Se han completado los siguientes pasos:

> [!div class="checklist"]
> * Implementación del contenedor desde Azure Container Registry mediante la CLI de Azure
> * Visualización de la aplicación en el explorador
> * Visualización de los registros del contenedor

<!-- LINKS -->
[prepare-app]: ./container-instances-tutorial-prepare-app.md

<!-- IMAGES -->
[aci-app-browser]: ./media/container-instances-quickstart/aci-app-browser.png

