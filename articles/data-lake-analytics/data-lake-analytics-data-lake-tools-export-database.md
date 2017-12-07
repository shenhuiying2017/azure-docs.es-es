---
title: "Exportación de bases de datos de U-SQL mediante Herramientas de Azure Data Lake para Visual Studio | Microsoft Docs"
description: "Obtenga información acerca de cómo usar Herramientas de Azure Data Lake para Visual Studio para exportar una base de datos de U-SQL e importarla en la cuenta local al mismo tiempo."
services: data-lake-analytics
documentationcenter: 
author: yanancai
manager: 
editor: 
ms.assetid: dc9b21d8-c5f4-4f77-bcbc-eff458f48de2
ms.service: data-lake-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 11/27/2017
ms.author: yanacai
ms.openlocfilehash: 9f11e07f7fbaf2b708d6fbc8a746238745132bda
ms.sourcegitcommit: 29bac59f1d62f38740b60274cb4912816ee775ea
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/29/2017
---
# <a name="how-to-export-u-sql-database"></a>Exportación de la base de datos de U-SQL

En este documento, obtendrá información sobre cómo usar [Herramientas de Azure Data Lake para Visual Studio](http://aka.ms/adltoolsvs) para exportar una base de datos de U-SQL como un único script de U-SQL y recursos descargados. También se admite la importación de la base de datos exportada a la cuenta local en el mismo proceso.

Normalmente, los clientes mantienen varios entornos de desarrollo, prueba y producción. Estos entornos se hospedan tanto en una cuenta local en el equipo local de los desarrolladores como en una cuenta de Azure Data Lake Analytics en Azure. Al desarrollar y ajustar las consultas de U-SQL en entornos de desarrollo y prueba, es común que los desarrolladores tengan que volver a crear todo el contenido de la base de datos de producción. El **Asistente para la exportación de bases de datos** ayuda a acelerar este proceso. Mediante el asistente, los programadores pueden clonar el entorno de base de datos existente y los datos de ejemplo a otras cuentas de Azure Data Lake Analytics.

## <a name="export-steps"></a>Pasos de exportación

### <a name="step-1-right-click-the-database-in-server-explorer-and-click-export"></a>Paso 1: Haga clic en con el botón derecho en la base de datos en el Explorador de servidores y haga clic en "Exportar...".

Todas las cuentas de Azure Data Lake Analytics para las que tiene permiso se muestran en el Explorador de servidores. Expanda la que contiene la base de datos que desea exportar y haga clic derecho en la base de datos para elegir **Exportar...** Si no encuentra el menú contextual, deberá [actualizar la herramienta a la última versión](http://aka.ms/adltoolsvs).

![Exportación de base de datos en Herramientas de Data Lake Analytics](./media/data-lake-analytics-data-lake-tools-export-database/export-database.png)

### <a name="step-2-configure-the-objects-you-want-to-export"></a>Paso 2: Configure los objetos que desea exportar

A veces, una base de datos es grande pero solo necesita una pequeña parte de esta; en ese caso, puede configurar el subconjunto de objetos que desea exportar en el Asistente para exportación. Tenga en cuenta que la acción de exportación se completa mediante la ejecución de un trabajo de U-SQL; por lo tanto, la exportación desde una cuenta de Azure puede tener algún coste.

![Asistente para exportación de base de datos de Herramientas de Data Lake Analytics](./media/data-lake-analytics-data-lake-tools-export-database/export-database-wizard.png)

### <a name="step-3-check-the-objects-list-and-more-configurations"></a>Paso 3: Compruebe la lista de objetos y otras configuraciones

En este paso, puede comprobar de nuevo los objetos seleccionados en la parte superior del cuadro de diálogo. Si hay errores, puede hacer clic en Anterior para volver atrás y configurar de nuevo los objetos que desea exportar.

También puede establecer otras configuraciones sobre el destino de exportación, las descripciones de estas configuraciones se muestran en tabla siguiente:

|Configuración|Descripción|
|-------------|-----------|
|Nombre del destino|Este nombre indica el lugar en el que desea guardar los recursos de base de datos exportados, como ensamblados, archivos adicionales y datos de ejemplo. Se creará una carpeta con este nombre en la carpeta raíz de datos locales.|
|Directorio del proyecto|Esta ruta de acceso define el lugar en el que desea guardar el script de U-SQL exportado, que incluye todas las definiciones de objeto de base de datos.|
|Solo esquema|Al seleccionar esta opción se obtienen solo las definiciones y los recursos de la base de datos (como los ensamblados y los archivos adicionales) que se están exportando.|
|Esquema y datos|Al seleccionar esta opción se obtienen las definiciones de base de datos, los recursos y los datos que se van a exportar. Se exportan las primeras N filas de las tablas.|
|Importar de forma automática en la base de datos local|Al activar esta configuración, la base de datos exportada se importará en la base de datos local automáticamente una vez completada la exportación.|

![Configuración del Asistente para exportación de base de datos de Herramientas de Data Lake Analytics](./media/data-lake-analytics-data-lake-tools-export-database/export-database-wizard-configuration.png)

### <a name="step-4-check-the-export-results"></a>Paso 4: Compruebe los resultados de la exportación

Cuando haya terminado con todos estos valores de configuración y el proceso de exportación, encontrará los resultados exportados en la ventana de registros del asistente. El registro marcado con un rectángulo rojo en la siguiente captura de pantalla le ayudará a encontrar la ubicación del script de U-SQL y los recursos de bases de datos exportados, como ensamblados, archivos adicionales y datos de ejemplo.

![Asistente para exportación de base de datos completado de Herramientas de Data Lake Analytics](./media/data-lake-analytics-data-lake-tools-export-database/export-database-wizard-completed.png)

## <a name="how-to-import-the-exported-database-to-local-account"></a>Importación de la base de datos exportada a la cuenta local

La manera más cómoda de hacer esta importación es activar la opción **Importar de forma automática en la base de datos local** durante el progreso de la exportación del paso 3. Si olvidó hacerlo, puede encontrar el script de U-SQL exportado a través del registro de exportación y ejecutar el script de U-SQL localmente para importar la base de datos en la cuenta local.

## <a name="how-to-import-the-exported-database-to-azure-data-lake-analytics-account"></a>Importación de la base de datos exportada en la cuenta de Azure Data Lake Analytics

Para importar la base de datos en otra cuenta de análisis de Azure Data Lake Analytics se requieren dos pasos:

1. Cargue los recursos exportados, incluidos los ensamblados, los archivos adicionales y los datos de ejemplo en la cuenta de Azure Data Lake Store predeterminada de la cuenta de Azure Data Lake Analytics en la que desea importar. Puede encontrar la carpeta de recursos exportados en la carpeta raíz de datos locales, y cargar toda la carpeta en la raíz de la cuenta de almacenamiento predeterminada.
2. Envíe el script de U-SQL exportado a la cuenta de Azure Data Lake Analytics en la que desea importar la base de datos una vez que la carga esté completa.

## <a name="known-limitation"></a>Limitación conocida

Actualmente, si seleccionó **Esquema y datos** en el asistente, la herramienta ejecuta un trabajo de U-SQL para exportar los datos almacenados en tablas. Por eso el proceso de exportación de datos puede ser lento y suponer un costo. 

## <a name="next-steps"></a>Pasos siguientes

* [Understand U-SQL database](https://msdn.microsoft.com/library/azure/mt621299.aspx) (Descripción de la base de datos de U-SQL) 
* [Prueba y depuración de trabajos U-SQL mediante la ejecución local y el SDK de U-SQL para Azure Data Lake](data-lake-analytics-data-lake-tools-local-run.md)


