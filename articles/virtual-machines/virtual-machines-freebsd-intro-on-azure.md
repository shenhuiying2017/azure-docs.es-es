---
title: "Introducción a FreeBSD en Azure | Microsoft Docs"
description: "Aprenda a utilizar máquinas virtuales de FreeBSD en Azure"
services: virtual-machines-linux
documentationcenter: 
author: KylieLiang
manager: timlt
editor: 
tags: azure-service-management
ms.assetid: 32b87a5f-d024-4da0-8bf0-77e233d1422b
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 01/09/2017
ms.author: kyliel
translationtype: Human Translation
ms.sourcegitcommit: 71ad04b10bc49500197db6fecdcc0305a1ea0dd2
ms.openlocfilehash: ecb1c385de6c1b12674326afe7d5a0ebf6cd9ad0


---
# <a name="introduction-to-freebsd-on-azure"></a>Introducción a FreeBSD en Azure
Este tema proporciona una visión general de la ejecución de una máquina virtual de FreeBSD en Azure.

## <a name="overview"></a>Información general
FreeBSD para Microsoft Azure es un sistema operativo avanzado que se utiliza para servidores modernos, equipos de escritorio y plataformas insertadas.

Microsoft Corporation ofrece imágenes de FreeBSD en Azure con el [agente de invitado de VM de Azure](https://github.com/Azure/WALinuxAgent/) preconfigurado. Actualmente, Microsoft ofrece las siguientes versiones de FreeBSD como imágenes:

- FreeBSD 10.3-RELEASE
- FreeBSD 11.0-RELEASE

El agente es responsable de la comunicación entre la VM de FreeBSD y el tejido de Azure para operaciones como el aprovisionamiento de la VM cuando se usa por primera vez (nombre de usuario, contraseña o clave SSH, nombre de host, etc.) y la habilitación de la funcionalidad para extensiones de VM selectivas.

En lo que respecta a futuras versiones de FreeBSD, la estrategia es mantenerse al día y que las últimas versiones estén disponibles al poco tiempo de que el equipo de ingeniería de lanzamientos de FreeBSD las publique.

## <a name="deploying-a-freebsd-virtual-machine"></a>Implementación de una máquina virtual de FreeBSD
La implementación de una máquina virtual de FreeBSD es un proceso sencillo cuando se usa una imagen de Azure Marketplace:

- [FreeBSD 10.3 en Azure Marketplace](https://azure.microsoft.com/marketplace/partners/microsoft/freebsd103/)
- [FreeBSD 11.0 en Azure Marketplace](https://azure.microsoft.com/marketplace/partners/microsoft/freebsd110/)

## <a name="vm-extensions-for-freebsd"></a>Extensiones de VM para FreeBSD
A continuación se muestran las extensiones de VM compatibles en FreeBSD.

### <a name="vmaccess"></a>VMAccess
La extensión [VMAccess](https://github.com/Azure/azure-linux-extensions/tree/master/VMAccess) puede:

* Restablecer la contraseña del usuario de sudo original.
* Crear un nuevo usuario de sudo con la contraseña especificada.
* Establecer la clave pública del host con la clave dada.
* Restablecer la clave pública del host proporcionada durante el aprovisionamiento de VM si no se proporciona clave de host.
* Abrir el puerto (22) SSH y restaurar sshd_config si reset_ssh está establecido en true.
* Quitar el usuario existente.
* Comprobar discos.
* Reparar un disco agregado.

### <a name="customscript"></a>CustomScript
La extensión [CustomScript](https://github.com/Azure/azure-linux-extensions/tree/master/CustomScript) puede:

* Si se proporciona, descargar los scripts desde Azure Storage o desde un almacenamiento público externo (por ejemplo, GitHub).
* Ejecutar el script de punto de entrada.
* Admitir comandos en línea.
* Convertir la nueva línea de estilo de Windows en scripts de Shell y Python automáticamente.
* Quitar la marca BOM en scripts de Shell y Python automáticamente.
* Proteger la información confidencial en CommandToExecute.

[!NOTE]Por el momento, la máquina virtual de FreeBSD solo es compatible con la versión 1.x de CustomScript.  

## <a name="authentication-user-names-passwords-and-ssh-keys"></a>Autenticación: nombres de usuario, contraseñas y claves SSH
Al crear una máquina virtual FreeBSD con el Azure Portal, debe proporcionar un nombre de usuario, una contraseña o una clave pública SSH.
Los nombres de usuario para implementar una máquina virtual de FreeBSD en Azure no deben coincidir con los nombres de cuentas del sistema (UID <&100;) ya presentes en la máquina virtual ("raíz", por ejemplo).
Actualmente, solo se admite la clave RSA SSH. Una clave SSH multilínea debe comenzar con `---- BEGIN SSH2 PUBLIC KEY ----` y terminar con `---- END SSH2 PUBLIC KEY ----`.

## <a name="obtaining-superuser-privileges"></a>Obtención de privilegios de superusuario
La cuenta de usuario especificada durante la implementación de la instancia de máquina virtual en Azure es una cuenta con privilegios. Se instaló el paquete de sudo en la imagen de FreeBSD publicada.
Después de iniciar sesión con esta cuenta de usuario, puede ejecutar comandos como root con usando la sintaxis de comando.

    $ sudo <COMMAND>

También puede obtener un shell root con `sudo -s`.

## <a name="known-issues"></a>Problemas conocidos
1. Actualmente no hay cuestiones pendientes con la versión 11.0 de FreeBSD en Hyper-V (y Azure) que puedan hacer que las máquinas virtuales no puedan iniciarse si se han aplicado revisiones al sistema operativo mediante `freebsd-update`. La [revisión propuesta](https://bugs.freebsd.org/bugzilla/show_bug.cgi?id=212721) se incluye en las imágenes de FreeBSD en Azure Marketplace. Sin embargo, el equipo de FreeBSD no las ha combinado en origen, por lo que ejecutar `freebsd-update` reemplazar el kernel por uno sin revisiones. Se recomienda que los usuarios de Azure no instalen revisiones para la versión 11.0 de FreeBSD hasta que la corrección se publique como ERRATA.

2. La versión 2.2.2 de [Agente invitado de máquina virtual de Azure](https://github.com/Azure/WALinuxAgent/) tiene un [problema conocido] (https://github.com/Azure/WALinuxAgent/pull/517) que provoca un error de aprovisionamiento de la máquina virtual de FreeBSD en Azure. Se recomienda que los usuarios de máquina virtual de FreeBSD en Azure usen la versión 2.2.1 o una versión anterior. La corrección se incluirá en la versión 2.2.3 de [Agente invitado de máquina virtual de Azure](https://github.com/Azure/WALinuxAgent/). 

## <a name="next-steps"></a>Pasos siguientes
* Acuda a [Azure Marketplace](https://azure.microsoft.com/marketplace/partners/microsoft/freebsd110/) para crear una máquina virtual de FreeBSD.
* Si desea incorporar su propio FreeBSD a Azure, consulte [Creación y carga de un VHD de FreeBSD en Azure](./virtual-machines-linux-classic-freebsd-create-upload-vhd.md).



<!--HONumber=Jan17_HO2-->


