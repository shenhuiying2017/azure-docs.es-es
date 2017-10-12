---
title: "Administración de directivas de laboratorio básicas en Azure DevTest Labs | Microsoft Docs"
description: "Obtenga información sobre cómo establecer algunas de las directivas básicas (configuración) para un laboratorio de DevTest Labs."
services: devtest-lab,virtual-machines
documentationcenter: na
author: tomarcher
manager: douge
editor: 
ms.assetid: 
ms.service: devtest-lab
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/29/2017
ms.author: tarcher
ms.openlocfilehash: ed35d081b191ec41ed9e5970515057a4715c0d59
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="manage-basic-policies-for-a-lab-in-azure-devtest-labs"></a>Administración de directivas básicas para un laboratorio de Azure DevTest Labs

Azure DevTest Labs permite controlar los costos y desperdiciar lo mínimo posible en sus laboratorios gracias a la posibilidad de administrar políticas (configuración) en cada uno de ellos. En este artículo, empezará a trabajar con las directivas. Aprenderá a establecer dos de las directivas más importantes: limitar el número de máquinas virtuales que puede crear o reclamar un solo usuario, y configurar el apagado automático. Para ver cómo establecer todas las directivas de laboratorio, consulte el artículo [Definición de directivas de laboratorio en Azure DevTest Labs](devtest-lab-set-lab-policy.md).  

## <a name="accessing-a-labs-policies-in-azure-devtest-labs"></a>Acceso a las directivas de laboratorio en Azure DevTest Labs
Los siguientes pasos le guiarán a través de la configuración de directivas para un laboratorio en Azure DevTest Labs:

Para ver (y cambiar) las directivas de un laboratorio, siga estos pasos:

1. Inicie sesión en el [Portal de Azure](http://go.microsoft.com/fwlink/p/?LinkID=525040).

1. Seleccione **Más servicios** y, luego, **DevTest Labs** en la lista.

1. En la lista de laboratorios, seleccione el laboratorio que desee.   

1. Seleccione **Configuration and policies** (Directivas y configuración).

    ![Hoja de configuración de directiva](./media/devtest-lab-set-lab-policy/policies-menu.png)

1. La hoja **Configuration and policies** (Directivas y configuración) contiene un menú de configuración donde puede especificar parámetros. Este artículo trata solo la configuración de **máquinas virtuales por usuario** y el **apagado automático**. Para obtener información sobre las opciones restantes, vea [Definición de directivas de laboratorio en Azure DevTest Labs](./devtest-lab-set-lab-policy.md). 
   
## <a name="set-virtual-machines-per-user"></a>Establecimiento de máquinas virtuales por usuario
La directiva de **Máquinas virtuales por usuario** le permite especificar el número máximo de máquinas virtuales que puede crear un usuario individual. Si un usuario trata de crear o reclamar una máquina virtual una vez alcanzado el límite, aparece un mensaje de error que indica que la máquina virtual no se puede crear ni exigir. 

1. En el menú **Configuration and policies** (Directivas y configuración) del laboratorio, seleccione **Máquinas virtuales por usuario**.
   
    ![Máquinas virtuales por usuario](./media/devtest-lab-set-lab-policy/max-vms-per-user.png)

1. Seleccione **Sí** para limitar el número de máquinas virtuales por usuario. Si no quiere limitar el número de máquinas virtuales por usuario, seleccione **No**. Si elige **Sí**, escriba un valor numérico que indique el número máximo de máquinas virtuales que un usuario puede crear o reclamar. 

1. Seleccione **Sí** para limitar el número de máquinas virtuales que puede usar SSD (discos de estado sólido). Si no quiere limitar el número de máquinas virtuales que puede usar SSD, seleccione **No**. Si elige **Sí**, escriba un valor que indique el número máximo de máquinas virtuales que se pueden crear con SSD. 

1. Seleccione **Guardar**.

## <a name="set-auto-shutdown"></a>Establecimiento del apagado automático
La directiva de apagado automático ayuda a minimizar la pérdida del laboratorio, ya que permite especificar la hora de apagado de la máquina virtual de este laboratorio.

1. En la hoja **Configuration and Policies** (Directivas y configuración) del laboratorio, seleccione **Apagado automático**.
   
    ![Apagado automático](./media/devtest-lab-set-lab-policy/auto-shutdown.png)

1. Seleccione **Activado** para habilitar esta directiva, y **Desactivado** para deshabilitarla.

1. Si habilita esta directiva, especifique la hora local (y la zona horaria) para apagar todas las máquinas virtuales del laboratorio actual.

1. Especifique **Sí** o **No** en la opción de enviar una notificación 15 minutos antes de la hora especificada para el apagado automático. Si selecciona **Sí**, escriba un punto de conexión de URL de webhooks para recibir la notificación. Para obtener más información sobre los webhooks, consulte [Creación de un webhook o una función de API de Azure](../azure-functions/functions-create-a-web-hook-or-api-function.md). 

1. Seleccione **Guardar**.

    De manera predeterminada, una vez que se habilite, esta directiva se aplica a todas las máquinas virtuales del laboratorio actual. Para quitar esta configuración de una máquina virtual específica, abra la hoja de la máquina virtual y cambie la configuración de **Apagado automático** . 

## <a name="set-auto-start"></a>Establecimiento del inicio automático
La directiva de inicio automático le permite especificar cuándo se deben iniciar las máquinas virtuales del laboratorio actual.  

1. En la hoja **Configuration and Policies** (Directivas y configuración) del laboratorio, seleccione **Inicio automático**.
   
    ![Inicio automático](./media/devtest-lab-set-lab-policy/auto-start.png)

2. Seleccione **Activado** para habilitar esta directiva, y **Desactivado** para deshabilitarla.

3. Si habilita esta directiva, especifique a la hora local de inicio programado, la zona horaria los días de la semana en los que se aplica esta hora. 

4. Seleccione **Guardar**.

    Una vez que se habilite, esta directiva no se aplica automáticamente a ninguna máquina virtual del laboratorio actual. Para aplicar esta configuración a una máquina virtual específica, abra la hoja de la máquina virtual y cambie su configuración de **Inicio automático** . 

## <a name="next-steps"></a>Pasos siguientes

- [Definición de directivas de laboratorio en Azure DevTest Labs](devtest-lab-set-lab-policy.md) (información sobre cómo modificar otras directivas de laboratorio) 
