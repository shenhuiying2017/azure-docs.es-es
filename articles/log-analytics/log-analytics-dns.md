---
title: "Solución DNS Analytics en Azure Log Analytics | Microsoft Docs"
description: "Configure y use la solución DNS Analytics de Log Analytics para recopilar más información sobre seguridad, rendimiento y operaciones en la infraestructura de DNS."
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
ms.date: 04/18/2017
ms.author: banders
translationtype: Human Translation
ms.sourcegitcommit: 8c4e33a63f39d22c336efd9d77def098bd4fa0df
ms.openlocfilehash: 4473ca68374b96f10c60282135e83f7ec390bb48
ms.lasthandoff: 04/20/2017


---

# <a name="gather-insights-about-your-dns-infrastructure-with-the-dns-analytics-preview-solution"></a>Recopilación de información sobre la infraestructura de DNS con la solución DNS Analytics (versión preliminar)

En este artículo se explica cómo configurar y usar la solución DNS Analytics de Log Analytics para recopilar más información sobre seguridad, rendimiento y operaciones en la infraestructura de DNS.

DNS Analytics le ayuda a:

- Identificar a los clientes que intentan resolver nombres de dominio malintencionados
- Identificar los registros de recursos obsoletos
- Identificar los nombres de dominio consultados con más frecuencia y los clientes DNS participativos
- Ver la carga de solicitudes en los servidores DNS
- Ver errores de registro DNS dinámicos

La solución recopila, analiza y correlaciona los registros analíticos y de auditoría de Windows DNS y otros datos relacionados a partir de los servidores DNS.

## <a name="connected-sources"></a>Orígenes conectados

En la tabla siguiente se describen los orígenes conectados que son compatibles con esta solución.

| **Origen conectado** | **Soporte técnico** | **Descripción** |
| --- | --- | --- |
| [Agentes de Windows](log-analytics-windows-agents.md) | Sí | La solución recopila información de DNS de los agentes de Windows. |
| [Agentes de Linux](log-analytics-linux-agents.md) | No | La solución no recopila información de DNS de los agentes directos de Linux. |
| [Grupo de administración de SCOM](log-analytics-om-agents.md) | Sí | La solución recopila información de DNS de los agentes de un grupo de administración SCOM conectado. No se requiere una conexión directa entre el agente de SCOM y OMS. Los datos se reenvían del grupo de administración al repositorio de OMS. |
| [Cuenta de Azure Storage](log-analytics-azure-storage.md) | No | La solución no usa Azure Storage. |

### <a name="data-collection-details"></a>Detalles de la recopilación de datos

La solución permite recopilar inventario y datos relacionados con eventos de DNS de los servidores DNS donde se ha instalado un agente de Log Analytics. Posteriormente, estos datos se cargan en Log Analytics y se muestran en el panel de la solución. Los datos relacionados con el inventario como, por ejemplo, el número de servidores DNS, las zonas y los registros de recursos se recopilan con la ejecución de cmdlets de Powershell para DNS. Los datos se actualizan una vez cada dos días. Los datos relacionados con eventos se recopilan casi en tiempo real a partir de los [registros analíticos y de auditoría](https://technet.microsoft.com/library/dn800669.aspx#enhanc) proporcionados por las funcionalidades mejoradas de registro y diagnóstico de Windows Server 2012 R2.

## <a name="configuration"></a>Configuración

Utilice la siguiente información para configurar la solución.

- Debe tener un agente de [Windows](log-analytics-windows-agents.md) o de [Operations Manager](log-analytics-om-agents.md) en cada servidor DNS que desee supervisar.
- Agregue la solución DNS Analytics al área de trabajo de OMS desde [Azure Marketplace](https://aka.ms/dnsanalyticsazuremarketplace) o mediante el proceso descrito en el artículo sobre [incorporación de soluciones de Log Analytics desde la Galería de soluciones](log-analytics-add-solutions.md).

La solución empezará a recopilar datos sin necesidad de ninguna configuración adicional. Sin embargo, puede usar la siguiente configuración para personalizar la recopilación de datos.

###  <a name="configure-the-solution"></a>Configuración de la solución

En el panel de soluciones, haga clic en **Configuración** para abrir la página de configuración de DNS Analytics. Hay dos tipos de cambios de configuración que puede realizar:

- **Nombres de dominio de la lista de permitidos**: la solución no procesa todas las consultas de búsqueda. Tiene una lista de los sufijos de nombres de dominio permitidos. Las consultas de búsqueda, que se resuelven en los nombres de dominio que coinciden con los sufijos de nombres de dominio de esta lista de permitidos, no las procesa la solución. No procesar los nombres de dominio de la lista de permitidos ayuda a optimizar los datos enviados a Log Analytics. La lista de permitidos predeterminada incluye nombres de dominio público populares, como www.google.com y www.facebook.com. Puede ver la lista predeterminada completa mediante la barra de desplazamiento.

Puede modificar la lista para agregar (o quitar) cualquier sufijo de nombre de dominio del que no le interese ver información de búsqueda.

- **Umbral de cliente participativo**: los clientes DNS que superan el umbral del número de solicitudes de búsqueda, se resaltan en la hoja **Clientes DNS**. El umbral predeterminado es 1000. Puede modificar el umbral.

![nombres de dominio de la lista de permitidos](./media/log-analytics-dns/dns-config.png)

## <a name="management-packs"></a>Módulos de administración

Si está utilizando Microsoft Monitoring Agent (MMA) para conectarse a su área de trabajo de OMS, se instalará el siguiente módulo de administración.

- Microsoft DNS Data Collector Intelligence Pack (Microsft.IntelligencePacks.Dns)

Si el grupo de administración de SCOM está conectado al área de trabajo de OMS, los módulos de administración siguientes se instalarán en SCOM al agregar esta solución. No es necesario realizar tareas de configuración o mantenimiento de estos módulos de administración.

- Microsoft DNS Data Collector Intelligence Pack (Microsft.IntelligencePacks.Dns)
- Microsoft System Center Advisor DNS Analytics Configuration (Microsoft.IntelligencePack.Dns.Configuration)

Para obtener más información sobre cómo se actualizan los módulos de administración de soluciones, consulte [Conexión de Operations Manager con Log Analytics](log-analytics-om-agents.md).

## <a name="use-the-solution"></a>Uso de la solución

En esta sección se explican todas las funciones del panel y cómo utilizarlas.

Después de agregar la solución DNS Analytics al área de trabajo, el icono de la solución en la página de información general de OMS proporciona un resumen rápido de la infraestructura DNS. Incluye el número de servidores DNS en los que se están recopilando los datos y el número de solicitudes realizadas por los clientes para resolver dominios malintencionados en las últimas 24 horas. Al hacer clic en el icono, se abrirá el panel de la solución.

![Icono de DNS Analytics](./media/log-analytics-dns/dns-tile.png)

### <a name="solution-dashboard"></a>Panel de soluciones

El panel de la solución muestra información resumida de las distintas características de la solución. También incluye vínculos a la vista detallada para un análisis y diagnóstico en profundidad. De forma predeterminada, se muestran los datos de los últimos siete días. Puede cambiar el intervalo de fecha y hora con el control de selección de fecha y hora similar al de la siguiente imagen.

![control de selección de hora](./media/log-analytics-dns/dns-time.png)

El panel de la solución muestra las siguientes hojas:

**Seguridad de DNS**: informa de los clientes DNS que están intentando comunicarse con dominios malintencionados. Mediante el uso de las fuentes de inteligencia de amenazas de Microsoft, DNS Analytics puede detectar las direcciones IP de clientes que están intentando acceder a los dominios malintencionados. En muchos casos, los dispositivos infectados con malware &quot;marcan&quot; al centro de &quot;comando y control&quot; del dominio malintencionado resolviendo el nombre de dominio del malware.

![Hoja Seguridad de DNS](./media/log-analytics-dns/dns-security-blade.png)

Al hacer clic en una dirección IP de cliente de la lista, se abre Búsqueda de registros mostrando los detalles de la búsqueda de la consulta correspondiente. En el siguiente ejemplo, DNS Analytics detectó que la comunicación se ha realizado con un [IRCbot](https://www.microsoft.com/security/portal/threat/encyclopedia/entry.aspx?Name=Win32/IRCbot).

![resultados de Búsqueda de registros que muestran ircbot](./media/log-analytics-dns/ircbot.png)

La información puede ayudarle a identificar:

- la dirección IP de cliente que inició la comunicación
- el nombre de dominio que se resuelve en la dirección IP malintencionada
- las direcciones IP en las que se resuelve el nombre de dominio
- la dirección IP malintencionada
- la gravedad del problema
- el motivo para incluir en la lista de denegadas la dirección IP malintencionada
- la hora de detección

**Dominios consultados**: proporciona los nombres de los dominios que los clientes DNS consultan con más frecuencia en el entorno. Puede ver la lista de todos los nombres de dominio consultados y profundizar en los detalles de la solicitud de búsqueda de un nombre de dominio específico en Búsqueda de registros.

![Hoja Dominios consultados](./media/log-analytics-dns/domains-queried-blade.png)

**Clientes DNS**: informa de los clientes que **superan el umbral** del número de consultas en el período de tiempo seleccionado. Puede ver la lista de todos los clientes DNS y los detalles de las consultas efectuadas por ellos mediante Búsqueda de registros.

![Hoja Clientes DNS](./media/log-analytics-dns/dns-clients-blade.png)

**Registros de DNS dinámico**: informa sobre los errores de registro de nombres. Todos los errores de registro de los [registros de recursos](https://en.wikipedia.org/wiki/List_of_DNS_record_types) de direcciones (tipos A y AAAA) se resaltan junto con el de la dirección IP que realizó las solicitudes de registro. A continuación, puede usar esta información para buscar la causa raíz del error de registro:

1. Busque la zona que sea autoritativa para el nombre que el cliente está intentando actualizar.
2. Use la solución para comprobar la información de inventario de esa zona.
3. Compruebe que está habilitada la actualización dinámica de la zona.
4. Compruebe si la zona está configurada para la actualización dinámica segura o no.

![Hoja Registros de DNS dinámico](./media/log-analytics-dns/dynamic-dns-reg-blade.png)

**Solicitudes de registro de nombres**: el icono superior muestra una tendencia de recuentos de solicitudes de actualización dinámica de DNS correctos y con error. El icono inferior muestra los principales 10 clientes que envían solicitudes de actualización de DNS con errores a los servidores DNS, ordenados por el número de errores.

![Hoja Solicitudes de registro de nombres ](./media/log-analytics-dns/name-reg-req-blade.png)

**Consultas de DNS Analytics de ejemplo**: contiene una lista de las consultas de búsqueda más comunes que capturan directamente datos de análisis sin procesar.

![consultas de ejemplo](./media/log-analytics-dns/queries.png)

Puede usar estas consultas como punto de partida a fin de crear las suyas propias para generar informes personalizados.

- **Lista de servidores:** este vínculo abre la página de Búsqueda de registros de DNS que muestra una lista de todos los servidores DNS con su nombre de dominio completo (FQDN), nombre de dominio, nombre de bosque y direcciones IP del servidor asociados.
- **Lista de zonas DNS:** este vínculo abre la página de Búsqueda de registros de DNS que muestra una lista de todas las zonas DNS con el nombre de zona, el estado de actualización dinámica, los servidores de nombres y el estado de la firma DNSSEC asociados.
- **Registros de recursos no utilizados:** este vínculo abre la página de Búsqueda de registros de DNS y muestra una lista de todos los registros de recursos sin usar u obsoletos. Esta lista contiene el nombre de registro de recurso, el tipo de registro, el servidor DNS asociado, la hora de generación de los registros y el nombre de la zona. Puede usar esta lista para identificar los registros de recursos DNS que ya no están en uso. En función de esta información puede, posteriormente, actuar para quitar esas entradas de los servidores DNS.
- **Carga de consultas de los servidores DNS:** este vínculo abre la página de Búsqueda de registros de DNS donde puede obtener una perspectiva de la carga DNS en los servidores DNS para ayudarle a planear la capacidad de los servidores. Puede ir a la pestaña **Métricas** para cambiar la vista a una visualización gráfica. Esta vista le ayudará a comprender cómo se distribuye la carga DNS entre todos los servidores DNS. Muestra las tendencias de velocidad de consultas de DNS de cada servidor.
    ![Resultados de Búsqueda de registros de consultas de servidores DNS](./media/log-analytics-dns/dns-servers-query-load.png)  
- **Carga de consultas de las zonas DNS:** este vínculo abre la página de Búsqueda de registros de DNS donde puede ver las estadísticas de consultas por segundo de la zona DNS para todas las zonas de los servidores DNS que está administrando la solución. Haga clic en la pestaña **Métricas** para cambiar la vista de registros detallados a una visualización gráfica de los resultados.
- **Eventos de configuración:** este vínculo abre la página de Búsqueda de registros de DNS donde puede ver todos los eventos de cambio de configuración de DNS y los mensajes asociados. A continuación, puede filtrar estos eventos en función de la hora del evento, el identificador de este, el servidor DNS o la categoría de la tarea. Puede ayudarle a auditar los cambios realizados en servidores DNS específicos a horas específicas.
- **Registro analítico de DNS:** este vínculo abre la página de Búsqueda de registros de DNS donde puede ver todos los eventos analíticos de todos los servidores DNS administrados por la solución. A continuación, puede filtrar estos eventos en función de la hora del evento, el identificador de este, el servidor DNS, la dirección IP del cliente que realizó la consulta de búsqueda y la categoría de la tarea del tipo de consulta. Los eventos analíticos del servidor DNS habilitan el seguimiento de actividades en este. Un evento analítico se registra cada vez que el servidor envía o recibe información de DNS.

### <a name="dns-log-search"></a>Búsqueda de registros de DNS

En la página Buscar, puede crear una consulta y luego, al buscar, puede filtrar los resultados mediante controles de faceta. También puede crear consultas avanzadas para transformar, filtrar y informar sobre sus resultados. Puede comenzar mediante las siguientes consultas.

En el campo de consulta de búsqueda, escriba `Type=DnsEvents` para ver todos los eventos DNS generados por los servidores DNS administrados por la solución. Los resultados muestran los datos de registro de todos los eventos relacionados con consultas de búsqueda, registros dinámicos y cambios de configuración.

![búsqueda de registros de dnsevents](./media/log-analytics-dns/log-search-dnsevents.png)  

- Para ver los datos de registro de las consultas de búsqueda, filtre SubType por **LookUpQuery** desde el control de facetas LHS. Aparecerá una lista o tabla que contiene los eventos de consulta de búsqueda para el período de tiempo seleccionado.
- Para ver los datos del registro de los registros dinámicos, filtre SubType por **DynamicRegistration** desde el control de facetas LHS. Aparecerá una lista o tabla que contiene todos los eventos de registros dinámicos para el período de tiempo seleccionado.
- Para ver los datos del registro de los cambios de configuración, filtre SubType por **ConfigurationChange** desde el control de facetas LHS. Aparecerá una lista o tabla que contiene todos los cambios de configuración para el período de tiempo seleccionado.

En el campo de consulta de búsqueda, escriba `Type=DnsInventory` para ver todos los datos relacionados con el inventario de DNS de los servidores DNS administrados por la solución. Los resultados muestran los datos del registro para los servidores DNS, las zonas DNS y los registros de recursos.
![búsqueda de registros de dnsinventory](./media/log-analytics-dns/log-search-dnsinventory.png)

## <a name="feedback"></a>Comentarios

Hay dos alternativas diferentes para enviar sus comentarios:

- **UserVoice**: publique ideas sobre características de DNS Analytics en las que trabajar. Visite la [página de UserVoice de OMS](https://aka.ms/dnsanalyticsuservoice).
- **Únase a nuestra comunidad**: siempre estamos interesados en que se unan nuevos clientes a nuestra comunidad para que consigan los primeros las nuevas características y nos ayuden a mejorar DNS Analytics de aquí en adelante. Si está interesado en unirse, rellene esta [encuesta rápida](https://aka.ms/dnsanalyticssurvey).

## <a name="next-steps"></a>Pasos siguientes

- [Búsqueda de registros](log-analytics-log-searches.md), para ver los registros de datos detallados de DNS.

