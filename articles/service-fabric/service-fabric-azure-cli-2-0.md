---
title: "Introducción a Service Fabric y la CLI de Azure 2.0"
description: "El uso del módulo de comandos de Service Fabric en la CLI de Azure, versión 2.0, incluye la conexión a un clúster y la administración de aplicaciones"
services: service-fabric
author: samedder
manager: timlt
ms.service: service-fabric
ms.topic: get-started-article
ms.date: 06/21/2017
ms.author: edwardsa
ms.translationtype: Human Translation
ms.sourcegitcommit: 6efa2cca46c2d8e4c00150ff964f8af02397ef99
ms.openlocfilehash: c5cc6e54acf27456185eeb48858c4d981aa46b4b
ms.contentlocale: es-es
ms.lasthandoff: 07/01/2017

---
<a id="service-fabric-and-azure-cli-20" class="xliff"></a>

# Service Fabric y CLI de Azure 2.0

La nueva versión 2.0 de la CLI de Azure ahora incluye comandos para administrar clústeres de Service Fabric. Esta documentación incluye los pasos necesarios para empezar a trabajar con la CLI de Azure.

<a id="install-azure-cli-20" class="xliff"></a>

## Instalación de la CLI de Azure 2.0

Ahora, la CLI de Azure incluye comandos para interactuar con clústeres de Service Fabric y administrarlos. Para obtener la versión más reciente de la CLI de Azure, puede seguir el [proceso de instalación estándar](https://docs.microsoft.com/en-us/cli/azure/install-azure-cli).

Para más información, consulte la [documentación de la CLI de Azure 2.0](https://docs.microsoft.com/en-us/cli/azure/overview)

<a id="cli-syntax" class="xliff"></a>

## Sintaxis de la CLI

Todos los comandos de Azure Service Fabric tienen el prefijo `az sf` en la CLI de Azure. Para más información acerca de los comandos disponibles, puede ejecutar `az sf -h` para obtener información general. O bien, puede ejecutar `az sf <command> -h` si lo que desea es ayuda detallada acerca de un comando individual.

Los comandos de Azure Service Fabric en la CLI siguen un patrón de nomenclatura

```azurecli
az sf <object> <action>
```

En este caso, `<object>` es el destino de `<action>`.

<a id="selecting-a-cluster" class="xliff"></a>

## Selección de un clúster

Para poder realizar cualquier operación, es preciso seleccionar un clúster al que conectarse. Por ejemplo, vea el siguiente fragmento de código para conectarse a un clúster no seguro.

> [!WARNING]
> No utilice clústeres de Service Fabric que no sean seguros para entornos de producción

```azurecli
az sf cluster select --endpoint http://testcluster.com:19080
```

El punto de conexión del clúster debe ir precedido por `http` o `https`y debe incluir el puerto para la puerta de enlace HTTP. Este puerto y dirección coinciden con la dirección URL de Service Fabric Explorer.

Para proteger los clústeres con un certificado, se admiten archivos sin cifrar `pem` o `crt` y `key`.

```azurecli
az sf cluster select --endpoint https://testsecurecluster.com:19080 --pem ./client.pem
```

Para más información, consulte el [documento detallado acerca de cómo conectarse a clústeres seguros](service-fabric-connect-to-secure-cluster.md).

> [!NOTE]
> El comando select no realiza ninguna solicitud antes de devolver. Para comprobar que un clúster se ha especificado correctamente, ejecute un comando como `az sf cluster health`y compruebe que el comando no devuelve ningún error.

<a id="performing-basic-operations" class="xliff"></a>

## Realización de operaciones básicas

La información de la conexión del clúster se mantiene en las distintas sesiones de la CLI de Azure. Una vez que se selecciona un clúster de Service Fabric, se puede ejecutar cualquier comando de Service Fabric.

Por ejemplo, para obtener el estado del clúster de Service Fabric ejecute el siguiente comando

```azurecli
az sf cluster health
```

El comando genera la siguiente salida, suponiendo que la salida JSON se especifica en la configuración de la CLI de Azure

```json
{
  "aggregatedHealthState": "Ok",
  "applicationHealthStates": [
    {
      "aggregatedHealthState": "Ok",
      "name": "fabric:/System"
    }
  ],
  "healthEvents": [],
  "nodeHealthStates": [
    {
      "aggregatedHealthState": "Ok",
      "id": {
        "id": "66aa824a642124089ee474b398d06a57"
      },
      "name": "_Test_0"
    }
  ],
  "unhealthyEvaluations": []
}
```

<a id="tips-and-faq" class="xliff"></a>

## Sugerencias y preguntas más frecuentes

Esta información puede resultar útil cuando surgen problemas al utilizar los comandos de Service Fabric en la CLI de Azure

<a id="converting-a-certificate-from-pfx-to-pem" class="xliff"></a>

### Conversión de un certificado de PFX a PEM

La CLI de Azure admite los certificados de cliente, como los archivos PEM (extensión `.pem`). Si usa archivos PFX de Windows, estos certificados deben convertirse al formato PEM. Para convertir un archivo PFX en uno PEM, use el comando siguiente:

```bash
openssl pkcs12 -in certificate.pfx -out mycert.pem -nodes
```

Consulte la [documentación de OpenSSL](https://www.openssl.org/docs/man1.0.1/apps/pkcs12.html) para obtener más información.

<a id="connection-issues" class="xliff"></a>

### Problemas de conexión

Cuando se realizan operaciones, puede aparecer el siguiente error:

> `Failed to establish a new connection: [Errno 8] nodename nor servname provided, or not known`

En este caso, vuelva a comprobar que se puede acceder el punto de conexión del clúster especificado y que este realiza escuchas. Compruebe también que se puede acceder a la interfaz de usuario de Service Fabric Explorer en dicho host y puerto. Use `az sf cluster select` para actualizar el punto de conexión.

<a id="getting-detailed-logs" class="xliff"></a>

### Obtención de registros detallados

Al depurar un problema o informar acerca del mismo, resulta útil incluir registros detallados. La CLI de Azure incluye una marca `--debug` global que aumenta el nivel de detalle de los registros.

<a id="command-help-and-syntax" class="xliff"></a>

### Ayuda y sintaxis de los comandos

Los comandos de Service Fabric siguen la misma convención que la CLI de Azure. Especifique la marca `-h` para obtener ayuda acerca de un comando específico, o grupo de comandos. Por ejemplo:

```azurecli
az sf application -h
```

o

```azurecli
az sf application create -h
```

