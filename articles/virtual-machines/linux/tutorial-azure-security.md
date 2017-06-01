---
title: "Azure Security Center y máquinas virtuales Linux | Microsoft Docs"
description: "Obtenga información sobre la seguridad para máquinas virtuales Linux de Azure con Azure Security Center."
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
ms.date: 05/07/2017
ms.author: nepeters
ms.custom: mvc
ms.translationtype: Human Translation
ms.sourcegitcommit: c308183ffe6a01f4d4bf6f5817945629cbcedc92
ms.openlocfilehash: 222cb9629e50e49ce08e0737d7f2570e9187317a
ms.contentlocale: es-es
ms.lasthandoff: 05/17/2017

---
# <a name="monitor-virtual-machine-security-by-using-azure-security-center"></a>Supervisión de la seguridad de máquinas virtuales con Azure Security Center

Azure Security Center puede ayudarlo a obtener una mayor visibilidad de las prácticas de seguridad de los recursos de Azure. Security Center ofrece una característica de supervisión de la seguridad integrada que puede detectar las amenazas que podrían pasar desapercibidas. En este tutorial, aprenderá a usar Azure Security Center y a lo siguiente:
 
> [!div class="checklist"]
> * Configurar la recopilación de datos
> * Configurar directivas de seguridad
> * Ver y corregir problemas de estado de configuraciones
> * Revisar las amenazas detectadas  

## <a name="security-center-overview"></a>Introducción a Security Center

Security Center identifica posibles problemas de configuración de máquinas virtuales y amenazas de seguridad dirigidas. Entre ellas se incluyen las máquinas virtuales que no tienen grupos de seguridad de red, discos sin cifrar y ataques de Protocolo de escritorio remoto (RDP por fuerza bruta). Esta información se presenta en el panel de Security Center en forma de gráficos fáciles de leer.

Para acceder al panel de Security Center, desde Azure Portal, en el menú, seleccione **Security Center**. Desde ahí, puede ver el estado de seguridad de su entorno de Azure, buscar las recomendaciones actuales y ver el estado actual de las alertas de amenaza. Puede expandir cada gráfico de resumen ver más detalles.

![Panel de Security Center](./media/tutorial-azure-security/asc-dash.png)

Security Center no solo se limita a detectar datos con el objetivo de proporcionar recomendaciones para los problemas que identifica. Por ejemplo, si se implementa una máquina virtual sin un grupo de seguridad de red conectado, Security Center mostrará una recomendación con los pasos de corrección que puede aplicar. Obtenga la corrección automatizada sin salir del contexto de Security Center.  

![Recomendaciones](./media/tutorial-azure-security/recommendations.png)

## <a name="set-up-data-collection"></a>Configurar la recopilación de datos

Antes de poder tener visibilidad de las configuraciones de seguridad de máquinas virtuales, hay que configurar la recopilación de datos de Security Center. Para ello, se requiere habilitar la recopilación de datos y crear una cuenta de almacenamiento de Azure para que contenga los datos recopilados. 

1. En el panel de Security Center, haga clic en **Directiva de seguridad** y, luego, seleccione su suscripción. 
2. En **Recopilación de datos**, seleccione **Activado**.
3. Para crear una cuenta de almacenamiento, seleccione **Elija una cuenta de almacenamiento**. Después, seleccione **Aceptar**.
4. En la hoja **Directiva de seguridad**, seleccione **Guardar**. 

Después, se instala el agente de recopilación de datos de Security Center en todas las máquinas virtuales y se inicia la recopilación de datos. 

## <a name="set-up-a-security-policy"></a>Configuración de una directiva de seguridad

Las directivas de seguridad se utilizan para definir los elementos para los que Security Center recopila los datos y hace recomendaciones. Esta configuración ofrece la posibilidad de aplicar diferentes directivas de seguridad a distintos conjuntos de recursos de Azure. Aunque de forma predeterminada los recursos de Azure se evalúan con respecto a todos los elementos de directiva, puede desactivar elementos específicos de la directiva para todos los recursos de Azure o para uno solo. Para obtener más información sobre las directivas de seguridad de Security Center, consulte [Establecimiento de directivas de seguridad en Azure Security Center](../../security-center/security-center-policies.md). 

Para configurar una directiva de seguridad para todos los recursos de Azure, siga estos pasos:

1. En el panel de Security Center, haga clic en **Directiva de seguridad** y, luego, seleccione su suscripción.
2. Seleccione **Directiva de prevención**.
3. Active o desactive los elementos de directiva que desea aplicar a todos los recursos de Azure.
4. Cuando haya terminado de seleccionar la configuración, haga clic en **Aceptar**.
5. En la hoja **Directiva de seguridad**, seleccione **Guardar**. 

Para configurar una directiva para un grupo de recursos específico, siga estos pasos:

1. En el panel de Security Center, haga clic en **Directiva de seguridad** y, luego, seleccione un grupo de recursos.
2. Seleccione **Directiva de prevención**.
3. Active o desactive los elementos de directiva que desea aplicar al grupo de recursos.
4. En **HERENCIA**, seleccione **Único**.
5. Cuando haya terminado de seleccionar la configuración, haga clic en **Aceptar**.
6. En la hoja **Directiva de seguridad**, seleccione **Guardar**.  

También puede desactivar la recopilación de datos para un grupo de recursos específico en esta página.

En el ejemplo siguiente, se ha creado una directiva única para un grupo de recursos denominado "*myResoureGroup*". En esta directiva, se han desactivado las recomendaciones tanto para el cifrado de disco como para el Firewall de aplicaciones web.

![Directiva única](./media/tutorial-azure-security/unique-policy.png)

## <a name="view-vm-configuration-health"></a>Visualización del estado de configuración de una máquina virtual

Después de que se haya activado la recopilación de datos y establecido una directiva de seguridad, Security Center empezará a proporcionar alertas y recomendaciones. A medida que se implementan las máquinas virtuales, se instala el agente de recopilación de datos. Después, Security Center se rellena con datos de las nuevas máquinas virtuales. Para obtener información detallada sobre el estado de configuración de las máquinas virtuales, consulte [Protección de las máquinas virtuales en Azure Security Center](../../security-center/security-center-virtual-machine-recommendations.md). 

A medida que se recopilan datos, se agrega el estado de recursos de cada máquina virtual y de los recursos de Azure relacionados y esta información se presenta en un gráfico fácil de leer. 

Para ver el estado de los recursos, siga estos pasos:

1.  En el panel de Security Center, en **Resource security health** (Estado de seguridad de los recursos), seleccione **Compute**. 
2.  En la hoja **Compute**, seleccione **Máquinas virtuales**. Esta vista proporciona un resumen del estado de configuración de todas las máquinas virtuales.

![Estado de Compute](./media/tutorial-azure-security/compute-health.png)

Para ver todas las recomendaciones de una máquina virtual, seleccione esa máquina virtual. Las recomendaciones y la corrección se tratan con más detalle en la siguiente sección de este tutorial.

## <a name="remediate-configuration-issues"></a>Corrección de los problemas de configuración

Una vez que Security Center comienza a rellenarse con datos de configuración, se realizan recomendaciones en función de la directiva de seguridad configurada. Por ejemplo, si se ha configurado una máquina virtual sin un grupo de seguridad de red asociado, se realiza una recomendación para crear uno. 

Para ver una lista de todas las recomendaciones: 

1. En el panel de Security Center, seleccione **Recomendaciones**.
2. Seleccione una recomendación concreta. Se mostrará una lista de todos los recursos a los que se aplica la recomendación.
3. Para aplicar una recomendación, seleccione un recurso concreto. 
4. Siga las instrucciones de los pasos de corrección. 

En muchos casos, Security Center proporciona pasos accionables para abordar la recomendación sin salir del contexto de Security Center. En el ejemplo siguiente, Security Center detecta un grupo de seguridad de red que tiene una regla de entrada sin restricciones. En la página de la recomendación, puede seleccionar el botón **Editar reglas de entrada**. Se mostrará la interfaz de usuario necesaria para modificar la regla. 

![Recomendaciones](./media/tutorial-azure-security/remediation.png)

A medida que se corrigen las recomendaciones, se marcan como resueltas. 

## <a name="view-detected-threats"></a>Visualización de amenazas detectadas

Además de las recomendaciones de configuración de recursos, Security Center también muestra alertas de detección de amenazas. La característica de alerta de seguridad agrega datos recopilados de cada máquina virtual, registros de redes de Azure y soluciones de asociados conectados para detectar las amenazas de seguridad de los recursos de Azure. Para obtener más información sobre las funcionalidades de detección de amenazas de Security Center, consulte [Funcionalidades de detección de Azure Security Center](../../security-center/security-center-detection-capabilities.md).

Para poder usar la característica de alertas de seguridad, es necesario aumentar el plan de tarifa de Security Center de *Gratis* a *Estándar*. Si se pasa a este plan de tarifa superior, podrá disfrutar de una **evaluación gratuita** de 30 días. 

Para cambiar el plan de tarifa:  

1. En el panel de Security Center, haga clic en **Directiva de seguridad** y, luego, seleccione su suscripción.
2. Seleccione **Plan de tarifa**.
3. Elija el nuevo plan y haga clic en **Seleccionar**.
4. En la hoja **Directiva de seguridad**, seleccione **Guardar**. 

Cuando haya cambiado el plan de tarifa, el gráfico de alertas de seguridad se empieza a rellenar cuando se detectan amenazas de seguridad.

![Alertas de seguridad](./media/tutorial-azure-security/security-alerts.png)

Seleccione una alerta para ver información. Por ejemplo, puede ver una descripción de la amenaza, el tiempo de detección, todos los intentos de amenaza y la corrección recomendada. En el ejemplo siguiente, se detectó un ataque de RDP por fuerza bruta con 294 intentos fallidos de RDP. Se proporciona una solución recomendada.

![Ataque de RDP](./media/tutorial-azure-security/rdp-attack.png)

## <a name="next-steps"></a>Pasos siguientes
En este tutorial, configurará Azure Security Center y, luego, revisó las máquinas virtuales de Security Center. Ha aprendido a:

> [!div class="checklist"]
> * Configurar la recopilación de datos
> * Configurar directivas de seguridad
> * Ver y corregir problemas de estado de configuraciones
> * Revisar las amenazas detectadas

Avance hasta el siguiente tutorial para obtener más información cómo crear una canalización de CI/CD con Jenkins, GitHub y Docker.

> [!div class="nextstepaction"]
> [Creación de una infraestructura de CI/CD con Jenkins, GitHub y Docker](tutorial-jenkins-github-docker-cicd.md)


