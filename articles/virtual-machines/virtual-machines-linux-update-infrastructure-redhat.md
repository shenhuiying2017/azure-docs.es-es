---
title: Red Hat Update Infrastructure (RHUI) | Microsoft Docs
description: Obtenga información acerca de Red Hat Update Infrastructure (RHUI) para instancias de Red Hat Enterprise Linux a petición de Microsoft Azure
services: virtual-machines-linux
documentationcenter: ''
author: BorisB2015
manager: timlt
editor: ''

ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 09/22/2016
ms.author: borisb

---
# Red Hat Update Infrastructure (RHUI) para máquinas virtuales Red Hat Enterprise Linux a petición en Azure
Las máquinas virtuales creadas a partir de las imágenes a petición de Red Hat Enterprise Linux (RHEL) en Azure Marketplace se registran para acceder a la instancia de Red Hat Update Infrastructure (RHUI) implementada en Azure. Las instancias RHEL a petición tienen acceso a un repositorio yum regional y pueden recibir las actualizaciones incrementales.

La lista del repositorio yum, que se administra mediante RHUI, se configura en la instancia de RHEL durante el aprovisionamiento. Por ello, no es necesario realizar ninguna configuración adicional: ejecute `yum update` una vez que la instancia de RHEL esté lista para obtener las últimas actualizaciones.

> [!NOTE]
> La infraestructura RHUI hospedada en Azure se ha actualizado recientemente (septiembre de 2016) y requiere cambios en la configuración de las instancias de RHEL existentes para obtener un acceso ininterrumpido a la RHUI en Azure. Consulte la sección Actualización de la infraestructura RHUI en Azure para más información.
> 
> 

## Actualización de la infraestructura RHUI en Azure
A partir de septiembre de 2016, Azure tiene un nuevo conjunto de servidores de Red Hat Update Infrastructure (RHUI). Estos servidores se implementan con [Azure Traffic Manager](https://azure.microsoft.com/services/traffic-manager/) para que cualquier máquina virtual pueda usar un único punto de conexión (rhui-1.micrsoft.com), independientemente de la región. También usa un certificado SSL que se encadena a una entidad de certificación conocida (raíz Baltimore). Realizar esta actualización de forma automática sería peligroso para algunos clientes que tienen ACL o tablas de enrutamiento personalizadas para los servidores de actualización de RHUI, por lo que esta actualización es optativa. Los pasos manuales para la incorporación a estos nuevos servidores están disponibles en esta página, así como un script completo para la incorporación de forma automática (tras la comprobación de los pasos individuales). Las nuevas imágenes RHEL de pago por uso en Azure Marketplace (versiones con fecha de septiembre de 2016 o posteriores) apuntan automáticamente a los nuevos servidores de RHUI en Azure y no necesitan ninguna acción adicional.

### Plazos de incorporación de la nueva infraestructura RHUI en Azure
| Date | Nota: |
| --- | --- |
| 22 de septiembre de 2016 |Servidores RHUI e instrucciones de instalación disponibles para su uso. Las máquinas virtuales implementadas con las nuevas imágenes RHEL de pago por uso de Marketplace (con fecha de septiembre de 2016) usarán automáticamente los nuevos servidores de RHUI, pero las máquinas virtuales existentes tienen que elegirlos. |
| 1 de noviembre de 2016 |Las imágenes de máquina virtual RHEL de pago por uso heredadas (que usan los antiguos servidores de RHUI en Azure) se quitarán de la galería de Azure Marketplace. |
| 16 de enero de 2017 |Se darán de baja los servidores de RHUI en Azure antiguos. Antes de esta fecha, actualice todas las máquinas virtuales RHEL de pago por uso afectadas para mantener el acceso a RHUI en Azure. |

### Direcciones IP de los nuevos servidores de RHUI
```
13.91.45.82
52.187.72.244
52.174.166.207
40.112.59.164
```

### Procedimiento de actualización manual para usar los nuevos servidores de RHUI en Azure
Descargue (mediante curl) la firma de clave pública.

```
curl -o RPM-GPG-KEY-microsoft-azure-release https://download.microsoft.com/download/9/D/9/9d945f05-541d-494f-9977-289b3ce8e774/microsoft-sign-public.asc 
```

Compruebe la clave descargada.

```
gpg --list-packets --verbose < RPM-GPG-KEY-microsoft-azure-release
```

En el archivo de salida, compruebe `keyid` y `user ID packet`:

```
Version: GnuPG v1.4.7 (GNU/Linux)
:public key packet:
        version 4, algo 1, created 1446074508, expires 0
        pkey[0]: [2048 bits]
        pkey[1]: [17 bits]
        keyid: EB3E94ADBE1229CF
:user ID packet: "Microsoft (Release signing) <gpgsecurity@microsoft.com>"
:signature packet: algo 1, keyid EB3E94ADBE1229CF
        version 4, created 1446074508, md5len 0, sigclass 0x13
        digest algo 2, begin of digest 1a 9b
        hashed subpkt 2 len 4 (sig created 2015-10-28)
        hashed subpkt 27 len 1 (key flags: 03)
        hashed subpkt 11 len 5 (pref-sym-algos: 9 8 7 3 2)
        hashed subpkt 21 len 3 (pref-hash-algos: 2 8 3)
        hashed subpkt 22 len 2 (pref-zip-algos: 2 1)
        hashed subpkt 30 len 1 (features: 01)
        hashed subpkt 23 len 1 (key server preferences: 80)
        subpkt 16 len 8 (issuer key ID EB3E94ADBE1229CF)
        data: [2047 bits]
```

Instale la clave pública.

```
sudo install -o root -g root -m 644 RPM-GPG-KEY-microsoft-azure-release /etc/pki/rpm-gpg
sudo rpm --import /etc/pki/rpm-gpg/RPM-GPG-KEY-microsoft-azure-release
```

Descargue, compruebe e instale el cliente RPM.

Descargue lo siguiente para RHEL 6:

```
curl -o azureclient.rpm https://rhui-1.microsoft.com/pulp/repos/microsoft-azure-rhel6/rhui-azure-rhel6-2.0-2.noarch.rpm 
```

Para RHEL 7:

```
curl -o azureclient.rpm https://rhui-1.microsoft.com/pulp/repos/microsoft-azure-rhel7/rhui-azure-rhel7-2.0-2.noarch.rpm  
```

Compruebe:

```
rpm -Kv azureclient.rpm
```

Compruebe en la salida que la firma del paquete sea correcta.

```
azureclient.rpm:
    Header V3 RSA/SHA256 Signature, key ID be1229cf: OK
    Header SHA1 digest: OK (927a3b548146c95a3f6c1a5d5ae52258a8859ab3)
    V3 RSA/SHA256 Signature, key ID be1229cf: OK
    MD5 digest: OK (c04ff605f82f4be8c96020bf5c23b86c)
```

Instale el RPM.

```
sudo rpm -U azureclient.rpm
```

Al finalizar, compruebe que puede acceder a la RHUI en Azure desde la máquina virtual.

### Script ‘todo en uno’ para automatizar la tarea anterior
Use el siguiente script según sea necesario para automatizar la tarea de actualización de máquinas virtuales afectadas a los nuevos servidores de RHUI en Azure.

```
# Download key
curl -o RPM-GPG-KEY-microsoft-azure-release https://download.microsoft.com/download/9/D/9/9d945f05-541d-494f-9977-289b3ce8e774/microsoft-sign-public.asc 

# Validate key
if ! gpg --list-packets --verbose < RPM-GPG-KEY-microsoft-azure-release | grep -q "keyid: EB3E94ADBE1229CF"; then
    echo "Keyfile azure.asc NOT valid. Exiting."
    exit 1
fi

# Install Key
sudo install -o root -g root -m 644 RPM-GPG-KEY-microsoft-azure-release /etc/pki/rpm-gpg
sudo rpm --import /etc/pki/rpm-gpg/RPM-GPG-KEY-microsoft-azure-release

# Download RPM package
if grep -q "release 7" /etc/redhat-release; then
    ver=7
elif  grep -q "release 6" /etc/redhat-release; then
    ver=6
else
    echo "Version not supported, exiting"
    exit 1
fi

url=https://rhui-1.microsoft.com/pulp/repos/microsoft-azure-rhel$ver/rhui-azure-rhel$ver-2.0-2.noarch.rpm
curl -o azureclient.rpm "$url"

# Verify package
if ! rpm -Kv azureclient.rpm | grep -q "key ID be1229cf: OK"; then
    echo "RPM failed validation ($url)"
    exit 1
fi

# Install package
sudo rpm -U azureclient.rpm
```

## Introducción sobre RHUI
[Red Hat Update Infrastructure](https://access.redhat.com/products/red-hat-update-infrastructure) ofrece una solución altamente escalable para administrar el contenido del repositorio yum para las instancias en la nube de Red Hat Enterprise Linux hospedadas por proveedores de nube con certificación de Red Hat. Basado en el proyecto Pulp ascendente, RHUI permite a los proveedores de nube reflejar localmente el contenido del repositorio hospedado en Red Hat, crear repositorios personalizados con su propio contenido y poner los repositorios a disposición de un grupo grande de usuarios finales a través de un sistema de entrega de contenido de carga equilibrada.

## Regiones donde se implementa RHUI
RHUI está disponible en todas las regiones donde estén disponibles las imágenes a petición RHEL. Actualmente incluye todas las regiones públicas enumeradas en la página [Estado de Azure](https://azure.microsoft.com/status/). En el precio se incluye el acceso de RHUI a las máquinas virtuales aprovisionadas desde imágenes RHEL a petición. La disponibilidad de la nube regional o nacional se actualizará cuando se expanda la disponibilidad RHEL a petición en el futuro.

> [!NOTE]
> El acceso a la RHUI hospedada en Azure se limita a las máquinas virtuales dentro de los [intervalos de direcciones IP del centro de datos de Microsoft Azure](https://www.microsoft.com/download/details.aspx?id=41653).
> 
> 

## Obtención de actualizaciones de otro repositorio de actualización
Si necesita obtener actualizaciones desde un repositorio de actualización diferente (en lugar de una RHUI hospedada en Azure) debe anular el registro de las instancias de RHUI y volver a registrarlas con la infraestructura de actualización deseada (por ejemplo, Red Hat Satellite o Red Hat Customer Portal CDN). Necesitará suscripciones apropiadas a Red Hat para estos servicios y registrarse en [Red Hat Cloud Access en Azure](https://access.redhat.com/ecosystem/partners/ccsp/microsoft-azure).

Para anular el registro de RHUI y volver a registrarse en su infraestructura de actualización, siga estos pasos.

1. Edite /etc/yum.repos.d/rh-cloud.repo y cambie todas las apariciones de `enabled=1` por `enabled=0`. Por ejemplo:
   
   # sed -i 's/enabled=1/enabled=0/g' /etc/yum.repos.d/rh-cloud.repo
2. Edite /etc/yum/pluginconf.d/rhnplugin.conf y cambie `enabled=0` por `enabled=1`.
3. A continuación, registre la infraestructura deseada, como el portal del cliente de Red Hat. Siga la guía de soluciones en Red Hat en [How to register and subscribe a system to the Red Hat Customer Portal](https://access.redhat.com/solutions/253273) (Cómo registrar y suscribirse a un sistema en el portal de cliente de Red Hat).

> [!NOTE]
> El acceso a RHUI hospedado en Azure se incluye en el precio de la imagen de pago por uso RHEL. Anular el registro de una máquina virtual RHEL de pago por uso desde la RHUI hospedada de Azure no convierte a la máquina virtual en una máquina virtual de tipo traiga su propia-licencia (BYOL) y, por tanto, se pueden incurrir en cargos dobles si se registra la misma máquina virtual con otro origen de actualizaciones.
> 
> Si necesita utilizar una infraestructura coherente de actualizaciones que no sea la RHUI hospedada en Azure, considere la posibilidad de crear e implementar sus propias imágenes (del tipo BYOL), tal y como se describe en el artículo sobre la [creación y carga de una máquina virtual basada en Red Hat para Azure](virtual-machines-linux-redhat-create-upload-vhd.md).
> 
> 

## Pasos siguientes
Para crear una máquina virtual Linux de Red Hat Enterprise a partir de la imagen de pago por uso de Azure Marketplace y aprovechar la RHUI hospedada en Azure, vaya a [Azure Marketplace](https://azure.microsoft.com/marketplace/partners/redhat/). Podrá utilizar `yum update` en su instancia de RHEL sin ninguna configuración adicional.

<!---HONumber=AcomDC_0928_2016-->