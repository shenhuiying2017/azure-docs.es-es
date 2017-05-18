---
title: "Generalización de una máquina virtual Windows para su uso en Azure | Microsoft Docs"
description: "Aprenda a usar Sysprep para generalizar una máquina virtual Windows para usar con el modelo de implementación de Resource Manager."
services: virtual-machines-windows
documentationcenter: 
author: cynthn
manager: timlt
editor: tysonn
tags: azure-resource-manager
ms.assetid: a745d400-c8be-48b4-a891-4a18495ef3fd
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 05/11/2017
ms.author: cynthn
ms.translationtype: Human Translation
ms.sourcegitcommit: c308183ffe6a01f4d4bf6f5817945629cbcedc92
ms.openlocfilehash: 3be9805bfd1a71df1ff761531406adcbb7bbe612
ms.contentlocale: es-es
ms.lasthandoff: 05/17/2017


---
# <a name="generalize-a-windows-virtual-machine-using-sysprep"></a>Generalización de una máquina virtual Windows con Sysprep
En esta sección se muestra cómo generalizar la máquina virtual Windows para usarla como imagen. Entre otras características, Sysprep elimina toda la información personal de la cuenta y prepara, entre otras cosas, la máquina para usarse como imagen. Para obtener más información sobre Sysprep, vea [Uso de Sysprep: Introducción](http://technet.microsoft.com/library/bb457073.aspx).

Asegúrese de que los roles de servidor que se ejecutan en la máquina sean compatibles con Sysprep. Para más información, consulte [Sysprep Support for Server Roles](https://msdn.microsoft.com/windows/hardware/commercialize/manufacture/desktop/sysprep-support-for-server-roles)

> [!IMPORTANT]
> Si ejecuta Sysprep antes de cargar el VHD en Azure por primera vez, asegúrese de que tiene [preparada la máquina virtual](prepare-for-upload-vhd-image.md) antes de ejecutar Sysprep. 
> 
> 

1. Inicie sesión en la máquina virtual de Windows.
2. Abra una ventana del símbolo del sistema como administrador. Cambie el directorio a **%windir%\system32\sysprep** y, después, ejecute `sysprep.exe`.
3. En **Herramienta de preparación del sistema**, seleccione **Iniciar la Configuración rápida (OOBE)** y asegúrese de que la casilla **Generalizar** está activada.
4. En **Opciones de apagado**, seleccione **Apagar**.
5. Haga clic en **Aceptar**.
   
    ![Iniciar Sysprep](./media/upload-generalized-managed/sysprepgeneral.png)
6. Cuando Sysprep finaliza, apaga la máquina virtual. 

> [!IMPORTANT]
> No reinicie la máquina virtual hasta que haya terminado de cargar el VHD en Azure o de crear una imagen de la máquina virtual. Si la máquina virtual se reinicia accidentalmente, ejecute Sysprep para generalizarla de nuevo.
> 
> 

## <a name="next-steps"></a>Pasos siguientes
* Si la máquina virtual es local, ahora puede [cargar el VHD en Azure](upload-image.md).
* Si la máquina virtual ya está en Azure, ahora puede [crear una imagen de la máquina virtual generalizada](capture-image.md).


