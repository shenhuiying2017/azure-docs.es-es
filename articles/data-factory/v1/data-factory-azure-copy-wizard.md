---
title: Asistente para copia de Azure Data Factory | Microsoft Docs
description: "Obtenga información sobre cómo utilizar el Asistente para copia de Azure Data Factory para copiar datos de orígenes de datos admitidos en receptores."
services: data-factory
documentationcenter: 
author: spelluru
manager: jhubbard
editor: monicar
ms.assetid: 0974eb40-db98-4149-a50d-48db46817076
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/22/2018
ms.author: spelluru
robots: noindex
ms.openlocfilehash: d9f3fea0db5a08fc91d9e4dc525b48575c512634
ms.sourcegitcommit: 9cc3d9b9c36e4c973dd9c9028361af1ec5d29910
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/23/2018
---
# <a name="azure-data-factory-copy-wizard"></a>Asistente para copia de Azure Data Factory
> [!NOTE]
> Este artículo se aplica a la versión 1 de Data Factory, que está disponible con carácter general. 

El Asistente para copia de Azure Data Factory simplifica el proceso de ingesta de datos, que normalmente es el primer paso en un escenario de integración de datos de un extremo a otro. Para aprender el uso del Asistente para copia de Azure Data Factory no es preciso conocer las definiciones de JSON de servicios vinculados, conjuntos de datos y canalizaciones. El asistente crea automáticamente una canalización para copiar datos del origen de datos seleccionado al destino seleccionado. Además, el Asistente para copia le ayuda a validar los datos que se ingieren en el momento de la creación. Esto le permite ahorrar tiempo, especialmente cuando ingiere datos por primera vez desde el origen de datos. Si quiere iniciar el Asistente para copia, haga clic en el icono **Copiar datos** de la página principal de Data Factory.

![Asistente para copia](./media/data-factory-copy-wizard/copy-data-wizard.png)

## <a name="designed-for-big-data"></a>Diseñado para macrodatos
Este asistente permite mover fácilmente los datos de una gran variedad de orígenes a distintos destinos en cuestión de minutos. Después de pasar por el asistente, se crea automáticamente una canalización con una actividad de copia, junto con las entidades de Data Factory dependientes (servicios vinculados y conjuntos de datos). No se requieren más pasos para crear la canalización.   

![Selección de origen de datos](./media/data-factory-copy-wizard/select-data-source-page.png)

> [!NOTE]
> Para ver instrucciones detalladas para crear una canalización de ejemplo para copiar datos desde un blob de Azure a una tabla de Azure SQL Database, consulte el [tutorial del Asistente para copia](data-factory-copy-data-wizard-tutorial.md).
>
>

El asistente está diseñado pensando en los macrodatos desde el principio, con compatibilidad para diversos datos y tipos de objeto. Puede crear canalizaciones de Data Factory que trasladen cientos de carpetas, archivos o tablas. El asistente admite vista previa de datos automática, captura y asignación de esquema, y filtrado de datos.

## <a name="automatic-data-preview"></a>Vista previa de datos automática
Puede obtener una vista previa de la parte de los datos del origen de datos seleccionado para validar si los datos son los que desea copiar. Además, si los datos de origen están en un archivo de texto, el Asistente para copia analiza dicho archivo para obtener más información sobre el esquema y los delimitadores de columna y fila automáticamente.

![Configuración del formato de archivo](./media/data-factory-copy-wizard/file-format-settings.png)

## <a name="schema-capture-and-mapping"></a>Captura y asignación de esquema
En algunos casos, es posible que el esquema de datos de entrada no coincida con el esquema de datos de salida. En este escenario, es preciso asignar columnas del esquema de origen a columnas del esquema de destino.

> [!TIP]
> Al copiar datos desde SQL Server o Azure SQL Database en SQL Data Warehouse, si la tabla no se encuentra en el almacén de destino, Data Factory podrá crear la tabla automáticamente mediante el esquema del origen. Obtenga más información en [Movimiento de datos hacia y desde SQL Data Warehouse mediante Azure Data Factory](./data-factory-azure-sql-data-warehouse-connector.md).
>

Use una lista desplegable para seleccionar una columna del esquema de origen para asignar a una columna en el esquema de destino. El Asistente para copia intenta entender el patrón para la asignación de columnas. Se aplica el mismo patrón para el resto de las columnas, por lo que no debe seleccionar cada una de las columnas individualmente para completar la asignación de esquema. Si lo prefiere, puede invalidar estas asignaciones mediante el uso de las listas desplegables para asignar las columnas una por una. El patrón se vuelve más preciso a medida que asigna más columnas. El Asistente para copia actualiza el patrón constantemente y, en última instancia, alcanza el patrón correcto para la asignación de columnas que desea conseguir.     

![Asignación de esquemas](./media/data-factory-copy-wizard/schema-mapping.png)

## <a name="filtering-data"></a>Filtrado de datos
Puede filtrar los datos de origen para seleccionar solo aquellos que deben copiarse en el almacén de datos receptor. El filtrado reduce el volumen de los datos se copian en el almacén de datos del receptor, por lo que mejora el rendimiento de la operación de copia. Proporciona una manera flexible de filtrar los datos de una base de datos relacional mediante el lenguaje de consulta SQL, o archivos en una carpeta de blobs de Azure mediante [Funciones y variables de Data Factory](data-factory-functions-variables.md).   

### <a name="filtering-of-data-in-a-database"></a>Filtrado de datos de una base de datos
La siguiente captura de pantalla muestra una consulta SQL con la función `Text.Format` y la variable `WindowStart`.

![Validación de expresiones](./media/data-factory-copy-wizard/validate-expressions.png)

### <a name="filtering-of-data-in-an-azure-blob-folder"></a>Filtrar datos en una carpeta de blobs de Azure
Puede usar variables en la ruta de acceso de la carpeta para copiar datos desde una carpeta que se determina en el runtime según las [variables del sistema](data-factory-functions-variables.md#data-factory-system-variables). Estas son las variables que se admiten: **{year}**, **{month}**, **{day}**, **{hour}**, **{minute}** y **{custom}**. Por ejemplo: carpetaDeEntrada/{year}/{month}/{day}.

Supongamos que tiene carpetas de entrada con el siguiente formato:

    2016/03/01/01
    2016/03/01/02
    2016/03/01/03
    ...

Haga clic en el botón **Examinar** de **Archivo o carpeta**, vaya a una de estas carpetas (por ejemplo, 2016->03->01->02) y haga clic en **Elegir**. En el cuadro de texto, debería aparecer `2016/03/01/02`. Sustituya **2016** por **{year}**, **03** por **{month}**, **01** por **{day}** y **02** por **{hour}** y presione la tecla **Tabulación**. Aparecerán listas desplegables en las que podrá seleccionar el formato de estas cuatro variables:

![Uso de variables del sistema](./media/data-factory-copy-wizard/blob-standard-variables-in-folder-path.png)   

Como se muestra en la siguiente captura de pantalla, también puede usar una variable **personalizada** y cualquier [cadena de formato admitida](https://msdn.microsoft.com/library/8kb3ddd4.aspx). Para seleccionar una carpeta con esa estructura, utilice primero el botón **Examinar** . A continuación, reemplace un valor por **{custom}** y presione la tecla **Tabulación** para ver el cuadro de texto donde puede escribir la cadena de formato.     

![Uso de la variable personalizada](./media/data-factory-copy-wizard/blob-custom-variables-in-folder-path.png)

## <a name="scheduling-options"></a>Opciones de programación
Puede ejecutar la operación de copia una vez o de forma periódica (cada hora, día, etc.). Estas opciones se pueden utilizar para los distintos conectores en los diferentes entornos, ya sean locales y en la nube, así como para la copia del escritorio local.

Una operación de copia única permite el movimiento de datos desde un origen a un destino solamente una vez. Se aplica a los datos de cualquier tamaño y en cualquier formato admitido. La copia programada permite copiar datos con una frecuencia prescrita. Puede utilizar las opciones avanzadas (como el reintento, el tiempo de espera y las alertas) para configurar la copia programada.

![Propiedades de programación](./media/data-factory-copy-wizard/scheduling-properties.png)

## <a name="next-steps"></a>pasos siguientes
Si quiere ver un tutorial rápido sobre el uso del Asistente para copia de Data Factory a fin de crear una canalización con una actividad de copia, consulte [Tutorial: crear una canalización con la actividad de copia mediante el Asistente para copia de Data Factory](data-factory-copy-data-wizard-tutorial.md).
