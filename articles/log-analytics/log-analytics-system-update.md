---
title: Solución System Update Assessment de Log Analytics | Microsoft Docs
description: Puede usar la solución de actualizaciones del sistema de Log Analytics para ayudarle a aplicar las actualizaciones que faltan a los servidores de su infraestructura.
services: log-analytics
documentationcenter: ''
author: bandersmsft
manager: jwhit
editor: ''

ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/11/2016
ms.author: banders

---
# <a name="system-update-assessment-solution-in-log-analytics"></a>Solución System Update Assessment de Log Analytics
Puede usar la solución de actualizaciones del sistema de Log Analytics para ayudarle a aplicar las actualizaciones que faltan a los servidores de su infraestructura. Una vez instalada la solución, puede ver las actualizaciones que faltan en sus servidores supervisados mediante el icono **System Update Assessment** en la página **Información general** de OMS.

Si se detectan actualizaciones que faltan, se muestran los detalles en el panel **Actualizaciones** . Puede usar el panel **Actualizaciones** para trabajar con actualizaciones que faltan y desarrollar un plan para aplicarlas a los servidores que las necesiten.

## <a name="installing-and-configuring-the-solution"></a>Instalación y configuración de la solución
Utilice la siguiente información para instalar y configurar la solución.

* Agregue la solución System Update Assessment al área de trabajo de OMS mediante el proceso descrito en [Incorporación de soluciones de Log Analytics desde la galería de soluciones](log-analytics-add-solutions.md).  No es necesario realizar ninguna configuración más.

## <a name="system-update-data-collection-details"></a>Detalles de la recopilación de datos de actualizaciones del sistema
La solución System Update Assessment recopila metadatos y datos de estado con los agentes habilitados.

En la tabla siguiente se muestran los métodos de recopilación de datos y otros detalles sobre cómo se recopilan los datos para System Update Assessment.

| plataforma | Agente directo | Agente de SCOM | Almacenamiento de Azure | ¿Se necesita SCOM? | Datos del agente de SCOM enviados a través del grupo de administración | Frecuencia de recopilación |
| --- | --- | --- | --- | --- | --- | --- |
| Windows |![Sí](./media/log-analytics-system-update/oms-bullet-green.png) |![Sí](./media/log-analytics-system-update/oms-bullet-green.png) |![No](./media/log-analytics-system-update/oms-bullet-red.png) |![No](./media/log-analytics-system-update/oms-bullet-red.png) |![Sí](./media/log-analytics-system-update/oms-bullet-green.png) |Al menos 2 veces al día y 15 minutos después de instalar una actualización |

En la tabla siguiente se muestran ejemplos de tipos de datos recopilados por la solución System Update Assessment:

| **Tipo de datos** | **Fields** |
| --- | --- |
| Metadatos |BaseManagedEntityId, ObjectStatus, OrganizationalUnit, ActiveDirectoryObjectSid, PhysicalProcessors, NetworkName, IPAddress, ForestDNSName, NetbiosComputerName, VirtualMachineName, LastInventoryDate, HostServerNameIsVirtualMachine, IP Address, NetbiosDomainName, LogicalProcessors, DNSName, DisplayName, DomainDnsName, ActiveDirectorySite, PrincipalName, OffsetInMinuteFromGreenwichTime |
| Estado |StateChangeEventId, StateId, NewHealthState, OldHealthState, Context, TimeGenerated, TimeAdded, StateId2, BaseManagedEntityId, MonitorId, HealthState, LastModified, LastGreenAlertGenerated, DatabaseTimeModified |

### <a name="to-work-with-updates"></a>Para trabajar con actualizaciones, siga estos pasos:
1. En la página **Información general**, haga clic en el icono **System Update Assessment**.  
    ![Icono de Evaluación de la actualización del sistema](./media/log-analytics-system-update/sys-update-tile.png)
2. En el panel **Actualizaciones** , vea las categorías de actualización.  
    ![Panel Actualizaciones](./media/log-analytics-system-update/sys-updates02.png)
3. Desplácese a la derecha de la página para ver la hoja **Actualizaciones críticas o de seguridad de Windows** y, después, en **Clasificación**, haga clic en **Actualizaciones de seguridad**.  
    ![Actualizaciones de seguridad](./media/log-analytics-system-update/sys-updates03.png)
4. En la página Búsqueda, se muestra diversa información sobre las actualizaciones de seguridad que se detectaron que faltaban en los servidores de la infraestructura. Haga clic en **Lista** para ver información detallada de las actualizaciones.  
    ![Resultados de la búsqueda: Lista](./media/log-analytics-system-update/sys-updates04.png)
5. En la página Búsqueda de registros, se muestra información detallada sobre cada actualización. Junto al número KBID, haga clic en **Ver** para ver el artículo correspondiente en el sitio web de soporte técnico de Microsoft.  
    ![Resultados de Búsqueda de registros: Ver KB](./media/log-analytics-system-update/sys-updates05.png)
6. El explorador web abre la página web de soporte técnico de Microsoft correspondiente a la actualización en una nueva pestaña. Vea la información sobre la actualización que falta.  
    ![Página web de soporte técnico de Microsoft](./media/log-analytics-system-update/sys-updates06.png)
7. Mediante el uso de la información encontrada, puede crear un plan para aplicar manualmente la actualización ausente, o bien continuar con los pasos restantes para aplicar automáticamente la actualización.
8. Si quiere aplicar automáticamente la actualización que falta, vuelva al panel **Actualizaciones** y, después, en **Ejecuciones de actualización**, haga clic en **Haga clic para programar la ejecución de una actualización**.  
    ![Ejecuciones de actualización: pestaña Programado](./media/log-analytics-system-update/sys-updates07.png)
9. En la página **Ejecuciones de actualización** de la pestaña **Programado**, haga clic en **Agregar** para crear una nueva actualización.  
    ![Pestaña Programado: Agregar](./media/log-analytics-system-update/sys-updates08.png)
10. En la página **Nueva ejecución de la actualización**, escriba un nombre dela ejecución de la actualización, agregue los equipos individuales o grupos de equipos, defina una programación y, después, haga clic en **Guardar**.  
    ![Nueva ejecución de la actualización](./media/log-analytics-system-update/sys-updates09.png)
11. La pestaña **Programado** de la página **Ejecuciones de actualización** muestra la nueva actualización de ejecución que ha programado.  
    ![Pestaña Programado](./media/log-analytics-system-update/sys-updates10.png)
12. Cuando se inicie la ejecución de la actualización, verá información sobre la pestaña **En ejecución**.  
    ![Pestaña En ejecución](./media/log-analytics-system-update/sys-updates11.png)
13. Una vez finalizada la ejecución de la actualización, la pestaña **Completado** muestra el estado.
14. Si se han aplicado las actualizaciones de la ejecución de la actualización, en la hoja **Actualizaciones críticas o de seguridad de Windows**, verá que se reduce el número de actualizaciones.  
    ![Hoja Actualizaciones críticas o de seguridad de Windows: reducción del número de actualizaciones](./media/log-analytics-system-update/sys-updates12.png)

## <a name="next-steps"></a>Pasos siguientes
* [Búsquedas de registros en Log Analytics](log-analytics-log-searches.md) para más información sobre cómo ver datos detallados de la actualización del sistema.

<!--HONumber=Oct16_HO2-->


