---
title: "Introducción a la CLI de Azure Service Fabric"
description: "Aprenda a utilizar la CLI de Azure Service Fabric. Aprenda a conectarse a un clúster y administrar aplicaciones."
services: service-fabric
author: samedder
manager: timlt
ms.service: service-fabric
ms.topic: get-started-article
ms.date: 10/20/2017
ms.author: edwardsa
ms.openlocfilehash: d24c7618c5d53cfe2871d596bfc0fe2cadd5940a
ms.sourcegitcommit: cf4c0ad6a628dfcbf5b841896ab3c78b97d4eafd
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/21/2017
---
# <a name="azure-service-fabric-cli"></a>CLI de Azure Service Fabric

La interfaz de línea de comandos (CLI) de Azure Service Fabric es una utilidad de línea de comandos para interactuar y administrar entidades de Service Fabric. La CLI de Service Fabric puede utilizarse con clústeres Windows o Linux. La CLI de Service Fabric se ejecuta en cualquier plataforma que sea compatible con Python.

[!INCLUDE [links to azure cli and service fabric cli](../../includes/service-fabric-sfctl.md)]

## <a name="prerequisites"></a>Requisitos previos

Antes de la instalación, asegúrese de que el entorno tiene Python y pip instalados. Para más información, consulte la [documentación de inicio rápido de pip](https://pip.pypa.io/en/latest/quickstart/) y la [documentación oficial de instalación de Python](https://wiki.python.org/moin/BeginnersGuide/Download).

La CLI admite las versiones 2.7, 3.5 y 3.6 de Python. Se recomienda usar Python 3.6, ya que el soporte técnico de Python 2.7 finalizará pronto.

### <a name="service-fabric-target-runtime"></a>Runtime de destino de Service Fabric

La CLI de Service Fabric debería ser compatible con la versión más reciente del runtime del SDK de Service Fabric. Use la siguiente tabla para determinar qué versión de la CLI debe instalar:

| Versión de la CLI   | versión compatible del runtime |
|---------------|---------------------------|
| Más reciente (~ = 3)  | Más reciente (~=6.0)            |
| 1.1.0         | 5.6, 5.7                  |

También puede especificar la versión de destino de la CLI que se va a instalar. Para ello debe usar `==<version>` delante del comando `pip install`. Por ejemplo, en el caso de la versión 1.1.0 la sintaxis sería:

```
pip install -I sfctl==1.1.0
```

Reemplace el siguiente comando `pip install` por el comando mencionado anteriormente cuando sea necesario.

Para más información sobre las versiones de la CLI de Service Fabric, consulte la [documentación de GitHub](https://github.com/Azure/service-fabric-cli/releases).

## <a name="install-pip-python-and-the-service-fabric-cli"></a>Instalación de pip, Python y la CLI de Service Fabric

Hay muchas maneras de instalar pip y Python en la plataforma. Estos son algunos pasos para configurar rápidamente los principales sistemas operativos con Python 3 y pip.

### <a name="windows"></a>Windows

Para Windows 10, Windows Server 2016 y Windows Server 2012 R2, siga las instrucciones oficiales de la instalación estándar. El programa de instalación de Python también instala pip de forma predeterminada.

1. Vaya a la [página de descargas de Python](https://www.python.org/downloads/) oficial y descargue la versión más reciente de Python 3.6.

2. Inicie el programa de instalación.

3. En la parte inferior de la interfaz, seleccione **Agregar Python 3.6 a la ruta de acceso**.

4. Seleccione **Instalar ahora** para finalizar la instalación.

Ahora puede abrir una nueva ventana de comandos y obtener la versión de Python y pip.

```bat
python --version
pip --version
```

A continuación, ejecute el siguiente comando para instalar la CLI de Service Fabric:

```bat
pip install sfctl
sfctl -h
```

### <a name="ubuntu-and-windows-subsystem-for-linux"></a>Ubuntu y subsistema de Windows para Linux

Para ejecutar la CLI de Service Fabric, ejecute los siguientes comandos:

```bash
sudo apt-get install python3
sudo apt-get install python3-pip
pip3 install sfctl
```

A continuación, puede probar la instalación con:

```bash
sfctl -h
```

Si recibe un error del tipo command not found, como:

`sfctl: command not found`

Asegúrese de que se puede acceder a `~/.local/bin` desde `$PATH`:

```bash
export PATH=$PATH:~/.local/bin
echo "export PATH=$PATH:~/.local/bin" >> .bashrc
```

Si se produce un error en la instalación en el subsistema de Windows para Linux debido a que los permisos de carpeta no son correctos, es posible que sea necesario volver a intentarlo con permisos superiores:

```bash
sudo pip3 install sfctl
```

<a name = "cli-mac"></a>
### <a name="macos"></a>MacOS

Para MacOS, se recomienda utilizar el [administrador de paquetes de HomeBrew](https://brew.sh). Si HomeBrew si no está ya instalado, puede hacerlo ejecutando el comando siguiente:

```bash
/usr/bin/ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"
```

A continuación, desde el terminal, ejecute los siguientes comandos para instalar Python 3.6, pip y la CLI de Service Fabric:

```bash
brew install python3
pip3 install sfctl
sfctl -h
```

## <a name="cli-syntax"></a>Sintaxis de la CLI

Los comandos siempre tienen el prefijo `sfctl`. Para obtener información general acerca de todos los comandos que puede usar, use `sfctl -h`. Para obtener ayuda con un único comando, use `sfctl <command> -h`.

Los comandos siguen una estructura repetible, con el destino del comando precediendo al verbo o la acción.

```azurecli
sfctl <object> <action>
```

En este ejemplo, `<object>` es el destino de `<action>`.

## <a name="select-a-cluster"></a>Selección de un clúster

Para poder realizar cualquier operación, es preciso seleccionar un clúster al que conectarse. Por ejemplo, ejecute el siguiente comando para seleccionar y conectarse al clúster con el nombre `testcluster.com`:

> [!WARNING]
> No utilice clústeres de Service Fabric que no sean seguros en entornos de producción.

```azurecli
sfctl cluster select --endpoint http://testcluster.com:19080
```

El punto de conexión del clúster debe ir precedido por el prefijo `http` o `https`. Debe incluir el puerto para la puerta de enlace HTTP. El puerto y la dirección coinciden con la dirección URL de Service Fabric Explorer.

Para clústeres que están protegidos con un certificado, puede especificar un certificado PEM codificado. El certificado se puede especificar como un único archivo o como un par de certificado y clave. Si es un certificado autofirmado sin firma de una entidad de certificación, puede pasar la opción `--no-verify` para omitir la comprobación de la entidad de certificación.

```azurecli
sfctl cluster select --endpoint https://testsecurecluster.com:19080 --pem ./client.pem --no-verify
```

Para más información, consulte [Conexión a un clúster seguro de Azure Service Fabric](service-fabric-connect-to-secure-cluster.md).

## <a name="basic-operations"></a>Operaciones básicas

La información de la conexión del clúster se mantiene en varias sesiones de la CLI de Service Fabric. Una vez que se selecciona un clúster de Service Fabric, se puede ejecutar cualquier comando de Service Fabric en él.

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

Análogamente, para convertir un archivo PEM en uno PFX, puede usar el siguiente comando (la contraseña no se proporciona aquí):

```bash
openssl  pkcs12 -export -out Certificates.pfx -inkey Certificates.pem -in Certificates.pem -passout pass:'' 
```

Para más información, consulte la documentación de [OpenSSL](https://www.openssl.org/docs/).

### <a name="connection-problems"></a>Problemas de conexión

Algunas operaciones pueden generar el mensaje siguiente:

`Failed to establish a new connection: [Errno 8] nodename nor servname provided, or not known`

Compruebe que el punto de conexión del clúster especificado está disponible y a la escucha. Compruebe también que la interfaz de usuario de Service Fabric Explorer está disponible en dicho host y puerto. Use `sfctl cluster select` para actualizar el punto de conexión.

### <a name="detailed-logs"></a>Registros detallados

Los registros detallados suelen ser útiles para depurar o notificar un problema. Una marca `--debug` global aumenta el nivel de detalle de los archivos de registro.

### <a name="command-help-and-syntax"></a>Ayuda y sintaxis de los comandos

Para obtener ayuda con un comando específico o un grupo de comandos, use la marca `-h`.

```azurecli
sfctl application -h
```

Este es otro ejemplo:

```azurecli
sfctl application create -h
```

## <a name="updating-the-service-fabric-cli"></a>Actualización de la CLI de Service Fabric 

Para actualizar la CLI de Service Fabric, ejecute los siguientes comandos (reemplace `pip` con `pip3`, según lo que especificara durante la instalación original):

```bash
pip uninstall sfctl
pip install sfctl
```

## <a name="next-steps"></a>Pasos siguientes

* [Implementación de una aplicación con la CLI de Azure Service Fabric](service-fabric-application-lifecycle-sfctl.md)
* [Introducción a Service Fabric con Linux](service-fabric-get-started-linux.md)
