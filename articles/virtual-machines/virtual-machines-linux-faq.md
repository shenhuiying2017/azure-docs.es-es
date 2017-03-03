---
title: "Preguntas más frecuentes sobre VM de Linux en Azure | Microsoft Docs"
description: "Proporciona respuestas a algunas de las preguntas frecuentes sobre las máquinas virtuales de Linux creadas con el modelo de Resource Manager."
services: virtual-machines-linux
documentationcenter: 
author: cynthn
manager: timlt
editor: 
tags: azure-resource-management
ms.assetid: 3648e09c-1115-4818-93c6-688d7a54a353
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 08/16/2016
ms.author: cynthn
translationtype: Human Translation
ms.sourcegitcommit: 233116deaaaf2ac62981453b05c4a5254e836806
ms.openlocfilehash: 78b476b31f030fba4034dc1f499b020d244e8288
ms.lasthandoff: 01/31/2017


---
# <a name="frequently-asked-question-about-linux-virtual-machines"></a>Preguntas frecuentes sobre las máquinas virtuales de Linux
En este artículo se responden algunas preguntas comunes que los usuarios plantean sobre las máquinas virtuales Linux creadas en Azure mediante el modelo de implementación de Resource Manager. Para ver la versión de Windows de este tema, consulte [Preguntas más frecuentes sobre máquinas virtuales Windows](virtual-machines-windows-faq.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

## <a name="what-can-i-run-on-an-azure-vm"></a>¿Qué puedo ejecutar en una máquina virtual de Azure?
Todos los suscriptores pueden ejecutar software de servidor en una máquina virtual de Azure. Para más información, consulte [Linux en distribuciones aprobadas por Azure](virtual-machines-linux-endorsed-distros.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

## <a name="how-much-storage-can-i-use-with-a-virtual-machine"></a>¿Cuánto almacenamiento puedo usar con una máquina virtual?
Cada disco de datos puede ser de hasta 1 TB. El número de discos de datos que puede usar depende del tamaño de la máquina virtual. Para obtener más información, consulte [Tamaños de máquinas virtuales](virtual-machines-linux-sizes.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

Una cuenta de almacenamiento de Azure proporciona almacenamiento para el disco del sistema operativo y los discos de datos. Cada disco es un archivo .vhd almacenado como un blob en páginas. Para obtener información detallada sobre los precios, consulte [Detalles de precios de almacenamiento](https://azure.microsoft.com/pricing/details/storage/).

## <a name="how-can-i-access-my-virtual-machine"></a>¿Cómo puedo tener acceso a mi máquina virtual?
Establezca una conexión remota para iniciar sesión en la máquina virtual mediante Secure Shell (SSH). Consulte las instrucciones sobre cómo conectarse [desde Windows](virtual-machines-linux-ssh-from-windows.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) o [desde Linux y Mac](virtual-machines-linux-mac-create-ssh-keys.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). De forma predeterminada, SSH permite un máximo de 10 conexiones simultáneas. Puede aumentar este número editando el archivo de configuración.

Si tiene problemas, consulte [Solución de problemas de conexiones de Secure Shell (SSH)](virtual-machines-linux-troubleshoot-ssh-connection.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

## <a name="can-i-use-the-temporary-disk-devsdb1-to-store-data"></a>¿Puedo usar el disco temporal (/dev/sdb1) para almacenar datos?
No utilice el disco temporal (/dev/sdb1) para almacenar datos. Solo existe para el almacenamiento temporal. Corre el riesgo de perder datos que no se podrán recuperar.

## <a name="can-i-copy-or-clone-an-existing-azure-vm"></a>¿Puedo copiar o clonar una máquina virtual de Azure existente?
Sí. Para obtener instrucciones, consulte [Creación de una copia de una máquina virtual Linux en el modelo de implementación de Resource Manager](virtual-machines-linux-copy-vm.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

## <a name="why-am-i-not-seeing-canada-central-and-canada-east-regions-through-azure-resource-manager"></a>¿Por qué no veo las regiones de Canadá central y Canadá oriental por medio de Azure Resource Manager?
Las dos nuevas áreas Canadá central y Canadá oriental no se registran automáticamente para la creación de máquinas virtuales en las suscripciones de Azure existentes. Este registro se realizará automáticamente cuando se implementa una máquina virtual mediante el Portal de Azure en cualquier otra región usando Azure Resource Manager. Después de implementar una máquina virtual en cualquier otra región de Azure, las áreas nuevas deberán estar disponibles para las máquinas virtuales siguientes.

## <a name="can-i-add-a-nic-to-my-vm-after-its-created"></a>¿Puedo agregar una NIC a mi máquina virtual después de crearla?
No. Solo se puede agregar una NIC en el momento de la creación.

## <a name="are-there-any-computer-name-requirements"></a>¿Hay algún requisito de nombre de equipo?
Sí. El nombre del equipo puede tener un máximo de 64 caracteres. Consulte [Directrices de nomenclatura de la infraestructura](virtual-machines-linux-infrastructure-naming-guidelines.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) para obtener más información sobre la nomenclatura de sus recursos.

## <a name="what-are-the-username-requirements-when-creating-a-vm"></a>¿Cuáles son los requisitos de nombre de usuario cuando se crea una VM?
Los nombres de usuario deben tener entre 1 y 64 caracteres.

No se permiten los siguientes nombres de usuario:

<table>
    <tr>
        <td style="text-align:center">administrator </td><td style="text-align:center"> admin </td><td style="text-align:center"> user </td><td style="text-align:center"> user1</td>
    </tr>
    <tr>
        <td style="text-align:center">test </td><td style="text-align:center"> user2 </td><td style="text-align:center"> test1 </td><td style="text-align:center"> user3</td>
    </tr>
    <tr>
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


## <a name="what-are-the-password-requirements-when-creating-a-vm"></a>¿Cuáles son los requisitos de contraseña cuando se crea una VM?
Las contraseñas deben tener entre 6 y 72 caracteres y deben cumplir 3 de estos 4 requisitos de complejidad:

* Deben incluir caracteres en minúsculas.
* Deben incluir caracteres en mayúsculas.
* Deben incluir un dígito.
* Deben incluir un carácter especial (REGEX.MATCH [\W_]).

No se permiten las siguientes contraseñas:

<table>
    <tr>
        <td style="text-align:center">abc@123</td>
        <td style="text-align:center">P@$$w0rd</td>
        <td style="text-align:center">P@ssw0rd</td>
        <td style="text-align:center">P@ssword123</td>
        <td style="text-align:center">Pa$$word</td>
    </tr>
    <tr>
        <td style="text-align:center">pass@word1</td>
        <td style="text-align:center">Password!</td>
        <td style="text-align:center">Password1</td>
        <td style="text-align:center">Password22</td>
        <td style="text-align:center">iloveyou!</td>
    </tr>
</table>

