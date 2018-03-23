---
title: Análisis de tráfico de Azure | Microsoft Docs
description: Aprenda a analizar los registros de flujo del grupo de seguridad de redes de Azure con Análisis de tráfico.
services: network-watcher
documentationcenter: na
author: jimdial
manager: jeconnoc
editor: ''
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2018
ms.author: jdial
ms.openlocfilehash: 9fc44fdd6ce01452ffc2506c599e3d05aa0803e1
ms.sourcegitcommit: 8c3267c34fc46c681ea476fee87f5fb0bf858f9e
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 03/09/2018
---
# <a name="traffic-analytics"></a>Análisis de tráfico

Análisis de tráfico es una solución basada en la nube, que proporciona visibilidad de la actividad de usuarios y aplicaciones en las redes en la nube. Análisis de tráfico permite analizar los registros de flujo del grupo de seguridad de Network Watcher para proporcionar información sobre el flujo de tráfico en la nube de Azure. Con Análisis de tráfico, puede:

- Visualizar la actividad de la red en las suscripciones de Azure e identificar las zonas activas.
- Identificar las amenazas de seguridad y proteger la red, con información sobre puertos abiertos, aplicaciones que intentan acceder a Internet y máquinas virtuales (VM) que se conectan a redes no autorizadas.
- Entender los patrones de flujo de tráfico entre regiones de Azure e Internet para optimizar el rendimiento y la capacidad de su implementación de red.
- Identificar errores de configuración de red que dan lugar a errores de conexión.

## <a name="why-traffic-analytics"></a>Motivos para usar Análisis de tráfico

Es esencial supervisar, administrar y conocer su propia red para no poner en peligro la seguridad, el cumplimiento y el rendimiento. Conocer su propio entorno es de gran importancia para protegerlo y optimizarlo. A menudo, es necesario conocer el estado actual de la red, quién se conecta y a qué se conecta, qué puertos están abiertos a Internet, el comportamiento esperado de la red, comportamientos irregulares de esta o picos repentinos de tráfico.

Las redes en la nube son diferentes a las redes empresariales locales, en las que se dispone de enrutadores y conmutadores compatibles con Netflow u otro protocolo equivalente, que proporcionan una funcionalidad para recopilar el tráfico de red de IP según entra o sale de una interfaz de red. Mediante el análisis de los datos de flujo de tráfico, puede crear un análisis del flujo y volumen del tráfico de red.

Las redes virtuales de Azure tienen registros de flujos de grupos de seguridad de red que le proporcionan información acerca del tráfico de IP de entrada y salida mediante un grupo de seguridad de red asociado a interfaces de red, máquinas virtuales o subredes individuales. Mediante el análisis de los registros de flujos del grupo de seguridad de red y la inserción de inteligencia de seguridad, topología y geografía, Análisis de tráfico puede proporcionarle información sobre el flujo de tráfico del entorno. Análisis de tráfico proporciona información sobre los hosts de mayor comunicación, los protocolos de las aplicaciones de mayor comunicación, los pares de hosts interlocutores, el tráfico permitido o bloqueado, el tráfico de entrada o salida, los puertos abiertos a Internet, las reglas de bloqueo, la distribución del tráfico por centro de datos de Azure, la red virtual, las subredes o las redes no autorizadas.

## <a name="key-components"></a>Componentes claves 

- **Grupo de seguridad de red (NSG)**: contiene una lista de reglas de seguridad que permiten o deniegan el tráfico de red a recursos conectados a Azure Virtual Network. Los grupos de seguridad de red se pueden asociar a subredes, máquinas virtuales individuales (clásicas) o interfaces de red (NIC) individuales conectadas a máquinas virtuales (Resource Manager). Para más información, consulte [Introducción a los grupos de seguridad de red](../virtual-network/security-overview.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json).
- **Los registros de flujo del grupo de seguridad de red** le permiten visualizar información sobre el tráfico IP de entrada y salida mediante un grupo de seguridad de red. Estos registros de flujo se escriben en formato JSON y muestran los flujos de entrada y salida en función de cada regla, la NIC a la que se aplica el flujo, información de 5-tupla sobre el flujo (dirección IP de origen o destino, puerto de origen o destino y protocolo), y si se permitió o denegó el tráfico. Para más información acerca de los registros de flujos de un grupo de seguridad de red, consulte [Introducción a los registros de flujo de grupos de seguridad de red](network-watcher-nsg-flow-logging-overview.md).
- **Log Analytics**: servicio de Azure que recopila datos de supervisión y almacena los datos en un repositorio central. Estos datos pueden incluir eventos, datos de rendimiento o datos personalizados proporcionados mediante la API de Azure. Una vez recopilados, los datos están disponibles para las alertas, el análisis y la exportación. Las aplicaciones de supervisión como Network Performance Monitor y Análisis de tráfico, se crean usando Log Analytics como base. Para más información, consulte [Log Analytics](../log-analytics/log-analytics-overview.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json).
- **Área de trabajo de Log Analytics**: una instancia de Log Analytics en la que se almacenan los datos que pertenecen a una cuenta de Azure. Para más información acerca de las áreas de trabajo de Log Analytics, consulte [Creación de un área de trabajo de Log Analytics](../log-analytics/log-analytics-quick-create-workspace.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json).
- **Network Watcher** es un servicio regional que permite supervisar y diagnosticar problemas en un nivel de escenario de red en Azure. Puede activar y desactivar los registros de flujo del grupo de seguridad de red con Network Watcher. Para más información, consulte [Network Watcher](network-watcher-monitoring-overview.md#network-watcher).

## <a name="how-traffic-analytics-works"></a>Funcionamiento de Análisis de tráfico 

Análisis de tráfico examina los registros de flujo sin formato del grupo de seguridad de red y captura los registros reducidos agregando los flujos habituales en la misma dirección IP de origen, IP de destino, puerto de destino y protocolo. Por ejemplo, el Host 1 (dirección IP: 10.10.10.10) en comunicación con el Host 2 (dirección IP: 10.10.20.10), 100 veces durante un período de 1 hora mediante un puerto (por ejemplo, 80) y un protocolo (por ejemplo, http). El registro reducido tiene una entrada que indica que el Host 1 y el Host 2 se comunicaron 100 veces durante un período de 1 hora mediante el puerto *80* y el protocolo *HTTP*, en lugar de incluir 100 entradas. Los registros reducidos se mejoran con información sobre geografía, seguridad y topología y, a continuación, se almacenan en un área de trabajo de Log Analytics. La siguiente imagen muestra el flujo de datos:

![Flujo de datos del procesamiento de registros de flujo del grupo de seguridad de red](media/traffic-analytics/data-flow-for-nsg-flow-log-processing.png)

## <a name="supported-regions"></a>Regiones admitidas

Análisis de tráfico está disponible en versión preliminar. Las características de la versión preliminar no tienen el mismo nivel de disponibilidad y confiabilidad que las características de la versión general.  Mientras está en versión preliminar, puede usar Análisis de tráfico para grupos de seguridad de red en cualquiera de las siguientes regiones: Centro-oeste de EE. UU., Este de EE. UU., Este de EE. UU. 2, Centro y norte de EE. UU., Centro y Sur de EE. UU., Centro de EE. UU., Oeste de EE. UU., Oeste de EE. UU. 2, Europa Occidental, Europa del Norte, Oeste de Reino Unido, Sur de Reino Unido, Este de Australia y Sudeste de Australia. El área de trabajo de Log Analytics debe existir en las regiones Centro-oeste de EE. UU., Este de EE. UU., Europa Occidental, Sudeste de Australia o Sur de Reino Unido.

## <a name="prerequisites"></a>requisitos previos

### <a name="enable-network-watcher"></a>Habilitación de Network Watcher 

Para analizar el tráfico, debe tener una instancia de Network Watcher existente, o [habilitar una instancia de esta](network-watcher-create.md) en cada región en la que haya grupos de seguridad de red de los que desee analizar el tráfico. Análisis de tráfico se puede habilitar para los grupos de seguridad de red hospedados en cualquiera de las [regiones admitidas](#supported-regions).

### <a name="re-register-the-network-resource-provider"></a>Volver a registrar el proveedor de recursos de red 

Para poder usar Análisis de tráfico durante la versión preliminar, debe volver a registrar el proveedor de recursos de red. Haga clic en **inténtelo** en el cuadro de código siguiente para abrir Azure Cloud Shell. Cloud Shell inicia la sesión automáticamente en su suscripción de Azure. Una vez abierto Cloud Shell, escriba el siguiente comando para volver a registrar el proveedor de recursos de red:

```azurepowershell-interactive
Register-AzureRmResourceProvider -ProviderNamespace "Microsoft.Network"
```

### <a name="select-a-network-security-group"></a>Selección de un grupo de seguridad de red 

Antes de habilitar el registro de flujos del grupo de seguridad de red, debe tener un grupo de seguridad de red del que registrar los flujos. Si no tiene ningún grupo de seguridad de red, consulte [Creación de grupos de seguridad de red](../virtual-network/virtual-networks-create-nsg-arm-pportal.md) para crear uno.

En el lado izquierdo de Azure Portal, seleccione **Monitor** y, a continuación, **Network Watcher**y, finalmente, **Registro de flujos de NSG**. Seleccione el grupo de seguridad de red para el que desee habilitar un registro de flujos, tal como se muestra en la siguiente imagen:

![Selección de los grupos de seguridad de red que requieren la habilitación del registro de flujos de NSG](media/traffic-analytics/selection-of-nsgs-that-require- enablement-of-nsg-flow-logging.png)

Si intenta habilitar Análisis de tráfico para un grupo de seguridad de red que se hospeda en una región distinta de las [regiones admitidas](#supported-regions), recibirá un error "No encontrado". 

## <a name="enable-flow-log-settings"></a>Habilitación de la configuración del flujo de registros

Antes de habilitar la configuración del flujo de registro, debe completar las tareas siguientes:

Registre el proveedor de Azure Insights, si aún no está registrado para su suscripción:

```azurepowershell-interactive
Register-AzureRmResourceProvider -ProviderNamespace Microsoft.Insights
```

Si aún no dispone de una cuenta de Azure Storage para almacenar los registros de flujos de los grupos de seguridad de red, debe crearla. Puede crear una cuenta de almacenamiento con el comando siguiente. Antes de ejecutar el comando, reemplace `<replace-with-your-unique-storage-account-name>` por un nombre que sea exclusivo en todas las ubicaciones de Azure, de entre 3 y 24 caracteres de longitud y que use solo números y letras en minúsculas. También puede cambiar el nombre del grupo de recursos si es necesario.

```azurepowershell-interactive
New-AzureRmStorageAccount `
  -Location westcentralus `
  -Name <replace-with-your-unique-storage-account-name> `
  -ResourceGroupName myResourceGroup `
  -SkuName Standard_LRS `
  -Kind StorageV2
```

Seleccione las opciones siguientes, tal y como se muestra en la imagen:

1. En *Estado*, seleccione **Activo**.
2. Seleccione una cuenta de almacenamiento existente para almacenar los registros de flujos. Si desea almacenar los datos de forma permanente, establezca el valor en *0*. Puede incurrir en gastos de Azure Storage por la cuenta de almacenamiento.
3. Establezca **Retención** en el número de días que desea almacenar los datos.
4. Seleccione *Activo* en **Estado de Análisis de tráfico**.
5. Seleccione un área de trabajo de Log Analytics (OMS) existente o seleccione **Crear área de trabajo nueva** para crear una. Análisis de tráfico usa un área de trabajo de Log Analytics para almacenar los datos agregados e indexados que se emplean posteriormente para generar los análisis. Si selecciona un área de trabajo existente, esta debe estar en una de las [regiones admitidas](#traffic-analytics-supported-regions) y se debe haber actualizado al nuevo lenguaje de consulta. Si no desea actualizar un área de trabajo existente, o no tiene ninguna en una región admitida, cree una nueva. Para más información sobre los lenguajes de consulta, consulte [Actualización de Azure Log Analytics para la nueva búsqueda de registros](../log-analytics/log-analytics-log-search-upgrade.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json).

    No es necesario que el área de trabajo de Log Analytics (OMS) que hospeda la solución Análisis de tráfico y los grupos de seguridad de red estén en la misma región. Por ejemplo, puede tener Análisis de tráfico en un área de trabajo de la región Europa Occidental y, en cambio, tener los grupos de seguridad de red en las regiones Este de EE. UU. y Oeste de EE. UU. Se pueden configurar varios grupos de seguridad de red en la misma área de trabajo.
6. Seleccione **Guardar**.

    ![Selección de cuenta de almacenamiento, área de trabajo de Log Analytics y habilitación de Análisis de tráfico](media/traffic-analytics/selection-of-storage-account-log-analytics-workspace-and-traffic-analytics-enablement.png)

Repita los pasos anteriores con todos los demás grupos de seguridad de red para los que desee habilitar Análisis de tráfico. Los datos de los registros de flujos se envían al área de trabajo, por tanto, asegúrese de que las leyes y reglamentos locales de su país permiten el almacenamiento de datos en la región donde existe el área de trabajo.

## <a name="view-traffic-analytics"></a>Visualización de Análisis de tráfico

A la izquierda del portal, seleccione **Todos los servicios** y, a continuación, escriba *Monitor* en el cuadro de **filtro**. Seleccione **Monitor** cuando aparezca en los resultados de búsqueda. Para empezar a explorar Análisis de tráfico y sus funcionalidades, seleccione **Network Watcher** y, a continuación, **Análisis de tráfico (versión preliminar)**.

![Acceso al panel de Análisis de tráfico](media/traffic-analytics/accessing-the-traffic-analytics-dashboard.png)

Puede que el panel tarde hasta 30 minutos en aparecer por primera vez, ya que Análisis de tráfico debe primero agregar los suficientes datos para poder proporcionar datos significativos y antes de que pueda generar ningún tipo de informe.

## <a name="usage-scenarios"></a>Escenarios de uso

Algunas de las informaciones detalladas que puede obtener una vez que Análisis de tráfico está totalmente configurada son las siguientes:

### <a name="find-traffic-hotspots"></a>Búsqueda de zonas activas de tráfico

**¿Qué buscar?**

- ¿Qué hosts envían o reciben más tráfico?
    - Conocer los hosts que envían o reciben más tráfico puede ayudarle a identificar cuáles son los que procesan la mayor parte del tráfico.
    - Puede evaluar si el volumen de tráfico es adecuado para un host. ¿Tiene el volumen de tráfico un comportamiento normal o requiere una investigación más exhaustiva?
- ¿Cuánto tráfico entrante y saliente hay?
    -   ¿Se espera que el host reciba más tráfico de entrada que de salida, o viceversa?
- Estadísticas del tráfico permitido o bloqueado.
    - ¿Por qué un host permite o bloquea un volumen significativo de tráfico?

    Seleccione **Más detalles**, en **Hosts with most traffic** (Hosts con más tráfico), tal y como se muestra en la siguiente imagen:

    ![Panel que muestra información sobre los hosts con más tráfico](media/traffic-analytics/dashboard-showcasing-host-with-most-traffic-details.png)

- En la siguiente imagen se muestran las tendencias temporales de los cinco hosts con mayor comunicación y los detalles relacionados con los flujos (flujos de entrada o salida permitidos o denegados) de una máquina virtual:

    ![Tendencias de los cinco hosts con mayor comunicación](media/traffic-analytics/top-five-most-talking-host-trend.png)

**¿Qué buscar?**

- ¿Cuáles son los pares de hosts con más interlocuciones?
    - Comportamiento esperado del tipo comunicación entre front-end y back-end, o comportamiento irregular como tráfico de back-end a Internet.
- Estadísticas del tráfico permitido o bloqueado
    - ¿Por qué un host permite o bloquea un volumen de tráfico significativo?
- El protocolo de aplicación usado con mayor frecuencia entre los pares de hosts con más interlocuciones:
    - ¿Se permiten estas aplicaciones en esta red?
    - ¿Están las aplicaciones configuradas correctamente? ¿Usan el protocolo adecuado para la comunicación? Seleccione **Más detalles** en **Most frequent conversations** (Conversaciones más frecuentes), como se indica en la imagen siguiente:

        ![Panel que muestra la conversación más frecuente](media/traffic-analytics/dashboard-showcasing-most-frequent-conversation.png)

- En la siguiente imagen se muestran las tendencias temporales de las cinco principales conversaciones y los detalles relacionados con los flujos como, por ejemplo, los flujos de entrada o salida rechazados de un par de conversación:

    ![Tendencias y detalles de las cinco conversaciones principales](media/traffic-analytics/top-five-chatty-conversation-details-and-trend.png)

**¿Qué buscar?**

- ¿Qué protocolo de aplicación se usa con más frecuencia en su entorno, y qué pares de hosts interlocutores lo usan más frecuentemente?
    - ¿Se permiten estas aplicaciones en esta red?
    - ¿Están las aplicaciones configuradas correctamente? ¿Usan el protocolo adecuado para la comunicación? Los puertos más habituales en un comportamiento esperado son el 80 y el 443. Para la comunicación estándar, si aparecen puertos no usuales, puede que sea necesario un cambio de configuración. Seleccione **Más detalles** en **Principales protocolos de aplicaciones**, en la siguiente imagen:

        ![Panel que muestra los protocolos de aplicaciones principales](media/traffic-analytics/dashboard-showcasing-top-application-protocols.png)

- Las siguientes imágenes muestran las tendencias temporales de los cinco principales protocolos de nivel 7 y los detalles relacionados con los flujos (por ejemplo, flujos permitidos y rechazados) de un protocolo de nivel 7:

    ![Detalles y tendencias de los cinco principales protocolos de nivel 7](media/traffic-analytics/top five-layer-seven-protocols-details-and-trend.png)

    ![Detalles de los flujos del protocolo de aplicación para la búsqueda de registros](media/traffic-analytics/flow-details-for-application-protocol-in-log-search.png)

**¿Qué buscar?**

- Tendencias de uso de la capacidad de una puerta de enlace VPN en su entorno.
    - Cada SKU de VPN permite una determinada cantidad de ancho de banda. ¿Se usan poco las puertas de enlace de VPN?
    - ¿Están las puertas de enlace alcanzando el límite? ¿Se debe actualizar a la siguiente SKU superior?
- ¿Cuáles son los hosts con más interlocuciones, a través de qué puerta de enlace de VPN y a través de qué puerto?
    - ¿Es normal este patrón? Seleccione **Más detalles**, en **Principales conexiones de VPN activas**, tal y como se muestra en la siguiente imagen:

        ![Panel que muestra las principales conexiones de VPN](media/traffic-analytics/dashboard-showcasing-top-active-vpn-connections.png)

- La siguiente imagen muestra las tendencias temporales del uso de la capacidad para una instancia de Azure VPN Gateway y los detalles relacionados con los flujos (como, por ejemplo, los flujos y puertos permitidos):

    ![Tendencias de uso y detalles de flujos de VPN Gateway](media/traffic-analytics/vpn-gateway-utilization-trend-and-flow-details.png)

### <a name="visualize-traffic-distribution-by-geography"></a>Visualización de la distribución del tráfico por geografía

**¿Qué buscar?**

- La distribución del tráfico por centro de datos como, por ejemplo, los principales orígenes del tráfico a un centro de datos, las principales redes no autorizadas que conversan con el centro de datos y los principales protocolos de aplicaciones de conversación.
    - Si observa más carga en un centro de datos, puede planear una distribución del tráfico más eficaz.
    - Si hay redes no autorizadas conversando en el centro de datos, corrija las reglas del grupo de seguridad de red para bloquearlas.

    Seleccione **Haga clic aquí para ver el mapa geográfico en directo** en **Traffic distribution across Azure data centers** (Distribución del tráfico entre los centros de datos de Azure), tal y como se muestra en la siguiente imagen:

  ![Panel que muestra la distribución de tráfico](media/traffic-analytics/dashboard-showcasing-traffic-distribution.png)

- El mapa geográfico muestra la cinta de opciones superior para la selección de parámetros como los centros de datos (Implementados, Sin implementación, Activos, Inactivos, Análisis de tráfico habilitado, Análisis de tráfico no habilitado) y los países que contribuyen con tráfico inofensivo o malintencionado a la implementación activa:

    ![Vista de un mapa geográfico que muestra la implementación activa](media/traffic-analytics/geo-map-view-showcasing-active-deployment.png)

- El mapa geográfico muestra la distribución del tráfico con dirección a un centro de datos desde países y continentes que se comunican con él mediante líneas de color azul (tráfico inofensivo) y de color rojo (tráfico malintencionado):

    ![Vista de mapa geográfico que muestra una distribución del tráfico hacia países y continentes](media/traffic-analytics/geo-map-view-showcasing-traffic-distribution-to-countries-and-continents.png)

    ![Detalles de flujos para la distribución de tráfico en búsqueda de registros](media/traffic-analytics/flow-details-for-traffic-distribution-in-log-search.png)

### <a name="visualize-traffic-distribution-by-virtual-networks"></a>Visualización de la distribución del tráfico por redes virtuales

**¿Qué buscar?**

- La distribución del tráfico por red virtual, la topología, los principales orígenes de tráfico a la red virtual, las principales redes no autorizadas que conversan con la red virtual y los principales protocolos de aplicaciones de conversación.
    - Saber qué red virtual está conversando con qué red virtual. Si no es una conversación esperada, esto se puede corregir.
    - Si algunas redes no autorizadas conversan con una red virtual, puede corregir las reglas del grupo de seguridad de red para bloquear esas redes.
 
    Seleccione **Haga clic aquí para ver la topología de tráfico de la red virtual** en **Distribución de redes virtuales** como se muestra en la siguiente imagen: 

    ![Panel que muestra la distribución de redes virtuales](media/traffic-analytics/dashboard-showcasing-virtual-network-distribution.png)

- La topología de red virtual muestra la cinta de opciones superior para la selección de parámetros como las conexiones entre redes virtuales activas o inactivas, o las conexiones externas, los flujos activos y los flujos malintencionados de la red virtual.
- La topología de red virtual muestra la distribución del tráfico hacia una red virtual en relación con los flujos (Permitidos, bloqueados, entrantes, salientes, inofensivos o malintencionados), los protocolos de aplicación y los grupos de seguridad de red, por ejemplo:

    ![Topología de red virtual que muestra la distribución del tráfico y los detalles del flujo](media/traffic-analytics/virtual-network-topology-showcasing-traffic-distribution-and-flow-details.png)

    ![Detalles de flujos para la distribución del tráfico de red virtual en búsqueda de registros](media/traffic-analytics/flow-details-for-virtual-network-traffic-distribution-in-log-search.png)

**¿Qué buscar?**

- La distribución del tráfico por subred, la topología, los principales orígenes de tráfico a la subred, las principales redes no autorizadas que conversan con la subred y los principales protocolos de aplicaciones de conversación.
    - Sepa qué subred está conversando con qué subred. Si ve conversaciones inesperadas, puede corregir la configuración.
    - Por ejemplo, si hay redes no autorizadas conversando con una subred, podrá solucionarlo corrigiendo la configuración de las reglas del grupo de seguridad de red para bloquear las redes no autorizadas.
- La topología de subredes muestra la cinta de opciones superior para la selección de parámetros como los de subred activa o inactiva, conexiones externas, flujos activos y flujos malintencionados de la subred.
- La topología de subred muestra la distribución del tráfico hacia una red virtual en relación con los flujos (Permitidos, bloqueados, entrantes, salientes, inofensivos o malintencionados), los protocolos de aplicación y los grupos de seguridad de red, por ejemplo:

    ![Topología de subred que muestra la distribución de tráfico hacia una subred de red virtual en relación con los flujos](media/traffic-analytics/subnet-topology-showcasing-traffic-distribution-to-a-virtual-subnet-with-regards-to-flows.png)

### <a name="view-ports-and-virtual-machines-receiving-traffic-from-the-internet"></a>Visualización de los puertos y máquinas virtuales que reciben tráfico desde Internet

**¿Qué buscar?**

- ¿Qué puertos abiertos mantienen conversaciones a través de Internet?
    - Si se encuentran abiertos puertos inesperados, puede corregir la configuración:

        ![Panel que muestra puertos recibiendo y enviando tráfico a Internet](media/traffic-analytics/dashboard-showcasing-ports-receiving-and-sending-traffic-to-the-internet.png)

        ![Detalles de puertos y hosts de destino de Azure](media/traffic-analytics/details-of-azure-destination-ports-and-hosts.png)

**¿Qué buscar?**

¿Tiene tráfico malintencionado en su entorno? ¿Desde dónde se origina? ¿Cuál es su destino?

![Detalles de flujos de tráfico malintencionado en búsqueda de registros](media/traffic-analytics/malicious-traffic-flows-detail-in-log-search.png)


### <a name="visualize-the-trends-in-nsg-rule-hits"></a>Visualización de tendencias en las coincidencias de regla del grupo de seguridad de red

**¿Qué buscar?**

- ¿Qué grupo de seguridad de red o regla tiene el mayor número de visitas?
- ¿Cuáles son los pares de conversación de origen y destino principales por el grupo de seguridad de red?

    ![Panel que muestra las estadísticas de coincidencias del grupo de seguridad de red](media/traffic-analytics/dashboard-showcasing-nsg-hits-statistics.png)

- Las siguientes imágenes muestran las tendencias temporales de las coincidencias de regla del grupo de seguridad de red y los detalles de flujo de origen y destino para un grupo de seguridad de red:

    ![Panel que muestra las tendencias temporales de coincidencias de reglas del grupo de seguridad de red y las principales reglas](media/traffic-analytics/showcasing-time-trending-for-nsg-rule-hits-and-top-nsg-rules.png)

    ![Detalles sobre estadísticas de las principales reglas del grupo de seguridad de red](media/traffic-analytics/top-nsg-rules-statistics-details-in-log-search.png)

## <a name="frequently-asked-questions"></a>Preguntas más frecuentes

Para obtener respuestas a las preguntas más frecuentes, consulte [Preguntas más frecuentes de Análisis de tráfico](traffic-analytics-faq.md).