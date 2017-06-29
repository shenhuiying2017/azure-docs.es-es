---
title: Uso de Draft con Azure Container Service y Azure Container Registry | Microsoft Docs
description: "Creación de un clúster de ACS Kubernetes y Azure Container Registry para crear la primera aplicación en Azure con Draft."
services: container-service
documentationcenter: 
author: squillace
manager: gamonroy
editor: 
tags: draft, helm, acs, azure-container-service
keywords: Docker, contenedores, microservicios, Kubernetes, Draft, Azure
ms.service: container-service
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/31/2017
ms.author: rasquill
ms.translationtype: Human Translation
ms.sourcegitcommit: 43aab8d52e854636f7ea2ff3aae50d7827735cc7
ms.openlocfilehash: 20619fd21f376afee95facb688e35534f5979b90
ms.contentlocale: es-es
ms.lasthandoff: 06/03/2017



---

# <a name="use-draft-with-azure-container-service-and-azure-container-registry-to-build-and-deploy-an-application-to-kubernetes"></a>Uso de Draft con Azure Container Service y Azure Container Registry para crear e implementar una aplicación en Kubernetes

[Draft](https://aka.ms/draft) es una nueva herramienta de código abierto que facilita el desarrollo de aplicaciones basadas en contenedores y su implementación en clústeres de Kubernetes sin necesidad de tener un gran conocimiento sobre Docker y Kubernetes, o incluso su instalación. Con herramientas como Draft, usted y su equipo pueden centrarse en la creación de la aplicación con Kubernetes, sin tener que prestar mucha atención a la infraestructura.

Puede usar Draft con cualquier registro de imágenes de Docker y cualquier clúster de Kubernetes, incluidos los locales. Este tutorial muestra cómo utilizar ACS con Kubernetes, ACR y Azure DNS para crear una canalización de desarrollador de CI/CD en vivo mediante Draft.


## <a name="create-an-azure-container-registry"></a>Creación de una instancia de Azure Container Registry
Le resultará muy fácil [crear una nueva instancia de Azure Container Registry](../container-registry/container-registry-get-started-azure-cli.md) con los pasos siguientes:

1. Cree un grupo de recursos de Azure para administrar el registro ACR y el clúster de Kubernetes en ACS.
      ```azurecli
      az group create --name draft --location eastus
      ```

2. Cree un registro de imagen ACR con [az acr create](/cli/azure/acr#create)
      ```azurecli
      az acr create -g draft -n draftacs --sku Basic --admin-enabled true -l eastus
      ```


## <a name="create-an-azure-container-service-with-kubernetes"></a>Creación de Azure Container Service mediante Kubernetes

Ya está listo para usar [az acs create](/cli/azure/acs#create) y crear un clúster de ACS utilizando Kubernetes como el valor `--orchestrator-type`.
```azurecli
az acs create --resource-group draft --name draft-kube-acs --dns-prefix draft-cluster --orchestrator-type kubernetes
```

> [!NOTE]
> Como Kubernetes no es el tipo de orquestador predeterminado, asegúrese de utilizar el conmutador `--orchestrator-type kubernetes`.

El resultado, cuando es correcto, es parecido al siguiente.

```json
waiting for AAD role to propagate.done
{
  "id": "/subscriptions/<guid>/resourceGroups/draft/providers/Microsoft.Resources/deployments/azurecli14904.93snip09",
  "name": "azurecli1496227204.9323909",
  "properties": {
    "correlationId": "<guid>",
    "debugSetting": null,
    "dependencies": [],
    "mode": "Incremental",
    "outputs": null,
    "parameters": {
      "clientSecret": {
        "type": "SecureString"
      }
    },
    "parametersLink": null,
    "providers": [
      {
        "id": null,
        "namespace": "Microsoft.ContainerService",
        "registrationState": null,
        "resourceTypes": [
          {
            "aliases": null,
            "apiVersions": null,
            "locations": [
              "westus"
            ],
            "properties": null,
            "resourceType": "containerServices"
          }
        ]
      }
    ],
    "provisioningState": "Succeeded",
    "template": null,
    "templateLink": null,
    "timestamp": "2017-05-31T10:46:29.434095+00:00"
  },
  "resourceGroup": "draft"
}
```

Ahora que tiene un clúster, puede importar las credenciales mediante el comando [az acs kubernetes get-credentials](/cli/azure/acs/kubernetes#get-credentials). Ya tiene un archivo de configuración local para el clúster, que es lo que Helm y Draft necesitan para realizar su trabajo.

## <a name="install-and-configure-draft"></a>Instalación y configuración de Draft
Las instrucciones de instalación de Draft se encuentran en el [repositorio de Draft](https://github.com/Azure/draft/blob/master/docs/install.md). Son relativamente sencillas pero requieren cierta configuración, ya que dependen de [Helm](https://aka.ms/helm) para crear e implementar un gráfico de Helm en el clúster de Kubernetes.

1. [Descarga e instalación de Helm](https://aka.ms/helm#install).
2. Use Helm para buscar e instalar `stable/traefik` y el controlador de entrada para permitir las solicitudes entrantes de las compilaciones.
    ```bash
    $ helm search traefik
    NAME              VERSION    DESCRIPTION
    stable/traefik    1.2.1-a    A Traefik based Kubernetes ingress controller w...

    $ helm install stable/traefik --name ingress
    ```
    Ahora establezca una inspección en el controlador `ingress` para capturar el valor de IP externo cuando se implemente. Esta dirección IP será la [asignada a su dominio de implementación](#wire-up-deployment-domain) en la sección siguiente.

    ```bash
    kubectl get svc -w
    NAME                          CLUSTER-IP     EXTERNAL-IP     PORT(S)                      AGE
    ingress-traefik               10.0.248.104   13.64.108.240   80:31046/TCP,443:32556/TCP   1h
    kubernetes                    10.0.0.1       <none>          443/TCP                      7h
    ```

    En este caso, la dirección IP externa para el dominio de la implementación es `13.64.108.240`. Ya puede asignar su dominio a esa dirección IP.

## <a name="wire-up-deployment-domain"></a>Conexión del dominio de implementación

Draft crea una versión para cada gráfico de Helm que crea: cada aplicación en la que esté trabajando. Cada una obtiene un nombre generado que Draft usa como _subdominio_ además del _dominio de implementación_ de raíz que usted controla. (En este ejemplo, utilizamos `squillace.io` como dominio de implementación.) Para habilitar este comportamiento de subdominio, debe crear un registro D para `'*'` en las entradas de DNS para el dominio de implementación, de modo que cada subdominio generado se enrute al controlador de entrada del clúster de Kubernetes.

Su propio proveedor de dominios tiene su propia forma de asignar servidores DNS; para [delegar los servidores de nombres de dominio en Azure DNS](../dns/dns-delegate-domain-azure-dns.md), siga estos pasos:

1. Cree un grupo de recursos para su zona.
    ```azurecli
    az group create --name squillace.io --location eastus
    {
      "id": "/subscriptions/<guid>/resourceGroups/squillace.io",
      "location": "eastus",
      "managedBy": null,
      "name": "zones",
      "properties": {
        "provisioningState": "Succeeded"
      },
      "tags": null
    }
    ```

2. Cree una zona DNS para su dominio.
Utilice el comando [az network dns zone create](/cli/azure/network/dns/zone#create) para obtener que los servidores de nombres deleguen el control de DNS en Azure DNS para su dominio.
    ```azurecli
    az network dns zone create --resource-group squillace.io --name squillace.io
    {
      "etag": "<guid>",
      "id": "/subscriptions/<guid>/resourceGroups/zones/providers/Microsoft.Network/dnszones/squillace.io",
      "location": "global",
      "maxNumberOfRecordSets": 5000,
      "name": "squillace.io",
      "nameServers": [
        "ns1-09.azure-dns.com.",
        "ns2-09.azure-dns.net.",
        "ns3-09.azure-dns.org.",
        "ns4-09.azure-dns.info."
      ],
      "numberOfRecordSets": 2,
      "resourceGroup": "squillace.io",
      "tags": {},
      "type": "Microsoft.Network/dnszones"
    }
    ```
3. Agregue los servidores DNS que se le proporcionen al proveedor de dominios para su dominio de implementación, lo que le permite usar Azure DNS para redirigir el dominio tal como lo desee.
4. Cree una entrada de conjunto de registros D para la asignación del dominio de implementación para la IP `ingress` en el paso 2 de la sección anterior.
    ```azurecli
    az network dns record-set a add-record --ipv4-address 13.64.108.240 --record-set-name '*' -g squillace.io -z squillace.io
    ```
El resultado se parece a lo siguiente:
    ```json
    {
      "arecords": [
        {
          "ipv4Address": "13.64.108.240"
        }
      ],
      "etag": "<guid>",
      "id": "/subscriptions/<guid>/resourceGroups/squillace.io/providers/Microsoft.Network/dnszones/squillace.io/A/*",
      "metadata": null,
      "name": "*",
      "resourceGroup": "squillace.io",
      "ttl": 3600,
      "type": "Microsoft.Network/dnszones/A"
    }
    ```

5. Configure Draft para usar el registro y crear subdominios para cada gráfico de Helm que cree. Para configurar Draft, necesita:
  - el nombre de Azure Container Registry (en este ejemplo, `draftacs`)
  - la clave del registro, o la contraseña, de `az acr credential show -n $acrname --output tsv --query "passwords[0].value"`.
  - el dominio de implementación de raíz que ha configurado para asignar a la dirección IP externa de entrada de Kubernetes (en este caso, `13.64.108.240`)

  Con estos valores puede crear el valor codificado en base 64 de la cadena JSON de configuración, `{"username":"<user>","password":"<secret>","email":"email@example.com"}`. Una manera de codificar el valor es la siguiente (reemplace los valores de este ejemplo por los suyos).
      ```bash
      acrname="draftacs"
      password=$(az acr credential show -n $acrname --output tsv --query "passwords[0].value")
      authtoken=$(echo \{\"username\":\"$acrname\",\"password\":\"$password\",\"email\":\"rasquill@microsoft.com\"\} | base64)
      ```

  Para confirmar que la cadena JSON es correcta, escriba `echo $authtoken | base64 -D` para mostrar el resultado sin codificar.
  A continuación, inicialice Draft con este comando y argumento de configuración para la opción `-set`:
      ```bash
      draft init --set registry.url=$acrname.azurecr.io,registry.org=$acrname,registry.authtoken=$authtoken,basedomain=squillace.io
      ```
      > [!NOTE]
      > Es fácil olvidarse de que el valor `basedomain` es el dominio de implementación base que controla y ha configurado para que apunte a la dirección IP externa de entrada.

Ya está listo para implementar una aplicación.


## <a name="build-and-deploy-an-application"></a>Compilación e implementación de una aplicación

En el repositorio de Draft, hay [seis aplicaciones sencillas de ejemplo](https://github.com/Azure/draft/tree/master/examples). Clone el repositorio y vamos a usar el [ejemplo de Python](https://github.com/Azure/draft/tree/master/examples/python). Cambie al directorio examples/Python y escriba `draft create` para compilar la aplicación. Debe tener un aspecto similar al ejemplo siguiente.
```bash
$ draft create
--> Python app detected
--> Ready to sail
```

El resultado incluye un archivo Dockerfile y un gráfico de Helm. Para la compilación e implementación, solo tiene que escribir `draft up`. El resultado es exhaustivo pero comienza de forma similar al ejemplo siguiente.
```bash
$ draft up
--> Building Dockerfile
Step 1 : FROM python:onbuild
onbuild: Pulling from library/python
10a267c67f42: Pulling fs layer
fb5937da9414: Pulling fs layer
9021b2326a1e: Pulling fs layer
dbed9b09434e: Pulling fs layer
ea8a37f15161: Pulling fs layer
<snip>
```

y, cuando es correcto, finaliza de modo parecido al ejemplo siguiente.
```bash
ab68189731eb: Pushed
53c0ab0341bee12d01be3d3c192fbd63562af7f1: digest: sha256:bb0450ec37acf67ed461c1512ef21f58a500ff9326ce3ec623ce1e4427df9765 size: 2841
--> Deploying to Kubernetes
--> Status: DEPLOYED
--> Notes:

  http://gangly-bronco.squillace.io to access your application

Watching local files for changes...
```

Sea cual sea el nombre de su gráfico, ya puede ejecutar `curl http://gangly-bronco.squillace.io` para recibir la respuesta, `Hello World!`.

## <a name="next-steps"></a>Pasos siguientes

Ahora que tiene un clúster de ACS Kubernetes, puede investigar mediante [Azure Container Registry](../container-registry/container-registry-intro.md) para crear más implementaciones de este escenario y también otras distintas. Por ejemplo, puede crear un conjunto de registros DNS de dominios draft._basedomain.toplevel_ que controle aspectos de un subdominio más profundo para implementaciones de ACS específicas.







