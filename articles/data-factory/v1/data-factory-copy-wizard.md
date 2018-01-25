---
title: "Copia fácil de datos con el Asistente para copia - Azure | Microsoft Docs"
description: "Obtenga información sobre cómo utilizar el Asistente para copia de Data Factory para copiar datos de orígenes de datos admitidos en receptores."
services: data-factory
documentationcenter: 
author: spelluru
manager: jhubbard
editor: monicar
ms.assetid: f904972f-cd33-48db-9755-2b3196ae4168
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/22/2018
ms.author: spelluru
robots: noindex
ms.openlocfilehash: c993b1dfb0055da84751c042efccf42d943375d9
ms.sourcegitcommit: 9cc3d9b9c36e4c973dd9c9028361af1ec5d29910
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/23/2018
---
# <a name="copy-or-move-data-easily-with-azure-data-factory-copy-wizard"></a>Copia o movimiento sencillos de datos con el Asistente para copia de Azure Data Factory
> [!NOTE]
> Este artículo se aplica a la versión 1 de Data Factory, que está disponible con carácter general. Si usa la versión 2 del servicio Data Factory, que se encuentra en versión preliminar, vea el [tutorial de la actividad de copia en la documentación de la versión 2](../quickstart-create-data-factory-dot-net.md). 


El Asistente para copia de factoría de datos de Azure se usa para simplificar el proceso de ingesta de datos, que normalmente es el primer paso en un escenario de integración de datos de un extremo a otro. Para aprender el uso del Asistente para copia de Azure Data Factory no es preciso conocer las definiciones de JSON de servicios vinculados, conjuntos de datos y canalizaciones. Sin embargo, después de completar todos los pasos del asistente, este crea automáticamente una canalización para copiar datos del origen de datos seleccionado al destino seleccionado. Además, el Asistente para copia ayuda a validar los datos que se van a ingerir en el momento de creación, lo que ahorra mucho tiempo, especialmente la primera vez que se van a ingerir datos del origen de datos. Si quiere iniciar el Asistente para copia, haga clic en el icono **Copiar datos** de la página principal de Data Factory.

![Asistente para copia](./media/data-factory-copy-wizard/copy-data-wizard.png)

## <a name="an-intuitive-wizard-for-copying-data"></a>Un asistente intuitivo para copiar datos
Este asistente permite mover fácilmente los datos de una gran variedad de orígenes a distintos destinos en cuestión de minutos. Después de pasar por el asistente, se crea automáticamente una canalización con una actividad de copia, junto con las entidades de Data Factory dependientes (servicios vinculados y conjuntos de datos). No se requieren más pasos para crear la canalización.   

![Selección de origen de datos](./media/data-factory-copy-wizard/select-data-source-page.png)

> [!NOTE]
> Para ver instrucciones detalladas para crear una canalización de ejemplo para copiar datos desde un Azure blob a una tabla de Azure SQL Database, consulte el artículo [Tutorial: crear una canalización con la actividad de copia mediante el Asistente para copia de Data Factory](data-factory-copy-data-wizard-tutorial.md). 
> 
> 

El asistente se ha diseñado desde el principio específicamente para los macrodatos. Permite crear, de manera simple y eficiente, canalizaciones de Data Factory que muevan cientos de carpetas, archivos o tablas mediante el Asistente de copia de datos. El asistente admite las tres características siguientes: vista previa de datos automática, captura y asignación de esquema, y filtrado de datos. 

## <a name="automatic-data-preview"></a>Vista previa de datos automática
El Asistente para copia permite revisar parte de los datos del origen de datos seleccionado para que pueda validar si los datos son los que desea copiar. Además, si los datos de origen están en un archivo de texto, el Asistente para copia analiza dicho archivo para obtener más información sobre el esquema y los delimitadores de columna y fila automáticamente. 

![Configuración del formato de archivo](./media/data-factory-copy-wizard/file-format-settings.png)

## <a name="schema-capture-and-mapping"></a>Captura y asignación de esquema
En algunos casos, es posible que el esquema de datos de entrada no coincida con el esquema de datos de salida. En este escenario, es preciso asignar columnas del esquema de origen a columnas del esquema de destino. 

El Asistente para copia asigna automáticamente columnas del esquema de origen a columnas del esquema de destino. Las asignaciones se pueden invalidar mediante el uso de las listas desplegables (o) se puede especificar si una columna debe omitirse al copiar los datos.   

![Asignación de esquemas](./media/data-factory-copy-wizard/schema-mapping.png)

## <a name="filtering-data"></a>Filtrado de datos
El asistente permite filtrar los datos de origen para seleccionar solo aquellos que deben copiarse en el destino o en el almacén de datos receptor. El filtrado reduce el volumen de los datos se copian en el almacén de datos del receptor, por lo que mejora el rendimiento de la operación de copia. Proporciona una manera flexible de filtrar los datos de una base de datos relacional mediante el lenguaje de consulta SQL (o) archivos en una carpeta de blobs de Azure mediante [Funciones y variables de Data Factory](data-factory-functions-variables.md).   

### <a name="filtering-of-data-in-a-database"></a>Filtrado de datos de una base de datos
En el ejemplo, la consulta SQL usa la función `Text.Format` y la variable `WindowStart`. 

![Validación de expresiones](./media/data-factory-copy-wizard/validate-expressions.png)

### <a name="filtering-of-data-in-an-azure-blob-folder"></a>Filtrar datos en una carpeta de blobs de Azure
Puede usar variables en la ruta de acceso de la carpeta para copiar datos desde una carpeta que se determina en el runtime según las [variables del sistema](data-factory-functions-variables.md#data-factory-system-variables). Estas son las variables que se admiten: **{year}**, **{month}**, **{day}**, **{hour}**, **{minute}** y **{custom}**. Ejemplo: carpetaDeEntrada/{year}/{month}/{day}.

Supongamos que tiene carpetas de entrada con el siguiente formato:

    2016/03/01/01
    2016/03/01/02
    2016/03/01/03
    ...

Haga clic en el botón **Examinar** de **Archivo o carpeta**, vaya a una de estas carpetas (por ejemplo, 2016->03->01->02) y haga clic en **Elegir**. En el cuadro de texto, debería aparecer `2016/03/01/02`. Sustituya **2016** por **{year}**, **03** por **{month}**, **01** por **{day}** y **02** por **{hour}** y presione la tecla TAB. Aparecerán listas desplegables en las que podrá seleccionar el formato de estas cuatro variables:

![Uso de variables del sistema](./media/data-factory-copy-wizard/blob-standard-variables-in-folder-path.png)   

Como se muestra en la siguiente captura de pantalla, también puede usar una variable **personalizada** y cualquier [cadena de formato admitida](https://msdn.microsoft.com/library/8kb3ddd4.aspx). Para seleccionar una carpeta con esa estructura, utilice primero el botón **Examinar** . A continuación, reemplace un valor por **{custom}**y presione la tecla Tabulación para ver el cuadro de texto donde puede escribir la cadena de formato.     

![Uso de la variable personalizada](./media/data-factory-copy-wizard/blob-custom-variables-in-folder-path.png)

## <a name="support-for-diverse-data-and-object-types"></a>Compatibilidad con distintos tipos de objetos y datos
Gracias al Asistente para copia se pueden mover de manera eficaz cientos de tablas, archivos o carpetas.

![Selección de tablas desde las que se van a copiar datos](./media/data-factory-copy-wizard/select-tables-to-copy-data.png)

## <a name="scheduling-options"></a>Opciones de programación
Puede ejecutar la operación de copia una vez o de forma periódica (cada hora, día, etc.). Estas opciones se pueden utilizar para los distintos conectores locales y en la nube, así como para la copia del escritorio local.

Una operación de copia única permite el movimiento de datos desde un origen a un destino solamente una vez. Se aplica a los datos de cualquier tamaño y en cualquier formato admitido. La copia programada permite copiar datos con una frecuencia prescrita. Puede utilizar las opciones avanzadas (como el reintento, el tiempo de espera y las alertas) para configurar la copia programada.

![Propiedades de programación](./media/data-factory-copy-wizard/scheduling-properties.png)

## <a name="next-steps"></a>pasos siguientes
Si quiere ver un tutorial rápido sobre el uso del Asistente para copia de Data Factory a fin de crear una canalización con una actividad de copia, consulte [Tutorial: crear una canalización con la actividad de copia mediante el Asistente para copia de Data Factory](data-factory-copy-data-wizard-tutorial.md).

