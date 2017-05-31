---
title: Red Hat Update Infrastructure (RHUI) | Microsoft Docs
description: "Obtenga información acerca de Red Hat Update Infrastructure (RHUI) para instancias de Red Hat Enterprise Linux a petición de Microsoft Azure"
services: virtual-machines-linux
documentationcenter: 
author: BorisB2015
manager: timlt
editor: 
ms.assetid: f495f1b4-ae24-46b9-8d26-c617ce3daf3a
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 02/13/2017
ms.author: borisb
ms.translationtype: Human Translation
ms.sourcegitcommit: e72275ffc91559a30720a2b125fbd3d7703484f0
ms.openlocfilehash: 07815d691ffe57f0349f7a90ced4a2fcc1ab834f
ms.contentlocale: es-es
ms.lasthandoff: 05/05/2017


---
# <a name="red-hat-update-infrastructure-rhui-for-on-demand-red-hat-enterprise-linux-vms-in-azure"></a>Red Hat Update Infrastructure (RHUI) para máquinas virtuales Red Hat Enterprise Linux a petición en Azure
Las máquinas virtuales creadas a partir de las imágenes a petición de Red Hat Enterprise Linux (RHEL) en Azure Marketplace se registran para acceder a la instancia de Red Hat Update Infrastructure (RHUI) implementada en Azure.  Las instancias RHEL a petición tienen acceso a un repositorio yum regional y pueden recibir las actualizaciones incrementales.

La lista del repositorio yum, que se administra mediante RHUI, se configura en la instancia de RHEL durante el aprovisionamiento. Por ello, no es necesario realizar ninguna configuración adicional: ejecute `yum update` una vez que la instancia de RHEL esté lista para obtener las últimas actualizaciones.

> [!NOTE]
> En septiembre de 2016 implementamos una RHUI de Azure actualizada y en enero de 2017 iniciamos el apagado de la RHUI de Azure antigua. Si ha estado usando las imágenes de RHEL (o sus instantáneas) desde septiembre de 2016 o más tarde, es probable que no tenga que hacer nada. Sin embargo, si tiene instantáneas o máquinas virtuales antiguas, es necesario actualizar su configuración para el acceso ininterrumpido a la RHUI de Azure.
> 

## <a name="rhui-azure-infrastructure-update"></a>Actualización de la infraestructura RHUI en Azure
A partir de septiembre de 2016, Azure tiene un nuevo conjunto de servidores de Red Hat Update Infrastructure (RHUI). Estos servidores se implementan con [Azure Traffic Manager](https://azure.microsoft.com/services/traffic-manager/) para que cualquier máquina virtual, independientemente de la región, pueda usar un único punto de conexión (rhui-1.micrsoft.com). Las nuevas imágenes RHEL de pago por uso (PAYG) en Azure Marketplace (versiones con fecha de septiembre de 2016 o posteriores) apuntan automáticamente a los nuevos servidores de RHUI de Azure y no necesitan ninguna acción adicional.

### <a name="determine-if-action-is-required"></a>Determinar si se requiere alguna acción
Si experimenta problemas al conectarse a RHUI de Azure desde la máquina virtual de Azure RHEL PAYG, siga estos pasos:
1. Inspeccione la configuración de máquina virtual del punto de conexión de RHUI de Azure.

    Compruebe si el archivo `/etc/yum.repos.d/rh-cloud.repo` contiene referencias a `rhui-[1-3].microsoft.com` en baseurl de la sección `[rhui-microsoft-azure-rhel*]` del archivo. Si las tiene, significa que está usando la nueva RHUI de Azure.

    Si apunta a una ubicación con el siguiente patrón `mirrorlist.*cds[1-4].cloudapp.net`, es necesario actualizar la configuración.

    Si está usando la nueva configuración y sigue sin poder conectarse a la RHUI de Azure, presente una incidencia de soporte técnico a Microsoft o Red Hat.

    > [!NOTE]
    > El acceso a la RHUI hospedada en Azure se limita a las máquinas virtuales dentro de los [intervalos de direcciones IP del centro de datos de Microsoft Azure](https://www.microsoft.com/download/details.aspx?id=41653).
    > 

2. Si la antigua RHUI de Azure sigue estando disponible cuando realiza esta comprobación y le gustaría actualizar automáticamente la configuración, ejecute el siguiente comando:

    `sudo yum update RHEL6` o `sudo yum update RHEL7`, según la versión de la familia de RHEL.

3. Si ya no puede conectarse a la RHUI de Azure antigua, siga los pasos manuales que se describe en la sección siguiente.

4. Asegúrese de actualizar la configuración en la imagen o instantánea de origen desde las que se aprovisionó la máquina virtual afectada.

### <a name="phased-shutdown-of-the-old-azure-rhui"></a>Apagado por fases de la antigua RHUI de Azure
Durante el apagado de la antigua RHUI de Azure, restringimos el acceso a ella de la manera siguiente:

1. Restricción adicional del acceso (ACL) al conjunto de direcciones IP que ya están conectadas a ella. Posibles efectos secundarios: si sigue usando la antigua RHUI de Azure, puede que las nuevas máquinas virtuales no puedan conectarse a ella. Las máquinas virtuales RHEL con direcciones IP dinámicas que pasan por la secuencia de apagado/desasignación/inicio pueden recibir nuevas IP, por lo que es posible que tampoco pueda conectarse a la RHUI de Azure antigua.

2. Apagado de los servidores de entrega de contenido reflejados. Posibles efectos secundarios: a medida que apagamos más CDSes, puede que observe un tiempo de servicio `yum update` más largo y un número mayor de tiempos de espera, hasta el punto de que ya no puede conectarse a la RHUI de Azure antigua.

### <a name="the-ips-for-the-new-rhui-content-delivery-servers-are"></a>Las direcciones IP de los nuevos servidores RHUI de entrega de contenido son
Si va a usar la configuración de red para restringir aún más el acceso desde máquinas virtuales RHEL PAYG, asegúrese de que se permiten las siguientes direcciones IP para `yum update` con el fin de trabajar según el entorno en el que se encuentre. 

```
# Azure Global
13.91.47.76
40.85.190.91
52.187.75.218
52.174.163.213

# Azure US Government
13.72.186.193

# Azure Germany
51.5.243.77
51.4.228.145
```

### <a name="manual-update-procedure-to-use-the-new-azure-rhui-servers"></a>Procedimiento de actualización manual para usar los nuevos servidores de RHUI en Azure
Descargue (mediante curl) la firma de clave pública.

```bash
curl -o RPM-GPG-KEY-microsoft-azure-release https://download.microsoft.com/download/9/D/9/9d945f05-541d-494f-9977-289b3ce8e774/microsoft-sign-public.asc 
```

Compruebe la clave descargada.

```bash
gpg --list-packets --verbose < RPM-GPG-KEY-microsoft-azure-release
```

En el archivo de salida, compruebe `keyid` y `user ID packet`:

```bash
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

```bash
sudo install -o root -g root -m 644 RPM-GPG-KEY-microsoft-azure-release /etc/pki/rpm-gpg
sudo rpm --import /etc/pki/rpm-gpg/RPM-GPG-KEY-microsoft-azure-release
```

Descargue, compruebe e instale el cliente RPM.

Descargue lo siguiente para RHEL 6:

```bash
curl -o azureclient.rpm https://rhui-1.microsoft.com/pulp/repos/microsoft-azure-rhel6/rhui-azure-rhel6-2.0-2.noarch.rpm 
```

Para RHEL 7:

```bash
curl -o azureclient.rpm https://rhui-1.microsoft.com/pulp/repos/microsoft-azure-rhel7/rhui-azure-rhel7-2.0-2.noarch.rpm  
```

Compruebe:

```bash
rpm -Kv azureclient.rpm
```

Compruebe en la salida que la firma del paquete sea correcta.

```bash
azureclient.rpm:
    Header V3 RSA/SHA256 Signature, key ID be1229cf: OK
    Header SHA1 digest: OK (927a3b548146c95a3f6c1a5d5ae52258a8859ab3)
    V3 RSA/SHA256 Signature, key ID be1229cf: OK
    MD5 digest: OK (c04ff605f82f4be8c96020bf5c23b86c)
```

Instale el RPM.

```bash
sudo rpm -U azureclient.rpm
```

Al finalizar, compruebe que puede acceder a la RHUI en Azure desde la máquina virtual.

### <a name="all-in-one-script-for-automating-the-preceding-task"></a>Script "todo en uno" para automatizar la tarea anterior
Use el siguiente script según sea necesario para automatizar la tarea de actualización de máquinas virtuales afectadas a los nuevos servidores de RHUI en Azure.

```sh
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

## <a name="rhui-overview"></a>Introducción sobre RHUI
[Red Hat Update Infrastructure](https://access.redhat.com/products/red-hat-update-infrastructure) ofrece una solución altamente escalable para administrar el contenido del repositorio yum para las instancias en la nube de Red Hat Enterprise Linux hospedadas por proveedores de nube con certificación de Red Hat. Basado en el proyecto Pulp ascendente, RHUI permite a los proveedores de nube reflejar localmente el contenido del repositorio hospedado en Red Hat, crear repositorios personalizados con su propio contenido y poner los repositorios a disposición de un grupo grande de usuarios finales a través de un sistema de entrega de contenido de carga equilibrada.

## <a name="regions-where-rhui-is-available"></a>Regiones donde se implementa RHUI
RHUI está disponible en todas las regiones donde estén disponibles las imágenes a petición RHEL. Actualmente incluye todas las regiones públicas que aparecen en el panel [Estado de Azure](https://azure.microsoft.com/status/) y las regiones del Gobierno de EE. UU. de Azure y de Alemania. En el precio se incluye el acceso de RHUI a las máquinas virtuales aprovisionadas desde imágenes RHEL a petición. La disponibilidad adicional de la nube regional o nacional se actualizará cuando se expanda la disponibilidad a petición de RHEL en el futuro.

> [!NOTE]
> El acceso a la RHUI hospedada en Azure se limita a las máquinas virtuales dentro de los [intervalos de direcciones IP del centro de datos de Microsoft Azure](https://www.microsoft.com/download/details.aspx?id=41653).
> 
> 

## <a name="get-updates-from-another-update-repository"></a>Obtención de actualizaciones de otro repositorio de actualización
Si necesita obtener actualizaciones de un repositorio de actualización diferente (que no sea la RHUI hospedada por Azure), primero debe anular el registro de sus instancias de RHUI. Luego, debe volver a registrarlas en la infraestructura de actualización deseada (por ejemplo, Red Hat Satellite o la red de entrega de contenido (CDN) de Red Hat Customer Portal. Necesitará suscripciones apropiadas a Red Hat para estos servicios y registrarse en [Red Hat Cloud Access en Azure](https://access.redhat.com/ecosystem/partners/ccsp/microsoft-azure).

Para anular el registro de RHUI y volver a registrarla en su infraestructura de actualización, siga estos pasos.

1. Edite /etc/yum.repos.d/rh-cloud.repo y cambie todas las apariciones de `enabled=1` por `enabled=0`. Por ejemplo:
   
   ```bash
   sed -i 's/enabled=1/enabled=0/g' /etc/yum.repos.d/rh-cloud.repo
   ```
   
2. Edite /etc/yum/pluginconf.d/rhnplugin.conf y cambie `enabled=0` por `enabled=1`.
3. A continuación, registre la infraestructura deseada, como el portal del cliente de Red Hat. Siga la guía de soluciones en Red Hat en [How to register and subscribe a system to the Red Hat Customer Portal](https://access.redhat.com/solutions/253273)(Cómo registrar y suscribirse a un sistema en el portal de cliente de Red Hat).

> [!NOTE]
> El acceso a RHUI hospedado en Azure se incluye en el precio de la imagen de pago por uso RHEL. Al anular el registro de una máquina virtual PAYG RHEL en la RHUI hospedada por Azure, no se convierte el tipo de la máquina virtual a "traiga su propia licencia" (BYOL). Si registra la misma máquina virtual en otro origen de actualizaciones, puede que se le cobre el doble: primero por la cuota de software de Azure RHEL y segundo por la suscripción o suscripciones de Red Hat. 
> 
> Si necesita utilizar una infraestructura coherente de actualizaciones que no sea la RHUI hospedada en Azure, considere la posibilidad de crear e implementar sus propias imágenes (del tipo BYOL), tal y como se describe en el artículo sobre la [creación y carga de una máquina virtual basada en Red Hat para Azure](redhat-create-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) .
> 

## <a name="next-steps"></a>Pasos siguientes
Para crear una máquina virtual Linux de Red Hat Enterprise a partir de la imagen de pago por uso de Azure Marketplace y aprovechar la RHUI hospedada en Azure, vaya a [Azure Marketplace](https://azure.microsoft.com/marketplace/partners/redhat/). Podrá utilizar `yum update` en su instancia de RHEL sin ninguna configuración adicional.


