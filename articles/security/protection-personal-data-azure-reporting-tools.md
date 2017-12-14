---
title: "Documentación de protección de datos personales con herramientas de informes de Azure | Microsoft Docs"
description: "cómo usar servicios de informes de Azure y tecnologías para ayudar a proteger la privacidad de los datos personales."
services: security
documentationcenter: na
author: barclayn
manager: MBaldwin
editor: TomSh
ms.assetid: 
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/24/2017
ms.author: barclayn
ms.openlocfilehash: 7ce6297d7d6b61ac95df58db3fa1a2a0a123a64e
ms.sourcegitcommit: b07d06ea51a20e32fdc61980667e801cb5db7333
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/08/2017
---
# <a name="document-protection-of-personal-data-with-azure-reporting-tools"></a>Documentación de protección de datos personales con herramientas de informes de Azure

En este artículo se explicará cómo usar servicios de informes de Azure y tecnologías para ayudar a proteger la privacidad de los datos personales.

## <a name="scenario"></a>Escenario

Una gran empresa de cruceros, con sede en Estados Unidos, se encuentra en proceso de expansión de sus operaciones para ofrecer itinerarios en los mares Mediterráneo, Adriático y Báltico, así como en las Islas Británicas. Para contribuir a esos esfuerzos, ha adquirido varias líneas de cruceros más pequeñas establecidas en Italia, Alemania, Dinamarca y Reino Unido.

La empresa usa Microsoft Azure para el procesamiento y almacenamiento de datos corporativos. Esto incluye información de identificación personal como nombres, direcciones, números de teléfono e información de la tarjeta de crédito de su clientela global. También incluye información tradicional de recursos humanos como direcciones, números de teléfono, números de identificación fiscal y otra información sobre los empleados de la empresa de todas las ubicaciones. La línea de cruceros también conserva una gran base de datos de miembros del programa de recompensa y lealtad que incluye información personal para hacer un seguimiento de las relaciones con clientes actuales y antiguos.

Los empleados corporativos tienen acceso a la red de las oficinas remotas de la empresa, mientras que los agentes de viajes repartidos por todo el mundo tienen acceso a algunos recursos empresariales.

## <a name="problem-statement"></a>Declaración del problema

La empresa debe proteger la privacidad de los datos personales de los clientes y los empleados a través de una estrategia de seguridad que usa características de seguridad y administración de Azure para imponer controles estrictos al acceso a datos personales y su procesamiento. También debe poder demostrar sus medidas protectoras a los auditores internos y externos.

## <a name="company-goal"></a>Objetivo de la empresa

Como parte de su estrategia de seguridad de defensa en profundidad, se trata de un objetivo empresarial de seguimiento de todo el acceso a datos personales y su procesamiento. También se desea garantizar que la documentación de las protecciones de la privacidad adecuadas para los datos personales esté en su lugar y funcione.

## <a name="solutions"></a>Soluciones

Microsoft Azure proporciona herramientas de supervisión, registro y diagnóstico completas para contribuir al seguimiento y registro de actividades y eventos asociados al acceso a datos personales y su procesamiento, el flujo geográfico de datos y el acceso de terceros a datos personales. Dado que la seguridad de los datos personales en la nube es una responsabilidad compartida, Microsoft también proporciona a los clientes lo siguiente:

- Información detallada sobre su propio procesamiento de datos de los clientes

- Medidas de seguridad administradas por Microsoft

- Dónde y cómo envía datos de los clientes

- Detalles del proceso de revisiones de privacidad de Microsoft

### <a name="azure-active-directory"></a>Azure Active Directory

[Azure Active Directory](https://azure.microsoft.com/services/active-directory/) es el directorio multiinquilino basado en la nube y el servicio de administración de identidades de Microsoft. Las funcionalidades de informes de auditoría e inicio de sesión del servicio le proporcionan información de la actividad de uso de la aplicación e inicio de sesión detallada para ayudarle a supervisar y garantizar el acceso correcto a datos personales de los clientes y los empleados.

Hay dos tipos de informes de actividad:

- Los [registros o informes de actividad de auditoría](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-activity-audit-logs) proporcionan un registro detallado de tareas o actividades del sistema

- En el [registro o informe de actividad de inicios de sesión](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-activity-sign-ins) se muestra quién ha realizado cada una de las actividades que se incluyen en el informe de auditoría.

El uso conjunto de los dos le permite hacer un seguimiento del historial de todas las tareas llevadas a cabo, así como de quién ha realizado cada una. Ambos tipos de informes son personalizables y se pueden filtrar.

#### <a name="how-do-i-access-the-audit-and-security-logs"></a>¿Cómo se puede tener acceso a los registros de seguridad y auditoría?

Se puede tener acceso a los registros de seguridad y auditoría desde el portal de Active Directory de tres formas diferentes: a través de la sección **Actividad** (seleccione **Registros de auditoría** o **Inicios de sesión**), o bien desde **Usuarios y grupos** o **Aplicaciones empresariales** en **Administrar** en Active Directory. También se puede tener acceso a los informes a través de la API de informes de Azure Active Directory. 

1. En Azure Portal, seleccione **Azure Active Directory.**

2. En la sección **Actividad**, seleccione **Registros de auditoría.**

    ![](media/protection-personal-data-azure-reporting-tools/image001.png)

3. Personalice la vista de lista haciendo clic en **Columnas** en la barra de herramientas.

4.  Seleccione un artículo de la vista de lista para ver todos los detalles disponibles sobre él.

    ![](media/protection-personal-data-azure-reporting-tools/image003.png)

Los informes de Azure Active Directory también incluyen dos tipos de informes de seguridad, **usuarios marcados en riesgo** e **inicios de sesión no seguros**, que pueden ayudarle a supervisar posibles riesgos en su entorno de Azure.

Para obtener más información sobre el servicio de informes, consulte [Informes de Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-azure-portal)

Visite [Informes de actividad de Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-azure-portal#activity-reports) para obtener más detalles sobre los informes disponibles en Azure Active Directory. Este sitio incluye más detalles sobre cómo tener acceso y usar [informes de actividad de registros de auditoría](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-activity-audit-logs) e [informes de actividad de inicio de sesión](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-activity-sign-ins) en el portal. También incluye información sobre los informes de seguridad [usuarios marcados en riesgo](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-security-user-at-risk) e [inicio de sesión no seguro](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-security-risky-sign-ins).

Visite el sitio [Referencia de la API de auditoría de Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-api-audit-reference) para obtener más información sobre cómo conectarse a los informes de Azure Directory mediante programación.

### <a name="log-analytics"></a>Log Analytics

[Log Analytics](https://azure.microsoft.com/services/log-analytics/) puede [recopilar datos de Azure Monitor](https://docs.microsoft.com/azure/log-analytics/log-analytics-azure-storage) para correlacionarlos con otros datos y proporcionar análisis adicional. Azure Monitor recopila y analiza datos de supervisión para su entorno de Azure. 

Las herramientas de análisis de Log Analytics, como búsquedas de registros, vistas y soluciones funcionan con todos los datos recopilados, que proceden del análisis centralizado de todo el entorno. Log Analytics puede agregar y analizar registros de eventos de Windows, registros IIS y registros de Syslog, que pueden ayudar a detectar infracciones potenciales de datos personales que podrían exponer estos a usuarios no autorizados.

#### <a name="how-do-i-use-log-analytics"></a>¿Cómo se puede usar Log Analytics?

Puede tener acceso a Log Analytics a través del portal de OMS o Azure Portal desde cualquier explorador web. Log Analytics incluye un lenguaje de consulta para recuperar y consolidar rápidamente los datos en el repositorio. Puede crear y guardar búsquedas de registros para analizar datos directamente en el portal.

Para crear un área de trabajo de Log Analytics en Azure Portal, realice lo siguiente:

1. Seleccione **Log Analytics** de la lista de servicios de Marketplace.

2. Seleccione **Crear** y, a continuación, especifique el nombre del área de trabajo de OMS, seleccione la suscripción, el grupo de recursos, la ubicación y el plan de tarifa.

3. Haga clic en **Aceptar** y verá una lista de las áreas de trabajo.

4. Seleccione un área de trabajo para ver sus detalles.

    ![](media/protection-personal-data-azure-reporting-tools/image004.png)

Visite la [documentación de Log Analytics](https://docs.microsoft.com/azure/log-analytics/log-analytics-overview) para obtener más información sobre el servicio.

Visite el tutorial [Introducción a un área de trabajo de Log Analytics](https://docs.microsoft.com/azure/log-analytics/log-analytics-get-started) para crear un área de trabajo de evaluación y aprender los conceptos básicos de cómo usar el servicio.

Visite las siguientes páginas web para obtener información más específica sobre cómo conectarse para usar Log Analytics con los registros descritos anteriormente:

[Orígenes de datos de registros de eventos de Windows en Log Analytics](https://docs.microsoft.com/azure/log-analytics/log-analytics-data-sources-windows-events)

[Registros de IIS en Log Analytics](https://docs.microsoft.com/azure/log-analytics/log-analytics-data-sources-iis-logs)

[Orígenes de datos de Syslog en Log Analytics](https://docs.microsoft.com/azure/log-analytics/log-analytics-data-sources-syslog)

### <a name="azure-monitorazure-activity-log"></a>Azure Monitor o registro de actividad de Azure 

[Azure Monitor](https://azure.microsoft.com/services/monitor/) ofrece registros y métricas de infraestructuras a nivel básico para la mayoría de los servicios de Microsoft Azure.
La supervisión puede ayudarle a obtener información detallada sobre sus aplicaciones de Azure. Azure Monitor se basa en la extensión Diagnósticos de Azure (Windows o Linux) para recopilar la mayoría de los registros y las métricas a nivel de aplicación. [El registro de actividad de Azure](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs) es uno de los recursos que puede ver con Azure Monitor. Realiza un seguimiento de todas las llamadas API y proporciona abundante información sobre las actividades que se producen en [Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview).
Puede buscar el registro de actividad (anteriormente llamado registro operativo o de actividad) para obtener información sobre los recursos, tal y como se ven en la infraestructura de Azure. 

Aunque mucha de la información recogida en el registro de actividad pertenece al estado del servicio y rendimiento, también hay información relacionada con la protección de datos. Con el registro de actividad, se pueden determinar los interrogantes “qué, quién y cuándo” de las operaciones de escritura (PUT, POST, DELETE) en los recursos de la suscripción de Azure.

Por ejemplo, proporciona un registro cuando un administrador elimina un grupo de seguridad de red, que podría afectar a la protección de datos personales. Las entradas de registros de actividad se almacenan en Azure Monitor durante 90 días.

#### <a name="how-do-i-use-the-data-collected-by-azure-monitor"></a>¿Cómo se pueden usar los datos recopilados por Azure Monitor?

Existen varias formas de usar los datos en el registro de actividad y otros recursos de Azure Monitor.

- Puede transmitir los datos a otras ubicaciones en tiempo real.

- Puede almacenar los datos durante períodos de tiempo más largos que los predeterminados, mediante el uso de una [cuenta de Azure Storage](https://docs.microsoft.com/azure/storage/common/storage-introduction) y el establecimiento de una directiva de retención.

- Puede visualizar los datos en gráficos y diagramas, mediante [Azure Portal](https://azure.microsoft.com/features/azure-portal/), [Azure Application Insights](https://azure.microsoft.com/services/application-insights/), [Microsoft PowerBI](https://powerbi.microsoft.com/) o herramientas de visualización externas.

- Puede consultar los datos mediante la API de REST de Azure Monitor, comandos de la CLI, cmdlets de [PowerShell](https://docs.microsoft.com/powershell/) o el SDK de .NET.

Para empezar a trabajar con Azure Monitor, seleccione **Más servicios** en Azure Portal.

1. Desplácese hacia abajo hasta **Monitor** en la sección **Supervisión y administración**.

    ![](media/protection-personal-data-azure-reporting-tools/image005.png)

2.  Monitor se abre en la vista **Registro de actividad**.

    ![](media/protection-personal-data-azure-reporting-tools/image007.png)

Puede crear y guardar las consultas para los filtros comunes y luego anclar las consultas más importantes en un panel del portal, por lo que siempre sabrá si se han producido eventos que cumplen los criterios.

1. Puede filtrar la vista por grupo de recursos, intervalo de tiempo y categoría de eventos.

    ![](media/protection-personal-data-azure-reporting-tools/image008.png)

2. A continuación, puede anclar consultas a un panel del portal haciendo clic en el botón **Anclar**. Esto ayuda a crear un único origen de información de datos operativos de los servicios. El nombre de la consulta y el número de resultados se mostrarán en el panel.

También puede usar Monitor para ver métricas de todos los recursos de Azure, establecer la configuración de diagnóstico y configurar alertas, y buscar el registro. Para obtener más información sobre cómo usar Azure Monitor y el registro de actividad, consulte [Introducción a Azure Monitor](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-get-started).

### <a name="azure-diagnostics"></a>Diagnóstico de Azure 

La funcionalidad de diagnóstico de Azure habilita la recopilación de datos de varios orígenes. Los registros de eventos de Windows, que incluyen el registro de seguridad, pueden resultar especialmente útiles a la hora de documentar y realizar un seguimiento de la protección de datos personales. El registro de seguridad realiza un seguimiento de eventos de aciertos y errores de inicio de sesión, así como de cambios de permisos, detección de patrones que indican determinados tipos de ataques, cambios en directivas relacionadas con la seguridad, cambios de pertenencia a grupos de seguridad y mucho más.

Por ejemplo, el id. de evento 4695 le indica los intentos de desprotección de datos protegidos auditables. Esto pertenece a la API de protección de datos (DPAPI), que ayuda a proteger datos como claves privadas, credenciales almacenadas y otra información confidencial.

#### <a name="how-do-i-enable-the-diagnostics-extension-for-windows-vms"></a>¿Cómo se habilita la extensión de diagnósticos para máquinas virtuales Windows?

Puede usar PowerShell para habilitar la extensión de diagnósticos para una máquina virtual Windows, a fin de recopilar datos de registro. Los pasos para hacerlo dependen del modelo de implementación que use (Resource Manager o clásico). Para habilitar la extensión de diagnósticos en una VM existente creada mediante el modelo de implementación de Resource Manager, puede usar el [cmdlet de PowerShell Set-AzureRMVMDiagnosticsExtension](https://docs.microsoft.com/powershell/module/azurerm.compute/set-azurermvmdiagnosticsextension?view=azurermps-4.3.1).

   ![](media/protection-personal-data-azure-reporting-tools/image009.png)

*\$diagnosticsconfig_path* es la ruta de acceso del archivo que contiene la configuración de diagnóstico en XML. Para obtener instrucciones más detalladas sobre cómo habilitar Azure Diagnostics en una VM, consulte [Uso de PowerShell para habilitar Azure Diagnostics en una máquina virtual con Windows.](https://docs.microsoft.com/azure/virtual-machines/windows/ps-extensions-diagnostics)

La extensión de Azure Diagnostics puede transferir los datos recopilados a una cuenta de Azure Storage o enviarlos a servicios como Application Insights. A continuación, puede usar los datos para realizar la auditoría.

#### <a name="how-do-i-store-and-view-diagnostic-data"></a>¿Cómo se pueden almacenar y ver los datos de diagnóstico?

Es importante recordar que los datos de diagnóstico no se almacenan de forma permanente a menos que se transfieran al emulador de Microsoft Azure Storage o a Azure Storage. Para almacenar y ver datos de diagnóstico en Azure Storage, siga estos pasos:

1. Especifique una cuenta de almacenamiento en el archivo ServiceConfiguration.cscfg. Azure Diagnostics puede usar Blob service o Table service, dependiendo del tipo de datos. Los registros de eventos de Windows se almacenan con formato de tabla.

2. Transfiera los datos. Puede solicitar la transferencia de los datos de diagnóstico a través del archivo de configuración. En el caso de SDK 2.4 y versiones anteriores, también puede realizar la solicitud mediante programación.

3. Vea los datos mediante [Explorador de Azure Storage](https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer),  [Explorador de servidores](https://docs.microsoft.com/azure/vs-azure-tools-storage-resources-server-explorer-browse-manage) en Visual Studio o [Azure Diagnostics Manager](https://www.cerebrata.com/products/azure-diagnostics-manager) en Azure Management Studio.

Para obtener más información sobre cómo realizar cada uno de estos pasos, consulte [Almacenamiento y visualización de los datos de diagnóstico en Azure Storage.](https://docs.microsoft.com/azure/cloud-services/cloud-services-dotnet-diagnostics-storage)

### <a name="azure-storage-analytics"></a>Azure Storage Analytics 

Storage Analytics registra información detallada sobre las solicitudes correctas y erróneas realizadas a un servicio de almacenamiento. Esta información se puede usar para supervisar solicitudes individuales, lo que puede resultar de utilidad a la hora de documentar el acceso a datos personales almacenados en el servicio. Sin embargo, el registro de Storage Analytics no se habilita de forma predeterminada para su cuenta de almacenamiento. Puede habilitarlo en Azure Portal.

#### <a name="how-do-i-configure-monitoring-for-a-storage-account"></a>¿Cómo se puede configurar la supervisión para una cuenta de almacenamiento?

Para configurar la supervisión para una cuenta de almacenamiento, realice lo siguiente:

1. Seleccione **Cuentas de almacenamiento** en Azure Portal y, a continuación, seleccione el nombre de la cuanta que desee supervisar.

    ![](media/protection-personal-data-azure-reporting-tools/image011.png)

2. En la sección **Supervisión**, seleccione **Diagnósticos.**

3.  Seleccione el **tipo** de datos de métricas que desea supervisar para cada servicio (Blob, Table, File). Para indicar a Azure Storage que guarde los registros de diagnóstico de lectura, escritura y eliminación de las solicitudes de Blob service, Table service y Queue service, seleccione **registros de blob, registros de tabla** y **registros de cola.**

    ![](media/protection-personal-data-azure-reporting-tools/image013.png)

4. Mediante el control deslizante en la parte inferior, establezca la directiva de **retención** en días (valor entre uno y 365). Siete días es el valor predeterminado.

5. Seleccione **Guardar** para aplicar las opciones de configuración.

Las entradas de registro Registro de almacenamiento contienen la siguiente información sobre solicitudes individuales:

- Información de tiempos como hora de inicio, latencia de un extremo a otro y latencia del servidor.

- Detalles de la operación de almacenamiento como el tipo de operación, la clave del objeto de almacenamiento al que el cliente tiene acceso, aciertos o errores y el código de estado HTTP devuelto al cliente.

- Detalles de autenticación como el tipo de autenticación empleado por el cliente.

- Información de simultaneidad como el valor ETag y la marca de tiempo de última modificación.

- Tamaños de los mensajes de solicitud y respuesta.

Para obtener instrucciones más detalladas sobre cómo habilitar el registro de Storage Analytics, consulte [Supervisión de una cuenta de almacenamiento en Azure Portal.](https://docs.microsoft.com/azure/storage/common/storage-monitor-storage-account)

### <a name="azure-security-center"></a>Azure Security Center 

[Azure Security Center](https://azure.microsoft.com/services/security-center/) supervisa el estado de seguridad de los recursos de Azure para evitar y detectar amenazas, así como para proporcionar recomendaciones de respuesta. Ofrece varias formas de contribuir a la documentación de las medidas de seguridad que protegen la privacidad de los datos personales.

La supervisión del estado de seguridad le ayuda a garantizar el cumplimiento de sus directivas de seguridad. La supervisión de seguridad es una estrategia proactiva que audita los recursos a fin de identificar los sistemas que no cumplen con los estándares o los procedimientos recomendados de la organización. Puede supervisar el estado de seguridad de los siguientes recursos:

- Compute (máquinas virtuales y servicios en la nube)

- Redes (redes virtuales)

- Almacenamiento y datos (detección de amenazas y auditoría de la base de datos y el servidor, TDE y cifrado de almacenamiento)

- Aplicaciones (posibles problemas de seguridad)

Los problemas de seguridad que puedan producirse en cualquiera de estas categorías podrían suponer una amenaza contra la privacidad de los datos personales.

#### <a name="how-do-i-view-the-security-state-of-my-azure-resources"></a>¿Cómo se puede ver el estado de seguridad de los recursos de Azure?

El Centro de seguridad analiza periódicamente el estado de seguridad de los recursos de Azure. Puede ver cualquier posible vulnerabilidad de la seguridad que identifique en la sección **Prevención** del panel.

   ![](media/protection-personal-data-azure-reporting-tools/image014.png)

1. En la sección **Prevención**, seleccione el icono **Compute**. Aquí verá **Información general,** junto con el listado **Virtual Machines** de todas las máquinas virtuales y sus estados de seguridad, y la lista **Cloud Services** de roles web y de trabajo que supervisa Security Center.

2. En la pestaña **Información general**, secunde una recomendación para ver más información.

3. En la pestaña **Máquinas virtuales**, seleccione una máquina virtual para ver detalles adicionales.

Cuando se habilita la recopilación de datos en Azure Security Center, Microsoft Monitoring Agent se aprovisiona automáticamente en todas las máquinas virtuales existentes y recién admitidas que estén implementadas. Los datos que recopila este agente se almacenan en un área de trabajo de [Log Analytics](https://azure.microsoft.com/services/log-analytics/) asociada con la suscripción o en una nueva área de trabajo.

Security Center proporciona [informes de inteligencia de amenazas](https://docs.microsoft.com/azure/security-center/security-center-threat-report). En estos se proporciona información útil para ayudar a distinguir la identidad del atacante, los objetivos, las campañas de ataques actuales e históricas y las tácticas, herramientas y procedimientos usados. También se incluye información de corrección y mitigación.

El principal fin de estos informes de amenazas es ayudarle a responder de forma eficaz a la inminente amenaza y a tomar medidas posteriormente para mitigar el problema. La información de los informes también puede resultar de utilidad al documentar la respuesta de cara a los informes y la auditoría.

Los informes de inteligencia de amenazas se presentan en formato .PDF, teniéndose acceso a los mismos a través de un vínculo del campo **Informes** de la hoja **Proceso sospechoso ejecutado** para cada alerta de seguridad de Azure Security Center.

Para obtener más información sobre cómo ver y usar el informe de inteligencia de amenazas, consulte [Informe de inteligencia frente a amenazas de Azure Security Center.](https://docs.microsoft.com/azure/security-center/security-center-threat-report)

## <a name="next-steps"></a>Pasos siguientes:

[Introducción a la API de generación de informes de Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-api-getting-started-azure-portal)

[¿Qué es Log Analytics?](https://docs.microsoft.com/azure/log-analytics/log-analytics-overview)

[Información general sobre la supervisión en Microsoft Azure](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview)

[Introduction to the Azure Activity Log (Presentación del registro de actividad de Azure) (vídeo)](https://azure.microsoft.com/resources/videos/intro-activity-log/)
