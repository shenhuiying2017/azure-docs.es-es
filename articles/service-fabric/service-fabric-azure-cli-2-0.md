---
title: "Introducción a Service Fabric y la CLI de Azure 2.0"
description: "Aprenda a usar el módulo de comandos de Azure Service Fabric en la CLI de Azure, versión 2.0. Aprenda a conectarse a un clúster y a cómo administrar aplicaciones."
services: service-fabric
author: samedder
manager: timlt
ms.service: service-fabric
ms.topic: get-started-article
ms.date: 06/21/2017
ms.author: edwardsa
ms.translationtype: HT
ms.sourcegitcommit: bde1bc7e140f9eb7bb864c1c0a1387b9da5d4d22
ms.openlocfilehash: ee3302b984ca2f5509755dc17b0a5fd06ace0afe
ms.contentlocale: es-es
ms.lasthandoff: 07/21/2017

---
# <a name="azure-service-fabric-and-azure-cli-20"></a>Azure Service Fabric y CLI de Azure 2.0

La herramienta de línea de comandos de Azure (CLI de Azure) versión 2.0 incluye comandos para ayudarle a administrar los clústeres de Azure Service Fabric. Aprenda a empezar a usar la CLI de Azure y Service Fabric.

## <a name="install-azure-cli-20"></a>Instalación de la CLI de Azure 2.0

Puede usar los comandos de la CLI de Azure 2.0 para interactuar con clústeres de Service Fabric y administrarlos. Para obtener la última versión de la CLI de Azure, siga el [proceso de instalación estándar de la CLI de Azure 2.0](https://docs.microsoft.com/en-us/cli/azure/install-azure-cli).

Para más información, consulte la [introducción a la CLI de Azure 2.0](https://docs.microsoft.com/en-us/cli/azure/overview).

## <a name="azure-cli-syntax"></a>Sintaxis de la CLI de Azure

Todos los comandos de Service Fabric tienen el prefijo `az sf` en la CLI de Azure. Para obtener información general acerca de los comandos que puede usar, use `az sf -h`. Para obtener ayuda con un único comando, use `az sf <command> -h`.

Los comandos de Service Fabric en la CLI de Azure siguen este patrón de nomenclatura:

```azurecli
az sf <object> <action>
```

`<object>` es el destino de `<action>`.

## <a name="select-a-cluster"></a>Selección de un clúster

Para poder realizar cualquier operación, es preciso seleccionar un clúster al que conectarse. Por ejemplo, vea el siguiente ejemplo de código. El código permite conectarse a un clúster no seguro.

> [!WARNING]
> No utilice clústeres de Service Fabric que no sean seguros en entornos de producción.

```azurecli
az sf cluster select --endpoint http://testcluster.com:19080
```

El punto de conexión del clúster debe ir precedido por el prefijo `http` o `https`. Debe incluir el puerto para la puerta de enlace HTTP. El puerto y la dirección coinciden con la dirección URL de Service Fabric Explorer.

Para los clústeres que están protegidos con un certificado, puede usar archivos .pem o .crt y .key sin cifrar. Por ejemplo:

```azurecli
az sf cluster select --endpoint https://testsecurecluster.com:19080 --pem ./client.pem
```

Para más información, consulte [Conexión a un clúster seguro de Azure Service Fabric](service-fabric-connect-to-secure-cluster.md).

> [!NOTE]
> El comando `select` no actúa sobre ninguna solicitud antes de su devolución. Para comprobar que ha especificado correctamente un clúster, use un comando como `az sf cluster health`. Compruebe que el comando no devuelve ningún error.

## <a name="basic-operations"></a>Operaciones básicas

La información de la conexión del clúster se mantiene en varias sesiones de la CLI de Azure. Una vez que se selecciona un clúster de Service Fabric, se puede ejecutar cualquier comando de Service Fabric en él.

Por ejemplo, para obtener el estado del clúster de Service Fabric, use el siguiente comando:

```azurecli
az sf cluster health
```

El comando genera la siguiente salida (suponiendo que la salida JSON se especifica en la configuración de la CLI de Azure):

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

## <a name="tips-and-troubleshooting"></a>Sugerencias y solución de problemas

Puede que la siguiente información le resulte útil si tiene problemas al usar los comandos de Service Fabric en la CLI de Azure.

### <a name="convert-a-certificate-from-pfx-to-pem-format"></a>Conversión de un certificado de formato PFX a PEM

La CLI de Azure admite los certificados de cliente, como los archivos PEM (extensión .pem). Si utiliza los archivos PFX de Windows, debe convertir esos certificados al formato PEM. Para convertir un archivo PFX en uno PEM, use el comando siguiente:

```bash
openssl pkcs12 -in certificate.pfx -out mycert.pem -nodes
```

Para más información, consulte la documentación de [OpenSSL](https://www.openssl.org/docs/).

### <a name="connection-issues"></a>Problemas de conexión

Algunas operaciones pueden generar el mensaje siguiente:

`Failed to establish a new connection: [Errno 8] nodename nor servname provided, or not known`

Compruebe que el punto de conexión del clúster especificado está disponible y a la escucha. Compruebe también que la interfaz de usuario de Service Fabric Explorer está disponible en dicho host y puerto. Use `az sf cluster select` para actualizar el punto de conexión.

### <a name="detailed-logs"></a>Registros detallados

Los registros detallados a menudo son útiles al depurar o notificar problemas. La CLI de Azure incluye una marca `--debug` global que aumenta el nivel de detalle de los archivos de registro.

### <a name="command-help-and-syntax"></a>Ayuda y sintaxis de los comandos

Los comandos de Service Fabric siguen las mismas convenciones que la CLI de Azure. Para obtener ayuda con un comando específico o un grupo de comandos, use la marca `-h`:

```azurecli
az sf application -h
```

Este es otro ejemplo:

```azurecli
az sf application create -h
```

