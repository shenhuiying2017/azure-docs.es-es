---
title: "Restricción del acceso a través de puntos de conexión accesibles desde Internet en Azure Security Center | Microsoft Docs"
description: "En este documento se muestra cómo implementar la recomendación de Azure Security Center de restringir el acceso a través de puntos de conexión accesibles desde Internet."
services: security-center
documentationcenter: na
author: TerryLanfear
manager: MBaldwin
editor: 
ms.assetid: 727d88c9-163b-4ea0-a4ce-3be43686599f
ms.service: security-center
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/03/2017
ms.author: terrylan
ms.openlocfilehash: f7309c617f1705205e2c9f1b1b48d141391d45da
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="restrict-access-through-internet-facing-endpoints-in-azure-security-center"></a>Restricción del acceso a través de puntos de conexión accesibles desde Internet en Azure Security Center
Azure Security Center recomendará restringir el acceso a través de puntos de conexión accesibles desde Internet si alguno de los grupos de seguridad de red (NSG) tiene una o varias reglas de entrada que permitan el acceso desde cualquier dirección IP de origen. Al abrir el acceso a cualquier IP, los atacantes pueden lograr acceder a los recursos. Azure Security Center recomienda editar estas reglas de entrada para restringir el acceso a las direcciones IP de origen que realmente necesiten acceder.

Esta recomendación se genera para cualquier puerto no web que tenga la opción Cualquiera como origen.

> [!NOTE]
> En este documento se presenta el servicio mediante una implementación de ejemplo. No se trata de una guía paso a paso.
>
>

## <a name="implement-the-recommendation"></a>Implementación de la recomendación
1. En la hoja **Recomendaciones**, seleccione **Restringir el acceso a través de un punto de conexión accesible desde Internet**.

   ![Restricción del acceso a través de puntos de conexión accesibles desde Internet][1]
2. Se abrirá la hoja **Restrict access through Internet facing endpoint**(Restringir el acceso a través de puntos de conexión accesibles desde Internet). Esta hoja enumera las máquinas virtuales (VM) con reglas de entrada que generan un posible problema de seguridad. Seleccione una máquina virtual.

   ![Seleccionar una máquina virtual][2]
3. La hoja **NSG** muestra la información de los grupos de seguridad de red, las reglas de entrada relacionadas y la máquina virtual asociada. Seleccione **Editar reglas de entrada** para continuar con la edición de una regla de entrada.

   ![Hoja Grupo de seguridad de red][3]
4. En la hoja **Reglas de seguridad de entrada** , seleccione la regla de entrada que va a editar. En este ejemplo, vamos a seleccionar **AllowWeb**.

   ![Reglas de seguridad de entrada][4]

   Tenga en cuenta que también puede seleccionar **Reglas predeterminadas** para ver el conjunto de reglas predeterminadas que contiene todos los NSG. No se pueden eliminar las reglas predeterminadas, pero como que tienen asignada la prioridad mínima, pueden reemplazarse por las reglas que cree. Obtenga más información sobre [reglas predeterminadas](../virtual-network/virtual-networks-nsg.md#default-rules).

   ![Reglas predeterminadas][5]
5. En la hoja **AllowWeb**, edite las propiedades de la regla de entrada para que el **origen** sea una dirección IP o un bloque de direcciones IP. Para obtener más información sobre las propiedades de la regla de entrada, consulte [Reglas de grupo de seguridad de red](../virtual-network/virtual-networks-nsg.md#nsg-rules).

   ![Editar regla de entrada][6]

## <a name="see-also"></a>Consulte también
En este artículo se muestra cómo implementar la recomendación de Azure Security Center de restringir el acceso a través de puntos de conexión accesibles desde Internet. Para obtener más información sobre cómo habilitar NSG y reglas, consulte los siguientes recursos:

* [¿Qué es un grupo de seguridad de red?](../virtual-network/virtual-networks-nsg.md)
* [Cómo administrar grupos de seguridad de red con el Portal de Azure](../virtual-network/virtual-networks-create-nsg-arm-pportal.md)

Para más información sobre el Centro de seguridad, consulte los siguientes recursos:

* [Establecimiento de directivas de seguridad en Azure Security Center](security-center-policies.md): aprenda a configurar directivas de seguridad para las suscripciones y los grupos de recursos de Azure.
* [Administración de recomendaciones de seguridad en Azure Security Center](security-center-recommendations.md): recomendaciones que lo ayudan a proteger los recursos de Azure.
* [Supervisión del estado de seguridad en Centro de seguridad de Azure](security-center-monitoring.md): obtenga información sobre cómo supervisar el estado de los recursos de Azure.
* [Administración y respuesta a las alertas de seguridad en el Centro de seguridad de Azure](security-center-managing-and-responding-alerts.md): obtenga información sobre cómo administrar y responder a alertas de seguridad.
* [Supervisión de las soluciones de asociados con Azure Security Center](security-center-partner-solutions.md) : aprenda a supervisar el estado de mantenimiento de las soluciones de asociados.
* [Preguntas más frecuentes acerca del Centro de seguridad de Azure](security-center-faq.md): busque las preguntas más frecuentes sobre cómo usar el servicio.
* [Blog de seguridad de Azure](http://blogs.msdn.com/b/azuresecurity/): obtenga las últimas noticias e información sobre la seguridad en Azure.

<!--Image references-->
[1]: ./media/security-center-restrict-access-thru-internet-facing-endpoint/restrict-access-thru-internet-facing-endpoint.png
[2]: ./media/security-center-restrict-access-thru-internet-facing-endpoint/select-a-vm.png
[3]: ./media/security-center-restrict-access-thru-internet-facing-endpoint/network-security-group-blade.png
[4]: ./media/security-center-restrict-access-thru-internet-facing-endpoint/inbound-security-rules.png
[5]: ./media/security-center-restrict-access-thru-internet-facing-endpoint/default-rules.png
[6]: ./media/security-center-restrict-access-thru-internet-facing-endpoint/edit-inbound-rule.png
