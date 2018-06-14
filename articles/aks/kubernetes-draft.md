---
title: Uso de Draft con AKS y Azure Container Registry
description: Uso de Draft con AKS y Azure Container Registry
services: container-service
author: neilpeterson
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 03/29/2018
ms.author: nepeters
ms.custom: mvc
ms.openlocfilehash: a5dfecefb6ce1d74e02c64371a864a6d3b07a2e1
ms.sourcegitcommit: 96089449d17548263691d40e4f1e8f9557561197
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/17/2018
ms.locfileid: "34257322"
---
# <a name="use-draft-with-azure-kubernetes-service-aks"></a>Uso de Draft con Azure Kubernetes Service (AKS)

Draft es una herramienta de código abierto que ayuda a almacenar e implementar esos contenedores en un clúster de Kubernetes, permitiéndole concentrarse en el ciclo de desarrollo: el "bucle interno" del desarrollo concentrado. Draft trabaja durante el desarrollo del código y antes de la confirmación en el control de versiones. Con Draft, puede reimplementar rápidamente una aplicación en Kubernetes cuando se producen cambios en el código. Para obtener más información sobre Draft, consulte la [documentación de Draft en Github][draft-documentation].

En este documento se explica el uso de Draft con un clúster de Kubernetes en AKS.

## <a name="prerequisites"></a>requisitos previos

En los pasos que se detallan en este documento se asume que se ha creado un clúster de AKS y que ha establecido una conexión kubectl con dicho clúster. Si necesita estos elementos, vea la [Guía de inicio rápido de AKS][aks-quickstart].

También necesita un Docker Registry privado en Azure Container Registry (ACR). Para obtener instrucciones sobre la implementación de una instancia de ACR, consulte la [Guía de inicio rápido de Azure Container Registry][acr-quickstart].

Helm también debe instalarse en el clúster AKS. Para más información acerca de cómo instalar Helm, consulte [Uso de Helm con Azure Kubernetes Service (AKS)][aks-helm].

Por último, debe instalar [Docker](https://www.docker.com).

## <a name="install-draft"></a>Instalación de Draft

La CLI de Draft es un cliente que se ejecuta en su sistema de desarrollo, y que permite implementar código rápidamente en un clúster de Kubernetes.

> [!NOTE]
> Si tiene instalada una versión de Draft anterior a la 0.12, primero debe eliminar Draft del clúster mediante `helm delete --purge draft` y, a continuación, quitar la configuración local ejecutando `rm -rf ~/.draft`. Si se encuentra en Mac OS, ejecute `brew upgrade draft`.

Para instalar la CLI de Draft en un equipo Mac, use `brew`. Para conocer otras opciones de instalación, consulte la [Draft Install guide][draft-documentation] (Guía de instalación de Draft).

```console
brew tap azure/draft
brew install draft
```

Ahora, inicialice Draft con el comando `draft init`.

```console
draft init
```

## <a name="configure-draft"></a>Configuración de Draft

Draft crea las imágenes de contenedor de forma local para, posteriormente, implementarlas desde el registro local (en el caso de Minikube), o bien deberá especificar el registro de la imagen que desea usar. Este ejemplo usa Azure Container Registry (ACR), por lo que debe establecer una relación de confianza entre el clúster de AKS y el registro de ACR y configurar Draft para que inserte el contenedor en ACR.

### <a name="create-trust-between-aks-cluster-and-acr"></a>Creación de relación de confianza entre el clúster de AKS y ACR

Para establecer la confianza entre un clúster de AKS y un registro de ACR, modifique la entidad de servicio de Azure Active Directory que se usa con AKS agregando el rol Colaborador a esta con el ámbito del repositorio de ACR. Para ello, ejecute los siguientes comandos, reemplazando _&lt;aks-rg-name&gt;_ y _&lt;aks-cluster-name&gt;_ por el grupo de recursos y el nombre del clúster de AKS, y _&lt;acr-rg-nam&gt;_ y _&lt;acr-repo-name&gt;_ por el grupo de recursos y el nombre de repositorio del repositorio de ACR con el que desea crear una relación de confianza.

```console
export AKS_SP_ID=$(az aks show -g <aks-rg-name> -n <aks-cluster-name> --query "servicePrincipalProfile.clientId" -o tsv)
export ACR_RESOURCE_ID=$(az acr show -g <acr-rg-name> -n <acr-repo-name> --query "id" -o tsv)
az role assignment create --assignee $AKS_SP_ID --scope $ACR_RESOURCE_ID --role contributor
```

(Estos pasos y otros mecanismos de autenticación para acceder a ACR se describen en [autenticación con ACR](../container-registry/container-registry-auth-aks.md)).

### <a name="configure-draft-to-push-to-and-deploy-from-acr"></a>Configuración de Draft para que inserte en ACR e implemente desde este

Ahora que hay una relación de confianza entre AKS y ACR, los pasos siguientes habilitan el uso de ACR desde el clúster de AKS.
1. Establezca el valor de `registry` en la configuración de Draft ejecutando `draft config set registry <registry name>.azurecr.io`, en donde _&lt;registry name&lt;_ es el nombre del registro de ACR.
2. Inicie sesión en el registro de ACR ejecutando `az acr login -n <registry name>`.

Dado que ya ha iniciado sesión localmente en ACR y ha creado una relación de confianza con AKS y ACR, no se necesitan contraseñas ni secretos para insertar o extraer desde ACR en AKS. La autenticación se produce en el nivel de Azure Resource Manager mediante Azure Active Directory.

## <a name="run-an-application"></a>Ejecución de una aplicación

El repositorio de Draft incluye varias aplicaciones de ejemplo que pueden usarse para la demostración de Draft. Cree una copia clonada del repositorio.

```console
git clone https://github.com/Azure/draft
```

Cambie al directorio de ejemplos de Java.

```console
cd draft/examples/example-java/
```

Use el comando `draft create` para iniciar el proceso. Este comando crea los artefactos que se usan para ejecutar la aplicación en un clúster de Kubernetes. Estos elementos incluyen un Dockerfile, un gráfico de Helm y un archivo `draft.toml`, que es el archivo de configuración de Draft.

```console
draft create
```

Salida:

```console
--> Draft detected the primary language as Java with 92.205567% certainty.
--> Ready to sail
```

Para ejecutar la aplicación en un clúster de Kubernetes, use el comando `draft up`. Este comando compila el Dockerfile para crear una imagen de contenedor, inserta la imagen en ACR y, finalmente, instala el gráfico de Helm para iniciar la aplicación en AKS.

La primera vez que se ejecuta, los procesos de inserción y extracción de la imagen del contenedor pueden tardar algún tiempo en completarse. Una vez que se almacenan en caché los niveles de base, se reduce drásticamente el tiempo necesario.

```console
draft up
```

Salida:

```console
Draft Up Started: 'example-java'
example-java: Building Docker Image: SUCCESS ⚓  (1.0003s)
example-java: Pushing Docker Image: SUCCESS ⚓  (3.0007s)
example-java: Releasing Application: SUCCESS ⚓  (0.9322s)
example-java: Build ID: 01C9NPDYQQH2CZENDMZW7ESJAM
Inspect the logs with `draft logs 01C9NPDYQQH2CZENDMZW7ESJAM`
```

## <a name="test-the-application"></a>Prueba de la aplicación

Para probar la aplicación, use el comando `draft connect`. Este comando redirige una conexión mediante proxy al pod de Kubernetes, lo que permite una conexión local. Al terminar, se puede acceder a la aplicación en la dirección URL proporcionada.

En algunos casos, es posible que se tarde unos minutos en descargar la imagen del contenedor e iniciar la aplicación. Si recibe un error al acceder a la aplicación, vuelva a intentar la conexión.

```console
draft connect
```

Salida:

```console
Connecting to your app...SUCCESS...Connect to your app on localhost:46143
Starting log streaming...
SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
SLF4J: Defaulting to no-operation (NOP) logger implementation
SLF4J: See http://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
== Spark has ignited ...
>> Listening on 0.0.0.0:4567
```

Pruebe la aplicación ahora. Para ello, vaya a http://localhost:46143 (en el ejemplo anterior; el puerto puede ser distinto). Cuando termine de probar la aplicación, use `Control+C` para detener la conexión de proxy.

> [!NOTE]
> También puede usar el comando `draft up --auto-connect` para compilar e implementar la aplicación y conectarse inmediatamente al primer contenedor en ejecución para hacer que el ciclo de iteración sea aún más rápido.

## <a name="expose-application"></a>Exponer aplicación

Al probar una aplicación en Kubernetes, es posible que quiera que esté disponible en Internet. Para ello, use un servicio de Kubernetes con un tipo [LoadBalancer][kubernetes-service-loadbalancer] o un [controlador de entrada][kubernetes-ingress]. En este documento se describe el uso de un servicio de Kubernetes.


En primer lugar, el paquete de Draft debe actualizarse para especificar la necesidad de crear un servicio con un tipo `LoadBalancer`. Para ello, actualice el tipo de servicio en el archivo `values.yaml`.

```console
vi charts/java/values.yaml
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
example-java-java   10.0.141.72   <pending>     80:32150/TCP   14m
```

Cuando el parámetro EXTERNAL-IP haya cambiado de `pending` a una `IP address`, use `Control+C` para detener el proceso de inspección de kubectl.

```
example-java-java   10.0.141.72   52.175.224.118   80:32150/TCP   17m
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
        get("/", (req, res) -> "Hello World, I'm Java in AKS!");
    }
}
```

Ejecute el comando `draft up --auto-connect` para volver a implementar la aplicación tan pronto como haya un pod listo para responder.

```console
draft up --auto-connect
```

Salida

```
Draft Up Started: 'example-java'
example-java: Building Docker Image: SUCCESS ⚓  (1.0003s)
example-java: Pushing Docker Image: SUCCESS ⚓  (4.0010s)
example-java: Releasing Application: SUCCESS ⚓  (1.1336s)
example-java: Build ID: 01C9NPMJP6YM985GHKDR2J64KC
Inspect the logs with `draft logs 01C9NPMJP6YM985GHKDR2J64KC`
Connect to java:4567 on localhost:39249
Your connection is still active.
Connect to java:4567 on localhost:39249
[java]: SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
[java]: SLF4J: Defaulting to no-operation (NOP) logger implementation
[java]: SLF4J: See http://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
[java]: == Spark has ignited ...
[java]: >> Listening on 0.0.0.0:4567

```

Por último, consulte la aplicación para ver las actualizaciones.

```console
curl 52.175.224.118
```

Salida:

```
Hello World, I'm Java in AKS!
```

## <a name="next-steps"></a>Pasos siguientes

Para obtener más información sobre el uso de Draft, consulte la documentación de Draft en GitHub.

> [!div class="nextstepaction"]
> [Documentación de Draft][draft-documentation]

<!-- LINKS - external -->
[draft-documentation]: https://github.com/Azure/draft/tree/master/docs
[kubernetes-ingress]: ./ingress.md
[kubernetes-service-loadbalancer]: https://kubernetes.io/docs/concepts/services-networking/service/#type-loadbalancer

<!-- LINKS - internal -->
[acr-quickstart]: ../container-registry/container-registry-get-started-azure-cli.md
[aks-helm]: ./kubernetes-helm.md
[aks-quickstart]: ./kubernetes-walkthrough.md
