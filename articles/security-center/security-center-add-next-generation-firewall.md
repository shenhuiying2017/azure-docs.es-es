---
title: "Adición de un firewall de próxima generación en Azure Security Center | Microsoft Docs"
description: "En este documento se muestra cómo implementar la recomendación de Azure Security Center de agregar un firewall de próxima generación y enrutar el tráfico solo a través de NGFW."
services: security-center
documentationcenter: na
author: TerryLanfear
manager: MBaldwin
editor: 
ms.assetid: 48b99015-4db8-4ce8-85e4-b544c0fa203e
ms.service: security-center
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/02/2017
ms.author: terrylan
ms.openlocfilehash: 30589d0a943517c03394a3aae7c03c8094e78c1f
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/11/2017
---
# <a name="add-a-next-generation-firewall-in-azure-security-center"></a>Adición de un firewall de próxima generación en Azure Security Center
Azure Security Center puede recomendarle agregar un firewall de próxima generación (NGFW) de un asociado de Microsoft para aumentar la protección. Este documento le ofrece un ejemplo de cómo hacerlo:

> [!NOTE]
> En este documento se presenta el servicio mediante una implementación de ejemplo.  No se trata de una guía paso a paso.
>
>

## <a name="implement-the-recommendation"></a>Implementación de la recomendación
1. En la hoja **Recomendaciones**, seleccione **Agregar un firewall de última generación**.
   ![Add a Next Generation Firewall][1]
2. En la hoja **Add a Next Generation Firewall** (Agregar un firewall de próxima generación), seleccione un punto de conexión.
   ![Selección de un punto de conexión][2]
3. Se abrirá una segunda hoja **Add a Next Generation Firewall** (Agregar un firewall de próxima generación). Puede elegir usar una solución existente, si está disponible, o puede crear una nueva. En este ejemplo no hay ninguna solución existente disponible, por lo que vamos a crear un NGFW.
   ![Creación de un firewall de próxima generación][3]
4. Para crear un NGFW, seleccione una solución de la lista de asociados integrados. En este ejemplo se selecciona **Punto de comprobación**.
   ![Selección de una solución de firewall de próxima generación][4]
5. Se abrirá la hoja **Punto de comprobación** , que le proporcionará información sobre la solución del asociado. Seleccione **Crear** en la hoja de información.
   ![Hoja de información del firewall][5]
6. Se abrirá la hoja **Crear máquina virtual** . Aquí puede escribir la información que se requiere para poner en marcha una máquina virtual que ejecuta el NGFW. Siga los pasos y proporcione la información de NGFW que sea necesaria. Seleccione Aceptar para que se aplique.
   ![Creación de una máquina virtual para ejecutar un NGFW][6]

## <a name="route-traffic-through-ngfw-only"></a>Enrutar el tráfico solo a través de NGFW
Vuelva a la hoja **Recomendaciones** . Después de agregar un NGFW a través de Security Center, se genera una nueva entrada llamada **Enrutar el tráfico solo a través de un firewall de nueva generación**. Esta recomendación solo se crea si ha instalado el NGFW a través de Security Center. Si tiene puntos de conexión accesibles desde Internet, Security Center recomienda configurar reglas de grupos de seguridad de red que fuercen a que el tráfico entrante pase a la máquina virtual a través del NGFW.

1. En la hoja **Recomendaciones**, seleccione **Enrutar el tráfico solo a través de NGFW**.
   ![Enrutar el tráfico solo a través de NGFW][7]
2. Se abrirá la hoja **Enrutar el tráfico solo a través de un firewall de nueva generación** que enumera las máquinas virtuales a las que puede enrutar el tráfico. Seleccione una máquina virtual de la lista.
   ![Seleccionar una máquina virtual][8]
3. Se abre una hoja de la máquina virtual seleccionada, que muestra las reglas de entrada relacionadas. Una descripción proporciona más información sobre los posibles pasos posteriores. Seleccione **Editar reglas de entrada** para continuar con la edición de una regla de entrada. Se espera que el **Origen** no esté establecido en **Cualquiera** para los puntos de conexión accesibles desde Internet vinculados al NGFW. Para obtener más información sobre las propiedades de la regla de entrada, consulte [Reglas de grupo de seguridad de red](../virtual-network/virtual-networks-nsg.md#nsg-rules).
   ![Configuración de reglas para limitar el acceso][9]
   ![Editar regla de entrada][10]

## <a name="see-also"></a>Consulte también
En este documento se muestra cómo implementar la recomendación de Azure Security Center de agregar un firewall de próxima generación. Para obtener más información sobre los NGF y las soluciones de punto de comprobación de asociados, consulte los siguientes recursos:

* [Firewall de próxima generación](https://en.wikipedia.org/wiki/Next-Generation_Firewall)
* [Check Point vSEC](https://azure.microsoft.com/marketplace/partners/checkpoint/check-point-r77-10/)

Para más información sobre el Centro de seguridad, consulte los siguientes recursos:

* [Establecimiento de directivas de seguridad en Azure Security Center](security-center-policies.md) : aprenda a configurar directivas de seguridad.
* [Administración de recomendaciones de seguridad en Azure Security Center](security-center-recommendations.md) : recomendaciones que le ayudan a proteger los recursos de Azure.
* [Supervisión del estado de seguridad en Azure Security Center](security-center-monitoring.md): obtenga información sobre cómo supervisar el mantenimiento de los recursos de Azure.
* [Administración y respuesta a las alertas de seguridad en Azure Security Center](security-center-managing-and-responding-alerts.md): obtenga información sobre cómo administrar y responder a alertas de seguridad.
* [Supervisión de las soluciones de asociados con Azure Security Center](security-center-partner-solutions.md): aprenda a supervisar el estado de mantenimiento de las soluciones de asociados.
* [Preguntas más frecuentes sobre Azure Security Center](security-center-faq.md) : busque las preguntas más frecuentes sobre cómo usar el servicio.
* [Blog de seguridad de Azure](http://blogs.msdn.com/b/azuresecurity/) : encuentre publicaciones de blog sobre el cumplimiento y la seguridad de Azure.

<!--Image references-->
[1]: ./media/security-center-add-next-gen-firewall/add-next-gen-firewall.png
[2]: ./media/security-center-add-next-gen-firewall/select-an-endpoint.png
[3]: ./media/security-center-add-next-gen-firewall/create-new-next-gen-firewall.png
[4]: ./media/security-center-add-next-gen-firewall/select-next-gen-firewall.png
[5]: ./media/security-center-add-next-gen-firewall/firewall-solution-info-blade.png
[6]: ./media/security-center-add-next-gen-firewall/create-virtual-machine.png
[7]: ./media/security-center-add-next-gen-firewall/route-traffic-through-ngfw.png
[8]: ./media/security-center-add-next-gen-firewall/select-vm.png
[9]: ./media/security-center-add-next-gen-firewall/configure-rules-to-limit-access.png
[10]: ./media/security-center-add-next-gen-firewall/edit-inbound-rule.png
