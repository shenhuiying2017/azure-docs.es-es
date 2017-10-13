---
title: "Preguntas frecuentes sobre la migración de la plataforma de Security Center | Microsoft Docs"
description: "Estas preguntas frecuentes responden a interrogantes sobre la migración de la plataforma de Azure Security Center."
services: security-center
documentationcenter: na
author: TerryLanfear
manager: MBaldwin
editor: 
ms.assetid: 4d1364cd-7847-425a-bb3a-722cb0779f78
ms.service: security-center
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/14/2017
ms.author: terrylan
ms.openlocfilehash: 4b88b5015fcf44e8979b8b1a3aa1eb26f0fbb704
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="security-center-platform-migration-faq"></a>Preguntas frecuentes sobre la migración de la plataforma de Security Center
A principios de junio de 2017, Azure Security Center comenzó a usar Microsoft Monitoring Agent para recopilar y almacenar datos. Para obtener más información, consulte [Migración de la plataforma de Azure Security Center](security-center-platform-migration.md). Estas preguntas frecuentes responden a interrogantes sobre la migración de la plataforma.

## <a name="data-collection-agents-and-workspaces"></a>Recopilación de datos, agentes y áreas de trabajo

### <a name="how-is-data-collected"></a>¿Cómo se recopilan los datos?
Security Center usa Microsoft Monitoring Agent para recopilar datos de seguridad de VM. Los datos de seguridad incluyen información sobre las configuraciones de seguridad, que se usan para identificar las vulnerabilidades, y eventos de seguridad, que se usan para detectar amenazas. Los datos que recopila el agente se almacenan en un área de trabajo de Log Analytics existente conectado a la VM o en una nueva área de trabajo creada por Security Center. Cuando Security Center crea una nueva área de trabajo, la geolocalización de VM se tiene en cuenta.

> [!NOTE]
> Microsoft Monitoring Agent es el mismo agente que usan Operations Management Suite (OMS), el servicio Log Analytics y System Center Operations Manager (SCOM).
>
>

Cuando la recopilación de datos se habilita por primera vez o cuando se migran las suscripciones, Security Center comprueba si Microsoft Monitoring Agent ya está instalado como una extensión de Azure en cada una de las VM. Si Microsoft Monitoring Agent no está instalado, Security Center hará lo siguiente:

- Instalará Microsoft Monitoring Agent en la VM
   - Si un área de trabajo creado por Security Center ya existe en la misma geolocalización que la VM, el agente se conecta a esta área de trabajo
   - Si no existe un área de trabajo, Security Center crea un nuevo grupo de recursos y un área de trabajo predeterminada en esa geolocalización y conecta el agente a esa área de trabajo. La convención de nomenclatura del área de trabajo y el grupo de recursos es:

       Área de trabajo: DefaultWorkspace-[subscription-ID]-[geo]

       Grupo de recursos: DefaultResouceGroup-[geo]
- Instalará una solución de Security Center en el área de trabajo

La ubicación del área de trabajo se basa en la ubicación de la VM. Para más información, consulte [Seguridad de datos](security-center-data-security.md).

> [!NOTE]
> Antes de la migración de la plataforma, Security Center recopiló datos de seguridad de las VM mediante el agente de Supervisión de Azure y los datos se almacenaron en la cuenta de almacenamiento. Después de la migración de la plataforma, Security Center usa Microsoft Monitoring Agent y el área de trabajo para recopilar y almacenar los mismos datos. La cuenta de almacenamiento se puede quitar después de la migración.
>
>

### <a name="am-i-billed-for-log-analytics-or-oms-on-the-workspaces-created-by-security-center"></a>¿Se me factura por Log Analytics u OMS en las áreas de trabajo creadas por Security Center?
No. Las áreas de trabajo creadas por Security Center, mientras estén configuradas para OMS por facturación de nodo, no incurren en gastos de OMS. La facturación de Security Center siempre se basa en la directiva de seguridad de Security Center y en las soluciones instaladas en un área de trabajo:

- **Nivel Gratis**: Security Center instala la solución “SecurityCenterFree” en el área de trabajo predeterminada. No se le facturará por el nivel Gratis.
- **Nivel Estándar**: Security Center instala las soluciones “SecurityCenterFree” y “Security” en el área de trabajo predeterminada.

Para más información, vea [Precios de Security Center ](https://azure.microsoft.com/pricing/details/security-center/). La página de precios trata los cambios en el almacenamiento de datos de seguridad y la facturación prorrateada a partir de junio de 2017.

> [!NOTE]
> El plan de tarifa de OMS de áreas de trabajo creadas por Security Center no afecta a la facturación de Security Center.
>
>

### <a name="can-i-delete-the-default-workspaces-created-by-security-center"></a>¿Puedo eliminar las áreas de trabajo predeterminadas creadas por Security Center?
**La eliminación del área de trabajo predeterminada no es recomendable.** Security Center utiliza las áreas de trabajo predeterminadas para almacenar los datos de seguridad de las VM.  Si elimina un área de trabajo, Security Center no podrá recopilar estos datos y algunas recomendaciones de seguridad y alertas dejarán de estar disponibles.

Para realizar la recuperación, quite Microsoft Monitoring Agent de las VM conectadas al área de trabajo eliminado. Security Center vuelve a instalar el agente y crea nuevas áreas de trabajo predeterminadas.

### <a name="how-can-i-use-my-existing-log-analytics-workspace"></a>¿Cómo puedo usar mi área de trabajo de Log Analytics existente?

Puede seleccionar un área de trabajo de Log Analytics existente para almacenar los datos recopilados por Security Center. Para usar el área de trabajo de Log Analytics existente:

- El área de trabajo debe estar asociada con la suscripción de Azure seleccionada.
- Como mínimo, el usuario debe tener permisos de lectura para obtener acceso al área de trabajo.

Para seleccionar un área de trabajo existente de Log Analytics:

1. En **Security policy – Data Collection** (Directiva de seguridad: recopilación de datos), seleccione **Use another workspace** (Usar otro área de trabajo).

   ![Usar otro área de trabajo][5]

2. En el menú desplegable, seleccione un área de trabajo para almacenar los datos recopilados.

   > [!NOTE]
   > En el menú desplegable solo se muestran las áreas de trabajo a las que tiene acceso y las que están en su suscripción a Azure.
   >
   >

3. Seleccione **Guardar**.
4. Tras hacer clic en **Guardar**, se le preguntará si quiere reconfigurar las máquinas virtuales supervisadas.

   - Haga clic en **No** si quiere que la nueva configuración del área de trabajo **solo se aplique a las máquinas virtuales nuevas**. La nueva configuración del área de trabajo solo se aplica a las nuevas instalaciones de agente, aquellas máquinas virtuales recién detectadas que no tengan instalado Microsoft Monitoring Agent.
   - Haga clic en **Sí** si quiere que la nueva configuración del área de trabajo **se aplique a todas las máquinas virtuales**. Además, cada máquina virtual conectada a un área de trabajo creada por Security Center se reconecta a la nueva área de trabajo de destino.

   > [!NOTE]
   > Si hace clic en Sí, no debe eliminar las áreas de trabajo creadas por Security Center hasta que todas las máquinas virtuales se hayan reconectado al nuevo área de trabajo de destino. Esta operación no se lleva a cabo si se elimina un área de trabajo demasiado pronto.
   >
   >

   - Haga clic en **Cancelar** para cancelar la operación.

      ![Reconfigurar máquinas virtuales supervisadas][6]

### <a name="what-if-the-microsoft-monitoring-agent-was-already-installed-as-an-extension-on-the-vm"></a>¿Qué ocurre si Microsoft Monitoring Agent ya estaba instalado como una extensión en la VM?
Security Center no invalida las conexiones existentes con áreas de trabajo de usuario. Security Center almacena los datos de seguridad de la VM en el área de trabajo ya conectada.

### <a name="what-if-i-had-a-microsoft-monitoring-agent-installed-on-the-machine-but-not-as-an-extension"></a>¿Qué ocurre si tenía Microsoft Monitoring Agent instalado en la máquina pero no como una extensión?
Si Microsoft Monitoring Agent se instala directamente en la VM (no como una extensión de Azure), Security Center no instala Microsoft Monitoring Agent y la supervisión de seguridad estará limitada.

### <a name="what-is-the-impact-of-removing-these-extensions"></a>¿Qué impacto tiene quitar estas extensiones?
Si quita la extensión de Microsoft Monitoring, Security Center no será capaz de recopilar datos de seguridad de la VM y algunas recomendaciones de seguridad y las alertas no estarán disponibles. En un plazo de 24 horas, Security Center determina que la VM no tiene la extensión y vuelve a instalarla.

### <a name="how-do-i-stop-the-automatic-agent-installation-and-workspace-creation"></a>¿Cómo detengo la instalación automática del agente y la creación del área de trabajo?
Puede desactivar el aprovisionamiento automático en las suscripciones en la directiva de seguridad, pero no es recomendable. La desactivación del aprovisionamiento automático limita las recomendaciones y las alertas de Security Center. El aprovisionamiento automático es necesario en las suscripciones del plan de tarifa Estándar. Para deshabilitar el aprovisionamiento automático:

1. Si la suscripción está configurada para el nivel Estándar, abra la directiva de seguridad de esa suscripción y seleccione el nivel **Gratis**.

   ![Plan de tarifa ][1]

2. Después, desactive el aprovisionamiento automático seleccionando **Desactivar** en la hoja **Security policy - Data collection** (Directiva de seguridad: Recopilación de datos).
   ![Recopilación de datos][2]

### <a name="how-do-i-remove-oms-extensions-installed-by-security-center"></a>¿Cómo quito extensiones OMS instaladas por Security Center?
También puede quitar manualmente Microsoft Monitoring Agent. Sin embargo, no es recomendable porque limita las recomendaciones y las alertas de Security Center.

> [!NOTE]
> Si la recopilación de datos está habilitada, Security Center volverá a instalar el agente después de quitarlo.  Debe deshabilitar la recopilación de datos antes de quitar manualmente el agente. Consulte [¿Cómo detengo la instalación automática del agente y la creación del área de trabajo?](#how-do-i-stop-the-automatic-agent-installation-and-workspace-creation?) para obtener instrucciones sobre la deshabilitación de la recopilación de datos.
>
>

Para quitar manualmente el agente:

1.  En el portal, abra **Log Analytics**.
2.  En la hoja Log Analytics, seleccione un área de trabajo:
3.  Seleccione las VM que no desea supervisar y seleccione **Desconectar**.

   ![Eliminación del agente][3]

> [!NOTE]
> Si una VM Linux ya tiene un agente OMS que no es una extensión, la eliminación de la extensión también quita el agente y el cliente tiene que volver a instalarlo.
>
>

## <a name="existing-oms-customers"></a>Clientes de OMS existentes

### <a name="does-security-center-override-any-existing-connections-between-vms-and-workspaces"></a>¿Invalida Security Center las conexiones existentes entre VM y áreas de trabajo?
Si una VM ya tiene Microsoft Monitoring Agent instalado como una extensión de Azure, Security Center no invalida la conexión de área de trabajo existente. En su lugar, Security Center usa el área de trabajo existente.

Una solución de Security Center se instala en el área de trabajo si todavía no está presente y la solución solo se aplica a VM relevantes. Cuando se agrega una solución, se implementa automáticamente de forma predeterminada en todos los agentes de Windows y Linux conectados al área de trabajo de Log Analytics. La [selección de destino de solución](../operations-management-suite/operations-management-suite-solution-targeting.md), que es una característica de OMS, le permite aplicar un ámbito a sus soluciones.

Si Microsoft Monitoring Agent se instala directamente en la VM (no como una extensión de Azure), Security Center no instala Microsoft Monitoring Agent y la supervisión de seguridad estará limitada.

### <a name="what-should-i-do-if-i-suspect-that-the-data-platform-migration-broke-the-connection-between-one-of-my-vms-and-my-workspace"></a>¿Qué debo hacer si sospecho que la migración de la plataforma de datos interrumpió la conexión entre una de mis VM y mi área de trabajo?
Esto no debería ocurrir. Si ocurre, [cree una solicitud de soporte técnico de Azure](../azure-supportability/how-to-create-azure-support-request.md) e incluya los detalles siguientes:

- El identificador del recurso de Azure de la VM afectada
- El identificador del recurso de Azure del área de trabajo configurada en la extensión antes de que la conexión se interrumpiera
- El agente y la versión que estaban previamente instalados

### <a name="does-security-center-install-solutions-on-my-existing-oms-workspaces-what-are-the-billing-implications"></a>¿Instala Security Center soluciones en mis áreas de trabajo OMS existentes? ¿Qué implica desde el punto de vista de la facturación?
Cuando Security Center identifica que una VM ya está conectada a un área de trabajo creado por el usuario, habilita las soluciones en esta área de trabajo según el plan de tarifa. Las soluciones se aplican solo a las VM de Azure pertinentes a través de la [selección de destino de solución](https://docs.microsoft.com/azure/operations-management-suite/operations-management-suite-solution-targeting), por lo que la facturación sigue siendo la misma.

- **Nivel Gratis**: Security Center instala la solución “SecurityCenterFree” en el área de trabajo. No se le facturará por el nivel Gratis.
- **Nivel Estándar**: Security Center instala las soluciones “SecurityCenterFree” y “Security” en el área de trabajo.

   ![Soluciones del área de trabajo predeterminada][4]

> [!NOTE]
> La solución “Seguridad” de Log Analytics es la solución Seguridad y auditoría de OMS.
>
>

### <a name="i-already-have-workspaces-in-my-environment-can-i-use-them-to-collect-security-data"></a>Ya tengo áreas de trabajo en mi entorno, ¿puedo utilizarlas para recopilar datos de seguridad?
Si una VM ya tiene Microsoft Monitoring Agent instalado como una extensión de Azure, Security Center usa el área de trabajo conectada existente. Una solución de Security Center se instala en el área de trabajo si todavía no está presente y la solución solo se aplica a VM relevantes mediante la [selección de destino de solución](https://docs.microsoft.com/azure/operations-management-suite/operations-management-suite-solution-targeting).

Cuando Security Center instala Microsoft Monitoring Agent en VM, utiliza los espacios de trabajo predeterminados creados por Security Center. Pronto los clientes podrán configurar qué áreas de trabajo se utilizan.

### <a name="i-already-have-security-solution-on-my-workspaces-what-are-the-billing-implications"></a>Ya tengo una solución de seguridad en mis áreas de trabajo. ¿Qué implica desde el punto de vista de la facturación?
La solución Seguridad y auditoría se utiliza para habilitar las características del nivel Estándar de Security Center para VM de Azure. Si la solución Seguridad y auditoría ya está instalada en un área de trabajo, Security Center utiliza la solución existente. No hay ningún cambio en la facturación.

## <a name="next-steps"></a>Pasos siguientes
Para más información sobre la migración de la plataforma de Security Center, consulte:

- [Migración de la plataforma de Azure Security Center](security-center-platform-migration.md)
- [Guía de solución de problemas de Azure Security Center](security-center-troubleshooting-guide.md)

<!--Image references-->
[1]: ./media/security-center-platform-migration-faq/pricing-tier.png
[2]: ./media/security-center-platform-migration-faq/data-collection.png
[3]: ./media/security-center-platform-migration-faq/remove-the-agent.png
[4]: ./media/security-center-platform-migration-faq/solutions.png
[5]: ./media/security-center-platform-migration-faq/use-another-workspace.png
[6]: ./media/security-center-platform-migration-faq/reconfigure-monitored-vm.png
