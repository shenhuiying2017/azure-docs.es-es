---
title: Red Hat Update Infrastructure | Microsoft Docs
description: "Obtenga información acerca de Red Hat Update Infrastructure para instancias de Red Hat Enterprise Linux a petición de Microsoft Azure"
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
ms.date: 09/10/2017
ms.author: borisb
ms.openlocfilehash: ea9a5cbd9b9b7b67ceb131cb8ba1d2476dbd5f72
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="red-hat-update-infrastructure-for-on-demand-red-hat-enterprise-linux-vms-in-azure"></a>Red Hat Update Infrastructure para máquinas virtuales Red Hat Enterprise Linux a petición en Azure
 [Red Hat Update Infrastructure](https://access.redhat.com/products/red-hat-update-infrastructure) (RHUI) permite que los proveedores de nube, como Azure, reflejen el contenido del repositorio hospedado en Red Hat, creen repositorios personalizados con contenido específico de Azure y lo pongan a disposición de las máquinas virtuales del usuario final.

Las imágenes de pago por uso (PAYG) de Red Hat Enterprise Linux (RHEL) vienen preconfiguradas para acceder a RHUI de Azure. No se necesita ninguna configuración adicional. Para obtener las actualizaciones más recientes, ejecute `sudo yum update` después de que la instancia RHEL esté lista. Este servicio se incluye como parte de las tarifas de software de PAYG de RHEL.

## <a name="important-information-about-azure-rhui"></a>Información importante sobre RHUI de Azure
* Azure RHUI actualmente solo admite la versión menor más reciente de cada familia de RHEL (RHEL6 o RHEL7). Para actualizar una instancia de máquina virtual de RHEL conectada a RHUI a la versión menor más reciente, ejecute `sudo yum update`.

    Por ejemplo, si aprovisiona una máquina virtual desde la imagen de PAYG de RHEL 7.2 y ejecuta `sudo yum update`, terminará con una máquina virtual de RHEL 7.4 VM (versión menor más reciente de la familia RHEL7).

    Para evitar este comportamiento, necesita crear su propia imagen como se describe en el artículo [Create and upload a Red Hat-based virtual machine for Azure](redhat-create-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) (Creación y carga de una máquina virtual basada en Red Hat para Azure). A continuación, tiene que conectarla a una infraestructura de actualización diferente ([directamente a los servidores de entrega de contenido de Red Hat](https://access.redhat.com/solutions/253273) o a un [servidor de Red Hat Satellite](https://access.redhat.com/products/red-hat-satellite)).

* El acceso a RHUI hospedado en Azure se incluye en el precio de la imagen de PAYG de RHEL. Al anular el registro de una máquina virtual PAYG RHEL en la RHUI hospedada por Azure, no se convierte el tipo de la máquina virtual a "traiga su propia licencia" (BYOL). Si registra la misma máquina virtual con otro origen de actualizaciones, podrían generarse gastos _indirectos_ por duplicado. Se le cobrará la primera vez la cuota de software de Azure RHEL. Se le cobrará la segunda vez para las suscripciones de Red Hat que se adquirieron previamente. Si necesita usar una infraestructura coherente de actualizaciones que no sea la RHUI hospedada en Azure, considere la posibilidad de crear e implementar sus propias imágenes (del tipo BYOL). Este proceso se describe en [Create and upload a Red Hat-based virtual machine for Azure](redhat-create-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) (Creación y carga de una máquina virtual basada en Red Hat para Azure).

* Las dos clases de las imágenes de PAYG de RHEL en Azure (RHEL for SAP HANA y RHEL for SAP Business Applications) están conectadas a canales de RHUI dedicados que permanecen en la versión menor específica de RHEL, tal como se requiere para la certificación de SAP. 

* El acceso a la RHUI hospedada en Azure se limita a las máquinas virtuales dentro de los [intervalos de direcciones IP del centro de datos de Azure](https://www.microsoft.com/download/details.aspx?id=41653). Si remite todo el tráfico de máquina virtual a través de un proxy mediante la infraestructura de red local, es posible que tenga que configurar rutas definidas por el usuario para que las máquinas virtuales de PAYG de RHEL accedan a RHUI de Azure.

### <a name="the-ips-for-the-rhui-content-delivery-servers"></a>Las direcciones IP para los servidores de entrega de contenido de RHUI

RHUI está disponible en todas las regiones donde estén disponibles las imágenes a petición RHEL. Actualmente incluye todas las regiones públicas que aparecen en el [panel de estado de Azure](https://azure.microsoft.com/status/) y las regiones del Gobierno de EE. UU. de Azure y regiones de Microsoft Azure Alemania. 

Si va a usar la configuración de red para restringir aún más el acceso desde máquinas virtuales RHEL PAYG, asegúrese de que se permiten las siguientes direcciones IP para `yum update` con el fin de trabajar según el entorno en el que se encuentre: 

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

## <a name="rhui-azure-infrastructure-update"></a>Actualización de la infraestructura RHUI en Azure

En septiembre de 2016, implementamos una RHUI de Azure actualizada. En abril de 2017, antigua RHUI de Azure dejó de prestar servicio. Si ha usado las imágenes de PAYG de RHEL (o sus instantáneas) desde septiembre de 2016 o después, se conecta automáticamente al nuevo RHUI de Azure. Sin embargo, si instantáneas anteriores en sus máquinas virtuales, debe actualizar manualmente su configuración para acceder a la RHUI de Azure tal como se describe en la siguiente sección.

Los nuevos servidores RHUI de Azure se implementan con [Azure Traffic Manager](https://azure.microsoft.com/services/traffic-manager/). En Traffic Manager, una máquina virtual puede usar cualquier punto de conexión único (rhui-1.micrsoft.com), independientemente de la región. 

### <a name="troubleshoot-connection-problems-to-azure-rhui"></a>Solución de problemas de conexión a RHUI de Azure
Si experimenta problemas al conectarse a RHUI de Azure desde la máquina virtual de Azure RHEL PAYG, siga estos pasos:

1. Inspeccione la configuración de máquina virtual del punto de conexión de RHUI de Azure:

    a. Compruebe si el archivo `/etc/yum.repos.d/rh-cloud.repo` contiene referencias a `rhui-[1-3].microsoft.com` en `baseurl` de la sección `[rhui-microsoft-azure-rhel*]` del archivo. Si las tiene, significa que está usando la nueva RHUI de Azure.

    b. Si apunta a una ubicación con el siguiente patrón `mirrorlist.*cds[1-4].cloudapp.net`, es necesario actualizar la configuración. Está usando la instantánea de máquina virtual anterior y tiene que actualizarla para dirigirla al nuevo RHUI de Azure.

2. El acceso a la RHUI hospedada en Azure se limita a las máquinas virtuales dentro de los [intervalos IP del centro de datos de Azure] (https://www.microsoft.com/download/details.aspx?id=41653).
 
3. Si está usando la configuración nueva y comprobó que la máquina virtual se conecta desde el intervalo IP de Azure pero sigue sin poder conectarse a RHUI de Azure, presente una incidencia de soporte técnico a Microsoft o Red Hat.

### <a name="manual-update-procedure-to-use-the-azure-rhui-servers"></a>Procedimiento de actualización manual para usar los servidores de RHUI de Azure
Este procedimiento se proporciona solo como referencia. Las imágenes de RHEL PAYG ya tienen la configuración correcta para conectarse a la RHUI de Azure. Para actualizar manualmente la configuración para usar los servidores de RHUI de Azure, complete los pasos siguientes:

1. Descargue la firma de clave pública mediante curl.

   ```bash
   curl -o RPM-GPG-KEY-microsoft-azure-release https://download.microsoft.com/download/9/D/9/9d945f05-541d-494f-9977-289b3ce8e774/microsoft-sign-public.asc 
   ```

2. Compruebe la validez de la clave descargada.

   ```bash
   gpg --list-packets --verbose < RPM-GPG-KEY-microsoft-azure-release
   ```

3. Compruebe la salida y, a continuación, compruebe el `keyid` y el `user ID packet`.

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

4. Instale la clave pública.

   ```bash
   sudo install -o root -g root -m 644 RPM-GPG-KEY-microsoft-azure-release /etc/pki/rpm-gpg
   sudo rpm --import /etc/pki/rpm-gpg/RPM-GPG-KEY-microsoft-azure-release
   ```

5. Descargue, compruebe e instale un RPM Package Manager (RPM) de cliente.
    
    >[!NOTE]
    >Las versiones del paquete cambian. Si se conecta manualmente a RHUI de Azure, puede encontrar la versión más reciente del paquete de cliente para cada familia de RHEL si aprovisiona la imagen más reciente de la galería.
  
   a. Descargue. 
   
    - Para RHEL 6:
        ```bash
        curl -o azureclient.rpm https://rhui-1.microsoft.com/pulp/repos/microsoft-azure-rhel6/rhui-azure-rhel6-2.1-32.noarch.rpm 
        ```
    
    - Para RHEL 7:
        ```bash
        curl -o azureclient.rpm https://rhui-1.microsoft.com/pulp/repos/microsoft-azure-rhel7/rhui-azure-rhel7-2.1-19.noarch.rpm  
        ```

   b. Compruebe.

   ```bash
   rpm -Kv azureclient.rpm
   ```

   c. Compruebe la salida para asegurarse de que la firma del paquete es correcta.

   ```bash
   azureclient.rpm:
       Header V3 RSA/SHA256 Signature, key ID be1229cf: OK
       Header SHA1 digest: OK (927a3b548146c95a3f6c1a5d5ae52258a8859ab3)
       V3 RSA/SHA256 Signature, key ID be1229cf: OK
       MD5 digest: OK (c04ff605f82f4be8c96020bf5c23b86c)
   ```

   d. Instale el RPM.

    ```bash
    sudo rpm -U azureclient.rpm
    ```

6. Al finalizar, compruebe que puede acceder a la RHUI en Azure desde la máquina virtual.

## <a name="next-steps"></a>Pasos siguientes
Para crear una máquina virtual Linux de Red Hat Enterprise a partir de la imagen de pago por uso de Azure Marketplace y usar la RHUI hospedada en Azure, vaya a [Azure Marketplace](https://azure.microsoft.com/marketplace/partners/redhat/). 