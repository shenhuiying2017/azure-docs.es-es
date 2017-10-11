---
title: "Solución DNS Analytics en Azure Log Analytics | Microsoft Docs"
description: "Configure y use la solución DNS Analytics en Log Analytics para recopilar información en la infraestructura de DNS sobre seguridad, rendimiento y operaciones."
services: log-analytics
documentationcenter: 
author: bandersmsft
manager: carmonm
editor: 
ms.assetid: f44a40c4-820a-406e-8c40-70bd8dc67ae7
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/07/2017
ms.author: banders
ms.openlocfilehash: 0e8fc0ffb8e0d0bdf00bea46594fe050c00b6c8e
ms.sourcegitcommit: 02e69c4a9d17645633357fe3d46677c2ff22c85a
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 08/03/2017
---
# <a name="gather-insights-about-your-dns-infrastructure-with-the-dns-analytics-preview-solution"></a>Recopilación de información sobre la infraestructura de DNS con la solución DNS Analytics

![Símbolo de DNS Analytics](./media/log-analytics-dns/dns-analytics-symbol.png)

En este artículo se describe cómo configurar y usar la solución Azure DNS Analytics en Azure Log Analytics para recopilar información en la infraestructura de DNS sobre seguridad, rendimiento y operaciones.

DNS Analytics le ayuda a:

- Identificar a los clientes que intentan resolver nombres de dominio malintencionados.
- Identificar los registros de recursos obsoletos.
- Identificar los nombres de dominio consultados con más frecuencia y los clientes DNS participativos.
- Ver la carga de solicitudes en los servidores DNS.
- Ver errores de registro DNS dinámicos.

La solución recopila, analiza y correlaciona los registros analíticos y de auditoría de Windows DNS y otros datos relacionados a partir de los servidores DNS.

## <a name="connected-sources"></a>Orígenes conectados

En la tabla siguiente se describen los orígenes conectados que son compatibles con esta solución:

| **Origen conectado** | **Soporte técnico** | **Descripción** |
| --- | --- | --- |
| [Agentes de Windows](log-analytics-windows-agents.md) | Sí | La solución recopila información de DNS de los agentes de Windows. |
| [Agentes de Linux](log-analytics-linux-agents.md) | No | La solución no recopila información de DNS de los agentes directos de Linux. |
| [Grupo de administración de System Center Operations](log-analytics-om-agents.md) | Sí | La solución recopila información de DNS de los agentes en un grupo de administración de Operations Manager conectado. No se requiere conexión directa entre el agente de Operations Manager y Operations Management Suite. Los datos se reenvían del grupo de administración al repositorio de Operations Management Suite. |
| [Cuenta de Azure Storage](log-analytics-azure-storage.md) | No | La solución no usa Azure Storage. |

### <a name="data-collection-details"></a>Detalles de la recopilación de datos

La solución permite recopilar inventario y datos relacionados con eventos de DNS de los servidores DNS donde se ha instalado un agente de Log Analytics. Posteriormente, estos datos se cargan en Log Analytics y se muestran en el panel de la solución. Los datos relacionados con el inventario como, por ejemplo, el número de servidores DNS, las zonas y los registros de recursos se recopilan con la ejecución de cmdlets de Powershell para DNS. Los datos se actualizan una vez cada dos días. Los datos relacionados con eventos se recopilan casi en tiempo real a partir de los [registros analíticos y de auditoría](https://technet.microsoft.com/library/dn800669.aspx#enhanc) proporcionados por las funcionalidades mejoradas de registro y diagnóstico de Windows Server 2012 R2.

## <a name="configuration"></a>Configuración

Use la siguiente información para configurar la solución:

- Debe tener un agente de [Windows](log-analytics-windows-agents.md) o de [Operations Manager](log-analytics-om-agents.md) en cada servidor DNS que desee supervisar.
- Puede agregar la solución DNS Analytics al área de trabajo de Operations Management Suite desde [Azure Marketplace](https://aka.ms/dnsanalyticsazuremarketplace). También puede usar el proceso que se describe en [Incorporación de soluciones de Log Analytics desde la galería de soluciones](log-analytics-add-solutions.md).

La solución empezará a recopilar datos sin necesidad de ninguna configuración adicional. Sin embargo, puede usar la siguiente configuración para personalizar la recopilación de datos.

### <a name="configure-the-solution"></a>Configuración de la solución

En el panel de soluciones, haga clic en **Configuración** para abrir la página de configuración de DNS Analytics. Hay dos tipos de cambios de configuración que puede realizar:

- **Nombres de dominio de la lista de permitidos**. La solución no procesa todas las consultas de búsqueda. Tiene una lista de los sufijos de nombres de dominio permitidos. Las consultas de búsqueda que se resuelven en los nombres de dominio que coinciden con los sufijos de nombres de dominio de esta lista de permitidos no las procesa la solución. No procesar los nombres de dominio de la lista de permitidos ayuda a optimizar los datos enviados a Log Analytics. La lista de permitidos predeterminada incluye nombres de dominio público populares, como www.google.com y www.facebook.com. Puede ver la lista predeterminada completa si se desplaza.

 Puede modificar la lista para agregar cualquier sufijo de nombre de dominio del que no le interese ver información de búsqueda. También puede quitar cualquier sufijo de nombre de dominio del que no le interese ver información de búsqueda.

- **Umbral de clientes participativos**. Los clientes DNS que superan el umbral del número de solicitudes de búsqueda se resaltan en la hoja **Clientes DNS**. El umbral predeterminado es 1000. Puede modificar el umbral.

    ![Nombre de dominio de la lista de permitidos](./media/log-analytics-dns/dns-config.png)

## <a name="management-packs"></a>Módulos de administración

Si usa Microsoft Monitoring Agent para conectarse con el área de trabajo de Operations Management Suite, se instalará el siguiente módulo de administración:

- Microsoft DNS Data Collector Intelligence Pack (Microsft.IntelligencePacks.Dns)

Si el grupo de administración de Operations Manager está conectado al área de trabajo de Operations Management Suite, se instalarán los siguientes módulos de administración en Operations Manager al agregar esta solución. No es necesario realizar tareas de configuración o mantenimiento de estos módulos de administración:

- Microsoft DNS Data Collector Intelligence Pack (Microsft.IntelligencePacks.Dns)
- Microsoft System Center Advisor DNS Analytics Configuration (Microsoft.IntelligencePack.Dns.Configuration)

Para obtener más información sobre cómo se actualizan los módulos de administración de soluciones, consulte [Conexión de Operations Manager con Log Analytics](log-analytics-om-agents.md).

## <a name="use-the-dns-analytics-solution"></a>Uso de la solución DNS Analytics

En esta sección se explican todas las funciones del panel y cómo utilizarlas.

Después de agregar la solución al área de trabajo, el icono de la solución en la página de información general de Operations Management Suite proporciona un resumen rápido de la infraestructura DNS. Incluye la cantidad de servidores DNS donde se recopilan los datos. También incluye la cantidad de solicitudes que los clientes hicieron para solucionar dominios malintencionados durante las últimas 24 horas. Al hacer clic en el icono, se abrirá el panel de la solución.

![Icono de DNS Analytics](./media/log-analytics-dns/dns-tile.png)

### <a name="solution-dashboard"></a>Panel de soluciones

El panel de la solución muestra información resumida de las distintas características de la solución. También incluye vínculos a la vista detallada para un análisis y diagnóstico en profundidad. De forma predeterminada, se muestran los datos de los últimos siete días. Puede cambiar el intervalo de fecha y hora con el **control de selección de fecha y hora**, tal como se muestra en la imagen siguiente:

![Control de selección de hora](./media/log-analytics-dns/dns-time.png)

El panel de la solución muestra las siguientes hojas:

**Seguridad de DNS**. Informa los clientes DNS que están intentando comunicarse con dominios malintencionados. Mediante el uso de las fuentes de inteligencia de amenazas de Microsoft, DNS Analytics puede detectar las direcciones IP de clientes que están intentando acceder a los dominios malintencionados. En muchos casos, los dispositivos infectados con malware "marcan" al centro de "comando y control" del dominio malintencionado resolviendo el nombre de dominio del malware.

![Hoja Seguridad de DNS](./media/log-analytics-dns/dns-security-blade.png)

Al hacer clic en una dirección IP de cliente de la lista, se abre Búsqueda de registros y muestra los detalles de la búsqueda de la consulta correspondiente. En el siguiente ejemplo, DNS Analytics detectó que la comunicación se ha realizado con un [IRCbot](https://www.microsoft.com/security/portal/threat/encyclopedia/entry.aspx?Name=Win32/IRCbot):

![Resultados de Búsqueda de registros que muestran ircbot](./media/log-analytics-dns/ircbot.png)

La información puede ayudarle a identificar:

- La dirección IP de cliente que inició la comunicación.
- El nombre de dominio que se resuelve en la dirección IP malintencionada.
- Las direcciones IP en las que se resuelve el nombre de dominio.
- La dirección IP malintencionada.
- La gravedad del problema.
- El motivo para incluir en la lista de denegadas la dirección IP malintencionada.
- La hora de detección.

**Dominios consultados**. Proporciona los nombres de los dominios que los clientes DNS consultan con más frecuencia en el entorno. Puede ver la lista de todos los nombres de dominios consultados. También puede profundizar en los detalles de la solicitud de búsqueda de un nombre de dominio específico en Búsqueda de registros.

![Hoja Dominios consultados](./media/log-analytics-dns/domains-queried-blade.png)

**Clientes DNS**. Informa los clientes que *superan el umbral* del número de consultas en el período de tiempo seleccionado. Puede ver la lista de todos los clientes DNS y los detalles de las consultas efectuadas por ellos mediante Búsqueda de registros.

![Hoja Clientes DNS](./media/log-analytics-dns/dns-clients-blade.png)

**Registros de DNS dinámico**. Informa errores de registro del agente. Todos los errores de registro de los [registros de recursos](https://en.wikipedia.org/wiki/List_of_DNS_record_types) de direcciones (tipos A y AAAA) se resaltan junto con el de la dirección IP que realizó las solicitudes de registro. A continuación, puede usar esta información para buscar la causa raíz del error de registro con estos pasos:

1. Busque la zona que sea autoritativa para el nombre que el cliente está intentando actualizar.

2. Use la solución para comprobar la información de inventario de esa zona.

3. Compruebe que está habilitada la actualización dinámica de la zona.

4. Compruebe si la zona está configurada para la actualización dinámica segura o no.

    ![Hoja Registros de DNS dinámico](./media/log-analytics-dns/dynamic-dns-reg-blade.png)

**Solicitudes de registro de nombres**. El icono superior muestra una tendencia de solicitudes de actualización dinámica de DNS correctos y con error. El icono inferior muestra los principales 10 clientes que envían solicitudes de actualización de DNS con errores a los servidores DNS, ordenados por el número de errores.

![Hoja Solicitudes de registro de nombres ](./media/log-analytics-dns/name-reg-req-blade.png)

**Consultas de DDI Analytics de ejemplo**. Contiene una lista de las consultas de búsqueda más comunes que capturan directamente datos de análisis sin procesar.

[!include[log-analytics-log-search-nextgeneration](../../includes/log-analytics-log-search-nextgeneration.md)]

![Consultas de ejemplo](./media/log-analytics-dns/queries.png)

Puede usar estas consultas como punto de partida a fin de crear las suyas propias para generar informes personalizados. Las consultas vinculan a la página Búsqueda de registros de DNS Analytics donde se muestran los resultados:

- **Lista de servidores DNS**. Muestra una lista de todos los servidores DNS con el nombre de dominio público, el nombre de dominio, el nombre del bloque y las direcciones IP de servidor asociados.
- **Lista de zonas DNS**. Muestra una lista de todas las zonas DNS con el nombre de zona, el estado de actualización dinámica, los servidores de nombres y el estado de la firma DNSSEC asociados.
- **Registros de recursos sin usar**. Muestra una lista de todos los registros de recursos obsoletos/sin usar. Esta lista contiene el nombre de registro de recurso, el tipo de registro, el servidor DNS asociado, la hora de generación de los registros y el nombre de la zona. Puede usar esta lista para identificar los registros de recursos DNS que ya no están en uso. En función de esta información puede, posteriormente, quitar esas entradas de los servidores DNS.
- **Carga de consultas de servidores DNS**. Muestra información para que pueda obtener una perspectiva de la carga DNS en los servidores DNS. Esta información puede ayudarlo a planea la capacidad de los servidores. Puede ir a la pestaña **Métricas** para cambiar la vista a una visualización gráfica. Esta vista le ayudará a comprender cómo se distribuye la carga DNS entre todos los servidores DNS. Muestra las tendencias de velocidad de consultas de DNS de cada servidor.

    ![Resultados de Búsqueda de registros de consultas de servidores DNS](./media/log-analytics-dns/dns-servers-query-load.png)

- **Carga de consultas de zonas DNS**. Muestra las estadísticas de consulta de zona DNS por segundo de todas las zonas en los servidores DNS que administra la solución. Haga clic en la pestaña **Métricas** para cambiar la vista de registros detallados a una visualización gráfica de los resultados.
- **Eventos de configuración**. Muestra todos los eventos de cambio de configuración de DNS y los mensajes asociados. A continuación, puede filtrar estos eventos en función de la hora del evento, el identificador de este, el servidor DNS o la categoría de la tarea. Los datos pueden ayudarle a auditar los cambios realizados en servidores DNS específicos a horas específicas.
- **Registro analítico de DNS**. Muestra todos los eventos analíticos de todos los servidores DNS que administra la solución. A continuación, puede filtrar estos eventos en función de la hora del evento, el identificador de este, el servidor DNS, la dirección IP del cliente que realizó la consulta de búsqueda y la categoría de la tarea del tipo de consulta. Los eventos analíticos del servidor DNS habilitan el seguimiento de actividades en este. Un evento analítico se registra cada vez que el servidor envía o recibe información de DNS.

### <a name="search-by-using-dns-analytics-log-search"></a>Búsqueda mediante Búsqueda de registros de DNS Analytics

En la página Búsqueda de registros, puede crear una consulta. Puede filtrar los resultados de la búsqueda mediante controles de faceta. También puede crear consultas avanzadas para transformar, filtrar y informar sobre sus resultados. Comience con las siguientes consultas:

1. En el **cuadro de consulta de búsqueda**, escriba `Type=DnsEvents` para ver todos los eventos DNS generados por los servidores DNS administrados por la solución. Los resultados muestran los datos de registro de todos los eventos relacionados con consultas de búsqueda, registros dinámicos y cambios de configuración.

    ![Búsqueda de registros de Dnsevents](./media/log-analytics-dns/log-search-dnsevents.png)  

    a. Para ver los datos de registro de las consultas de búsqueda, seleccione **LookUpQuery** como el filtro de **subtipo** en el control de faceta que se encuentra a la izquierda. Aparecerá una tabla que muestra todos los eventos de consulta de búsqueda para el período de tiempo seleccionado.

    b. Para ver los datos de registro de los registros dinámicos, seleccione **DynamicRegistration** como el filtro de **subtipo** en el control de faceta que se encuentra a la izquierda. Aparecerá una tabla que muestra todos los eventos de registro dinámicos para el período de tiempo seleccionado.

    c. Para ver los datos de registro de los cambios de configuración, seleccione **ConfigurationChange** como el filtro de **subtipo** en el control de faceta que se encuentra a la izquierda. Aparecerá una tabla que muestra todos los eventos de cambios de configuración para el período de tiempo seleccionado.

2. En el **cuadro de consulta de búsqueda**, escriba `Type=DnsInventory` para ver todos los datos relacionados con el inventario de DNS de los servidores DNS administrados por la solución. Los resultados muestran los datos del registro para los servidores DNS, las zonas DNS y los registros de recursos.

    ![Búsqueda de registros de DnsInventory](./media/log-analytics-dns/log-search-dnsinventory.png)

## <a name="feedback"></a>Comentarios

Hay dos maneras de proporcionar comentarios:

- **UserVoice**. Publique ideas sobre características de DNS Analytics en las que trabajar. Visite la [página UserVoice de Operations Management Suite](https://aka.ms/dnsanalyticsuservoice).
- **Únase a nuestra cohorte**. Siempre estamos interesados en que se unan nuevos clientes a nuestra comunidad para que consigan los primeros las nuevas características y nos ayuden a mejorar DNS Analytics. Si está interesado en unirse, rellene esta [encuesta rápida](https://aka.ms/dnsanalyticssurvey).

## <a name="next-steps"></a>Pasos siguientes

[Búsqueda de registros](log-analytics-log-searches.md), para ver los registros de datos detallados de DNS.
