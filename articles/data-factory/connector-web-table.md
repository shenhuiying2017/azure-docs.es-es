---
title: Copia de datos de una tabla web mediante Azure Data Factory | Microsoft Docs
description: "Obtenga información sobre el conector de tabla web de Azure Data Factory que permite copiar datos desde una tabla web a almacenes de datos compatibles con Data Factory como receptores."
services: data-factory
documentationcenter: 
author: linda33wj
manager: jhubbard
editor: spelluru
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/05/2018
ms.author: jingwang
ms.openlocfilehash: c5d2fdb3ed3c00114437b0be9759bf8bea2521b7
ms.sourcegitcommit: be9a42d7b321304d9a33786ed8e2b9b972a5977e
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/19/2018
---
# <a name="copy-data-from-web-table-by-using-azure-data-factory"></a>Copia de datos de una tabla web mediante Azure Data Factory
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Versión 1: Disponibilidad general](v1/data-factory-web-table-connector.md)
> * [Versión 2: versión preliminar](connector-web-table.md)

En este artículo se explica el uso de la actividad de copia de Azure Data Factory para copiar datos desde una base de datos de tabla web. El documento se basa en el artículo de [introducción a la actividad de copia](copy-activity-overview.md) que describe información general de la actividad de copia.

> [!NOTE]
> Este artículo se aplica a la versión 2 de Data Factory, que actualmente se encuentra en versión preliminar. Si usa la versión 1 del servicio Data Factory, que está disponible con carácter general, vea el artículo sobre [la versión 1 del conector de tabla web](v1/data-factory-web-table-connector.md).

## <a name="supported-capabilities"></a>Funcionalidades admitidas

Puede copiar datos desde la base de datos de la tabla web en cualquier almacén de datos receptor compatible. Consulte la tabla de [almacenes de datos compatibles](copy-activity-overview.md#supported-data-stores-and-formats) para ver una lista de almacenes de datos que la actividad de copia admite como orígenes o receptores.

En concreto, este conector de tabla web permite **extraer contenido de tablas de una página HTML**. Para recuperar datos de un punto de conexión HTTP/s, utilice el [conector HTTP](connector-http.md) en su lugar.

## <a name="prerequisites"></a>requisitos previos

Para usar este conector de tabla web, tiene que configurar una instancia de Integration Runtime autohospedada. Consulte el artículo sobre [Integration Runtime autohospedado](create-self-hosted-integration-runtime.md) para más información.

## <a name="getting-started"></a>Introducción

[!INCLUDE [data-factory-v2-connector-get-started-2](../../includes/data-factory-v2-connector-get-started-2.md)]

Las secciones siguientes proporcionan detalles sobre las propiedades que se usan para definir entidades de Data Factory específicas del conector de tabla web.

## <a name="linked-service-properties"></a>Propiedades del servicio vinculado

Las siguientes propiedades son compatibles con el servicio vinculado de tabla web:

| Propiedad | DESCRIPCIÓN | Obligatorio |
|:--- |:--- |:--- |
| Tipo | La propiedad type debe establecerse en: **Web** |Sí |
| URL | Dirección URL para el origen de Web |Sí |
| authenticationType | El valor permitido es: **Anónima**. |Sí |
| connectVia | El entorno [Integration Runtime](concepts-integration-runtime.md) que se usará para conectarse al almacén de datos. Tal y como se mencionó en los [requisitos previos](#prerequisites), se requiere un entorno Integration Runtime autohospedado. |Sí |

**Ejemplo:**

```json
{
    "name": "WebLinkedService",
    "properties": {
        "type": "Web",
        "typeProperties": {
            "url" : "https://en.wikipedia.org/wiki/",
            "authenticationType": "Anonymous"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

## <a name="dataset-properties"></a>Propiedades del conjunto de datos

Si desea ver una lista completa de las secciones y propiedades disponibles para definir conjuntos de datos, consulte el artículo sobre conjuntos de datos. En esta sección se proporciona una lista de las propiedades que admite el conjunto de datos de tabla web.

Para copiar datos desde una tabla web, establezca la propiedad type del conjunto de datos en **RelationalTable**. Se admiten las siguientes propiedades:

| Propiedad | DESCRIPCIÓN | Obligatorio |
|:--- |:--- |:--- |
| Tipo | La propiedad type del conjunto de datos debe establecerse en: **WebTable** | Sí |
| path |Dirección URL relativa al recurso que contiene la tabla. |Nº Cuando no se especifica la ruta de acceso, se solo se usa la dirección URL especificada en la definición de servicio vinculado. |
| index |Índice de la tabla en el recurso. Consulte la sección [Obtención de índice de una tabla en una página HTML](#get-index-of-a-table-in-an-html-page) para saber los pasos necesarios para obtener el índice de una tabla en una página HTML. |Sí |

**Ejemplo:**

```json
{
    "name": "WebTableInput",
    "properties": {
        "type": "WebTable",
        "linkedServiceName": "WebLinkedService",
        "typeProperties": {
            "index": 1,
            "path": "AFI's_100_Years...100_Movies"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Propiedades de la actividad de copia

Si desea ver una lista completa de las secciones y propiedades disponibles para definir actividades, consulte el artículo sobre [canalizaciones](concepts-pipelines-activities.md). En esta sección se proporciona una lista de las propiedades que admite el origen de datos de tabla web.

### <a name="web-table-as-source"></a>Tabla web como origen

Para copiar datos desde una tabla web, establezca el tipo de origen de la actividad de copia en **WebSource**. No se admite ninguna otra propiedad.

**Ejemplo:**

```json
"activities":[
    {
        "name": "CopyFromWebTable",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Web table input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "WebSource"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

## <a name="get-index-of-a-table-in-an-html-page"></a>Obtención de índice de una tabla en una página HTML

1. Inicie **Excel 2016** y cambie a la pestaña **Datos**.
2. Haga clic en **Nueva consulta** en la barra de herramientas, elija **De otros orígenes** y haga clic en **Desde Web**.

    ![Menú de Power Query](./media/copy-data-from-web-table/PowerQuery-Menu.png)
3. En el cuadro de diálogo **Desde Web**, escriba la **dirección URL** que se use en el JSON del servicio vinculado (por ejemplo: https://en.wikipedia.org/wiki/) junto con la ruta de acceso que se especifique para el conjunto de datos (por ejemplo: AFI%27s_100_Years...100_Movies) y haga clic en **Aceptar**.

    ![Cuadro de diálogo Desde Web](./media/copy-data-from-web-table/FromWeb-DialogBox.png)

    Dirección URL usada en este ejemplo: https://en.wikipedia.org/wiki/AFI%27s_100_Years...100_Movies
4. Si ve el cuadro de diálogo **Acceso a contenido web**, seleccione la **dirección URL** correcta, la **autenticación** y haga clic en **Conectar**.

   ![Cuadro de diálogo Acceso a contenido web](./media/copy-data-from-web-table/AccessWebContentDialog.png)
5. Haga clic en un elemento de **tabla** en la vista de árbol para ver el contenido de la tabla y después en el botón **Editar** ubicado en la parte inferior.  

   ![Cuadro de diálogo Navegador](./media/copy-data-from-web-table/Navigator-DialogBox.png)
6. En la ventana **Editor de consultas**, haga clic en el botón **Editor avanzado** de la barra de herramientas.

    ![Botón Editor avanzado](./media/copy-data-from-web-table/QueryEditor-AdvancedEditorButton.png)
7. En el cuadro de diálogo Editor avanzado, el número que aparece junto a "Origen" es el índice.

    ![Editor avanzado - Índice](./media/copy-data-from-web-table/AdvancedEditor-Index.png)

Si usa Excel 2013, use [Microsoft Power Query para Excel](https://www.microsoft.com/download/details.aspx?id=39379) para obtener el índice. Consulte el artículo [Conectarse a una página web](https://support.office.com/article/Connect-to-a-web-page-Power-Query-b2725d67-c9e8-43e6-a590-c0a175bd64d8) para más información. Los pasos son similares si usa [Microsoft Power BI Desktop](https://powerbi.microsoft.com/desktop/).


## <a name="next-steps"></a>pasos siguientes
Consulte los [almacenes de datos compatibles](copy-activity-overview.md#supported-data-stores-and-formats) para ver la lista de almacenes de datos que la actividad de copia de Azure Data Factory admite como orígenes y receptores.