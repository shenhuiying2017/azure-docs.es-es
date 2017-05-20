---
title: "Importación y exportación en Azure Database for MySQL | Microsoft Docs"
description: "Presenta la importación y exportación de bases de datos en Azure Database for MySQL."
services: mysql
author: v-chenyh
ms.author: v-chenyh
manager: jhubbard
editor: jasonh
ms.assetid: 
ms.service: mysql-database
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: portal
ms.date: 05/10/2017
ms.translationtype: Human Translation
ms.sourcegitcommit: 71fea4a41b2e3a60f2f610609a14372e678b7ec4
ms.openlocfilehash: 582f83e3e80d855049abece42726748fcd113ab7
ms.contentlocale: es-es
ms.lasthandoff: 05/10/2017

---

# <a name="migrate-your-mysql-database-using-import-and-export"></a>Migre su Base de datos de MySQL mediante importación y exportación.
En este tutorial se le mostrará la manera más común de importar y exportar los datos en Azure Database for MySQL mediante MySQL Workbench. 

## <a name="before-you-begin"></a>Antes de empezar
Para seguir esta guía, necesita lo siguiente:
- Una instancia de Azure Database for MySQL: [diseñe su primera base de datos de MySQL en Azure](quickstart-create-mysql-server-database-using-azure-portal.md)
- MySQL Workbench: [descargue MySQL Workbench](https://dev.mysql.com/downloads/workbench/)

## <a name="use-common-tools"></a>Uso de herramientas comunes
Use herramientas comunes como MySQL Workbench, Toad o Navicat para conectarse y restaurar datos de forma remota en Azure Database for MySQL. Use estas herramientas en el equipo cliente con una conexión a Internet para conectarse a Azure Database for MySQL. Use una conexión cifrada SSL para aprovechar los procedimientos recomendados de seguridad; vea también la información sobre [conectividad SSL en Azure Database for MySQL](concepts-ssl-connection-security.md). No es necesario mover los archivos de importación y exportación a ninguna ubicación en la nube especial al migrar a Azure Database for MySQL. 

## <a name="create-a-database-on-the-target-azure-database-for-mysql-service"></a>Creación de una base de datos en el servicio de Azure Database for MySQL
Debe crear una base de datos vacía en el servidor de Azure Database for MySQL de destino en el que desea a migrar los datos con MySQL Workbench, Toad, Navicat o cualquier herramienta de terceros para MySQL. La base de datos puede tener el mismo nombre que la base de datos que contiene los datos volcados, o puede crear una base de datos con un nombre diferente.

![Cadena de conexión de Azure Database for MySQL](./media/concepts-migrate-import-export/p5.png)

![Cadena de conexión de MySQL Workbench](./media/concepts-migrate-import-export/p4.png)

## <a name="import-and-export-using-mysql-workbench"></a>Importación y exportación mediante MySQL Workbench
Hay dos maneras de exportar e importar datos en MySQL Workbench, y cada una de ellas sirve para un propósito diferente. 

## <a name="table-data-export-and-import-wizard-using-object-browser-context-menu"></a>Asistente para la exportación y la importación de datos de tablas mediante el menú contextual del examinador de objetos
![Importación y exportación en MySQL Workbench mediante el menú contextual del examinador de objetos](./media/concepts-migrate-import-export/p1.png)

Este asistente admite operaciones de importación y exportación con archivos CSV y JSON, e incluye varias opciones de configuración (separadores, selección de columna, selección de codificación, etc.). El asistente puede utilizarse con servidores de MySQL locales o conectados de manera remota, y la acción de importación incluye la asignación de tablas, columnas y tipos. Para tener acceso al asistente, desde el menú contextual del examinador de objetos haga clic con el botón secundario en una tabla y elija **Table Data Export Wizard** (Asistente para la exportación de datos de tabla) o **Table Data Import Wizard** (Asistente para la importación de datos de tabla). 

## <a name="table-data-export-wizard"></a>Asistente para exportación de datos de tabla
En el ejemplo siguiente se exporta la tabla a un archivo CSV. 
- Haga clic en con el botón derecho en la tabla de la base de datos que se va a exportar. 
- Seleccione **Table Data Export Wizard** (Asistente para exportación de datos de tabla). Seleccione las columnas que se exportarán, el desplazamiento de fila (si lo hubiera) y el número (si lo hubiera). 
- Haga clic en **Next** (Siguiente) en la ventana de selección de datos para exportar. Seleccione la ruta del archivo, el tipo de archivo CSV o JSON, el separador de línea, el valor para enmarcar cadenas y el separador de campos. 
- Seleccione **Next** (Siguiente) en la ventana de selección de la ubicación del archivo de salida y de nuevo "Next" (Siguiente) en la ventana de exportación de datos.


## <a name="table-data-import-wizard"></a>Asistente para importación de datos de tabla
En el ejemplo siguiente se importa la tabla desde un archivo CSV.
- Haga clic en con el botón derecho en la tabla de la base de datos que se va a importar. 
- Busque y seleccione el archivo CSV que desea importar y luego haga clic en el botón "Next" (Siguiente). 
- Seleccione la tabla de destino (nueva o existente) y seleccione o anule la selección de la casilla "Truncate table before import" (Truncar tabla antes de la importación). Luego, haga clic en "Next" (Siguiente).
- Seleccione la codificación y las columnas que desea importar y seleccione el botón "Next" (Siguiente). 
- Seleccione "Next" en la ventana de importación de datos para que se importen los datos de la manera correspondiente.

## <a name="sql-data-export-and-import-wizard-from-management-navigator"></a>Asistente para la exportación e importación de datos de SQL desde Management Navigator
Use este asistente para exportar o para importar datos de SQL generados desde MySQL Workbench o con el comando mysqldump. Obtenga acceso a estos asistentes desde el panel de Navigator o seleccione Server (Servidor) en el menú principal y luego Data Import (Importación de datos) o Data Export (Exportación de datos). 

## <a name="data-export"></a>Exportación de datos
![Exportación de los datos en MySQL Workbench mediante Management Navigator](./media/concepts-migrate-import-export/p2.png)

Esta pestaña le permite exportar sus datos de MySQL. 
- Seleccione cada esquema que desee exportar, elija opcionalmente objetos o tablas de esquema específicos de cada uno de ellos y genere la exportación. Entre las opciones de configuración se incluye la exportación a una carpeta de proyecto o un archivo independiente de SQL, el volcado opcional de rutinas y eventos almacenados o la omisión de datos de tabla. 
- También puede usar **Export a Result Set** (Exportar un conjunto de resultados) para exportar un resultado concreto establecido en el editor de SQL a otro formato como CSV, JSON, HTML y XML. 
- Seleccione los objetos de base de datos que desea exportar y configure las opciones relacionadas. 
- Haga clic en **Refresh** (Actualizar) para cargar los objetos actuales. 
- Si lo desea, abra la pestaña Advanced Options (Opciones avanzadas) para concretar más la operación de exportación. Por ejemplo, agregue bloqueos de tabla, utilice instrucciones replace en lugar de insert, entrecomille los identificadores con caracteres de acento grave, etc. 
- Haga clic en **Start Export** (Iniciar exportación) para comenzar el proceso de exportación. 


## <a name="data-import"></a>Importación de datos
![Importación de los datos en MySQL Workbench mediante Management Navigator](./media/concepts-migrate-import-export/p3.png)

Esta pestaña le permite importar o restaurar los datos exportados con la operación de exportación de datos, o desde otros datos exportados con el comando mysqldump. 
- Elija la carpeta de proyecto o archivo SQL independiente, elija el esquema en el que se importarán los datos o elija "New" (Nuevo) para definir un nuevo esquema. 
- Haga clic en **Start Import** (Iniciar importación) para iniciar el proceso de importación, que importará los datos de la manera correspondiente.

## <a name="next-steps"></a>Pasos siguientes
Si desea obtener más información acerca de cómo empezar a utilizar este servicio de base de datos, revise estos artículos:
-  [Create an Azure Database for MySQL server using Azure Portal](quickstart-create-mysql-server-database-using-azure-portal.md) (Creación de un servidor de Azure Database for MySQL mediante Azure Portal) 
- [Create an Azure Database for MySQL server using Azure CLI](quickstart-create-mysql-server-database-using-azure-cli.md) (Creación de un servidor de Azure Database for MySQL mediante la CLI de Azure)

