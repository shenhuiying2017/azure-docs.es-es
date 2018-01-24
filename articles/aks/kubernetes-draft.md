---
title: Uso de Draft con AKS y Azure Container Registry
description: Uso de Draft con AKS y Azure Container Registry
services: container-service
author: neilpeterson
manager: timlt
ms.service: container-service
ms.topic: article
ms.date: 10/24/2017
ms.author: nepeters
ms.custom: mvc
ms.openlocfilehash: a77e214c1138ce936b2ec6c521950704e5beb3ff
ms.sourcegitcommit: 821b6306aab244d2feacbd722f60d99881e9d2a4
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/16/2017
---
# <a name="use-draft-with-azure-container-service-aks"></a>Uso de Draft con Azure Container Service (AKS)

Draft es una herramienta de código abierto que ayuda a empaquetar y ejecutar código en un clúster de Kubernetes. La herramienta Draft está destinada al ciclo de iteración de desarrollo: mientras se desarrolla el código, pero antes de la confirmación en el control de versiones. Con Draft, puede reimplementar rápidamente una aplicación en Kubernetes cuando se producen cambios en el código. Para obtener más información sobre Draft, consulte la [documentación de Draft en Github][draft-documentation].

En este documento se explica el uso de Draft con un clúster de Kubernetes en AKS.

## <a name="prerequisites"></a>requisitos previos

En los pasos que se detallan en este documento se asume que se ha creado un clúster de AKS y que ha establecido una conexión kubectl con dicho clúster. Si necesita estos elementos, vea la [Guía de inicio rápido de AKS][aks-quickstart].

También necesita un Docker Registry privado en Azure Container Registry (ACR). Para obtener instrucciones sobre la implementación de una instancia de ACR, consulte la [Guía de inicio rápido de Azure Container Registry][acr-quickstart].

## <a name="install-helm"></a>Instalación de Helm

La CLI de Helm es un cliente que se ejecuta en su sistema de desarrollo, y que permite iniciar, detener y administrar aplicaciones con gráficos de Helm.

Para instalar la CLI de Helm en un equipo Mac, use `brew`. Para conocer otras opciones de instalación, vea [Installing Helm][install-helm] (Instalación de Helm).

```console
brew install kubernetes-helm
```

Salida:

```
==> Downloading https://homebrew.bintray.com/bottles/kubernetes-helm-2.6.2.sierra.bottle.1.tar.gz
######################################################################## 100.0%
==> Pouring kubernetes-helm-2.6.2.sierra.bottle.1.tar.gz
==> Caveats
Bash completion has been installed to:
  /usr/local/etc/bash_completion.d
==> Summary
🍺  /usr/local/Cellar/kubernetes-helm/2.6.2: 50 files, 132.4MB
```

## <a name="install-draft"></a>Instalación de Draft

La CLI de Draft es un cliente que se ejecuta en su sistema de desarrollo, y que permite implementar código rápidamente en un clúster de Kubernetes.

Para instalar la CLI de Draft en un equipo Mac, use `brew`. Para conocer otras opciones de instalación, consulte la [Draft Install guide][install-draft] (Guía de instalación de Draft).

```console
brew install draft
```

Salida:

```
==> Installing draft from azure/draft
==> Downloading https://azuredraft.blob.core.windows.net/draft/draft-v0.7.0-darwin-amd64.tar.gz
Already downloaded: /Users/neilpeterson/Library/Caches/Homebrew/draft-0.7.0.tar.gz
==> /usr/local/Cellar/draft/0.7.0/bin/draft init --client-only
🍺  /usr/local/Cellar/draft/0.7.0: 6 files, 61.2MB, built in 1 second
```

## <a name="configure-draft"></a>Configuración de Draft

Al configurar Draft, es necesario especificar un registro de contenedor. En este ejemplo, se utiliza Azure Container Registry.

Ejecute el siguiente comando para obtener el nombre y el nombre del servidor de inicio de sesión de la instancia de ACR. Actualice el comando con el nombre del grupo de recursos que contiene la instancia de ACR.

```console
az acr list --resource-group <resource group> --query "[].{Name:name,LoginServer:loginServer}" --output table
```

La contraseña de la instancia de ACR también es necesaria.

Ejecute el comando siguiente para la devolución de la contraseña de ACR. Actualice el comando con el nombre de la instancia de ACR.

```console
az acr credential show --name <acr name> --query "passwords[0].value" --output table
```

Inicialice Draft con el comando `draft init`.

```console
draft init
```

Durante este proceso, se le pedirán las credenciales del registro de contenedor. Cuando se usa una instancia de Azure Container Registry, la dirección URL del registro es el nombre del servidor de inicio de sesión ACR, el nombre de usuario es el nombre de la instancia de ACR y la contraseña es la contraseña de ACR.

```console
1. Enter your Docker registry URL (e.g. docker.io/myuser, quay.io/myuser, myregistry.azurecr.io): <ACR Login Server>
2. Enter your username: <ACR Name>
3. Enter your password: <ACR Password>
```

Al finalizar, Draft se configura en el clúster de Kubernetes y está listo para usarse.

```
Draft has been installed into your Kubernetes Cluster.
Happy Sailing!
```

## <a name="run-an-application"></a>Ejecución de una aplicación

El repositorio de Draft incluye varias aplicaciones de ejemplo que pueden usarse para la demostración de Draft. Cree una copia clonada del repositorio.

```console
git clone https://github.com/Azure/draft
```

Cambie al directorio de ejemplos de Java.

```console
cd draft/examples/java/
```

Use el comando `draft create` para iniciar el proceso. Este comando crea los artefactos que se usan para ejecutar la aplicación en un clúster de Kubernetes. Estos elementos incluyen un Dockerfile, un gráfico de Helm y un archivo `draft.toml`, que es el archivo de configuración de Draft.

```console
draft create
```

Salida:

```
--> Draft detected the primary language as Java with 92.205567% certainty.
--> Ready to sail
```

Para ejecutar la aplicación en un clúster de Kubernetes, use el comando `draft up`. Este comando carga los archivos de configuración y código de la aplicación en el clúster de Kubernetes. A continuación, ejecuta el Dockerfile para crear una imagen de contenedor, inserta la imagen en el registro de contenedor y, finalmente, ejecuta el gráfico de Helm para iniciar la aplicación.

```console
draft up
```

Salida:

```
Draft Up Started: 'open-jaguar'
open-jaguar: Building Docker Image: SUCCESS ⚓  (28.0342s)
open-jaguar: Pushing Docker Image: SUCCESS ⚓  (7.0647s)
open-jaguar: Releasing Application: SUCCESS ⚓  (4.5056s)
open-jaguar: Build ID: 01BW3VVNZYQ5NQ8V1QSDGNVD0S
```

## <a name="test-the-application"></a>Prueba de la aplicación

Para probar la aplicación, use el comando `draft connect`. Este comando redirige una conexión mediante proxy al pod de Kubernetes, lo que permite una conexión local. Al terminar, se puede acceder a la aplicación en la dirección URL proporcionada.

En algunos casos, es posible que se tarde unos minutos en descargar la imagen del contenedor e iniciar la aplicación. Si recibe un error al acceder a la aplicación, vuelva a intentar la conexión.

```console
draft connect
```

Salida:

```
Connecting to your app...SUCCESS...Connect to your app on localhost:46143
Starting log streaming...
SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
SLF4J: Defaulting to no-operation (NOP) logger implementation
SLF4J: See http://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
== Spark has ignited ...
>> Listening on 0.0.0.0:4567
```

Cuando termine de probar la aplicación, use `Control+C` para detener la conexión de proxy.

## <a name="expose-application"></a>Exponer aplicación

Al probar una aplicación en Kubernetes, es posible que quiera que esté disponible en Internet. Para ello, use un servicio de Kubernetes con un tipo [LoadBalancer][kubernetes-service-loadbalancer] o un [controlador de entrada][kubernetes-ingress]. En este documento se describe el uso de un servicio de Kubernetes.


En primer lugar, el paquete de Draft debe actualizarse para especificar la necesidad de crear un servicio con un tipo `LoadBalancer`. Para ello, actualice el tipo de servicio en el archivo `values.yaml`.

```console
vi chart/java/values.yaml
```

Busque la propiedad `service.type` y actualice el valor de `ClusterIP` a `LoadBalancer`.

```yaml
replicaCount: 2
image:
  repository: openjdk
  tag: 8-jdk-alpine
  pullPolicy: IfNotPresent
service:
  name: java
  type: LoadBalancer
  externalPort: 80
  internalPort: 4567
resources:
  limits:
    cpu: 100m
    memory: 128Mi
  requests:
    cpu: 100m
    memory: 128Mi
  ```

Ejecute `draft up` para volver a ejecutar la aplicación.

```console
draft up
```

El servicio puede tardar unos minutos en devolver una dirección IP pública. Para supervisar el progreso, use el comando `kubectl get service` con un reloj.

```console
kubectl get service -w
```

En un primer momento, el parámetro *EXTERNAL-IP* del servicio aparece como `pending`.

```
deadly-squid-java   10.0.141.72   <pending>     80:32150/TCP   14m
```

Cuando el parámetro EXTERNAL-IP haya cambiado de `pending` a una `IP address`, use `Control+C` para detener el proceso de inspección de kubectl.

```
deadly-squid-java   10.0.141.72   52.175.224.118   80:32150/TCP   17m
```

Para ver la aplicación, navegue hasta la dirección IP externa.

```console
curl 52.175.224.118
```

Salida:

```
Hello World, I'm Java
```

## <a name="iterate-on-the-application"></a>Iteración en la aplicación

Ahora que Draft está configurado y que la aplicación se ejecuta en Kubernetes, está preparado para la iteración de código. Cada vez que quiera probar el código actualizado, ejecute el comando `draft up` para actualizar la aplicación en ejecución.

Para este ejemplo, actualice la Hola mundo de Java.

```console
vi src/main/java/helloworld/Hello.java
```

Actualice el texto Hola mundo.

```java
package helloworld;

import static spark.Spark.*;

public class Hello {
    public static void main(String[] args) {
        get("/", (req, res) -> "Hello World, I'm Java - Draft Rocks!");
    }
}
```

Ejecute el comando `draft up` para volver a implementar la aplicación.

```console
draft up
```

Salida

```
Draft Up Started: 'deadly-squid'
deadly-squid: Building Docker Image: SUCCESS ⚓  (18.0813s)
deadly-squid: Pushing Docker Image: SUCCESS ⚓  (7.9394s)
deadly-squid: Releasing Application: SUCCESS ⚓  (6.5005s)
deadly-squid: Build ID: 01BWK8C8X922F5C0HCQ8FT12RR
```

Por último, consulte la aplicación para ver las actualizaciones.

```console
curl 52.175.224.118
```

Salida:

```
Hello World, I'm Java - Draft Rocks!
```

## <a name="next-steps"></a>pasos siguientes

Para obtener más información sobre el uso de Draft, consulte la documentación de Draft en GitHub.

> [!div class="nextstepaction"]
> [Documentación de Draft][draft-documentation]

<!-- LINKS - external -->
[draft-documentation]: https://github.com/Azure/draft/tree/master/docs
[install-draft]: https://github.com/Azure/draft/blob/master/docs/install.md
[install-helm]: https://github.com/kubernetes/helm/blob/master/docs/install.md
[kubernetes-ingress]: https://kubernetes.io/docs/concepts/services-networking/ingress/
[kubernetes-service-loadbalancer]: https://kubernetes.io/docs/concepts/services-networking/service/#type-loadbalancer

<!-- LINKS - internal -->
[acr-quickstart]: ../container-registry/container-registry-get-started-azure-cli.md
[aks-quickstart]: ./kubernetes-walkthrough.md