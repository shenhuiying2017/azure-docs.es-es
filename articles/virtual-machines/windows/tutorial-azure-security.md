---
title: "Azure Security Center y máquinas virtuales Windows en Azure | Microsoft Docs"
description: "Obtenga información acerca de la seguridad de máquinas virtuales Windows en Azure con Azure Security Center."
services: virtual-machines-windows
documentationcenter: virtual-machines
author: neilpeterson
manager: timlt
editor: tysonn
tags: azure-service-management
ms.assetid: 
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 05/01/2017
ms.author: nepeters
ms.custom: mvc
ms.openlocfilehash: adb00e28b0b204858a763f83836ee2ac96f8f9e4
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="monitor-virtual-machine-security-by-using-azure-security-center"></a>Supervisión de la seguridad de máquinas virtuales mediante Azure Security Center

Azure Security Center puede ayudarle a conocer mejor los procedimientos de seguridad de los recursos de Azure. Security Center ofrece una supervisión integrada de la seguridad. que puede detectar las amenazas que podrían pasar desapercibidas. En este tutorial, aprenderá no solo a usar Azure Security Center, sino también a:
 
> [!div class="checklist"]
> * Configuración de una recolección de datos
> * Configurar directivas de seguridad
> * Ver y corregir problemas de estado de la configuración
> * Revisar las amenazas que se detecten  

## <a name="security-center-overview"></a>Introducción a Security Center

Security Center identifica posibles problemas de configuración de máquinas virtuales (VM) y amenazas de seguridad dirigidas. Aquí se incluyen las máquinas virtuales que no tienen grupos de seguridad de red, los discos sin cifrar y ataques de RDP por fuerza bruta. Esta información se muestra en el panel de Security Center en forma de gráficos fáciles de leer.

Para acceder a dicho panel, en Azure Portal, en el menú, seleccione **Security Center**. Desde ahí, puede ver el estado de la seguridad de su entorno de Azure, buscar las recomendaciones actuales y ver el estado actual de las alertas de amenaza. Todos los gráficos de alto nivel se pueden expandir para ver más detalles.

![Panel de Security Center](./media/tutorial-azure-security/asc-dash.png)

Security Center no se limita a la detección de datos para proporcionar recomendaciones para solucionar los problemas que identifica. Por ejemplo, si se implementa una máquina virtual sin un grupo de seguridad de red conectado, Security Center mostrará una recomendación con los pasos que se pueden dar para aplicarla. Obtenga la corrección automatizada sin salir del contexto de Security Center.  

![Recomendaciones](./media/tutorial-azure-security/recommendations.png)

## <a name="set-up-data-collection"></a>Configuración de una recolección de datos

Para poder ver las configuraciones de seguridad de máquinas virtuales, es preciso configurar la recolección de datos de Security Center, lo que implica habilitar la recolección de datos y crear una cuenta de Azure Storage que contenga los datos recopilados. 

1. En el panel de Security Center, haga clic en **Directiva de seguridad** y, luego, seleccione su suscripción. 
2. En **Recopilación de datos**, seleccione **Activado**.
3. Para crear una cuenta de almacenamiento, seleccione **Elija una cuenta de almacenamiento**. Después, seleccione **Aceptar**.
4. En la hoja **Directiva de seguridad**, seleccione **Guardar**. 

El agente de recopilación de datos de Security Center se instala en todas las máquinas virtuales y se inicia la recolección de datos. 

## <a name="set-up-a-security-policy"></a>Configuración de una directiva de seguridad

Las directivas de seguridad se utilizan para definir los elementos para los que Security Center recopila datos y hace recomendaciones. Se pueden aplicar diferentes directivas de seguridad a distintos conjuntos de recursos de Azure. Aunque de forma predeterminada los recursos de Azure se evalúan con respecto a todos los elementos de la directiva, es posible desactivar elementos individuales de la directiva tanto para todos los recursos de Azure como para un solo grupo de recursos. Para más información acerca de las directivas de seguridad de Security Center, consulte [Establecimiento de directivas de seguridad en Azure Security Center](../../security-center/security-center-policies.md). 

Para configurar una directiva de seguridad para todos los recursos de Azure, siga estos pasos:

1. En el panel de Security Center, haga clic en **Directiva de seguridad** y, luego, seleccione su suscripción.
2. Seleccione **Prevention policy** (Directiva de prevención).
3. Active o desactive los elementos de la directiva que desea aplicar a todos los recursos de Azure.
4. Cuando haya terminado de seleccionar la configuración, haga clic en **Aceptar**.
5. En la hoja **Directiva de seguridad**, seleccione **Guardar**. 

Para configurar una directiva para un grupo de recursos concreto, siga estos pasos:

1. En el panel de Security Center, haga clic en **Directiva de seguridad** y, después, seleccione un grupo de recursos.
2. Seleccione **Prevention policy** (Directiva de prevención).
3. Active o desactive los elementos de la directiva que desea aplicar al grupo de recursos.
4. En **INHERITANCE** (HERENCIA), seleccione **Unique** (Único).
5. Cuando haya terminado de seleccionar la configuración, haga clic en **Aceptar**.
6. En la hoja **Directiva de seguridad**, seleccione **Guardar**.  

En esta página también se puede desactivar la recopilación de datos de un grupo de recursos concreto.

En el ejemplo siguiente, se ha creado una directiva única para un grupo de recursos denominado *myResoureGroup*. En esta directiva, se han desactivado las recomendaciones tanto para el cifrado de disco como para el Firewall de aplicaciones web.

![Directiva única](./media/tutorial-azure-security/unique-policy.png)

## <a name="view-vm-configuration-health"></a>Visualización del estado de configuración de una máquina virtual

Después de que haya activado la recolección de datos y establecido una directiva de seguridad, Security Center empieza a proporcionar alertas y recomendaciones. Cuando se implementan las máquinas virtuales, se instala el agente de recopilación de datos. Después, Security Center se rellena con datos de las nuevas máquinas virtuales. Para obtener información detallada acerca del estado de configuración de las máquinas virtuales, consulte [Protección de las máquinas virtuales en Azure Security Center](../../security-center/security-center-virtual-machine-recommendations.md). 

A medida que se recopilan datos, se agrega el estado de los recursos de cada máquina virtual y de los recursos de Azure relacionados, y esta información se presenta en un gráfico fácil de leer. 

Para ver el estado de los recursos, siga estos pasos:

1.  En el panel de Security Center, en **Resource security health** (Estado de seguridad de los recursos), seleccione **Compute**. 
2.  En la hoja **Compute**, seleccione **Virtual machines** (Máquinas virtuales). Esta vista proporciona un resumen del estado de configuración de todas las máquinas virtuales.

![Estado de Compute](./media/tutorial-azure-security/compute-health.png)

Para ver todas las recomendaciones existentes para una máquina virtual, seleccione la máquina en cuestión. Las recomendaciones y las correcciones se tratan con más detalle en la siguiente sección de este tutorial.

## <a name="remediate-configuration-issues"></a>Corrección de los problemas de configuración

Una vez que Security Center comienza a llenarse con datos de configuración, se realizan recomendaciones en función de la directiva de seguridad configurada. Por ejemplo, si se ha configurado una máquina virtual sin un grupo de seguridad de red asociado, una recomendación sería crear uno. 

Para ver una lista de todas las recomendaciones: 

1. En el panel de Security Center, seleccione **Recomendaciones**.
2. Seleccione una recomendación concreta. Aparece una lista de todos los recursos a los que se aplica la recomendación.
3. Para aplicar una recomendación, seleccione un recurso concreto. 
4. Siga las instrucciones de los pasos de corrección. 

En muchos casos, Security Center proporciona pasos que se pueden dar para poner en práctica recomendación sin salir de Security Center. En el ejemplo siguiente, Security Center detecta un grupo de seguridad de red que tiene una regla de entrada sin restricciones. En la página de la recomendación, puede hacer clic en el botón **Editar reglas de entrada**. Aparecerá la interfaz de usuario necesaria para modificar la regla. 

![Recomendaciones](./media/tutorial-azure-security/remediation.png)

A medida que se corrigen las recomendaciones, se marcan como resueltas. 

## <a name="view-detected-threats"></a>Visualización de amenazas detectadas

Además de las recomendaciones de configuración de recursos, Security Center muestra alertas de detección de amenazas. La característica de alertas de seguridad agrega los datos recopilados de cada máquina virtual, de los registros de redes de Azure y de las soluciones de asociados conectados para detectar las amenazas de seguridad de los recursos de Azure. Para más información acerca de las funcionalidades de detección de amenazas de Security Center, consulte [Funcionalidades de detección de Azure Security Center](../../security-center/security-center-detection-capabilities.md).

Para poder usar la característica de alertas de seguridad, es preciso aumentar el plan de tarifa de Security Center de *Gratis* a *Estándar*. Al pasar a este plan de tarifa superior, podrá disfrutar de una **evaluación gratuita** de 30 días. 

Para cambiar el plan de tarifa:  

1. En el panel de Security Center, haga clic en **Directiva de seguridad** y, luego, seleccione su suscripción.
2. Seleccione **Plan de tarifa**.
3. Elija el nuevo plan y haga clic en **Seleccionar**.
4. En la hoja **Directiva de seguridad**, seleccione **Guardar**. 

Cuando haya cambiado el plan de tarifa, el gráfico de alertas de seguridad empezará a llenarse cuando se detecten amenazas de seguridad.

![Alertas de seguridad](./media/tutorial-azure-security/security-alerts.png)

Seleccione cualquiera de las alertas para ver información acerca de ella. Por ejemplo, puede ver una descripción de la amenaza, la hora a la que se detectó, todos los intentos de amenaza y la corrección recomendada. En el ejemplo siguiente, se detectó un ataque de RDP por fuerza bruta con 294 intentos de RDP infructuosos. Se proporciona una corrección recomendada.

![Ataque de RDP](./media/tutorial-azure-security/rdp-attack.png)

## <a name="next-steps"></a>Pasos siguientes
En este tutorial, configurará Azure Security Center y, luego, revisó las máquinas virtuales de Security Center. Ha aprendido a:

> [!div class="checklist"]
> * Configuración de una recolección de datos
> * Configurar directivas de seguridad
> * Ver y corregir problemas de estado de la configuración
> * Revisar las amenazas que se detecten

En el siguiente tutorial aprenderá a crear una canalización de CI/CD con Visual Studio Team Services y una máquina virtual Windows en la que se ejecuta IIS.

> [!div class="nextstepaction"]
> [Canalización de CI/CD de Visual Studio Team Services](./tutorial-vsts-iis-cicd.md)
