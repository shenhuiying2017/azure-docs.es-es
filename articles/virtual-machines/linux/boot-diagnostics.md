---
title: Diagnósticos de arranque de máquinas virtuales Linux en Azure | Microsoft Docs
description: Introducción a las dos características de depuración para máquinas virtuales Linux en Azure
services: virtual-machines-linux
documentationcenter: virtual-machines-linux
author: Deland-Han
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.workload: infrastructure
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 03/19/2018
ms.author: delhan
ms.openlocfilehash: 0183da348a515787d9382df6db3df8524d584d93
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 04/05/2018
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

- [Problemas del sistema de archivos](https://support.microsoft.com/help/3213321/linux-recovery-cannot-ssh-to-linux-vm-due-to-file-system-errors-fsck) 
- [Problemas de kernel](https://support.microsoft.com/help/4091524/how-recovery-azure-linux-vm-from-kernel-related-boot-related-issues/) 
- [Errores de FSTAB](https://support.microsoft.com/help/3206699/azure-linux-vm-cannot-start-because-of-fstab-errors)

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

## <a name="next-steps"></a>Pasos siguientes

Si ve el error "No se pudo obtener el contenido del registro" en diagnósticos de arranque de la máquina virtual, consulte [No se pudo obtener el contenido del registro en diagnósticos de arranque de la máquina virtual](https://support.microsoft.com/help/4094480/failed-to-get-contents-of-the-log-error-in-vm-boot-diagnostics-in-azur).