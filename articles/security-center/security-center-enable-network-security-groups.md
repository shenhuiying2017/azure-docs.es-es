---
title: Habilitación de grupos de seguridad de red en Azure Security Center | Microsoft Docs
description: En este documento se muestra cómo implementar la recomendación **Habilitar los grupos de seguridad de red** de Azure Security Center.
services: security-center
documentationcenter: na
author: TerryLanfear
manager: MBaldwin
editor: ''
ms.assetid: f53ed853-ffaf-4530-a019-1906ba6f341b
ms.service: security-center
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/02/2017
ms.author: terrylan
ms.openlocfilehash: 3c0ad4a0e1a5f4f2fd6def4f29599e2e55eb1a9d
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/20/2018
ms.locfileid: "34364461"
---
# <a name="enable-network-security-groups-in-azure-security-center"></a>Habilitación de los grupos de seguridad de red en Azure Security Center
Azure Security Center recomienda habilitar un grupo de seguridad de red (NSG) si no hay ninguno habilitado. Los NSG contienen una lista de reglas de la lista de control de acceso (ACL) que permiten o deniegan el tráfico de red a sus instancias de máquina virtual en una red virtual. Los NSG se pueden asociar con las subredes o las instancias individuales de máquina virtual dentro de esa subred. Cuando un NSG está asociado a una subred, las reglas de la ACL se aplican a todas las instancias de la máquina virtual de esa subred. Además, el tráfico que se llega a una máquina virtual se puede restringir aún más, para lo que se debe asociar un NSG directamente a dicha máquina virtual. Para más información, consulte [¿Qué es un grupo de seguridad de red?](../virtual-network/security-overview.md)

Si no tiene habilitados los NSG, Security Center presenta dos recomendaciones: habilitar grupos de seguridad de red en subredes y habilitar grupos de seguridad de red en máquinas virtuales. Elija el nivel, subred o máquina virtual donde aplicar el NSG.

> [!NOTE]
> En este documento se presenta el servicio mediante una implementación de ejemplo.  No se trata de una guía paso a paso.
>
>

## <a name="implement-the-recommendation"></a>Implementación de la recomendación
1. En la hoja **Recomendaciones**, seleccione **Habilitar los grupos de seguridad de red** en subredes o en máquinas virtuales.
   ![Habilitar los grupos de seguridad de red][1]
2. Se abrirá la hoja **Configurar los grupos de seguridad de red que faltan** para las subredes o para las máquinas virtuales, según la recomendación que seleccionó. Seleccione una subred o una máquina virtual en la que configurar un NSG.

   ![Configurar NSG para subred][2]

   ![Configurar NSG para máquina virtual][3]
3. En la hoja **Elegir grupo de seguridad de red** seleccione un NSG existente o seleccione la opción **Crear nuevo** para crear un NSG.

   ![Elegir grupo de seguridad de red][4]

Si crea un nuevo NSG, siga los pasos de [Cómo administrar grupos de seguridad de red con Azure Portal](../virtual-network/virtual-networks-create-nsg-arm-pportal.md) para crear un NSG y establecer las reglas de seguridad.

## <a name="see-also"></a>Otras referencias
En este artículo se ha mostrado cómo implementar la recomendación de Security Center "Habilitar los grupos de seguridad de red" para las subredes o las máquinas virtuales. Para más información sobre la habilitación de NSG, consulte los siguientes recursos:

* [¿Qué es un grupo de seguridad de red?](../virtual-network/security-overview.md)
* [Administrar un grupo de seguridad de red](../virtual-network/manage-network-security-group.md)

Para más información sobre el Centro de seguridad, consulte los siguientes recursos:

* [Establecimiento de directivas de seguridad en Azure Security Center](security-center-policies.md): aprenda a configurar directivas de seguridad para las suscripciones y los grupos de recursos de Azure.
* [Administración de recomendaciones de seguridad en Azure Security Center](security-center-recommendations.md): recomendaciones que le ayudan a proteger los recursos de Azure.
* [Supervisión del estado de seguridad en Azure Security Center](security-center-monitoring.md): obtenga información sobre cómo supervisar el mantenimiento de los recursos de Azure.
* [Administración y respuesta a las alertas de seguridad en Azure Security Center](security-center-managing-and-responding-alerts.md): obtenga información sobre cómo administrar y responder a alertas de seguridad.
* [Supervisión de las soluciones de asociados con Azure Security Center](security-center-partner-solutions.md): aprenda a supervisar el estado de mantenimiento de las soluciones de asociados.
* [Preguntas más frecuentes sobre Azure Security Center](security-center-faq.md): busque las preguntas más frecuentes sobre cómo usar el servicio.
* [Blog de seguridad de Azure](http://blogs.msdn.com/b/azuresecurity/): obtenga las últimas noticias e información sobre la seguridad en Azure.

<!--Image references-->
[1]: ./media/security-center-enable-nsg/enable-nsg.png
[2]:./media/security-center-enable-nsg/configure-nsg-for-subnet.png
[3]: ./media/security-center-enable-nsg/configure-nsg-for-vm.png
[4]: ./media/security-center-enable-nsg/choose-nsg.png
