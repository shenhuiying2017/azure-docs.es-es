---
title: "Recolección de datos en Azure Security Center | Microsoft Docs"
description: " Aprenda a habilitar la recolección de datos en Azure Security Center. "
services: security-center
documentationcenter: na
author: TerryLanfear
manager: MBaldwin
editor: 
ms.assetid: 411d7bae-c9d4-4e83-be63-9f2f2312b075
ms.service: security-center
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/12/2018
ms.author: terrylan
ms.openlocfilehash: d5f2c9960b720fc44f37956f9150e89d6425d154
ms.sourcegitcommit: e19f6a1709b0fe0f898386118fbef858d430e19d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/13/2018
---
# <a name="data-collection-in-azure-security-center"></a>Recolección de datos en Azure Security Center
Security Center recopila datos de las máquinas virtuales de Azure y de los equipos que no son de Azure para supervisar las amenazas y vulnerabilidades de seguridad. Los datos se recopilan con Microsoft Monitoring Agent, que lee distintas configuraciones relacionadas con la seguridad y distintos registros de eventos de la máquina y copia los datos en el área de trabajo para analizarlos. Estos son algunos ejemplos de dichos datos: tipo y versión del sistema operativo, registros del sistema operativo (registros de eventos de Windows), procesos en ejecución, nombre de la máquina, direcciones IP, usuario conectado e identificador de inquilino. Asimismo, copia los archivos de volcado de memoria en dicha área de trabajo.

## <a name="enable-automatic-provisioning-of-microsoft-monitoring-agent"></a>Habilitar el aprovisionamiento automático de Microsoft Monitoring Agent     
Si el aprovisionamiento automático está habilitado, Security Center aprovisiona Microsoft Monitoring Agent en todas las máquinas virtuales de Azure compatibles y en las que se creen. El aprovisionamiento automático es muy recomendable y es necesario para las suscripciones del nivel Estándar de Security Center.

> [!NOTE]
> La deshabilitación del aprovisionamiento automático limita la supervisión de seguridad de los recursos. Para obtener más información, vea [Deshabilitar el aprovisionamiento automático](security-center-enable-data-collection.md#disable-automatic-provisioning) en este artículo. Las instantáneas de disco de máquina virtual y la recopilación de artefactos seguirán habilitadas, aunque se deshabilite el aprovisionamiento automático.
>
>

Para habilitar el aprovisionamiento automático de Microsoft Monitoring Agent:
1. En el menú principal de Security Center, seleccione **Directiva de seguridad**.
2. Seleccione la suscripción.
3. En **Directiva de seguridad**, seleccione **Recopilación de datos**.
4. En **Incorporación**, seleccione **Activado** para habilitar el aprovisionamiento automático.
5. Seleccione **Guardar**.

![Habilitar el aprovisionamiento automático][1]

## <a name="default-workspace-configuration"></a>Configuración predeterminada del área de trabajo
Los datos recopilados por Security Center se almacenan en áreas de trabajo de Log Analytics.  Puede optar por que los datos se recopilen de las máquinas virtuales de Azure almacenadas en áreas de trabajo creadas por Security Center o en un área de trabajo que haya creado.

Para usar el área de trabajo existente de Log Analytics:
- El área de trabajo debe estar asociada con la suscripción de Azure seleccionada.
- Como mínimo, el usuario debe tener permisos de lectura para obtener acceso al área de trabajo.

Para seleccionar un área de trabajo existente de Log Analytics:

1. En **Directiva de seguridad: Recopilación de datos**, seleccione **Usar otra área de trabajo**.

   ![Seleccionar el área de trabajo existente][2]

2. En el menú desplegable, seleccione un área de trabajo para almacenar los datos recopilados.

> [!NOTE]
> En el menú desplegable solo se muestran las áreas de trabajo a las que tiene acceso y las que están en su suscripción a Azure.
>
>

3. Seleccione **Guardar**.
4. Tras hacer clic en **Guardar**, se le preguntará si quiere reconfigurar las máquinas virtuales supervisadas.

   - Haga clic en **No** si quiere que la nueva configuración del área de trabajo solo se aplique a las máquinas virtuales nuevas. La nueva configuración del área de trabajo solo se aplica a las nuevas instalaciones de agente, aquellas máquinas virtuales recién detectadas que no tengan instalado Microsoft Monitoring Agent.
   - Haga clic en **Sí** si quiere que la nueva configuración del área de trabajo se aplique a todas las máquinas virtuales. Además, todas las máquinas virtuales conectadas a un área de trabajo creada por Security Center se volverán a conectar a la nueva área de trabajo de destino.

   > [!NOTE]
   > Si hace clic en Sí, no debe eliminar las áreas de trabajo creadas por Security Center hasta que todas las máquinas virtuales se hayan reconectado al nuevo área de trabajo de destino. Esta operación no se lleva a cabo si se elimina un área de trabajo demasiado pronto.
   >
   >

   - Haga clic en **Cancelar** para cancelar la operación.

   ![Seleccionar el área de trabajo existente][3]

## <a name="data-collection-tier"></a>Nivel de recopilación de datos
Security Center puede reducir el volumen de eventos mientras mantiene suficientes eventos para la investigación, la auditoría y la detección de amenazas. Puede elegir la directiva de filtrado adecuada para sus suscripciones y áreas de trabajo de cuatro conjuntos de eventos que recopilará el agente.

- **Todos los eventos**: Para los clientes que quieren asegurarse de que se recopilan todos los eventos. Este es el valor predeterminado.
- **Común**: Es un conjunto de eventos que satisfacen a la mayoría de los clientes y les permite efectuar un registro de auditoría completo.
- **Mínimo**: Es un conjunto más pequeño de eventos para los clientes que quieren reducir el volumen de eventos.
- **Ninguno**: Se deshabilita la recopilación de eventos de seguridad de los registros de seguridad y de AppLocker. Para los clientes que elijan esta opción, sus paneles de seguridad solo tendrán registros del Firewall de Windows y evaluaciones preventivas, como el antimalware, la línea base y las actualizaciones.

> [!NOTE]
> Estos conjuntos se han diseñado para abordar escenarios típicos. Asegúrese de evaluar cuál se ajusta a sus necesidades antes de implementarlo.
>
>

Para determinar los eventos que formarán parte de los conjuntos de eventos **Común** y **Mínimo**, hemos colaborado con los clientes y los estándares del sector para obtener información sobre la frecuencia sin filtrar de cada evento y su uso. En este proceso se han empleado las siguientes directrices:

- **Mínimo**: Asegúrese de que este conjunto abarque solamente los eventos que podrían indicar una brecha correcta y eventos importantes que tengan un volumen muy bajo. Por ejemplo, este conjunto contiene un inicio de sesión de usuario correcto y uno erróneo (identificadores de evento 4624 y 4625), pero no contiene el cierre de sesión, que es importante para la auditoría pero no lo es para la detección y tiene un volumen relativamente alto. La mayor parte del volumen de datos de este conjunto son los eventos de inicio de sesión y el evento de creación de proceso (Id. de evento 4688).
- **Común**: Proporcione una pista de auditoría de usuario completa en este conjunto. Por ejemplo, este conjunto contiene los inicios de sesión y los cierres de sesión de usuario (Id. de evento 4634). Se incluyen acciones de auditoría como cambios en los grupos de seguridad, operaciones de Kerberos en los controladores de dominio de clave y otros eventos que recomiendan las organizaciones del sector.

Los eventos que tienen un volumen muy bajo se han incluido en el conjunto Común, ya que la motivación principal para elegirlo respecto de todos los eventos pasa por reducir el volumen, y no por filtrar eventos específicos.

A continuación se muestra un desglose completo de los identificadores de evento de seguridad y de AppLocker para cada conjunto:

| Capa de datos | Indicadores de eventos recopilados |
| --- | --- |
| Mínimo | 1102,4624,4625,4657,4663,4688,4700,4702,4719,4720,4722,4723,4724,4727,4728,4732,4735,4737,4739,4740,4754,4755, |
| | 4756,4767,4799,4825,4946,4948,4956,5024,5033,8001,8002,8003,8004,8005,8006,8007,8222 |
| Común (valor predeterminado) | 1,299,300,324,340,403,404,410,411,412,413,431,500,501,1100,1102,1107,1108,4608,4610,4611,4614,461,4622, |
| |  4624,4625,4634,4647,4648,4649,4657,4661,4662,4663,4665,4666,4667,4688,4670,4672,4673,4674,4675,4689,4697, |
| | 4700,4702,4704,4705,4716,4717,4718,4719,4720,4722,4723,4724,4725,4726,4727,4728,4729,4733,4732,4735,4737, |
| | 4738,4739,4740,4742,4744,4745,4746,4750,4751,4752,4754,4755,4756,4757,4760,4761,4762,4764,4767,4768,4771, |
| | 4774,4778,4779,4781,4793,4797,4798,4799,4800,4801,4802,4803,4825,4826,4870,4886,4887,4888,4893,4898,4902, |
| | 4904,4905,4907,4931,4932,4933,4946,4948,4956,4985,5024,5033,5059,5136,5137,5140,5145,5632,6144,6145,6272, |
| | 6273,6278,6416,6423,6424,8001,8002,8003,8004,8005,8006,8007,8222,26401,30004 |

> [!NOTE]
> Si se usa un objeto de directiva de grupo (GPO), se recomienda habilitar el evento 4688 de creación de procesos de las directivas de auditoría y el campo *CommandLine* del evento 4688. Para más información acerca del evento 4688 de creación de procesos, consulte las [preguntas más frecuentes](security-center-faq.md#what-happens-when-data-collection-is-enabled) acerca de Security Center. Para más información acerca de estas directivas de auditoría, consulte [Recomendaciones de la directiva de auditoría](https://docs.microsoft.com/windows-server/identity/ad-ds/plan/security-best-practices/audit-policy-recommendations).
>
>

Para elegir la directiva de filtrado:
1. En la hoja **Security policy & settings** (Directiva de seguridad y configuración), seleccione la directiva de filtrado en **Eventos de seguridad**.
2. Seleccione **Guardar**.

   ![Elegir la directiva de filtrado][5]

## <a name="disable-automatic-provisioning"></a>Deshabilitar el aprovisionamiento automático
Puede deshabilitar en cualquier momento el aprovisionamiento automático de los recursos desactivando esta opción en la directiva de seguridad. El aprovisionamiento automático es muy recomendable para poder obtener alertas de seguridad y recomendaciones sobre las actualizaciones del sistema, vulnerabilidades del sistema operativo y la protección de puntos de conexión.

> [!NOTE]
> La deshabilitación del aprovisionamiento automático no quita Microsoft Monitoring Agent de las máquinas virtuales de Azure en las que se ha aprovisionado el agente.
>
>

1. Vuelva al menú principal de Security Center y seleccione la directiva de seguridad.

   ![Deshabilitar el aprovisionamiento automático][6]

2. Seleccione la suscripción en la que quiere deshabilitar el aprovisionamiento automático.
3. En la hoja **Directiva de seguridad: Recopilación de datos**, en **Incorporación**, seleccione **Desactivado** para deshabilitar el aprovisionamiento automático.
4. Seleccione **Guardar**.  

## <a name="next-steps"></a>pasos siguientes
En este artículo le hemos mostrado cómo efectuar un aprovisionamiento automático y una recopilación de datos en Security Center. Para más información sobre el Centro de seguridad, consulte los siguientes recursos:

* [Establecimiento de directivas de seguridad en Azure Security Center](security-center-policies.md): aprenda a configurar directivas de seguridad para las suscripciones y los grupos de recursos de Azure.
* [Administración de recomendaciones de seguridad en Azure Security Center](security-center-recommendations.md) : recomendaciones que le ayudan a proteger los recursos de Azure.
* [Supervisión del estado de seguridad en Azure Security Center](security-center-monitoring.md): aprenda a supervisar el estado de los recursos de Azure.
* [Administración y respuesta a las alertas de seguridad en el Centro de seguridad de Azure](security-center-managing-and-responding-alerts.md): obtenga información sobre cómo administrar y responder a alertas de seguridad.
* [Supervisión de las soluciones de asociados con Azure Security Center](security-center-partner-solutions.md): aprenda a supervisar el estado de mantenimiento de las soluciones de asociados.
- [Seguridad de datos de Azure Security Center](security-center-data-security.md): aprenda cómo se administran y protegen los datos en Security Center.
* [Preguntas más frecuentes sobre Azure Security Center](security-center-faq.md): encuentre las preguntas más frecuentes sobre el uso del servicio.
* [Blog de seguridad de Azure](http://blogs.msdn.com/b/azuresecurity/): obtenga las últimas noticias e información sobre la seguridad en Azure.

<!--Image references-->
[1]: ./media/security-center-enable-data-collection/enable-automatic-provisioning.png
[2]: ./media/security-center-enable-data-collection/use-another-workspace.png
[3]: ./media/security-center-enable-data-collection/reconfigure-monitored-vm.png
[5]: ./media/security-center-enable-data-collection/data-collection-tiers.png
[6]: ./media/security-center-enable-data-collection/disable-automatic-provisioning.png
