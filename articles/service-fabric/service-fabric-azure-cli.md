---
title: "Getting started with Azure Service Fabric XPlat CLI (Introducción a la CLI de XPlat de Azure Service Fabric)"
description: "Getting started with Azure Service Fabric XPlat CLI (Introducción a la CLI de XPlat de Azure Service Fabric)"
services: service-fabric
documentationcenter: .net
author: mani-ramaswamy
manager: timlt
editor: 
ms.assetid: c3ec8ff3-3b78-420c-a7ea-0c5e443fb10e
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 06/29/2017
ms.author: subramar
ms.translationtype: Human Translation
ms.sourcegitcommit: 6dbb88577733d5ec0dc17acf7243b2ba7b829b38
ms.openlocfilehash: 9190b01b6ce42ea67ec7806c55a0013834d24211
ms.contentlocale: es-es
ms.lasthandoff: 07/04/2017

---

# <a name="using-the-xplat-cli-to-interact-with-a-service-fabric-cluster"></a>Uso de la CLI de XPlat para interactuar con un clúster de Service Fabric

Puede interactuar con un clúster de Service Fabric desde máquinas virtuales con Linux mediante la CLI de XPlat en Linux.

El primer paso es obtener la versión más reciente de la CLI en el repositorio de Git y configurarla en su ruta de acceso mediante los siguientes comandos:

```sh
 git clone https://github.com/Azure/azure-xplat-cli.git
 cd azure-xplat-cli
 npm install
 sudo ln -s \$(pwd)/bin/azure /usr/bin/azure
 azure servicefabric
```

Para cada comando que admite, puede escribir el nombre del comando para obtener ayuda sobre él.
Se admite la finalización automática de los comandos. Por ejemplo, el comando siguiente le proporciona ayuda para todos los comandos de la aplicación. 

```sh
 azure servicefabric application 
```

Puede filtrar aún más la ayuda para un comando específico, como se muestra en el ejemplo siguiente:

```sh
 azure servicefabric application  create
```

Para habilitar la finalización automática en la CLI, ejecute los siguientes comandos:

```sh
azure --completion >> ~/azure.completion.sh
echo 'source ~/azure.completion.sh' >> ~/.sh\_profile
source ~/azure.completion.sh
```

Los siguientes comandos permiten conectar al clúster y le muestran los nodos de este:

```sh
 azure servicefabric cluster connect http://localhost:19080
 azure servicefabric node show
```

Para utilizar parámetros con nombre y averiguar lo que son, puede escribir --help después del comando. Por ejemplo:

```sh
 azure servicefabric node show --help
 azure servicefabric application create --help
```

Si se conecta a un clúster de varias máquinas virtuales desde una máquina **que no forma parte de este**, utilice el siguiente comando:

```sh
 azure servicefabric cluster connect http://PublicIPorFQDN:19080
```

Reemplace la etiqueta PublicIPorFQDN por la dirección IP o FQDN real según corresponda. Si se conecta a un clúster de varias máquinas virtuales desde una máquina **que forma parte de este**, utilice el siguiente comando:

```sh
 azure servicefabric cluster connect --connection-endpoint http://localhost:19080 --client-connection-endpoint PublicIPorFQDN:19000
```

Puede usar PowerShell o CLI para interactuar con su clúster de Service Fabric para Linux creado mediante Azure Portal.

> [!WARNING]
> Estos clústeres no son seguros, por tanto, puede abrir el clúster one-box agregando la dirección IP pública en el manifiesto de clúster.

## <a name="using-the-xplat-cli-to-connect-to-a-service-fabric-cluster"></a>Uso de la CLI de XPlat para conectarse a un clúster de Service Fabric

Los siguientes comandos de la CLI de Azure describen cómo conectarse a un clúster seguro. Los detalles del certificado deben corresponder a un certificado de los nodos del clúster.

```sh
azure servicefabric cluster connect --connection-endpoint http://ip:19080 --client-key-path /tmp/key --client-cert-path /tmp/cert
```

Si el certificado tiene entidades de certificación (CA), debe agregar el parámetro --ca-cert-path como en el ejemplo siguiente: 

```sh
 azure servicefabric cluster connect --connection-endpoint http://ip:19080 --client-key-path /tmp/key --client-cert-path /tmp/cert --ca-cert-path /tmp/ca1,/tmp/ca2 
```

Si tiene varias entidades de certificación, use una coma como delimitador.

Si el nombre común del certificado no coincide con el punto de conexión de la conexión, puede usar el parámetro `--strict-ssl-false` para omitir la comprobación como se muestra en el siguiente comando:

```sh
azure servicefabric cluster connect --connection-endpoint http://ip:19080 --client-key-path /tmp/key --client-cert-path /tmp/cert --strict-ssl-false 
```

Si desea omitir la comprobación de la entidad de certificación, puede agregar el parámetro --reject-unauthorized-false como se muestra en el siguiente comando: 

```sh
azure servicefabric cluster connect --connection-endpoint http://ip:19080 --client-key-path /tmp/key --client-cert-path /tmp/cert --reject-unauthorized-false 
```

Después de conectarse, debería poder ejecutar otros comandos de la CLI para interactuar con el clúster.

## <a name="deploying-your-service-fabric-application"></a>Implementación de la aplicación de Service Fabric

Ejecute los comandos siguientes para copiar, registrar e iniciar la aplicación de Service fabric:

```sh
azure servicefabric application package copy [applicationPackagePath] [imageStoreConnectionString] [applicationPathInImageStore]
azure servicefabric application type register [applicationPathinImageStore]
azure servicefabric application create [applicationName] [applicationTypeName] [applicationTypeVersion]
```

## <a name="upgrading-your-application"></a>Actualización de la aplicación

El proceso es similar al [proceso en Windows](service-fabric-application-upgrade-tutorial-powershell.md)).

Compile, copie, registre y cree su aplicación desde el directorio raíz del proyecto. Si la instancia de la aplicación se denomina `fabric:/MySFApp`, y el tipo es MySFApp, los comandos deben ser como los siguientes:

```sh
 azure servicefabric cluster connect http://localhost:19080
 azure servicefabric application package copy MySFApp fabric:ImageStore
 azure servicefabric application type register MySFApp
 azure servicefabric application create fabric:/MySFApp MySFApp 1.0
```

Realice el cambio en la aplicación y vuelva a compilar el servicio modificado.  Actualice el archivo de manifiesto del servicio modificado (ServiceManifest.xml) con las versiones actualizadas para el servicio (y el código o la configuración o los datos según corresponda). Modifique también el manifiesto de la aplicación (ApplicationManifest.xml) con el número de la versión actualizada de la aplicación y el servicio modificado.  Después, copie y registre la aplicación actualizada con los comandos siguientes:

```sh
 azure servicefabric cluster connect http://localhost:19080>
 azure servicefabric application package copy MySFApp fabric:ImageStore
 azure servicefabric application type register MySFApp
```

Con esto, ya puede iniciar la actualización de la aplicación con el siguiente comando:

```sh
 azure servicefabric application upgrade start -–application-name fabric:/MySFApp -–target-application-type-version 2.0 --rolling-upgrade-mode UnmonitoredAuto
```

Ya puede supervisar mediante SFX la actualización de la aplicación. En unos minutos se habrá actualizado la aplicación. También puede probar una aplicación actualizada con un error y comprobar la funcionalidad de reversión automática en Service Fabric.

## <a name="converting-from-pfx-to-pem-and-vice-versa"></a>Conversión de PFX a PEM y viceversa

Podría necesitar instalar un certificado en el equipo local (con Windows o Linux) para tener acceso a los clústeres seguros que pueden estar en un entorno diferente. Por ejemplo, al acceder a un clúster de Linux seguro desde un equipo Windows y viceversa, debe convertir el certificado PFX a PEM y viceversa. 

Para convertir un archivo PEM a uno PFX, utilice el siguiente comando:

```bash
openssl pkcs12 -export -out certificate.pfx -inkey mycert.pem -in mycert.pem -certfile mycert.pem
```

Para convertir un archivo PFX en uno PEM, use el comando siguiente:

```bash
openssl pkcs12 -in certificate.pfx -out mycert.pem -nodes
```

Consulte la [documentación de OpenSSL](https://www.openssl.org/docs/man1.0.1/apps/pkcs12.html) para obtener más información.

<a id="troubleshooting"></a>

## <a name="troubleshooting"></a>Solución de problemas


### <a name="copying-of-the-application-package-does-not-succeed"></a>La copia del paquete de aplicación no se realiza correctamente

Compruebe si `openssh` está instalado. De forma predeterminada, el escritorio Ubuntu no lo tiene instalado. Instálelo con el comando siguiente:

```sh
sudo apt-get install openssh-server openssh-client**
```

Si el problema persiste, pruebe a deshabilitar PAM para ssh cambiando el archivo `sshd_config` mediante los siguientes comandos:

```sh
sudo vi /etc/ssh/sshd_config
#Change the line with UsePAM to the following: UsePAM no
sudo service sshd reload
```

Si aun así, el problema continúa, pruebe a aumentar el número de sesiones de ssh ejecutando los comandos siguientes:

```sh
sudo vi /etc/ssh/sshd\_config
# Add the following to lines:
# MaxSessions 500
# MaxStartups 300:30:500
sudo service sshd reload
```

El uso de claves para la autenticación ssh (en lugar de contraseñas) no se admite todavía (ya que la plataforma usa ssh para copiar los paquetes), así que use la autenticación de contraseña en su lugar.

## <a name="next-steps"></a>Pasos siguientes

[Configure el entorno de desarrollo e implementar una aplicación de Service Fabric en un clúster de Linux.](service-fabric-get-started-linux.md)

## <a name="related-articles"></a>Artículos relacionados

* [Getting started with Service Fabric and Azure CLI 2.0](service-fabric-azure-cli-2-0.md) (Introducción a Service Fabric y la CLI de Azure 2.0)

