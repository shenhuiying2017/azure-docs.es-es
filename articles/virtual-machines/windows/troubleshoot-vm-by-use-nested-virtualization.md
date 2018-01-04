---
title: "Solución de problemas de una máquina virtual de Azure mediante la virtualización anidada en Azure | Microsoft Docs"
description: "Cómo solucionar problemas de una máquina virtual de Azure mediante la virtualización anidada en Azure"
services: virtual-machines-windows
documentationcenter: 
author: glimoli
manager: timlt
editor: 
tags: azure-resource-manager
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 11/06/2017
ms.author: genli
ms.openlocfilehash: b50d139f62ef773fb5b9bb2638e40f8154e80979
ms.sourcegitcommit: 3f33787645e890ff3b73c4b3a28d90d5f814e46c
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/03/2018
---
# <a name="troubleshoot-a-problem-azure-vm-by-using-nested-virtualization-in-azure"></a>Solución de problemas de una máquina virtual de Azure mediante la virtualización anidada en Azure

En este artículo se muestra cómo crear un entorno de virtualización anidada en Microsoft Azure con el fin de que pueda montar el disco de la máquina virtual con problemas en el host de Hyper-V (máquina virtual de recuperación) y solucionar los problemas que puedan surgir.

## <a name="prerequisite"></a>Requisito previo

Para montar la máquina virtual con problemas, la máquina virtual de recuperación debe cumplir los siguientes requisitos previos:

-   La máquina virtual de recuperación debe estar en la misma ubicación que la máquina virtual con problemas.

-   La máquina virtual de recuperación debe estar en el mismo grupo de recursos que la máquina virtual con problemas.

-   La máquina virtual de recuperación debe usar el mismo tipo de cuenta de almacenamiento (Estándar o Premium) que la máquina virtual con problemas.

## <a name="step-1-create-a-recovery-vm-and-install-hyper-v-role"></a>Paso 1: Creación de una máquina virtual de recuperación e instalación del rol de Hyper-V

1.  Cree una nueva máquina virtual de recuperación:

    -  Sistema operativo: Windows Server 2016 Datacenter

    -  Tamaño: cualquier serie V3 con al menos dos núcleos que admitan la virtualización anidada. Para obtener más información, consulte la sección [Introducción a los nuevos tamaños Dv3 y Ev3](https://azure.microsoft.com/blog/introducing-the-new-dv3-and-ev3-vm-sizes/).

    -  La ubicación, la cuenta de almacenamiento y el grupo de recursos deben ser los mismos que los de la máquina virtual con problemas.

    -  Seleccione el mismo tipo de almacenamiento que la máquina virtual con problemas (Estándar o Premium).

2.  Una vez creada la máquina virtual de recuperación, establezca una conexión de escritorio remoto con la máquina virtual de recuperación.

3.  En Administrador del servidor, seleccione **Administrar** > **Agregar roles y características**.

4.  En la sección **Tipo de instalación**, seleccione **Instalación basada en características o en roles**.

5.  En la sección **Seleccionar servidor de destino**, asegúrese de que la máquina virtual de recuperación esté seleccionada.

6.  Seleccione **Rol de Hyper-V** > **Agregar características**.

7.  Seleccione **Siguiente** en la sección **Características**.

8.  Si hay un conmutador virtual disponible, selecciónelo. De lo contrario, seleccione **Siguiente**.

9.  En la sección **Migración**, seleccione **Siguiente**

10. En la sección **Almacenes predeterminados**, seleccione **Siguiente**.

11. Marque la casilla para reiniciar automáticamente el servidor si es necesario.

12. Seleccione **Instalar**.

13. Permita que el servidor instale el rol de Hyper-V. Esta operación tarda unos minutos y el servidor se reiniciará automáticamente.

## <a name="step-2-create-the-problem-vm-on-the-recovery-vms-hyper-v-server"></a>Paso 2: Creación de la máquina virtual con problemas en el servidor de Hyper-V de la máquina virtual de recuperación

1.  Registre el nombre del disco en la máquina virtual con problemas y, a continuación, elimínela. Asegúrese de conservar todos los discos conectados. 

2.  Conecte el disco del sistema operativo de la máquina virtual con problemas como un disco de datos de la máquina virtual de recuperación.

    1.  Una vez eliminada la máquina virtual con problemas, vaya a la máquina virtual de recuperación.

    2.  Seleccione **Discos** y, a continuación, **Agregar disco de datos**.

    3.  Seleccione el disco de la máquina virtual con problemas y, a continuación, **Guardar**.

3.  Después de haber conectado correctamente el disco, establezca una conexión de escritorio remoto con la máquina virtual de recuperación.

4.  Abra Administración de discos (diskmgmt.msc). Asegúrese de que el disco de la máquina virtual con problemas esté establecido en **Sin conexión**.

5.  Abra el Administrador de Hyper-V y, en **Administrador del servidor**, seleccione **Rol de Hyper-V**. Haga clic con el botón derecho en el servidor y, a continuación, seleccione **Administrador de Hyper-V**.

6.  En Administrador de Hyper-V, haga clic con el botón derecho en la máquina virtual de recuperación y, a continuación, seleccione **Nuevo** > **Máquina virtual** > **Siguiente**.

7.  Escriba un nombre para la máquina virtual y, a continuación, seleccione **Siguiente**.

8.  Seleccione **Generación 1**.

9.  Establezca la memoria de inicio en 1024 MB o más.

10. Si procede, seleccione el conmutador de red de Hyper-V que se ha creado. De lo contrario, vaya a la página siguiente.

11. Seleccione **Conectar un disco duro virtual más adelante**.

    ![Imagen de la opción Conectar un disco duro virtual más adelante](./media/troubleshoot-vm-by-use-nested-virtualization/attach-disk-later.png)

12. Seleccione **Finalizar** cuando se haya creado la máquina virtual.

13. Haga clic con el botón derecho en la máquina virtual que ha creado y, a continuación, seleccione **Configuración**.

14. Seleccione **Controlador IDE 0** y **Unidad de disco duro** y, a continuación, haga clic en **Agregar**.

    ![Imagen sobre cómo agregar una nueva unidad de disco duro](./media/troubleshoot-vm-by-use-nested-virtualization/create-new-drive.png)    

15. En **Disco duro físico**, seleccione el disco de la máquina virtual con problemas que ha conectado a la máquina virtual de Azure. Si no ve ningún disco en la lista, compruebe si el disco se ha establecido como sin conexión mediante Administración de discos.

    ![Imagen sobre al montaje del disco](./media/troubleshoot-vm-by-use-nested-virtualization/mount-disk.png)  


17. Seleccione **Aplicar** y luego **Aceptar**.

18. Haga doble clic en la máquina virtual y, a continuación, iníciela.

19. Ahora puede trabajar con la máquina virtual como máquina virtual local. Puede seguir los pasos para solucionar problemas que sean necesarios.

## <a name="step-3-re-create-your-azure-vm-in-azure"></a>Paso 3: Nueva creación de la máquina virtual de Azure en Azure

1.  Cuando la máquina virtual vuelva a estar en línea, apáguela en el administrador de Hyper-V.

2.  Vaya a [Azure Portal](https://portal.azure.com), seleccione Máquina virtual de recuperación > Discos y copie el nombre del disco. Utilizará el nombre en el paso siguiente. Desconecte el disco fijo de la máquina virtual de recuperación.

3.  Vaya a **Todos los recursos**, busque el nombre del disco y, a continuación, seleccione el disco.

     ![Imagen sobre la búsqueda del disco](./media/troubleshoot-vm-by-use-nested-virtualization/search-disk.png)     

4. Haga clic en **Crear máquina virtual**.

     ![Imagen sobre la creación de la máquina virtual desde el disco](./media/troubleshoot-vm-by-use-nested-virtualization/create-vm-from-vhd.png) 

También puede utilizar Azure PowerShell para crear la máquina virtual desde el disco. Para obtener más información, consulte cómo [crear la nueva máquina virtual desde un disco existente mediante PowerShell](create-vm-specialized.md#create-the-new-vm). 

## <a name="next-steps"></a>pasos siguientes

Si tiene problemas para conectarse a la máquina virtual, consulte [Solución de problemas de conexiones RDP a una máquina virtual de Azure](troubleshoot-rdp-connection.md). Para problemas con el acceso a aplicaciones que se ejecutan en su máquina virtual, consulte [Solucionar problemas de conectividad de aplicaciones en una máquina virtual Windows](troubleshoot-app-connection.md).
