---
title: "Administración de directivas de laboratorio en Azure DevTest Labs | Microsoft Docs"
description: "Obtenga información acerca de cómo definir directivas de laboratorio como tamaños de máquina virtual, máximo de máquinas virtuales por usuario y automatización de apagado."
services: devtest-lab,virtual-machines
documentationcenter: na
author: craigcaseyMSFT
manager: douge
editor: 
ms.assetid: 7756aa64-49ca-45a0-9f90-0fd101c7be85
ms.service: devtest-lab
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/03/2017
ms.author: v-craic
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: c2b71fa5ec2935a25b5fb37770dfb5163a286ded
ms.sourcegitcommit: 85012dbead7879f1f6c2965daa61302eb78bd366
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/02/2018
---
# <a name="manage-all-policies-for-a-lab-in-azure-devtest-labs"></a>Administración de todas las directivas para un laboratorio de Azure DevTest Labs

Azure DevTest Labs le permite controlar los costos y desperdiciar lo mínimo posible en sus laboratorios gracias a la posibilidad de administrar políticas (configuración) en cada uno de ellos. Este artículo explica en detalle paso a paso cómo configurar cada directiva.  

## <a name="set-allowed-virtual-machine-sizes"></a>Establecimiento de tamaños de máquina virtual permitidos
La directiva para establecer los tamaños permitidos de la máquina virtual ayuda a minimizar la pérdida del laboratorio al permitirle especificar los tamaños de máquina virtual que se permiten en este. Si se activa esta directiva, los tamaños de máquina virtual de esta lista son los únicos que pueden utilizarse en la creación de tales máquinas.

1. En [Azure Portal](http://go.microsoft.com/fwlink/p/?LinkID=525040), seleccione un laboratorio y, luego, seleccione **Configuración y directivas**.

    ![Acceso a la configuración y directivas del laboratorio](./media/devtest-lab-set-lab-policy/policies-menu.png)

1. En el panel **Configuración y directivas** del laboratorio, seleccione **Allowed virtual machines sizes** (Tamaños de máquinas virtuales permitidas).
   
    ![Allowed virtual machines sizes](./media/devtest-lab-set-lab-policy/allowed-vm-sizes.png)

1. Seleccione **Activado** para habilitar esta directiva, y **Desactivado** para deshabilitarla.

1. Si habilita esta directiva, seleccione uno o varios tamaños de máquina virtual que se pueden crear en el laboratorio.

1. Seleccione **Guardar**.

## <a name="set-virtual-machines-per-user"></a>Establecimiento de máquinas virtuales por usuario
La directiva **Virtual Machines por usuario** le permite especificar el número máximo de máquinas virtuales que puede crear un usuario individual. Si un usuario trata de crear o reclamar una máquina virtual una vez alcanzado el límite, aparece un mensaje de error que indica que la máquina virtual no se puede crear ni exigir. 

1. En el panel **Configuración y directivas** del laboratorio, seleccione **Virtual Machines por usuario**.
   
    ![Máquinas virtuales por usuario](./media/devtest-lab-set-lab-policy/max-vms-per-user.png)

1. Seleccione **Sí** para limitar el número de máquinas virtuales por usuario. Si no quiere limitar el número de máquinas virtuales por usuario, seleccione **No**. Si elige **Sí**, escriba un valor numérico que indique el número máximo de máquinas virtuales que un usuario puede crear o reclamar. 

1. Seleccione **Sí** para limitar el número de máquinas virtuales que puede usar SSD (discos de estado sólido). Si no quiere limitar el número de máquinas virtuales que puede usar SSD, seleccione **No**. Si elige **Sí**, escriba un valor que indique el número máximo de máquinas virtuales que se pueden crear con SSD. 

1. Seleccione **Guardar**.

## <a name="set-virtual-machines-per-lab"></a>Establecimiento de máquinas virtuales por laboratorio
La directiva **Virtual Machines por laboratorio** le permite especificar el número máximo de máquinas virtuales que se pueden crear para el laboratorio actual. Si un usuario intenta crear una máquina virtual una vez alcanzado el límite, aparece un mensaje de error que indica que la máquina virtual no se puede crear. 

1. En el panel **Configuración y directivas** del laboratorio, seleccione **Virtual Machines por laboratorio** .
   
    ![Máquinas virtuales por laboratorio](./media/devtest-lab-set-lab-policy/max-vms-per-lab.png)

1. Seleccione **Sí** para limitar el número de máquinas virtuales por laboratorio. Si no quiere limitar el número de máquinas virtuales por laboratorio, seleccione **No**. Si elige **Sí**, escriba un valor numérico que indique el número máximo de máquinas virtuales que un usuario puede crear o reclamar. 

1. Seleccione **Sí** para limitar el número de máquinas virtuales que puede usar SSD (discos de estado sólido). Si no quiere limitar el número de máquinas virtuales que puede usar SSD, seleccione **No**. Si elige **Sí**, escriba un valor que indique el número máximo de máquinas virtuales que se pueden crear con SSD. 

1. Seleccione **Guardar**.

## <a name="set-auto-shutdown"></a>Establecimiento del apagado automático
La directiva de apagado automático ayuda a minimizar la pérdida del laboratorio, ya que permite especificar la hora de apagado de la máquina virtual de este laboratorio.

1. En el panel **Configuración y directivas** del laboratorio, seleccione **Apagado automático**.
   
    ![Apagado automático](./media/devtest-lab-set-lab-policy/auto-shutdown.png)

1. Seleccione **Activado** para habilitar esta directiva, y **Desactivado** para deshabilitarla.

1. Si habilita esta directiva, especifique la hora local (y la zona horaria) para apagar todas las máquinas virtuales del laboratorio actual.

1. Especifique **Sí** o **No** en la opción de enviar una notificación 15 minutos antes de la hora especificada para el apagado automático. Si elige **Sí**, escriba un punto de conexión de URL del webhook o una dirección de correo electrónico para especificar dónde quiere que se publique o se envíe la notificación. El usuario recibe la notificación y se le ofrece la opción de retrasar el apagado.

   Para obtener más información sobre los webhooks, consulte [Creación de un webhook o una función de API de Azure](../azure-functions/functions-create-a-web-hook-or-api-function.md). 

1. Seleccione **Guardar**.

De manera predeterminada, una vez que se habilite, esta directiva se aplica a todas las máquinas virtuales del laboratorio actual. Para quitar esta configuración de una máquina virtual específica, abra el panel de administración de la máquina virtual y cambie la configuración de **Apagado automático**.

## <a name="set-auto-start"></a>Establecimiento del inicio automático
La directiva de inicio automático le permite especificar cuándo se deben iniciar las máquinas virtuales del laboratorio actual.  

1. En el panel **Configuración y directivas** del laboratorio, seleccione **Inicio automático**.
   
    ![Inicio automático](./media/devtest-lab-set-lab-policy/auto-start.png)

2. Seleccione **Activado** para habilitar esta directiva, y **Desactivado** para deshabilitarla.

3. Si habilita esta directiva, especifique a la hora local de inicio programado, la zona horaria los días de la semana en los que se aplica esta hora. 

4. Seleccione **Guardar**.

Una vez que se habilite, esta directiva no se aplica automáticamente a ninguna máquina virtual del laboratorio actual. Para aplicar esta configuración a una máquina virtual específica, abra el panel de administración de la máquina virtual y cambie su configuración de **Inicio automático**.

## <a name="set-expiration-date"></a>Establecimiento de la fecha de expiración
Puede establecer una fecha de expiración cuando [cree la VM](devtest-lab-add-vm.md). En **Configuración avanzada**, elija el icono del calendario para especificar una fecha en la que la máquina virtual se eliminará automáticamente. De forma predeterminada, la máquina virtual nunca expirará.

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## <a name="next-steps"></a>pasos siguientes
Una vez que defina y aplique las distintas configuraciones de las directivas de máquina virtual correspondientes al laboratorio, puede intentar algunos de los siguientes pasos:

* [Direcciones IP compartidas](devtest-lab-shared-ip.md); explica cómo las direcciones IP compartidas se usan en DevTest Labs para minimizar el número de direcciones IP públicas necesarias para conectarse a las máquinas virtuales del laboratorio.
* [Configuración de la administración de costos](devtest-lab-configure-cost-management.md): muestra cómo utilizar el gráfico **Tendencia de costo estimado mensual**  
  para ver el costo estimado hasta la fecha del mes de calendario actual, así como el costo previsto para fin de mes.
* [Creación de una imagen personalizada](devtest-lab-create-template.md): cuando cree una máquina virtual, deberá especificar una base, que puede ser una imagen personalizada o una imagen de Marketplace. Este artículo muestra cómo crear una imagen personalizada desde un archivo VHD.
* [Configuración de imágenes de Marketplace](devtest-lab-configure-marketplace-images.md): Azure DevTest Labs admite la creación de máquinas virtuales basadas en imágenes de Azure Marketplace. En este artículo se muestra cómo especificar las imágenes de Azure Marketplace, si corresponde, que se pueden usar en el momento de crear máquinas virtuales en un laboratorio.
* [Creación de una máquina virtual en un laboratorio](devtest-lab-add-vm.md): se muestra cómo crear una máquina virtual desde una imagen base (ya sea personalizada o de Marketplace) y cómo trabajar con artefactos en la máquina virtual.

