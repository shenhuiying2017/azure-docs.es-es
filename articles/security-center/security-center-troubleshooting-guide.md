---
title: "Guía de solución de problemas de Azure Security Center | Microsoft Docs"
description: Este documento ayuda a solucionar problemas en Azure Security Center.
services: security-center
documentationcenter: na
author: YuriDio
manager: mbaldwin
editor: 
ms.assetid: 44462de6-2cc5-4672-b1d3-dbb4749a28cd
ms.service: security-center
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/01/2018
ms.author: yurid
ms.openlocfilehash: e2e8b16bf720e2be8b8bc8ae81fc944af79dddab
ms.sourcegitcommit: eeb5daebf10564ec110a4e83874db0fb9f9f8061
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 02/03/2018
---
# <a name="azure-security-center-troubleshooting-guide"></a>Guía de solución de problemas de Azure Security Center
Esta guía está destinada a profesionales de tecnologías de la información (TI), analistas de seguridad de la información y administradores de la nube cuyas organizaciones utilizan Azure Security Center y necesitan solucionar problemas relacionados con Security Center.

>[!NOTE]
>Desde primeros de junio de 2017, Security Center usa Microsoft Monitoring Agent para recopilar y almacenar datos. Consulte [Migración de la plataforma de Azure Security Center](security-center-platform-migration.md) para más información. La información de este artículo representa la funcionalidad de Security Center después de la transición a Microsoft Monitoring Agent.
>

## <a name="troubleshooting-guide"></a>Guía de solución de problemas
Esta guía explica cómo solucionar problemas relacionados con Security Center. La mayoría de las soluciones de problemas en Security Center se realizan examinando primero el [registro de auditoría](https://azure.microsoft.com/updates/audit-logs-in-azure-preview-portal/) del componente afectado. A través de los registros de auditoría puede determinar:

* Qué operaciones se han llevado a cabo
* Quién inició la operación
* Cuándo tuvo lugar la operación
* El estado de la operación
* Los valores de otras propiedades que podrían ayudarle en la investigación de la operación

El registro de auditoría contiene todas las operaciones de escritura (PUT, POST, DELETE) realizadas en los recursos, pero no incluye las operaciones de lectura (GET).

## <a name="microsoft-monitoring-agent"></a>Microsoft Monitoring Agent
Security Center usa Microsoft Monitoring Agent, que es el mismo agente que usan la solución Operations Management Suite y el servicio Log Analytics, para recopilar datos de seguridad de las máquinas virtuales de Azure. Una vez que se ha habilitado la recopilación de datos y se ha instalado correctamente el agente en la máquina de destino, los siguientes procesos deben estar en ejecución:

* HealthService.exe

Si abre la consola de administración de servicios (services.msc), también verá el servicio Microsoft Monitoring Agent que se ejecuta como se muestra a continuación:

![Services](./media/security-center-troubleshooting-guide/security-center-troubleshooting-guide-fig5.png)

Para ver qué versión del agente tiene, abra **Administrador de tareas**, en la pestaña **Procesos** busque el servicio **Microsoft Monitoring Agent**, haga clic en él con el botón derecho y haga clic en **Propiedades**. En la pestaña **Detalles**, examine la versión del archivo tal y como se muestra a continuación:

![Archivo](./media/security-center-troubleshooting-guide/security-center-troubleshooting-guide-fig6.png)


## <a name="microsoft-monitoring-agent-installation-scenarios"></a>Escenarios de instalación de Microsoft Monitoring Agent
Existen dos escenarios de instalación que pueden producir resultados diferentes al instalar Microsoft Monitoring Agent en el equipo. Los escenarios admitidos son:

* **Agente instalado automáticamente por Security Center**: en este escenario podrá ver las alertas en ambas ubicaciones: Security Center y la búsqueda de registros. Recibirá notificaciones por correo electrónico a la dirección de correo electrónico que configuró en la directiva de seguridad para la suscripción a la que pertenece el recurso.
.
* **Agente instalado manualmente en una máquina virtual ubicada en Azure**: en este escenario, si está usando los agentes descargados e instalados antes de febrero de 2017, podrá ver las alertas en el portal de Security Center solo si filtra por la suscripción a la que pertenece el área de trabajo. En caso de filtrar por la suscripción a la que pertenece el recurso, no podrá ver ninguna alerta. Recibirá notificaciones por correo electrónico a la dirección de correo electrónico que configuró en la directiva de seguridad para la suscripción a la que pertenece el área de trabajo.

>[!NOTE]
> Para evitar el comportamiento explicado en el segundo caso, asegúrese de descargar la versión más reciente del agente.
>

## <a name="monitoring-agent-health-issues"></a>Supervisión de problemas de Agent Health
**Estado de supervisión** define el motivo por el que Security Center no puede supervisar correctamente las máquinas virtuales y los equipos inicializados para el aprovisionamiento automático. La tabla siguiente muestra los valores, las descripciones y los pasos de resolución de **Estado de supervisión**.

| Estado de supervisión | DESCRIPCIÓN | Pasos de la solución |
|---|---|---|
| Instalación del agente pendiente | La instalación de Microsoft Monitoring Agent aún está en ejecución.  La instalación puede tardar unas horas. | Espere hasta que finalice la instalación automática. |
| Estado de energía: desactivado | La máquina virtual está detenida.  Microsoft Monitoring Agent solo puede instalarse en una máquina virtual que está en ejecución. | Reinicie la máquina virtual. |
| Falta el agente de máquinas virtuales de Azure o este no es válido | Microsoft Monitoring Agent no está instalado aún.  Es necesario un agente de máquina virtual de Azure válido para que Security Center instale la extensión. | Instale, reinstale o actualice el agente de máquinas virtuales de Azure en la máquina virtual. |
| Estado de máquina virtual no preparada para la instalación  | Microsoft Monitoring Agent no está instalado todavía porque la máquina virtual no está lista para la instalación. La máquina virtual no está lista para la instalación debido a un problema con el agente de la máquina virtual o con el aprovisionamiento de máquinas virtuales. | Compruebe el estado de la máquina virtual. Vuelva a **Máquinas virtuales** en el portal y seleccione la máquina virtual para obtener información del estado. |
|Error de instalación: error general | Microsoft Monitoring Agent se instaló pero se ha producido a un error. | [Instale manualmente la extensión](../log-analytics/log-analytics-quick-collect-azurevm.md#enable-the-log-analytics-vm-extension) o desinstale la extensión para que Security Center intente instalarla de nuevo. |
| Error de instalación: agente local ya instalado | No se pudo instalar Microsoft Monitoring Agent. Security Center identifica que un agente local (OMS o SCOM) ya está instalado en la máquina virtual. Para evitar la configuración del hospedaje múltiple, en la que la máquina virtual informa a dos áreas de trabajo independientes, se detiene la instalación de Microsoft Monitoring Agent. | Hay dos maneras de resolverlo: [instalar manualmente la extensión](../log-analytics/log-analytics-quick-collect-azurevm.md#enable-the-log-analytics-vm-extension) y conectarla al área de trabajo deseada. O bien, establecer el área de trabajo deseada como el área de trabajo predeterminada y habilitar el aprovisionamiento automático del agente.  Consulte [Habilitación del aprovisionamiento automático](security-center-enable-data-collection.md). |
| El agente no puede conectarse al área de trabajo | Microsoft Monitoring Agent se instaló pero se ha producido a un error debido a la conectividad de red.  Compruebe que el sistema tiene acceso a Internet o que se ha configurado un servidor proxy HTTP válido para el agente. | Consulte los [requisitos de red del agente de supervisión](#troubleshooting-monitoring-agent-network-requirements). |
| Agente conectado a un área de trabajo desconocida o no encontrada | Security Center ha identificado que la instancia de Microsoft Monitoring Agent instalada en la máquina virtual está conectada a un área de trabajo a la que no tiene acceso. | Esto puede ocurrir en dos casos. El área de trabajo se ha eliminado y ya no existe. Vuelva a instalar al agente con el área de trabajo correcta o desinstale el agente y permita que Security Center complete la instalación de aprovisionamiento automático. El segundo caso se da cuando el área de trabajo forma parte de una suscripción para la que Security Center no tiene permisos. Security Center requiere que las suscripciones permitan el acceso al proveedor de recursos de seguridad de Microsoft. Para habilitarlo, registre la suscripción en el proveedor de recursos de seguridad de Microsoft. Esto se puede hacer mediante una API, PowerShell, el portal o, simplemente, filtrando por la suscripción en el panel **Información general** de Security Center. Para más información, consulte [Proveedores de recursos y sus tipos](../azure-resource-manager/resource-manager-supported-services.md#portal). |
| El agente no responde o falta el identificador | Security Center no puede recuperar los datos de seguridad escaneados de la máquina virtual, incluso aunque el agente está instalado. | El agente no notifica ningún dato, incluidos los latidos. El agente puede estar dañado o algo está bloqueando el tráfico. O bien, el agente está informando de datos pero le falta un identificador de recurso de Azure por lo que es imposible relacionar los datos con la máquina virtual de Azure. Para solucionar problemas de Linux, consulte la [Guía de solución de problemas del Agente de OMS para Linux](https://github.com/Microsoft/OMS-Agent-for-Linux/blob/master/docs/Troubleshooting.md#im-not-seeing-any-linux-data-in-the-oms-portal). Para solucionar problemas de Windows, consulte [Solución de problemas con máquinas virtuales Windows](https://github.com/MicrosoftDocs/azure-docs/blob/8c53ac4371d482eda3d85819a4fb8dac09996a89/articles/log-analytics/log-analytics-azure-vm-extension.md#troubleshooting-windows-virtual-machines). |
| Agente no instalado | La colección de datos está deshabilitada. | Active la colección de datos en la directiva de seguridad o instale manualmente Microsoft Monitoring Agent. |


## <a name="troubleshooting-monitoring-agent-network-requirements"></a>Solución de problemas de los requisitos e red del agente de supervisión
Para que los agentes se puedan conectar a Security Center y registrarse ahí, deben tener acceso a los recursos de red, lo que incluye los números de puerto y las direcciones URL de dominio.

- Para los servidores proxy, debe asegurarse de que los recursos de servidor proxy adecuados están configurados en la configuración del agente. Lea este artículo para más información sobre [cómo cambiar la configuración del servidor proxy](https://docs.microsoft.com/azure/log-analytics/log-analytics-windows-agents#configure-proxy-settings).
- Si usa un firewall para restringir el acceso a Internet, deberá configurarlo para que permita el acceso a OMS. No es necesario realizar ninguna acción en la configuración del agente.

En la siguiente tabla se muestran los recursos necesarios para la comunicación.

| Recurso del agente | Puertos | Omitir inspección de HTTPS |
|---|---|---|
| * .ods.opinsights.azure.com | 443 | Sí |
| *.oms.opinsights.azure.com | 443 | Sí |
| * .blob.core.windows.net | 443 | Sí |
| *.azure-automation.net | 443 | Sí |

Si experimenta problemas con la incorporación del agente, asegúrese de leer el artículo [Solución de problemas de incorporación en Operations Management Suite](https://support.microsoft.com/en-us/help/3126513/how-to-troubleshoot-operations-management-suite-onboarding-issues).


## <a name="troubleshooting-endpoint-protection-not-working-properly"></a>La solución de problemas de Endpoint Protection no funciona correctamente

El agente invitado es el proceso primario de todo lo que hace la extensión [Microsoft Antimalware](../security/azure-security-antimalware.md). Cuando se produce un error en el proceso del agente invitado, es posible que se produzca un error en la instancia de Microsoft Antimalware que se ejecuta como proceso secundario del agente invitado.  En escenarios como este se recomienda comprobar las opciones siguientes:

- Si la máquina virtual de destino es una imagen personalizada y el creador de la máquina virtual nunca instaló el agente invitado.
- Si el destino es una máquina virtual de Linux en lugar de una máquina virtual de Windows, se producirá un error en la instalación de la versión de Windows de la extensión del antimalware en una máquina virtual de Linux. El agente invitado Linux tiene requisitos específicos en términos de la versión de SO y los paquetes necesarios y si no se cumplen esos requisitos, el agente de máquina virtual no funcionará ahí tampoco.
- Si la máquina virtual se creó con una versión anterior del agente invitado. Si así fue, debe tener en cuenta que es posible que algunos agentes anteriores no realicen actualizaciones automáticas a la versión más recientes, lo que podría generar este problema. Use siempre la versión más reciente del agente invitado si crea sus propias imágenes.
- Algunos software de administración de terceros podrían deshabilitar el agente invitado o bloquear el acceso a ciertas ubicaciones de archivos. Si tiene instalado este tipo de software de terceros en la máquina virtual, asegúrese de que el agente esté en la lista de exclusiones.
- Ciertos ajustes del firewall o un grupo de seguridad de red (NSG) pueden bloquear el tráfico de red desde y hacia el agente invitado.
- Cierta lista de control de acceso (ACL) puede impedir el acceso al disco.
- La falta de espacio en disco puede impedir que el agente invitado funcione correctamente.

De manera predeterminada, la interfaz de usuario de Microsoft Antimalware está deshabilitada, lea [Enabling Microsoft Antimalware User Interface on Azure Resource Manager VMs Post Deployment](https://blogs.msdn.microsoft.com/azuresecurity/2016/03/09/enabling-microsoft-antimalware-user-interface-post-deployment/) (Habilitación de la interfaz de usuario de Microsoft Antimalware en máquinas virtuales de Azure Resource Manager tras la implementación) para más información sobre cómo habilitarla si necesita hacerlo.

## <a name="troubleshooting-problems-loading-the-dashboard"></a>Solución de problemas al cargar el panel

Si experimenta problemas al cargar el panel de Security Center, asegúrese de que el usuario que registra la suscripción a Security Center (es decir, el primer usuario que abrió Security Center con la suscripción) y el usuario que desearía activar la recopilación de datos son *propietarios* o *colaboradores* en la suscripción. A partir de ese momento, los usuarios *lectores* en la suscripción también podrán ver el panel, las alertas, las recomendaciones y las directivas.

## <a name="contacting-microsoft-support"></a>Contacto con el soporte técnico de Microsoft
Algunos problemas pueden identificarse mediante las instrucciones proporcionadas en este artículo; también puede encontrar otros en el [foro](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureSecurityCenter)público de Security Center. Sin embargo, si necesita más información para solucionar el problema, puede abrir una nueva solicitud de soporte técnico mediante **Azure Portal**, como se indica a continuación:

![Soporte técnico de Microsoft](./media/security-center-troubleshooting-guide/security-center-troubleshooting-guide-fig2.png)


## <a name="see-also"></a>Otras referencias
En este documento ha aprendido a configurar directivas de seguridad en el Centro de seguridad de Azure. Para obtener más información sobre el Centro de seguridad de Azure, consulte los siguientes recursos:

* [Guía de planeamiento y operaciones de Azure Security Center](security-center-planning-and-operations-guide.md) : aprenda a planear y conozca las consideraciones de diseño necesarias para usar Azure Security Center.
* [Supervisión del estado de seguridad en Azure Security Center](security-center-monitoring.md) : obtenga información sobre cómo supervisar el estado de los recursos de Azure.
* [Administración y respuesta a las alertas de seguridad en Azure Security Center](security-center-managing-and-responding-alerts.md) : obtenga información sobre cómo administrar y responder a alertas de seguridad.
* [Supervisión de las soluciones de asociados con Azure Security Center](security-center-partner-solutions.md) : aprenda a supervisar el estado de mantenimiento de las soluciones de asociados.
* [Preguntas más frecuentes sobre Azure Security Center](security-center-faq.md) : encuentre las preguntas más frecuentes sobre el uso del servicio.
* [Blog de seguridad de Azure](http://blogs.msdn.com/b/azuresecurity/) : encuentre entradas de blog sobre el cumplimiento y la seguridad de Azure.
