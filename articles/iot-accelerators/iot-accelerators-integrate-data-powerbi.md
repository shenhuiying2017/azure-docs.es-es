---
title: Visualización de datos de supervisión remota con Power BI (Azure) | Microsoft Docs
description: En este tutorial se usa Power BI Desktop y Cosmos DB para integrar datos de una solución de supervisión remota en una visualización personalizada. De esta forma, los usuarios pueden compilar sus propios paneles personalizados y compartirlos con los usuarios, pero no en la solución.
services: iot-suite
suite: iot-suite
author: asdonald
manager: hegate
ms.author: asdonald
ms.service: iot-suite
ms.date: 05/01/2018
ms.topic: article
ms.devlang: NA
ms.tgt_pltfrm: NA
ms.workload: NA
ms.openlocfilehash: 55feb56008a54676bd0af332e251da94a9653aaf
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/20/2018
---
# <a name="visualize-remote-monitoring-data-using-power-bi"></a>Visualización de datos de supervisión remota con Power BI

En este tutorial se ofrece orientación sobre cómo conectar los datos de la solución de supervisión remota de Cosmos DB a Power BI. Con esta conexión establecida, puede crear sus propios paneles personalizados y volver a agregarlos al panel de la solución de supervisión remota. Esta serie de tareas permite crear gráficos más especializados, además de otros listos para usar. Por tanto, puede usar este tutorial para integrarlo con otros flujos de datos o crear paneles personalizados para que se consuman fuera de la solución de supervisión remota. La creación de paneles en Power BI supone que también puede hacer que los paneles interactúen entre sí al seleccionar elementos específicos. Por ejemplo, podría disponer de un filtro para mostrar solo información sobre los camiones simulados y cada elemento del panel interactuaría para mostrar solo información simulada sobre el camión. Si desea usar una herramienta distinta a Power BI, también puede ampliar estos pasos para usar la herramienta de visualización de su elección y conectarla a la base de datos de Cosmos DB o a una base de datos personalizada, en caso de que haya configurado alguna. 

## <a name="prerequisites"></a>requisitos previos

- Debe disponer de una solución de supervisión remota que esté en ejecución actualmente.
- Debe acceder a [Azure Portal](https://portal.azure.com) y a la suscripción en que se ejecutan IoT Hub y la solución.
- Debe tener [Power BI Desktop](https://powerbi.microsoft.com) instalado, y sirve cualquier versión.


## <a name="information-needed-from-azure-portal"></a>Información necesaria de Azure Portal

1. Vaya a [Azure Portal](https://portal.azure.com) e inicie sesión si es necesario.

2. En el panel izquierdo, haga clic en Grupos de recursos.

    ![Panel de navegación lateral](./media/iot-accelerators-integrate-data-powerbi/side_panel.png)

3. Desplácese hasta el grupo de recursos en que se ejecuta la solución de IoT y haga clic en él para ir a la página de información general del grupo de recursos. 

4. En la página de información general, haga clic en el elemento, que es del tipo "Cuenta de Azure Cosmos DB", y se le remitirá a la página de información general del flujo de Cosmos DB de dicha solución de IoT.

    ![Grupo de recursos](./media/iot-accelerators-integrate-data-powerbi/resource_groups.png)

5. En el panel de la izquierda, haga clic en la sección "Claves" y tome nota de los siguientes valores para su uso en Power BI:

    - URI
    - Clave principal

    ![claves](./media/iot-accelerators-integrate-data-powerbi/keys.png)

## <a name="setting-up-the-stream-in-power-bi"></a>Configuración del flujo en Power BI
  
1. Abra la aplicación de Power BI Desktop y haga clic en "Obtener datos" en la esquina superior izquierda. 

    ![Obtención de datos](./media/iot-accelerators-integrate-data-powerbi/get_data.png)

2. Cuando se le pida que especifique los datos, elija buscar "Azure Cosmos DB" y seleccione este conector. Este conector básicamente extrae los datos directamente de la base de datos de Cosmos DB de la solución de Azure IoT.
  
    ![Cosmos DB](./media/iot-accelerators-integrate-data-powerbi/cosmos_db.png)
  
3. Escriba la información que haya registrado anteriormente:

    * URI
    * Clave principal

4. Seleccione todas las tablas que se importarán en Power BI. Esta acción iniciará la carga de los datos. Cuanto más tiempo se ejecute la solución, más tardarán los datos en cargarse (hasta unas horas). 

    ![Importación de tablas](./media/iot-accelerators-integrate-data-powerbi/import_tables.png)

5. Una vez finalizada la carga de los datos, haga clic en "Editar consultas" en la fila superior de Power BI y expanda todas las tablas haciendo clic en las flechas de la barra amarilla de cada tabla. Se expandirá básicamente para mostrar todas las columnas. Observará que los datos para aspectos como la humedad, el tiempo de aceleración, etc., no son del tipo correcto.

    ![Nueva columna](./media/iot-accelerators-integrate-data-powerbi/new_column.png)
  
    Por ejemplo, los datos que entran en Power BI se cambiaron por la hora UNIX cuando llegan a través del conector. Para adaptarse a esta conversión, si avanza, puede crear una columna y usar esta ecuación para adoptar el formato de fecha y hora: 

    ```text
    #datetime(1970, 1, 1, 0, 0, 0) + #duration(0, 0, 0, [Document.device.msg.received]/1000)
    ```

    ![Tabla actualizada](./media/iot-accelerators-integrate-data-powerbi/updated_table.png)
  
    Document.device.msg.received es solo una de las columnas con formato UNIX y se puede sustituir por otras que necesitan conversión. 
  
    Otros puntos de datos convertidos al tipo de cadena pueden cambiarse por dobles o enteros cuando sea necesario con el uso de los mismos pasos descritos anteriormente.

## <a name="creating-a-dashboard"></a>Creación de un panel

Una vez conectado el flujo, ya puede crear paneles personalizados. El panel siguiente es un ejemplo de la imitación de la telemetría por parte de nuestros dispositivos simulados con la visualización de distintos elementos dinámicos alrededor, como: 

* Ubicación del dispositivo en un mapa (derecha)
* Dispositivos con su estado y gravedad. (parte superior izquierda)
* Dispositivos con reglas en vigor y si existen diferentes alarmas sonando (parte inferior izquierda)

![Visualización de Power BI](./media/iot-accelerators-integrate-data-powerbi/visual_data.png)

## <a name="publishing-the-dashboard-and-refreshing-the-data"></a>Publicación en el panel y actualización de los datos

Una vez creados correctamente los paneles, se recomienda que [publique los paneles de Power BI](https://docs.microsoft.com/en-us/power-bi/desktop-upload-desktop-files) para compartirlos con otros.

También conviene [actualizar los datos](https://docs.microsoft.com/en-us/power-bi/refresh-data) en el panel publicado para asegurarse de que dispone del último conjunto de datos.

## <a name="next-steps"></a>Pasos siguientes

En este artículo, ha aprendido cómo visualizar datos de supervisión remota con Power BI.

Para más información sobre cómo personalizar la solución de supervisión remota, consulte:

* [Personalización de la interfaz de usuario de la solución de supervisión remota](iot-accelerators-remote-monitoring-customize.md)
* [Guía de referencia para desarrolladores](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/wiki/Developer-Reference-Guide)
* [Guía de solución de problemas para desarrolladores](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/wiki/Developer-Troubleshooting-Guide)

