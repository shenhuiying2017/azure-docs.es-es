---
title: "Preguntas más frecuentes sobre máquinas virtuales Windows en Azure | Microsoft Docs"
description: "Proporciona respuestas a algunas de las preguntas frecuentes sobre las máquinas virtuales Windows creadas con el modelo de Resource Manager."
services: virtual-machines-windows
documentationcenter: 
author: cynthn
manager: timlt
editor: 
tags: azure-resource-management
ms.assetid: 757da816-a050-4889-a010-6f75d7978eb7
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 05/10/2017
ms.author: cynthn
ms.translationtype: Human Translation
ms.sourcegitcommit: 97fa1d1d4dd81b055d5d3a10b6d812eaa9b86214
ms.openlocfilehash: 2b0e664be70cafe1b474382b939b4ceaa2eb80d9
ms.contentlocale: es-es
ms.lasthandoff: 05/11/2017


---

# Preguntas más frecuentes sobre máquinas virtuales Windows
<a id="frequently-asked-question-about-windows-virtual-machines" class="xliff"></a>
En este artículo se responden algunas preguntas frecuentes que los usuarios plantean sobre las máquinas virtuales Windows creadas en Azure mediante el modelo de implementación de Resource Manager. Para la versión de Linux de este tema, consulte [Preguntas frecuentes sobre las máquinas virtuales de Linux](../linux/faq.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

## ¿Qué puedo ejecutar en una máquina virtual de Azure?
<a id="what-can-i-run-on-an-azure-vm" class="xliff"></a>
Todos los suscriptores pueden ejecutar software de servidor en una máquina virtual de Azure. Para obtener información sobre la directiva de soporte de software de servidor de Microsoft ejecutado en Azure, consulte [Microsoft server software support for Azure Virtual Machines](https://support.microsoft.com/kb/2721672)

Existen determinadas versiones de Windows 7, Windows 8.1 y Windows 10 para suscriptores de ventajas de MSDN Azure y suscriptores de pago por uso de desarrollo y prueba de MSDN, para tareas de desarrollo y prueba. Para obtener más información, como instrucciones y limitaciones, consulte [Imágenes de cliente de Windows para los suscriptores de MSDN](http://azure.microsoft.com/blog/2014/05/29/windows-client-images-on-azure/). 

## ¿Cuánto almacenamiento puedo usar con una máquina virtual?
<a id="how-much-storage-can-i-use-with-a-virtual-machine" class="xliff"></a>
Cada disco de datos puede ser de hasta 1 TB. El número de discos de datos que puede usar depende del tamaño de la máquina virtual. Para obtener más información, consulte [Tamaños de máquinas virtuales](sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

Managed Disks de Azure son las nuevas ofertas de almacenamiento en disco recomendadas para usarse con Azure Virtual Machines y almacenar los datos de forma persistente. Puede usar varios de estos discos con cada máquina virtual. Managed Disks ofrecen dos tipos de opciones de almacenamiento duraderas: discos administrados premium y estándar. Para más información, consulte los [precios de Managed Disks](https://azure.microsoft.com/pricing/details/managed-disks).

Las cuentas de Azure Storage también pueden proporcionar almacenamiento para el disco del sistema operativo y los discos de datos. Cada disco es un archivo .vhd almacenado como un blob en páginas. Para obtener información detallada sobre los precios, consulte [Detalles de precios de almacenamiento](https://azure.microsoft.com/pricing/details/storage/).

## ¿Cómo puedo tener acceso a mi máquina virtual?
<a id="how-can-i-access-my-virtual-machine" class="xliff"></a>
Establezca una conexión remota mediante conexión a Escritorio remoto (RDP) para una máquina virtual Windows. Para obtener instrucciones, consulte [Conexión a una máquina virtual de Azure donde se ejecuta Windows e inicio de sesión en ella](connect-logon.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). Se admite un máximo de 2 conexiones simultáneas, a menos que el servidor esté configurado como un host de sesión de servicios de escritorio remoto.  

Si tiene problemas con Escritorio remoto, consulte [Solución de problemas de conexiones del Escritorio remoto a una máquina virtual de Azure con Windows](troubleshoot-rdp-connection.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). 

Si está familiarizado con Hyper-V, puede que esté buscando una herramienta similar a VMConnect. Azure no ofrece una herramienta similar porque no admite el acceso de la consola a una máquina virtual.

## ¿Puedo usar el disco temporal (la unidad D: de forma predeterminada) para almacenar datos?
<a id="can-i-use-the-temporary-disk-the-d-drive-by-default-to-store-data" class="xliff"></a>
No utilice el disco temporal para almacenar datos. Solo proporciona almacenamiento de forma temporal, por lo que se arriesgaría a perder datos que no se pueden recuperar. La pérdida de datos puede ocurrir cuando se mueve la máquina virtual a un host diferente. Cambiar el tamaño de una máquina virtual, actualizar el host o un error de hardware en el host son algunas de las razones por las que se puede mover una máquina virtual.

Si tiene una aplicación que necesita usar la letra de unidad D:, puede reasignar las letras de unidad para que el disco temporal utilice otra distinta a D:. Para obtener instrucciones, consulte [Cambio de la letra de unidad del disco temporal de Windows](change-drive-letter.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json).


## ¿Cómo puedo cambiar la letra de la unidad del disco temporal?
<a id="how-can-i-change-the-drive-letter-of-the-temporary-disk" class="xliff"></a>
Para cambiar la letra de la unidad, mueva el archivo de paginación y reasigne las letras de unidad; sin embargo, debe asegurarse de realizar los pasos en un orden específico. Para obtener instrucciones, consulte [Cambio de la letra de unidad del disco temporal de Windows](change-drive-letter.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json).

## ¿Puedo agregar una máquina virtual existente a un conjunto de disponibilidad?
<a id="can-i-add-an-existing-vm-to-an-availability-set" class="xliff"></a>
No. Si desea que la máquina virtual forme parte de un conjunto de disponibilidad, debe crearla dentro del conjunto. Actualmente no es posible agregar una máquina virtual a un conjunto de disponibilidad una vez creada.

## ¿Puedo cargar una máquina virtual en Azure?
<a id="can-i-upload-a-virtual-machine-to-azure" class="xliff"></a>
Sí. Para obtener instrucciones, consulte [Migrating on-premises VMs to Azure](on-prem-to-azure.md) (Migración de máquinas virtuales locales a Azure).

## ¿Puedo cambiar el tamaño del disco del sistema operativo?
<a id="can-i-resize-the-os-disk" class="xliff"></a>
Sí. Para obtener instrucciones, consulte [Cómo ampliar la unidad de sistema operativo de una máquina virtual en un grupo de recursos de Azure](expand-os-disk.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

## ¿Puedo copiar o clonar una máquina virtual de Azure existente?
<a id="can-i-copy-or-clone-an-existing-azure-vm" class="xliff"></a>
Sí. Mediante el uso de imágenes administradas, puede crear una imagen de una máquina virtual y luego usar la imagen para crear varias máquinas virtuales nuevas. Para obtener instrucciones, consulte [Creación de una imagen personalizada de una máquina virtual](tutorial-custom-images.md).

## ¿Por qué no veo las regiones de Canadá central y Canadá oriental por medio de Azure Resource Manager?
<a id="why-am-i-not-seeing-canada-central-and-canada-east-regions-through-azure-resource-manager" class="xliff"></a>

Las dos nuevas áreas Canadá central y Canadá oriental no se registran automáticamente para la creación de máquinas virtuales en las suscripciones de Azure existentes. Este registro se realizará automáticamente cuando se implementa una máquina virtual mediante el Portal de Azure en cualquier otra región usando Azure Resource Manager. Después de implementar una máquina virtual en cualquier otra región de Azure, las áreas nuevas deberán estar disponibles para las máquinas virtuales siguientes.

## ¿Se admiten máquinas virtuales Linux en Azure?
<a id="does-azure-support-linux-vms" class="xliff"></a>
Sí. Para crear rápidamente una máquina virtual Linux de prueba, consulte [Creación de una máquina virtual de Linux en Azure mediante el Portal](../linux/quick-create-portal.md).

## ¿Puedo agregar una NIC a mi máquina virtual después de crearla?
<a id="can-i-add-a-nic-to-my-vm-after-its-created" class="xliff"></a>
Sí, ahora es posible. En primer lugar, la máquina virtual debe detenerse desasignada. A continuación, puede agregar o quitar una NIC (a menos que sea la última en la máquina virtual). 

## ¿Hay algún requisito de nombre de equipo?
<a id="are-there-any-computer-name-requirements" class="xliff"></a>
Sí. El nombre del equipo puede tener un máximo de 15 caracteres. Consulte [Directrices de nomenclatura de la infraestructura](infrastructure-naming-guidelines.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) para obtener más información sobre la nomenclatura de sus recursos.

## ¿Existen algunos requisitos para los nombres de grupos de recursos?
<a id="are-there-any-resource-group-name-requirements" class="xliff"></a>
Sí. El nombre del grupo de recursos puede tener como máximo 90 caracteres. Consulte [Directrices para el grupo de recursos de infraestructura](infrastructure-resource-groups-guidelines.md) para más información sobre los grupos de recursos.

## ¿Cuáles son los requisitos de nombre de usuario cuando se crea una VM?
<a id="what-are-the-username-requirements-when-creating-a-vm" class="xliff"></a>

Los nombres de usuario pueden tener un máximo de 20 caracteres y no pueden terminar con un punto ("."). 


No se permiten los siguientes nombres de usuario:
<table>
    <tr>
        <td style="text-align:center">administrator </td><td style="text-align:center"> admin </td><td style="text-align:center"> user </td><td style="text-align:center"> user1</td>
    </tr>
    <tr>
        <td style="text-align:center">test </td><td style="text-align:center"> user2 </td><td style="text-align:center"> test1 </td><td style="text-align:center"> user3</td>
    </tr>    <tr>
        <td style="text-align:center">admin1 </td><td style="text-align:center"> 1 </td><td style="text-align:center"> 123 </td><td style="text-align:center"> a</td>
    </tr>
    <tr>
        <td style="text-align:center">actuser  </td><td style="text-align:center"> adm </td><td style="text-align:center"> admin2 </td><td style="text-align:center"> aspnet</td>
    </tr>
    <tr>
        <td style="text-align:center">backup </td><td style="text-align:center"> console </td><td style="text-align:center"> david </td><td style="text-align:center"> guest</td>
    </tr>
    <tr>
        <td style="text-align:center">john </td><td style="text-align:center"> owner </td><td style="text-align:center"> root </td><td style="text-align:center"> server</td>
    </tr>
    <tr>
        <td style="text-align:center">sql </td><td style="text-align:center"> support </td><td style="text-align:center"> support_388945a0 </td><td style="text-align:center"> sys</td>
    </tr>
    <tr>
        <td style="text-align:center">test2 </td><td style="text-align:center"> test3 </td><td style="text-align:center"> user4 </td><td style="text-align:center"> user5</td>
    </tr>
</table>

## ¿Cuáles son los requisitos de contraseña cuando se crea una VM?
<a id="what-are-the-password-requirements-when-creating-a-vm" class="xliff"></a>
Las contraseñas deben tener entre 12 y 123 caracteres y deben cumplir 3 de estos 4 requisitos de complejidad:

* Deben incluir caracteres en minúsculas.
* Deben incluir caracteres en mayúsculas.
* Deben incluir un dígito.
* Deben incluir un carácter especial (REGEX.MATCH [\W_]).

No se permiten las siguientes contraseñas:

<table>
    <tr>
        <td>abc@123 </td>
        <td>P@$$w0rd </td>
        <td>P@ssw0rd </td>
        <td>P@ssword123 </td>
        <td>Pa$$word </td>
    </tr>
    <tr>
        <td>pass@word1 </td>
        <td>Password! </td>
        <td>Password1 </td>
        <td>Password22 </td>
        <td>iloveyou! </td>
    </tr>
</table>

