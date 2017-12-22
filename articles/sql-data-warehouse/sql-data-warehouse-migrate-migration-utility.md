---
title: "Migración: Utilidad de migración de Data Warehouse | Microsoft Docs"
description: "Migración a SQL Data Warehouse"
services: sql-data-warehouse
documentationcenter: NA
author: sqlmojo
manager: jhubbard
editor: 
ms.assetid: 4d7ad981-ef31-4513-b337-50bdc4709c09
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.custom: migrate
ms.date: 10/31/2016
ms.author: joeyong;barbkess
ms.openlocfilehash: e8a8a84153a950f2d1bc002b34c83dc5ed8a5eb8
ms.sourcegitcommit: b5c6197f997aa6858f420302d375896360dd7ceb
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/21/2017
---
# <a name="data-warehouse-migration-utility-preview"></a>Utilidad de migración de Almacenamiento de datos (vista previa)
> [!div class="op_single_selector"]
> * [Descargar la utilidad de migración][Download Migration Utility]
> 
> 

La Utilidad de migración de almacenamiento de datos es una herramienta diseñada para migrar el esquema y los datos de SQL Server y Azure SQL Database a Azure SQL Data Warehouse. Durante la migración del esquema, la herramienta asigna automáticamente el esquema correspondiente del origen al destino. Después de migrar el esquema, las herramientas proporcionan la opción para mover datos con scripts generados automáticamente.

Además de migración de datos y el esquema, esta herramienta ofrece a los usuarios la opción de generar informes de compatibilidad que resumen las incompatibilidades entre las instancias de origen y de destino que podrían impedir la migración optimizada.

## <a name="get-started"></a>Primeros pasos
Como requisito previo para la instalación, necesitará la utilidad de línea de comandos BCP para ejecutar scripts de migración y Office para ver el informe de compatibilidad. Después de iniciar el archivo ejecutable descargado, se le pedirá que acepte unos términos de licencia estándar antes de que se instale la herramienta.

Además, para ejecutar la utilidad de migración, necesitará los siguientes permisos en la base de datos que busca migrar: CREAR BASE DE DATOS, MODIFICAR CUALQUIER BASE DE DATOS o VER CUALQUIER DEFINICIÓN.

### <a name="launching-the-tool-and-connecting"></a>Inicio de la herramienta y conexión
Inicie la herramienta haciendo clic en el icono del escritorio que aparece después de la instalación. Al abrir la herramienta, se le mostrará una página de conexión inicial donde podrá elegir el origen y destino de la herramienta de migración. En este momento se admiten SQL Server y Azure SQL Database como orígenes y SQL Data Warehouse como destino. Después de seleccionarlo, se le pedirá que se conecte al servidor de origen rellenando el nombre del servidor y la autenticación y, a continuación, que haga clic en «Conectar».

Después de autenticarse, la herramienta mostrará una lista de bases de datos que se encuentran en el servidor al que está conectado. Puede comenzar la migración seleccionando la base de datos que desea migrar y haciendo clic en «Migrate selected» (Migrar seleccionada).

## <a name="migration-report"></a>Informe de migración
Si se selecciona «Check Database Compatibility» (Comprobar compatibilidad de la base de datos) en la herramienta, se generará un informe de resumen de todas las incompatibilidades de los objetos de la base de datos que quiere migrar. Puede encontrar una lista más exhaustiva de algunas de las funciones de SQL Server que no están presentes en SQL Data Warehouse en nuestra [documentación de migración][migration documentation]. Una vez generado el informe, podrá guardarlo y abrirlo en Excel.

Tenga en cuenta que cuando se genera el esquema de migración, la mayoría de los problemas identificados como «Objeto» se ajustarán para permitir la migración inmediata de los datos. Revise los cambios para asegurarse de que no desea realizar ajustes adicionales antes de aplicar el esquema.

## <a name="migrate-schema"></a>Migración del esquema
Después de conectarse, si se selecciona «Migrar esquema», se generará un script de migración de esquema para las tablas seleccionadas. Este script lleva la estructura de la tabla, asigna tipos de datos no compatibles a otros formularios más compatibles y crea las credenciales de seguridad y el esquema si está indicado por el usuario en la configuración de migración. Este código puede ejecutarse en la instancia de SQL Data Warehouse de destino, se puede guardar en un archivo, copiar en el Portapapeles o incluso modificarse en línea antes de realizar otra acción.  

Como se indicó anteriormente, al realizar la migración, el esquema revisa los cambios en la migración que realizó la herramienta para asegurarse de que se comprendieron completamente.  

## <a name="migrate-data"></a>Migración de los datos
Si hace clic en la opción «Migrar datos», puede generar scripts BCP que mueven los datos primero a archivos sin formato en el servidor y después directamente a SQL Data Warehouse. Se recomienda este proceso para mover pequeñas cantidades de datos y porque los reintentos no están integrados y se pueden producir errores si hay una pérdida de conexión de red. Para ejecutarlo, debe tener instalada la utilidad de línea de comandos BCP y ya se debe haber creado el esquema de los datos.

Después de rellenar los parámetros anteriores, basta con que haga clic en la opción de ejecutar migración y se generará un conjunto de dos paquetes en la ubicación especificada. Ejecute el archivo de exportación para exportar datos desde el origen de migración a archivos sin formato y ejecute el archivo de importación para importar los datos en SQL Data Warehouse.

## <a name="next-steps"></a>Pasos siguientes
Ahora que migró algunos datos, aprenda a [desarrollarlos][develop].

<!--Image references-->

<!--Article references-->
[migration documentation]: sql-data-warehouse-overview-migrate.md
[develop]: sql-data-warehouse-overview-develop.md

<!--Other Web references--> 
[Download Migration Utility]: https://www.microsoft.com/en-us/download/details.aspx?id=49100
