---
title: "Seguridad de máquina virtual Linux con Azure Security Center | Microsoft Docs"
description: "Tutorial: Seguridad de máquina virtual con Azure Security Center"
services: virtual-machines-linux
documentationcenter: virtual-machines
author: neilpeterson
manager: timlt
editor: tysonn
tags: azure-service-management
ms.assetid: 
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 05/01/2017
ms.author: nepeters
ms.translationtype: Human Translation
ms.sourcegitcommit: be3ac7755934bca00190db6e21b6527c91a77ec2
ms.openlocfilehash: 4c680ee63e1c2d8e858c725adc42bbcbc49e4045
ms.contentlocale: es-es
ms.lasthandoff: 05/03/2017

---
# <a name="monitor-vm-security-with-the-azure-security-center"></a>Supervisión de seguridad de máquina virtual con Azure Security Center

Azure Security Center le ayuda a tener una mayor visibilidad sobre la configuración de los recursos de Azure relacionados con las prácticas de seguridad. También proporciona supervisión integrada de la seguridad, que puede detectar amenazas que de otro modo podrían pasar inadvertidas. En este tutorial se proporciona una breve introducción a Azure Security Center y se describe como usarlo con máquinas virtuales de Azure.   

## <a name="security-center-overview"></a>Introducción a Security Center

Azure Security Center ayuda a identificar posibles problemas de configuración de las máquinas virtuales y amenazas de seguridad dirigidas. Algunos ejemplos son la identificación de máquinas virtuales con grupos de seguridad de red que faltan, discos descifrados y ataques de RDP por fuerza bruta. Esta información se presenta en el panel de Azure Security Center en gráficos fáciles de leer.

Para acceder al panel de Azure Security Center, haga clic en **Security Center** en el panel de navegación izquierdo del portal de Azure. El panel proporciona una vista de alto nivel del estado de los recursos, las alertas de seguridad y las recomendaciones para la configuración. Desde aquí, puede ver el estado de seguridad de su entorno de Azure, buscar las recomendaciones actuales y ver el estado actual de las alertas de amenaza. Cada uno de estos gráficos de alto nivel se puede expandir con información más detallada sobre el área de enfoque.

![Panel de ASC](./media/tutorial-azure-security/asc-dash.png)

Azure Security Center no solo se encarga de la detección de datos, sino que proporciona recomendaciones para los problemas detectados. Por ejemplo, si una máquina virtual se ha implementado sin un grupo de seguridad de red asociado, se crea una recomendación con pasos para remediarlo. Estas recomendaciones también proporcionan soluciones automáticas sin salir del contexto de Azure Security Center.  

![Recomendaciones](./media/tutorial-azure-security/recommendations.png)

## <a name="configure-data-collection"></a>Configuración de la recopilación de datos

Antes de poder obtener visibilidad sobre las configuraciones de seguridad de las máquinas virtuales, es necesario configurar la recopilación de datos de Azure Security Center. Esto implica habilitar la recopilación de datos y crear una cuenta de almacenamiento de Azure para que contenga los datos recopilados. 

1. En el panel de Azure Security Center, haga clic en **Directiva de seguridad** y seleccione su suscripción. 
2. En **Recopilación de datos**, seleccione *Activado*.
3. Haga clic en **Elija una cuenta de almacenamiento** y cree una nueva cuenta de almacenamiento. Seleccione **Aceptar** cuando haya terminado.
4. Haga clic en **Guardar** en la hoja **Directiva de seguridad**. 

Cuando finaliza esta acción, el agente de recopilación de datos de Azure Security Center se instala en todas las máquinas virtuales y comienza la recopilación de datos. 

## <a name="configure-security-policy"></a>Configuración de la directiva de seguridad

Una directiva de seguridad define los elementos de la directiva de seguridad con respecto a qué datos se recopilan y qué recomendaciones se hacen. De forma predeterminada, los recursos de Azure se evalúan con respecto a todos los elementos de directiva. Se pueden deshabilitar elementos de directiva individuales de forma global para todos los recursos de Azure o se pueden deshabilitar por grupo de recursos. Esta configuración le ofrece la posibilidad de aplicar diferentes directivas de seguridad a diferentes conjuntos de recursos de Azure. Para más información sobre las directivas de seguridad de Azure Security Center, consulte [Establecimiento de directivas de seguridad en Azure Security Center](../../security-center/security-center-policies.md). 

Para configurar una directiva de seguridad para todos los recursos de Azure:

1. En el panel de Azure Security Center, haga clic en **Directiva de seguridad** y luego seleccione su suscripción. 
2. Haga clic en **Directiva de prevención**.
3. Habilite o deshabilite los elementos de directiva que deben aplicarse a todos los recursos de Azure.
4. Pulse **OK** (Aceptar) cuando haya terminado.
5. Haga clic en **Guardar** en la hoja **Directiva de seguridad**. 

Para configurar una directiva para un grupo de recursos específico, siga los mismos pasos; sin embargo, en lugar de seleccionar la suscripción de la hoja de la directiva de seguridad, seleccione un grupo de recursos. Al configurar la directiva, seleccione *Única* en **Herencia**. Si desea deshabilitar la recopilación de datos para un grupo de recursos específico, esta configuración también se puede realizar aquí.

En el ejemplo siguiente, se ha creado una directiva única para el grupo de recursos denominado *myResoureGroup*. En esta directiva, se han deshabilitado las recomendaciones tanto para el cifrado de datos como para el firewall de aplicación web.

![Directiva única](./media/tutorial-azure-security/unique-policy.png)

## <a name="view-vm-configuration-health"></a>Visualización del estado de configuración de una máquina virtual

Después de que se ha habilitado la recopilación de datos y se ha configurado una directiva de seguridad, Azure Security Center comienza a proporcionar alertas y recomendaciones. Cuando se implementan las máquinas virtuales, se instala el agente de recopilación de datos y Azure Security Center se rellena con datos de estas nuevas máquinas virtuales. Para más información sobre el estado de configuración de las máquinas virtuales, consulte [Protección de las máquinas virtuales en Azure Security Center](../../security-center/security-center-virtual-machine-recommendations.md). 

A medida que se recopilan datos, se agrega el estado de recursos de cada máquina virtual y de los recursos de Azure relacionados y esta información se presenta en un gráfico fácil de leer. Para ver el estado de los recursos, vuelva al panel de Azure Security Center. En **Estado de seguridad de recurso**, haga clic en **Compute**. Por último, en la hoja **Compute**, haga clic en **Máquinas virtuales**. Esta vista proporciona un resumen del estado de configuración de todas las máquinas virtuales.

![Estado de proceso](./media/tutorial-azure-security/compute-health.png)

Al seleccionar cada máquina virtual se muestran todas las recomendaciones correspondientes a ella. Las recomendaciones se detallan en la siguiente sección de este tutorial.

## <a name="remediate-configuration-issues"></a>Corrección de los problemas de configuración

Una vez que Azure Security Center comienza a rellenarse con datos de configuración, se realizan recomendaciones con respecto a la directiva de seguridad configurada. Por ejemplo, si se ha configurado una máquina virtual sin un grupo de seguridad de red asociado, se realiza una recomendación para crear uno. Para ver una lista de todas las recomendaciones: 

1. En el panel de Azure Security Center, haga clic en el icono **Recomendaciones**.
3. Seleccione una recomendación concreta; se abre una hoja con una lista de todos los recursos a los que se aplica la recomendación.
4. Seleccione un recurso específico que le gustaría abordar.
5. Siga las instrucciones en pantalla de los pasos de corrección. 

En muchos casos, Azure Security Center proporciona pasos procesables para abordar la recomendación sin salir del contexto de este centro de seguridad. Por ejemplo, en el ejemplo siguiente, se detectó un NSG con una regla de entrada sin restricciones. En esta recomendación, se puede seleccionar el botón **Editar reglas de entrada**, que proporciona la IU necesaria para modificar la regla. 

![Recomendaciones](./media/tutorial-azure-security/remediation.png)

A medida que se corrigen las recomendaciones, se marcan como resueltas. 

## <a name="view-detected-threats"></a>Visualización de amenazas detectadas

Además de las recomendaciones de configuración de recursos, Azure Security Center también proporciona alertas de detección de amenazas. La característica de alerta de seguridad agrega datos recopilados de cada máquina virtual, registros de redes de Azure y soluciones de asociados conectados para detectar las amenazas de seguridad de los recursos de Azure. Para más información sobre las funcionalidades de detección de amenazas de Azure Security Center, consulte [Funcionalidades de detección de Azure Security Center](../../security-center/security-center-detection-capabilities.md).

Para poder usar la característica de alertas de seguridad, es necesario aumentar el plan de tarifa de Azure Security Center de *Gratis* a *Estándar**. Cuando lo haga, dispondrá de una **evaluación gratuita** de 30 días. Para cambiar el plan de tarifa:  

1. En el panel de Azure Security Center, haga clic en **Directiva de seguridad** y luego seleccione su suscripción.
2. Haga clic en **Plan de tarifa**.
3. Seleccione un nuevo nivel y haga clic en **Seleccionar**.
5. Haga clic en **Guardar** en la hoja **Directiva de seguridad**. 

Una vez habilitada, el gráfico de alertas de seguridad se empieza a rellenar cuando se detectan amenazas de seguridad.

![Alertas de seguridad](./media/tutorial-azure-security/security-alerts.png)

Seleccione una alerta para ver información, como una descripción de la amenaza, la hora de detección, los intentos de amenaza y la corrección recomendada. En este ejemplo, se detecta un ataque por fuerza bruta de RDP con 294 intentos de RDP erróneos y se proporciona una solución recomendada.

![Ataque de RDP](./media/tutorial-azure-security/rdp-attack.png)
