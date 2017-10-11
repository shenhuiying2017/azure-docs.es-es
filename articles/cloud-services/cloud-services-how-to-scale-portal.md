---
title: "Escalar automáticamente un servicio en la nube en el portal | Microsoft Docs"
description: "Obtenga información sobre cómo usar el portal para configurar reglas de escalado automático de un rol de trabajo o un rol web de servicio en la nube en Azure."
services: cloud-services
documentationcenter: 
author: Thraka
manager: timlt
editor: 
ms.assetid: 701d4404-5cc0-454b-999c-feb94c1685c0
ms.service: cloud-services
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/18/2017
ms.author: adegeo
ms.openlocfilehash: e9683d4c5779450fd67fa42ab13095c7f201b4cd
ms.sourcegitcommit: 18ad9bc049589c8e44ed277f8f43dcaa483f3339
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 08/29/2017
---
# <a name="how-to-configure-auto-scaling-for-a-cloud-service-in-the-portal"></a>Procedimiento para configurar el escalado automático para un servicio en la nube en el Portal
> [!div class="op_single_selector"]
> * [Portal de Azure](cloud-services-how-to-scale-portal.md)
> * [Portal de Azure clásico](cloud-services-how-to-scale.md)

Las condiciones se pueden definir para un rol de trabajo de servicio en la nube que desencadene una operación de reducción horizontal y de escalabilidad horizontal. Las condiciones del rol pueden basarse en la CPU, el disco o la carga de red del rol. También puede establecer una condición basada en una cola de mensajes o en la métrica de algún otro recurso de Azure asociado a la suscripción.

> [!NOTE]
> Este artículo se centra en los roles de trabajo y en los roles web de Servicio en la nube. Al crear una máquina virtual (clásica) directamente, esta se hospeda en un servicio en la nube. Puede escalar una máquina virtual estándar asociándola con un [conjunto de disponibilidad](../virtual-machines/windows/classic/configure-availability.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json) y activándola o desactivándola manualmente.

## <a name="considerations"></a>Consideraciones
Debe considerar la siguiente información antes de configurar el escalado para su aplicación:

* El uso de núcleos afecta el escalado.

    Las instancias de rol más grandes usan más núcleos. Solo puede escalar una aplicación dentro del límite de núcleos para su suscripción. Por ejemplo, supongamos que su suscripción tiene un límite de 20 núcleos. Si ejecuta una aplicación con dos servicios en la nube de tamaño mediano (un total de 4 núcleos), solo puede escalar verticalmente otras implementaciones del servicio en la nube en su suscripción con los 16 núcleos restantes. Para obtener más información sobre los tamaños, consulte [Tamaños de los servicios en la nube](cloud-services-sizes-specs.md).

* Puede realizar una operación de escalabilidad basada en un umbral de mensajes de cola. Para obtener más información sobre las colas, consulte [Introducción al Almacenamiento en cola de Azure mediante .NET](../storage/queues/storage-dotnet-how-to-use-queues.md).

* También puede escalar otros recursos asociados a la suscripción.

* Para permitir una alta disponibilidad de la aplicación, debe asegurarse de que se implemente con dos o más instancias de rol. Para obtener más información, consulte [Contratos de nivel de servicio](https://azure.microsoft.com/support/legal/sla/).


## <a name="where-scale-is-located"></a>Ubicación de la escala
Después de seleccionar el servicio en la nube, debe tener visible la hoja del servicio en la nube.

1. En la hoja del servicio en la nube, seleccione el nombre del servicio en la nube en el icono de **Roles e instancias** .   
   **IMPORTANTE**: No se olvide de hacer clic en el rol del servicio en la nube, no en la instancia de rol que está debajo de dicho rol.

    ![](./media/cloud-services-how-to-scale-portal/roles-instances.png)
2. Seleccione el icono de **escala** .

    ![](./media/cloud-services-how-to-scale-portal/scale-tile.png)

## <a name="automatic-scale"></a>Escala automática
Puede configurar la configuración de escala de un rol con estos dos modos: **Manual** o **Automático**. Con el modo Manual, como cabe de esperar, establece el número absoluto de instancias. Sin embargo, con Automático, se pueden establecer reglas que rijan cómo se debe realizar la operación de escala y en qué medida.

Establezca la opción **Escalar por** para **programar las reglas de rendimiento y programación**.

![Configuración de escala de servicios en la nube con el perfil y la regla](./media/cloud-services-how-to-scale-portal/schedule-basics.png)

1. Un perfil existente.
2. Agregue una regla para el perfil principal.
3. Agregue otro perfil.

Seleccione **Agregar perfil**. El perfil determina qué modo va a utilizar para realizar la operación de escala: **siempre**, **periodicidad** y **fecha fija**.

Después de haber configurado el perfil y ñas reglas, seleccione el icono de **Guardar** situado en la parte superior.

#### <a name="profile"></a>Perfil
El perfil establece instancias mínimas y máximas para la escala, además de cuándo está activo el intervalo de escala.

* **Siempre**

    Mantenga siempre disponible este rango de instancias.  

    ![Servicio en la nube en el que siempre se realiza la operación de escala](./media/cloud-services-how-to-scale-portal/select-always.png)
* **Periodicidad**

    Elija un conjunto de días de la semana para realizar la escala.

    ![Escala de servicio en la nube con una programación de periodicidad](./media/cloud-services-how-to-scale-portal/select-recurrence.png)
* **Fecha fija**

    Un intervalo de fechas fijo para escalar el rol.

    ![Escala de servicio en la nube con una fecha fija](./media/cloud-services-how-to-scale-portal/select-fixed.png)

Después de haber configurado el perfil, seleccione el botón **Aceptar** situado en la parte inferior de la hoja de perfil.

#### <a name="rule"></a>Regla
Las reglas se agregan a un perfil y representan una condición que desencadenará la escala.

El desencadenador de reglas se basa en una métrica del servicio en la nube (uso de CPU o actividad del disco o de la red) a la que puede agregar un valor condicional. Además, puede basar la acción desencadenadora en una cola de mensajes o en la métrica de algún otro recurso de Azure asociado a la suscripción.

![](./media/cloud-services-how-to-scale-portal/rule-settings.png)

Después de haber configurado la regla, seleccione el botón **Aceptar** situado en la parte inferior de la hoja de la regla.

## <a name="back-to-manual-scale"></a>Regreso al paso de escala manual
Vaya a la [configuración de escala](#where-scale-is-located) y establezca el valor de la opción **Escalar por** en **un recuento de instancias que especifique manualmente**.

![Configuración de escala de servicios en la nube con el perfil y la regla](./media/cloud-services-how-to-scale-portal/manual-basics.png)

Con esta configuración se elimina la escala automática del rol y podrá establecer el recuento de instancias directamente.

1. La opción de escala (manual o automática).
2. Un regulador de instancias de rol para definir las instancias en las que se realizará la operación de escala.
3. Las instancias del rol en las que se realizar la operación de escala.

Después de configurar la configuración de escala, seleccione el icono de **Guardar** situado en la parte superior.
