---
title: Uso de Draft con Azure Container Service y Azure Container Registry
description: "Creación de un clúster de ACS Kubernetes y Azure Container Registry para crear la primera aplicación en Azure con Draft."
services: container-service
author: squillace
manager: timlt
ms.service: container-service
ms.topic: article
ms.date: 09/14/2017
ms.author: rasquill
ms.custom: mvc
ms.openlocfilehash: 9cf5b1227e69ada46bc0b1e5ff01cc12e73598f7
ms.sourcegitcommit: 5d3e99478a5f26e92d1e7f3cec6b0ff5fbd7cedf
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/06/2017
---
# <a name="use-draft-with-azure-container-service-and-azure-container-registry-to-build-and-deploy-an-application-to-kubernetes"></a>Uso de Draft con Azure Container Service y Azure Container Registry para crear e implementar una aplicación en Kubernetes

[!INCLUDE [aks-preview-redirect.md](../../../includes/aks-preview-redirect.md)]

[Draft](https://aka.ms/draft) es una nueva herramienta de código abierto que facilita el desarrollo de aplicaciones basadas en contenedores y su implementación en clústeres de Kubernetes sin necesidad de tener un gran conocimiento sobre Docker y Kubernetes, o incluso su instalación. Con herramientas como Draft, usted y su equipo pueden centrarse en la creación de la aplicación con Kubernetes, sin tener que prestar mucha atención a la infraestructura.

Puede usar Draft con cualquier registro de imágenes de Docker y cualquier clúster de Kubernetes, incluidos los locales. En este tutorial se muestra cómo usar ACS con Kubernetes y ACR para crear una canalización de desarrollador activa pero segura en Kubernetes mediante Draft. También se muestra cómo usar un DNS de Azure para exponer esa canalización de desarrollador para que otros usuarios puedan verla en un dominio.


## <a name="create-an-azure-container-registry"></a>Creación de una instancia de Azure Container Registry
Le resultará muy fácil [crear una nueva instancia de Azure Container Registry](../../container-registry/container-registry-get-started-azure-cli.md) con los pasos siguientes:

1. Cree un grupo de recursos de Azure para administrar el registro ACR y el clúster de Kubernetes en ACS.
      ```azurecli
      az group create --name draft --location eastus
      ```

2. Cree un registro de imagen ACR mediante [az acr create](/cli/azure/acr#create) y asegúrese de que la opción `--admin-enabled` está establecida en `true`.
      ```azurecli
      az acr create --resource-group draft --name draftacs --sku Basic
      ```


## <a name="create-an-azure-container-service-with-kubernetes"></a>Creación de Azure Container Service mediante Kubernetes

Ya está listo para usar [az acs create](/cli/azure/acs#create) y crear un clúster de ACS utilizando Kubernetes como el valor `--orchestrator-type`.
```azurecli
az acs create --resource-group draft --name draft-kube-acs --dns-prefix draft-cluster --orchestrator-type kubernetes --generate-ssh-keys
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


1. Descargue Draft para su entorno en https://github.com/Azure/draft/releases e instálelo en su RUTA DE ACCESO para que se pueda usar el comando.
2. Descargue Helm para su entorno en https://github.com/kubernetes/helm/releases e [instálelo en su RUTA DE ACCESO para que se pueda usar el comando](https://github.com/kubernetes/helm/blob/master/docs/install.md#installing-the-helm-client).
3. Configure Draft para usar el registro y crear subdominios para cada gráfico de Helm que cree. Para configurar Draft, necesita:
  - el nombre de Azure Container Registry (en este ejemplo, `draftacsdemo`)
  - la clave del registro, o la contraseña, de `az acr credential show -n <registry name> --output tsv --query "passwords[0].value"`.

  Llame a `draft init` y el proceso de configuración le pedirá los valores indicados anteriormente; tenga en cuenta que el formato de la dirección URL del registro es el nombre del registro (en este ejemplo, `draftacsdemo`) y `.azurecr.io`. El nombre de usuario es el nombre del registro por sí mismo. El proceso es algo parecido a lo siguiente la primera vez que lo ejecuta.
 ```bash
    $ draft init
    Creating /home/ralph/.draft 
    Creating /home/ralph/.draft/plugins 
    Creating /home/ralph/.draft/packs 
    Creating pack go...
    Creating pack python...
    Creating pack ruby...
    Creating pack javascript...
    Creating pack gradle...
    Creating pack java...
    Creating pack php...
    Creating pack csharp...
    $DRAFT_HOME has been configured at /home/ralph/.draft.

    In order to configure Draft, we need a bit more information...

    1. Enter your Docker registry URL (e.g. docker.io/myuser, quay.io/myuser, myregistry.azurecr.io): draftacsdemo.azurecr.io
    2. Enter your username: draftacsdemo
    3. Enter your password: 
    Draft has been installed into your Kubernetes Cluster.
    Happy Sailing!
```

Ya está listo para implementar una aplicación.


## <a name="build-and-deploy-an-application"></a>Compilación e implementación de una aplicación

En el repositorio de Draft, hay [seis aplicaciones sencillas de ejemplo](https://github.com/Azure/draft/tree/master/examples). Clone el repositorio y use el [ejemplo de Java](https://github.com/Azure/draft/tree/master/examples/java). Vaya al directorio examples/java y escriba `draft create` para compilar la aplicación. Debe tener un aspecto similar al ejemplo siguiente.
```bash
$ draft create
--> Draft detected the primary language as Java with 91.228814% certainty.
--> Ready to sail
```

El resultado incluye un archivo Dockerfile y un gráfico de Helm. Para la compilación e implementación, solo tiene que escribir `draft up`. El resultado es exhaustivo pero debe ser similar al ejemplo siguiente.
```bash
$ draft up
Draft Up Started: 'handy-labradoodle'
handy-labradoodle: Building Docker Image: SUCCESS ⚓  (35.0232s)
handy-labradoodle: Pushing Docker Image: SUCCESS ⚓  (17.0062s)
handy-labradoodle: Releasing Application: SUCCESS ⚓  (3.8903s)
handy-labradoodle: Build ID: 01BT0ZJ87NWCD7BBPK4Y3BTTPB
```

## <a name="securely-view-your-application"></a>Ver la aplicación de forma segura

El contenedor se está ejecutando en ACS. Para verla, use el comando `draft connect`, que crea una conexión segura a la dirección IP del clúster con un puerto específico para la aplicación, de forma que pueda verla de forma local. Si se realiza correctamente, busque la dirección URL para conectarse a la aplicación en la primera línea después del indicador **SUCCESS**.

> [!NOTE]
> Si recibe un mensaje que indica que no había ningún pod preparado, espere un momento e inténtelo de nuevo. También puede usar `kubectl get pods -w` para ver cuándo están preparados los pods y volver a intentarlo.

```bash
draft connect
Connecting to your app...SUCCESS...Connect to your app on localhost:46143
Starting log streaming...
SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
SLF4J: Defaulting to no-operation (NOP) logger implementation
SLF4J: See http://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
== Spark has ignited ...
>> Listening on 0.0.0.0:4567
```

En el ejemplo anterior, podría escribir `curl -s http://localhost:46143` para recibir la respuesta, `Hello World, I'm Java!`. Al pulsar CTRL+ o CMD+C (en función de su entorno de sistema operativo), se desactiva el túnel seguro y puede continuar con la iteración.

## <a name="sharing-your-application-by-configuring-a-deployment-domain-with-azure-dns"></a>Compartir la aplicación configurando un dominio de implementación con el DNS de Azure

Ya ha efectuado el bucle de iteración de desarrollador que crea Draft en los pasos anteriores. Pero puede compartir su aplicación a través de Internet de las siguientes maneras:
1. Instalando una entrada en el clúster de ACS (para proporcionar una dirección IP pública en la que se va a mostrar la aplicación).
2. Delegando el dominio personalizado en el DNS de Azure y asignando el dominio a la dirección IP que ACS asigna al controlador de entrada.

### <a name="use-helm-to-install-the-ingress-controller"></a>Usar Helm para instalar el controlador de entrada
Use **Helm** para buscar e instalar `stable/traefik`, un controlador de entrada, para permitir las solicitudes entrantes de las compilaciones.
```bash
$ helm search traefik
NAME            VERSION DESCRIPTION
stable/traefik  1.3.0   A Traefik based Kubernetes ingress controller w...

$ helm install stable/traefik --name ingress
```
Ahora establezca una inspección en el controlador `ingress` para capturar el valor de IP externo cuando se implemente. Esta dirección IP será la [asignada a su dominio de implementación](#wire-up-deployment-domain) en la sección siguiente.

```bash
$ kubectl get svc -w
NAME                          CLUSTER-IP     EXTERNAL-IP     PORT(S)                      AGE
ingress-traefik               10.0.248.104   13.64.108.240   80:31046/TCP,443:32556/TCP   1h
kubernetes                    10.0.0.1       <none>          443/TCP                      7h
```

En este caso, la dirección IP externa para el dominio de la implementación es `13.64.108.240`. Ya puede asignar su dominio a esa dirección IP.

### <a name="map-the-ingress-ip-to-a-custom-subdomain"></a>Asignar la dirección IP de entrada a un subdominio personalizado

Draft crea una versión para cada gráfico de Helm que crea: cada aplicación en la que esté trabajando. Cada una obtiene un nombre generado que **draft** usa como _subdominio_ además del _dominio de implementación_ de raíz que usted controla. (En este ejemplo, utilizamos `squillace.io` como dominio de implementación.) Para habilitar este comportamiento de subdominio, debe crear un registro D para `'*.draft'` en las entradas de DNS para el dominio de implementación, de modo que cada subdominio generado se enrute al controlador de entrada del clúster de Kubernetes. 

Su propio proveedor de dominios tiene su propia forma de asignar servidores DNS; para [delegar los servidores de nombres de dominio en Azure DNS](../../dns/dns-delegate-domain-azure-dns.md), siga estos pasos:

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
3. Agregue los servidores DNS que se le proporcionen al proveedor de dominios para su dominio de implementación, lo que le permite usar Azure DNS para redirigir el dominio tal como lo desee. La manera de hacerlo varía según el dominio proporcionado; [Delegación de un dominio en DNS de Azure](../../dns/dns-delegate-domain-azure-dns.md) contiene algunos de los detalles que debe conocer. 
4. Una vez delegado el dominio en el DNS de Azure, cree una entrada de conjunto de registros A para la asignación del dominio de implementación a la dirección IP `ingress` del paso 2 de la sección anterior.
  ```azurecli
  az network dns record-set a add-record --ipv4-address 13.64.108.240 --record-set-name '*.draft' -g squillace.io -z squillace.io
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
    "name": "*.draft",
    "resourceGroup": "squillace.io",
    "ttl": 3600,
    "type": "Microsoft.Network/dnszones/A"
  }
  ```
5. Vuelva a instalar **Draft**.

   1. Quite **draftd** del clúster escribiendo `helm delete --purge draft`. 
   2. Vuelva a instalar **Draft** con el mismo comando `draft-init`, pero con la opción `--ingress-enabled`:
    ```bash
    draft init --ingress-enabled
    ```
   Responda a las preguntas como hizo la primera vez. Esta vez habrá una pregunta más para responder mediante la ruta de acceso completa de dominio que configuró con el DNS de Azure.

6. Escriba el dominio de nivel superior para el tráfico de entrada (por ejemplo, draft.example.com): draft.squillace.io
7. Cuando llame a `draft up` esta vez podrá ver la aplicación (o `curl`) en la dirección URL del formulario `<appname>.draft.<domain>.<top-level-domain>`. En el caso de este ejemplo, `http://handy-labradoodle.draft.squillace.io`. 
```bash
curl -s http://handy-labradoodle.draft.squillace.io
Hello World, I'm Java!
```


## <a name="next-steps"></a>Pasos siguientes

Ahora que tiene un clúster de ACS Kubernetes, puede investigar mediante [Azure Container Registry](../../container-registry/container-registry-intro.md) para crear más implementaciones de este escenario y también otras distintas. Por ejemplo, puede crear un conjunto de registros DNS de dominios draft._basedomain.toplevel_ que controle aspectos de un subdominio más profundo para implementaciones de ACS específicas.






