---
title: "Diagnósticos de arranque de máquinas virtuales Linux en Azure | Microsoft Docs"
description: "Introducción a las dos características de depuración para máquinas virtuales Linux en Azure"
services: virtual-machines-linux
documentationcenter: virtual-machines-linux
author: Deland-Han
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-linux
ms.workload: infrastructure
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 08/21/2017
ms.author: delhan
ms.openlocfilehash: 70254d39b5c6326166f7e29fdfc99533835502f9
ms.sourcegitcommit: 3f33787645e890ff3b73c4b3a28d90d5f814e46c
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/03/2018
---
# <a name="how-to-use-boot-diagnostics-to-troubleshoot-linux-virtual-machines-in-azure"></a>Instrucciones de uso de diagnósticos de arranque para solucionar problemas de máquinas virtuales Linux en Azure

Ahora se ofrece en Azure compatibilidad con dos características de depuración, Salida de la consola y Captura de pantalla, para el modelo de implementación de Resource Manager en Azure Virtual Machines. 

Cuando lleva su propia imagen a Azure o incluso cuando arranca una de las imágenes de la plataforma, hay muchas razones por las que una máquina virtual puede entrar en un estado que no permita el arranque. Estas características le permiten diagnosticar y recuperar fácilmente las máquinas virtuales de los errores de arranque.

Para las máquinas virtuales Linux, puede ver fácilmente la salida de su registro de consola desde el portal:

![Azure Portal](./media/boot-diagnostics/screenshot1.png)
 
Sin embargo, para las máquinas virtuales Windows y Linux, Azure también le permite ver una captura de pantalla de la máquina virtual desde el hipervisor:

![Error](./media/boot-diagnostics/screenshot2.png)

Ambas características son compatibles para Azure Virtual Machines en todas las regiones. Tenga en cuenta que las capturas de pantalla y la salida pueden tardar hasta 10 minutos en aparecer en la cuenta de almacenamiento.

## <a name="common-boot-errors"></a>Errores comunes de arranque

- [Problemas del sistema de archivos](https://blogs.msdn.microsoft.com/linuxonazure/2016/09/13/linux-recovery-cannot-ssh-to-linux-vm-due-to-file-system-errors-fsck-inodes/)
- [Problemas de kernel](https://blogs.msdn.microsoft.com/linuxonazure/2016/10/09/linux-recovery-manually-fixing-non-boot-issues-related-to-kernel-problems/)
- [Errores de FSTAB](https://blogs.msdn.microsoft.com/linuxonazure/2016/07/21/cannot-ssh-to-linux-vm-after-adding-data-disk-to-etcfstab-and-rebooting/ )

## <a name="enable-diagnostics-on-a-new-virtual-machine"></a>Habilitación del diagnóstico en una nueva máquina virtual
1. Cuando cree una máquina virtual desde la versión preliminar del portal, seleccione **Azure Resource Manager** en la lista desplegable de modelo de implementación:
 
    ![Resource Manager](./media/boot-diagnostics/screenshot3.jpg)

2. Configure la opción Supervisión para seleccionar la cuenta de almacenamiento donde desee colocar estos archivos de diagnóstico.
 
    ![Creación de una máquina virtual](./media/boot-diagnostics/screenshot4.jpg)

3. Si va a realizar la implementación con una plantilla de Azure Resource Manager, vaya al recurso de máquina virtual y anexe la sección de perfil de diagnóstico. Recuerde usar el encabezado de versión de API "2015-06-15".

    ```json
    {
          "apiVersion": "2015-06-15",
          "type": "Microsoft.Compute/virtualMachines",
          … 
    ```

4. El perfil de diagnóstico le permite seleccionar la cuenta de almacenamiento donde desea colocar estos registros.

    ```json
            "diagnosticsProfile": {
                "bootDiagnostics": {
                "enabled": true,
                "storageUri": "[concat('http://', parameters('newStorageAccountName'), '.blob.core.windows.net')]"
                }
            }
            }
        }
    ```

## <a name="update-an-existing-virtual-machine"></a>Actualización de una máquina virtual existente

Para habilitar el diagnóstico de arranque a través del portal, también puede actualizar una máquina virtual existente en el portal. Seleccione la opción Diagnósticos de arranque y guarde. Reinicie la máquina virtual para que surta efecto.

![Actualización de una máquina virtual existente](./media/boot-diagnostics/screenshot5.png)