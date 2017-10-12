---
title: "Visualización de los patrones de tráfico de red con Azure Network Watcher y herramientas de código abierto | Microsoft Docs"
description: "Esta página describe cómo utilizar la captura de paquetes de Network Watcher con Capanalysis para visualizar los patrones de tráfico de entrada y salida de las máquinas virtuales."
services: network-watcher
documentationcenter: na
author: jimdial
manager: timlt
editor: 
ms.assetid: 936d881b-49f9-4798-8e45-d7185ec9fe89
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: jdial
ms.openlocfilehash: 61abda6053fe743e294f309df3a6e1041052ec6e
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="visualize-network-traffic-patterns-to-and-from-your-vms-using-open-source-tools"></a>Visualización de los patrones de tráfico de red de entrada y salida de las máquinas virtuales utilizando herramientas de código abierto

Las capturas de paquetes contienen datos de red que permiten realizar análisis forense de la red e inspección profunda de paquetes. Hay muchas herramientas de código abierto que puede usar para analizar las capturas de paquetes y obtener información sobre la red. Una de estas herramientas es CapAnalysis, una herramienta de código abierto para la visualización de captura de paquetes. La visualización de los datos de captura de paquetes es una forma muy eficaz de derivar rápidamente información sobre patrones y anomalías dentro de la red. Las visualizaciones también proporcionan un medio para compartir dicha información de una manera fácil de consumir.

Azure Network Watcher proporciona la capacidad para capturar estos valiosos datos permitiéndole realizar capturas de paquetes en su red. En este artículo, proporcionamos un tutorial en el que se explica cómo visualizar y obtener información de las capturas de paquetes usando CapAnalysis con Network Watcher.

## <a name="scenario"></a>Escenario

Tiene una aplicación web simple implementada en una máquina virtual en Azure y desea usar herramientas de código abierto para visualizar su tráfico de red para identificar rápidamente los patrones de flujo y las posibles anomalías. Con Network Watcher, puede obtener una captura de paquetes de su entorno de red y almacenarla directamente en su cuenta de almacenamiento. CapAnalysis puede a continuación ingerir la captura de paquetes directamente desde el blob de almacenamiento y visualizar su contenido.

![escenario][1]

## <a name="steps"></a>Pasos

### <a name="install-capanalysis"></a>Instalar CapAnalysis

Para instalar CapAnalysis en una máquina virtual, puede acudir a las instrucciones oficiales en esta página https://www.capanalysis.net/ca/how-to-install-capanalysis.
Para poder acceder de forma remota a CapAnalysis, es necesario abrir el puerto 9877 en la máquina virtual mediante la adición de una nueva regla de seguridad de entrada. Para más información sobre cómo crear reglas en los grupos de seguridad de red, consulte [Creación de reglas en un grupo de seguridad de red existente](../virtual-network/virtual-networks-create-nsg-arm-pportal.md#create-rules-in-an-existing-nsg). Una vez que la regla se ha agregado correctamente, debe poder tener acceso a CapAnalysis desde `http://<PublicIP>:9877`

### <a name="use-azure-network-watcher-to-start-a-packet-capture-session"></a>Utilice Azure Network Watcher para iniciar una sesión de captura de paquetes

Network Watcher le permite capturar paquetes para realizar el seguimiento del tráfico dentro y fuera de una máquina virtual. Puede acudir a las instrucciones en [Administración de capturas de paquetes con Network Watcher](network-watcher-packet-capture-manage-portal.md) para iniciar una sesión de captura de paquetes. Esta captura de paquetes se puede almacenar en un blob de almacenamiento para que CapAnalysis pueda acceder a ella.

### <a name="upload-a-packet-capture-to-capanalysis"></a>Carga de una captura de paquetes en CapAnalysis
Puede cargar directamente una captura de paquetes realizada por Network Watcher usando la pestaña "Importar desde dirección URL" y proporcionando un vínculo al blob de almacenamiento donde está almacenada la captura de paquete.

Cuando proporciona un vínculo a CapAnalysis, asegúrese de anexar un token de SAS a la dirección URL del blob de almacenamiento.  Para ello, vaya a la firma de acceso compartido de la cuenta de almacenamiento, designe los permisos concedidos y presione el botón Generar SAS para crear un token. A continuación, puede anexar este token de SAS a la URL del blob de almacenamiento de captura de paquetes.

La dirección URL resultante tendrá un aspecto similar al siguiente: http://storageaccount.blob.core.windows.net/container/location?addSASkeyhere


### <a name="analyzing-packet-captures"></a>Análisis de captura de paquetes

CapAnalysis ofrece varias opciones para visualizar la captura de paquetes, cada una proporciona un análisis desde una perspectiva distinta. Con estos resúmenes visuales, podrá entender las tendencias del tráfico de la red y detectar rápidamente cualquier actividad inusual. Algunas de estas características se muestran en la lista siguiente:

1. Tablas de flujo

    Esta tabla proporciona la lista de flujos en los datos del paquete, la marca de tiempo asociada a los flujos y los diversos protocolos asociados con el flujo, así como la IP de origen y de destino.

    ![Página de flujo de Capanalysis][5]

1. Información general sobre protocolos

    Este panel le permite ver rápidamente la distribución del tráfico de red a través de los distintos protocolos y regiones geográficas.

    ![Información general sobre protocolos de Capanalysis][6]

1. Estadísticas

    Este panel permite ver las estadísticas de tráfico de red: bytes enviados y recibidos desde las IP de origen y destino, flujos de cada una de las IP de origen y destino, protocolo usado para distintos flujos y duración de los flujos.

    ![Estadísticas de Capanalysis][7]

1. Geomap

    Este panel proporciona una vista del mapa del tráfico de red, con colores de ajuste de escala para el volumen de tráfico de cada país. Puede seleccionar países resaltados para ver las estadísticas de flujo adicionales, como la proporción de datos enviados y recibidos desde direcciones IP en ese país.

    ![Geomap][8]

1. Filtros

    CapAnalysis proporciona un conjunto de filtros para el análisis rápido de paquetes específicos. Por ejemplo, puede filtrar los datos por protocolo para obtener información específica en ese subconjunto de tráfico.

    ![filters][11]

    Visite [https://www.capanalysis.net/ca/#about](https://www.capanalysis.net/ca/#about) para más información sobre todas las funcionalidades de CapAnalysis.

## <a name="conclusion"></a>Conclusión

La característica de captura de paquetes de Network Watcher permite capturar los datos necesarios para realizar un análisis forense de red y comprender mejor el tráfico de red. En este escenario, hemos mostrado cómo las capturas de paquetes de Network Watcher se pueden integrar fácilmente con herramientas de visualización de código abierto. Mediante el uso de herramientas de código abierto como CapAnalysis para visualizar las capturas de paquetes, puede realizar una inspección profunda de los paquetes e identificar rápidamente las tendencias en el tráfico de red.

## <a name="next-steps"></a>Pasos siguientes

Para más información acerca de los registros de flujo de NSG, visite el artículo sobre [Registros de flujo de NSG](network-watcher-nsg-flow-logging-overview.md)

Aprenda a visualizar los registros de flujo de grupos de seguridad de red con Power BI en el artículo [Visualización de registros de flujo del grupo de seguridad de red de Azure con Power BI](network-watcher-visualize-nsg-flow-logs-power-bi.md)
<!--Image references-->

[1]: ./media/network-watcher-using-open-source-tools/figure1.png
[2]: ./media/network-watcher-using-open-source-tools/figure2.png
[3]: ./media/network-watcher-using-open-source-tools/figure3.png
[4]: ./media/network-watcher-using-open-source-tools/figure4.png
[5]: ./media/network-watcher-using-open-source-tools/figure5.png
[6]: ./media/network-watcher-using-open-source-tools/figure6.png
[7]: ./media/network-watcher-using-open-source-tools/figure7.png
[8]: ./media/network-watcher-using-open-source-tools/figure8.png
[9]: ./media/network-watcher-using-open-source-tools/figure9.png
[10]: ./media/network-watcher-using-open-source-tools/figure10.png
[11]: ./media/network-watcher-using-open-source-tools/figure11.png
