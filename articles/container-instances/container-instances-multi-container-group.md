---
title: "Implementación de grupos de varios contenedores en Azure Container Instances"
description: "Obtenga información sobre cómo implementar un grupo de contenedores con varios contenedores en Azure Container Instances."
services: container-instances
author: neilpeterson
manager: timlt
ms.service: container-instances
ms.topic: article
ms.date: 01/10/2018
ms.author: nepeters
ms.custom: mvc
ms.openlocfilehash: 41a47adb1f1da417038757934f0a6cf7e11555da
ms.sourcegitcommit: 9292e15fc80cc9df3e62731bafdcb0bb98c256e1
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/10/2018
---
# <a name="deploy-a-container-group"></a>Implementación de un grupo de contenedores

Azure Container Instances admite la implementación de varios contenedores en un solo host mediante un [grupo de contenedores](container-instances-container-groups.md). Esto es útil cuando se crea un sidecar de aplicación para el registro, la supervisión o cualquier otra configuración donde un servicio necesita un segundo proceso asociado.

Este documento describe la ejecución de una configuración de sidecar de varios contenedores sencilla mediante la implementación de una plantilla de Azure Resource Manager.

> [!NOTE]
> Los grupos de varios contenedores están restringidos actualmente a los contenedores Linux. Aunque estamos trabajando para traer todas las características a los contenedores Windows, puede encontrar diferencias en la plataforma actual en la [disponibilidad de cuotas y regiones para Azure Container Instances](container-instances-quotas.md).

## <a name="configure-the-template"></a>Configuración de la plantilla

Cree un archivo llamado `azuredeploy.json` y copie el siguiente código JSON en él.

En este ejemplo, se definen un grupo de contenedores con dos contenedores, una dirección IP pública y dos puertos expuestos. El primer contenedor del grupo ejecuta una aplicación accesible desde Internet. El segundo contenedor, el sidecar, realiza una solicitud HTTP a la aplicación web principal a través de la red local del grupo.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {},
  "variables": {
    "container1name": "aci-tutorial-app",
    "container1image": "microsoft/aci-helloworld:latest",
    "container2name": "aci-tutorial-sidecar",
    "container2image": "microsoft/aci-tutorial-sidecar"
  },
  "resources": [
    {
      "name": "myContainerGroup",
      "type": "Microsoft.ContainerInstance/containerGroups",
      "apiVersion": "2017-10-01-preview",
      "location": "[resourceGroup().location]",
      "properties": {
        "containers": [
          {
            "name": "[variables('container1name')]",
            "properties": {
              "image": "[variables('container1image')]",
              "resources": {
                "requests": {
                  "cpu": 1,
                  "memoryInGb": 1.5
                }
              },
              "ports": [
                {
                  "port": 80
                },
                {
                  "port": 8080
                }
              ]
            }
          },
          {
            "name": "[variables('container2name')]",
            "properties": {
              "image": "[variables('container2image')]",
              "resources": {
                "requests": {
                  "cpu": 1,
                  "memoryInGb": 1.5
                }
              }
            }
          }
        ],
        "osType": "Linux",
        "ipAddress": {
          "type": "Public",
          "ports": [
            {
              "protocol": "tcp",
              "port": "80"
            },
            {
                "protocol": "tcp",
                "port": "8080"
            }
          ]
        }
      }
    }
  ],
  "outputs": {
    "containerIPv4Address": {
      "type": "string",
      "value": "[reference(resourceId('Microsoft.ContainerInstance/containerGroups/', 'myContainerGroup')).ipAddress.ip]"
    }
  }
}
```

Para usar un registro de imagen de contenedor privado, agregue un objeto al documento JSON con el formato siguiente.

```json
"imageRegistryCredentials": [
  {
    "server": "[parameters('imageRegistryLoginServer')]",
    "username": "[parameters('imageRegistryUsername')]",
    "password": "[parameters('imageRegistryPassword')]"
  }
]
```

## <a name="deploy-the-template"></a>Implementación de la plantilla

Cree un grupo de recursos con el comando [az group create][az-group-create].

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

Implemente la plantilla con el comando [az group deployment create][az-group-deployment-create].

```azurecli-interactive
az group deployment create --resource-group myResourceGroup --name myContainerGroup --template-file azuredeploy.json
```

Al cabo de unos segundos, debe recibir una respuesta inicial de Azure.

## <a name="view-deployment-state"></a>Visualización del estado de la implementación

Para ver el estado de la implementación, use el comando [az container show][az-container-show]. Esto devuelve la dirección IP pública aprovisionada mediante la cual se puede acceder a la aplicación.

```azurecli-interactive
az container show --resource-group myResourceGroup --name myContainerGroup --output table
```

Salida:

```bash
Name              ResourceGroup    ProvisioningState    Image                                                           IP:ports               CPU/Memory       OsType    Location
----------------  ---------------  -------------------  --------------------------------------------------------------  ---------------------  ---------------  --------  ----------
myContainerGroup  myResourceGroup  Succeeded            microsoft/aci-helloworld:latest,microsoft/aci-tutorial-sidecar  52.168.26.124:80,8080  1.0 core/1.5 gb  Linux     westus
```

## <a name="view-logs"></a>Ver registros

Visualice la salida del registro de un contenedor con el comando [az container logs][az-container-logs]. El argumento `--container-name` especifica el contenedor del que se van a extraer registros. En este ejemplo, se especifica el primer contenedor.

```azurecli-interactive
az container logs --resource-group myResourceGroup --name myContainerGroup --container-name aci-tutorial-app
```

Salida:

```bash
listening on port 80
::1 - - [09/Jan/2018:23:17:48 +0000] "HEAD / HTTP/1.1" 200 1663 "-" "curl/7.54.0"
::1 - - [09/Jan/2018:23:17:51 +0000] "HEAD / HTTP/1.1" 200 1663 "-" "curl/7.54.0"
::1 - - [09/Jan/2018:23:17:54 +0000] "HEAD / HTTP/1.1" 200 1663 "-" "curl/7.54.0"
```

Para ver los registros para el contenedor sidecar, ejecute el mismo comando especificando el segundo nombre del contenedor.

```azurecli-interactive
az container logs --resource-group myResourceGroup --name myContainerGroup --container-name aci-tutorial-sidecar
```

Salida:

```bash
Every 3s: curl -I http://localhost                          2018-01-09 23:25:11

  % Total    % Received % Xferd  Average Speed   Time    Time     Time  Current
                                 Dload  Upload   Total   Spent    Left  Speed
  0  1663    0     0    0     0      0      0 --:--:-- --:--:-- --:--:--     0
HTTP/1.1 200 OK
X-Powered-By: Express
Accept-Ranges: bytes
Cache-Control: public, max-age=0
Last-Modified: Wed, 29 Nov 2017 06:40:40 GMT
ETag: W/"67f-16006818640"
Content-Type: text/html; charset=UTF-8
Content-Length: 1663
Date: Tue, 09 Jan 2018 23:25:11 GMT
Connection: keep-alive
```

Como puede ver, el sidecar realiza periódicamente una solicitud HTTP a la aplicación web principal a través de la red local del grupo para asegurarse de que se está ejecutando. Este ejemplo de sidecar podría ampliarse para desencadenar una alerta si recibe un código de respuesta HTTP distinto de 200 OK.

## <a name="next-steps"></a>pasos siguientes

En este artículo se explican los pasos necesarios para implementar una instancia de contenedor de Azure de varios contenedores. Para obtener una experiencia integral de Azure Container Instances, consulte el tutorial de Azure Container Instances.

> [!div class="nextstepaction"]
> [Tutorial de Azure Container Instances][aci-tutorial]

<!-- LINKS - Internal -->
[aci-tutorial]: ./container-instances-tutorial-prepare-app.md
[az-container-logs]: /cli/azure/container#az_container_logs
[az-container-show]: /cli/azure/container#az_container_show
[az-group-create]: /cli/azure/group#az_group_create
[az-group-deployment-create]: /cli/azure/group/deployment#az_group_deployment_create
