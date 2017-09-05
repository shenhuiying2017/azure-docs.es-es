---
title: "Introducción a la CLI de Azure Service Fabric (sfctl)"
description: "Aprenda a utilizar la CLI de Azure Service Fabric. Aprenda a conectarse a un clúster y a cómo administrar aplicaciones."
services: service-fabric
author: samedder
manager: timlt
ms.service: service-fabric
ms.topic: get-started-article
ms.date: 08/22/2017
ms.author: edwardsa
ms.translationtype: HT
ms.sourcegitcommit: a0b98d400db31e9bb85611b3029616cc7b2b4b3f
ms.openlocfilehash: 2faca2887f25b45d833dea7b2259277466290670
ms.contentlocale: es-es
ms.lasthandoff: 08/29/2017

---
# <a name="azure-service-fabric-command-line"></a>Línea de comandos de Azure Service Fabric

La CLI de Azure Service Fabric (sfctl) es una utilidad de línea de comandos para interactuar y administrar entidades de Azure Service Fabric. Sfctl puede utilizarse con clústeres de Windows o Linux. Sfctl se ejecuta en cualquier plataforma que sea compatible con Python.

## <a name="prerequisites"></a>Requisitos previos

Antes de la instalación, asegúrese de que el entorno tiene python y pip instalado. Para más información, eche un vistazo a la [documentación de inicio rápido de pip](https://pip.pypa.io/en/latest/quickstart/) y a la [documentación oficial de instalación de python](https://wiki.python.org/moin/BeginnersGuide/Download).

Aunque se admite python 2.7 y 3.6, se recomienda utilizar python 3.6. En la siguiente sección se indica cómo instalar todos los requisitos previos y la CLI.

## <a name="install-pip-python-and-sfctl"></a>Instalar pip, python y sfctl

Aunque hay muchas maneras de instalar pip y python en la plataforma, aquí se describen algunos pasos para configurar la aplicación rápidamente con python 3.6 y pip para los sistemas operativos principales:

### <a name="windows"></a>Windows

Para Windows 10, Server 2016 y Server 2012 R2, puede usar las instrucciones de instalación oficiales estándar. El programa de instalación de python también instala pip de forma predeterminada.

- Vaya a la [página de descargas de python](https://www.python.org/downloads/) oficial y descargue la versión más reciente de python 3.6.
- Inicie el programa de instalación.
- Seleccione la opción situada en la parte inferior del aviso `Add Python 3.6 to PATH`.
- Seleccionar `Install Now`
- Termine la instalación.

Ahora ya puede abrir una nueva ventana de comandos y obtener la versión de python y pip:

```bat
python --version
pip --version
```

A continuación, ejecute lo siguiente para instalar la CLI de Service Fabric

```
pip install sfctl
sfctl -h
```

### <a name="ubuntu"></a>Ubuntu

Para Ubuntu 16.04 Desktop, puede instalar python 3.6 mediante un PPA de terceros:

En el terminal, ejecute los siguientes comandos:

```bash
sudo add-apt-repository ppa:jonathonf/python-3.6
sudo apt-get update
sudo apt-get install python3.6
sudo apt-get install python3-pip
```

A continuación, para instalar sfctl solo para la instalación de python 3.6 ejecute el siguiente comando:

```bash
python3.6 -m pip install sfctl
sfctl -h
```

Estos pasos no afectan a las versiones de python 3.5 y 2.7 instaladas en el sistema. No intente modificar estas instalaciones, a menos que esté familiarizado con Ubuntu.

### <a name="macos"></a>MacOS

Para MacOS, se recomienda utilizar el [administrador de paquetes de HomeBrew](https://brew.sh). Instale HomeBrew si no está ya instalado, ejecutando el comando siguiente:

```bash
/usr/bin/ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"
```

A continuación, desde el terminal, instale python 3.6, pip y sfctl

```bash
brew install python3
pip3 install sfctl
sfctl -h
```

Estos pasos no modifican la instalación del sistema de python 2.7.

## <a name="cli-syntax"></a>Sintaxis de la CLI

Los comandos siempre tienen el prefijo `sfctl`. Para obtener información general de todos los comandos que puede usar, use `sfctl -h`. Para obtener ayuda con un único comando, use `sfctl <command> -h`.

Los comandos siguen una estructura repetible, con el destino del comando precediendo al verbo o la acción:

```azurecli
sfctl <object> <action>
```

En este ejemplo, `<object>` es el destino de `<action>`.

## <a name="select-a-cluster"></a>Selección de un clúster

Para poder realizar cualquier operación, es preciso seleccionar un clúster al que conectarse. Por ejemplo, ejecute lo siguiente para seleccionar y conectarse al clúster con el nombre `testcluster.com`.

> [!WARNING]
> No utilice clústeres de Service Fabric que no sean seguros en entornos de producción.

```azurecli
sfctl cluster select --endpoint http://testcluster.com:19080
```

El punto de conexión del clúster debe ir precedido por el prefijo `http` o `https`. Debe incluir el puerto para la puerta de enlace HTTP. El puerto y la dirección coinciden con la dirección URL de Service Fabric Explorer.

Para clústeres que están protegidos con un certificado, puede especificar un certificado PEM codificado. El certificado se puede especificar como un único archivo o como un par de certificado y clave.

```azurecli
sfctl cluster select --endpoint https://testsecurecluster.com:19080 --pem ./client.pem
```

Para más información, consulte [Conexión a un clúster seguro de Azure Service Fabric](service-fabric-connect-to-secure-cluster.md).

## <a name="basic-operations"></a>Operaciones básicas

La información de la conexión del clúster se mantiene en varias sesiones de sfctl. Una vez que se selecciona un clúster de Service Fabric, se puede ejecutar cualquier comando de Service Fabric en él.

Por ejemplo, para obtener el estado del clúster de Service Fabric, use el siguiente comando:

```azurecli
sfctl cluster health
```

El comando devuelve la siguiente salida:

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

Sugerencias y consejos para resolver problemas comunes.

### <a name="convert-a-certificate-from-pfx-to-pem-format"></a>Conversión de un certificado de formato PFX a PEM

La CLI de Service Fabric admite certificados de cliente como archivos PEM (extensión .pem). Si utiliza los archivos PFX de Windows, debe convertir esos certificados al formato PEM. Para convertir un archivo PFX en uno PEM, use el comando siguiente:

```bash
openssl pkcs12 -in certificate.pfx -out mycert.pem -nodes
```

Para más información, consulte la documentación de [OpenSSL](https://www.openssl.org/docs/).

### <a name="connection-issues"></a>Problemas de conexión

Algunas operaciones pueden generar el mensaje siguiente:

`Failed to establish a new connection: [Errno 8] nodename nor servname provided, or not known`

Compruebe que el punto de conexión del clúster especificado está disponible y a la escucha. Compruebe también que la interfaz de usuario de Service Fabric Explorer está disponible en dicho host y puerto. Use `sfctl cluster select` para actualizar el punto de conexión.

### <a name="detailed-logs"></a>Registros detallados

Los registros detallados a menudo son útiles al depurar o notificar problemas. Hay una marca `--debug` global que aumenta el nivel de detalle de los archivos de registro.

### <a name="command-help-and-syntax"></a>Ayuda y sintaxis de los comandos

Para obtener ayuda con un comando específico o un grupo de comandos, use la marca `-h`:

```azurecli
sfctl application -h
```

Otro ejemplo:

```azurecli
sfctl application create -h
```

## <a name="next-steps"></a>Pasos siguientes

* [Implementación de una aplicación con la CLI de Azure Service Fabric](service-fabric-application-lifecycle-sfctl.md)
* [Introducción a Service Fabric con Linux](service-fabric-get-started-linux.md)

