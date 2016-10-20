<properties
   pageTitle="Interactuación con los clústeres de Service Fabric mediante CLI | Microsoft Azure"
   description="Uso de la CLI de Azure para interactuar con un clúster de Service Fabric"
   services="service-fabric"
   documentationCenter=".net"
   authors="mani-ramaswamy"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotNet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="09/24/2016"
   ms.author="subramar"/>


# Uso de la CLI de Azure para interactuar con un clúster de Service Fabric

Puede interactuar con un clúster de Service Fabric desde máquinas virtuales con Linux mediante la CLI de Azure en Linux.

El primer paso es obtener la versión más reciente de la CLI en el repositorio de Git y configurarla en su ruta de acceso mediante los siguientes comandos:

```sh
 git clone https://github.com/Azure/azure-xplat-cli.git
 cd azure-xplat-cli
 npm install
 sudo ln -s \$(pwd)/bin/azure /usr/bin/azure
 azure servicefabric
```

Para cada comando que admite, puede escribir el nombre del comando para obtener ayuda sobre él. Se admite la finalización automática de los comandos. Por ejemplo, el comando siguiente le proporciona ayuda para todos los comandos de la aplicación.

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

**Precaución:** estos clústeres no son seguros, por tanto, puede abrir el clúster one-box agregando la dirección IP pública en el manifiesto de clúster.



## Uso de la CLI de Azure para conectarse a un clúster de Service Fabric

Los siguientes comandos de la CLI de Azure describen cómo conectarse a un clúster seguro. Los detalles del certificado deben corresponder a un certificado de los nodos del clúster.

```
azure servicefabric cluster connect --connection-endpoint http://ip:19080 --client-key-path /tmp/key --client-cert-path /tmp/cert
```
 
Si el certificado tiene entidades de certificación (CA), debe agregar el parámetro --ca-cert-path como en el ejemplo siguiente:

```
 azure servicefabric cluster connect --connection-endpoint http://ip:19080 --client-key-path /tmp/key --client-cert-path /tmp/cert --ca-cert-path /tmp/ca1,/tmp/ca2 
```
Si tiene varias entidades de certificación, use una coma como delimitador.
 
Si el nombre común del certificado no coincide con el punto de conexión de la conexión, puede usar el parámetro `--strict-ssl` para omitir la comprobación como se muestra en el siguiente comando:

```
azure servicefabric cluster connect --connection-endpoint http://ip:19080 --client-key-path /tmp/key --client-cert-path /tmp/cert --strict-ssl false 
```
 
Si desea omitir la comprobación de la entidad de certificación, puede agregar el parámetro --reject-unauthorized como se muestra en el siguiente comando:

```
azure servicefabric cluster connect --connection-endpoint http://ip:19080 --client-key-path /tmp/key --client-cert-path /tmp/cert --reject-unauthorized false 
```
 
Después de conectarse, debería poder ejecutar otros comandos de la CLI para interactuar con el clúster.

## Implementación de la aplicación de Service Fabric

Ejecute los comandos siguientes para copiar, registrar e iniciar la aplicación de Service fabric:

```
azure servicefabric application package copy [applicationPackagePath] [imageStoreConnectionString] [applicationPathInImageStore]
azure servicefabric application type register [applicationPathinImageStore]
azure servicefabric application create [applicationName] [applicationTypeName] [applicationTypeVersion]
```


## Actualización de la aplicación

El proceso es similar al [proceso en Windows](service-fabric-application-upgrade-tutorial-powershell.md)).

Compile, copie, registre y cree su aplicación desde el directorio raíz del proyecto. Si la instancia de la aplicación se denomina fabric:/MySFApp, y el tipo es MySFApp, los comandos deben ser como los siguientes:

```
 azure servicefabric cluster connect http://localhost:19080
 azure servicefabric application package copy MySFApp fabric:ImageStore
 azure servicefabric application type register MySFApp
 azure servicefabric application create fabric:/MySFApp MySFApp 1.0
```

Realice el cambio en la aplicación y vuelva a compilar el servicio modificado. Actualice el archivo de manifiesto del servicio modificado (ServiceManifest.xml) con las versiones actualizadas para el servicio (y el código o la configuración o los datos según corresponda). Modifique también el manifiesto de la aplicación (ApplicationManifest.xml) con el número de la versión actualizada de la aplicación y el servicio modificado. Después, copie y registre la aplicación actualizada con los comandos siguientes:

```
 azure servicefabric cluster connect http://localhost:19080>
 azure servicefabric application package copy MySFApp fabric:ImageStore
 azure servicefabric application type register MySFApp
```

Con esto, ya puede iniciar la actualización de la aplicación con el siguiente comando:

```
 azure servicefabric application upgrade start -–application-name fabric:/MySFApp -–application-type-version 2.0  --upgrade-mode UnmonitoredAuto
```

Ya puede supervisar mediante SFX la actualización de la aplicación. En unos minutos se habrá actualizado la aplicación. También puede probar una aplicación actualizada con un error y comprobar la funcionalidad de reversión automática en Service Fabric.

## Solución de problemas

### La copia del paquete de aplicación no se realiza correctamente

Compruebe si `openssh` está instalado. De forma predeterminada, el escritorio Ubuntu no lo tiene instalado. Instálelo con el comando siguiente:

```
 sudo apt-get install openssh-server openssh-client**
```

Si el problema persiste, pruebe a deshabilitar PAM para ssh cambiando el archivo **sshd\_config** mediante los siguientes comandos:

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


## Pasos siguientes

Configurar el entorno de desarrollo e implementar una aplicación de Service Fabric en un clúster de Linux.

<!---HONumber=AcomDC_0928_2016-->