---
title: "Identificación de bases de datos y tablas para Stretch Database mediante la ejecución de Stretch Database Advisor | Microsoft Docs"
description: Aprenda a identificar las bases de datos y las tablas que son candidatas a Stretch Database.
services: sql-server-stretch-database
documentationcenter: 
author: douglaslMS
manager: jhubbard
editor: 
ms.assetid: 9253c5a2-f135-4782-95ec-8ff39454c2c0
ms.service: sql-server-stretch-database
ms.workload: data-management
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/05/2017
ms.author: douglasl
translationtype: Human Translation
ms.sourcegitcommit: 47b666a7f2178daebc1b2b442fd1bb8cf31e40bb
ms.openlocfilehash: 3aef66c7b80ee356faedcaaadacd15aea3438714


---
# <a name="identify-databases-and-tables-for-stretch-database-by-running-stretch-database-advisor"></a>Identificación de bases de datos y tablas para Stretch Database mediante la ejecución de Stretch Database Advisor
Para identificar las bases de datos y las tablas que son candidatas a Stretch Database, descargue el Asesor de actualizaciones de SQL Server 2016 y ejecute Stretch Database Advisor. Stretch Database Advisor también identifica problemas de bloqueo.

## <a name="download-and-install-upgrade-advisor"></a>Descarga e instalación del Asesor de actualizaciones
Descargue e instale el Asesor de actualizaciones desde [aquí](http://go.microsoft.com/fwlink/?LinkID=613421). Esta herramienta no se incluye en los medios de instalación de SQL Server.

## <a name="run-the-stretch-database-advisor"></a>Ejecución de Stretch Database Advisor
1. Ejecute el Asesor de actualizaciones.
2. Seleccione **Escenarios** y, después, **RUN STRETCH DATABASE ADVISOR** (EJECUTAR STRETCH DATABASE ADVISOR).
3. En la hoja **Run Stretch Database Advisor** (Ejecutar Stretch Database Advisor), haga clic en **SELECT DATABASES TO ANALYZE** (SELECCIONAR BASES DE DATOS PARA ANALIZAR).
4. En la hoja **Seleccionar bases de datos** , escriba o seleccione el nombre del servidor y la información de autenticación. Haga clic en **Conectar**.
5. Aparece una lista de bases de datos en el servidor seleccionado. Seleccione las bases de datos que quiere analizar. Haga clic en **Seleccionar**.
6. En la hoja **Run Stretch Database Advisor** (Ejecutar Stretch Database Advisor), haga clic en **Ejecutar**.  Se ejecuta el análisis.

## <a name="review-the-results"></a>Revisión del resultado
1. Cuando finalice el análisis, en la hoja **Analyzed databases** (Bases de datos analizadas), seleccione una de las bases de datos que ha analizado para mostrar la hoja **Analysis results** (Resultados del análisis).
   
   En la hoja **Analysis results** (Resultados del análisis) se muestran las tablas recomendadas de la base de datos seleccionada que coinciden con los criterios de recomendación predeterminados.
2. En la lista de tablas de la hoja **Analysis results** (Resultados del análisis), seleccione una de las tablas recomendadas para mostrar la hoja **Table results** (Resultados de la tabla).
   
   En la hoja **Table results** (Resultados de la tabla) se muestran los problemas de bloqueo de la tabla seleccionada. Para más información sobre los problemas de bloqueo detectados por Stretch Database Advisor, consulte [Limitaciones de Stretch Database](sql-server-stretch-database-limitations.md).
3. En la lista de problemas de bloqueo en la hoja **Table results** (Resultados de la tabla), seleccione uno de los problemas para mostrar más información sobre el problema seleccionado y las propuestas con los pasos de solución. Implemente los pasos de solución sugeridos si quiere configurar la tabla seleccionada para Stretch Database.

## <a name="next-step"></a>Paso siguiente
Habilite Stretch Database.

* Para habilitar Stretch Database en una **base de datos**, consulte [Habilitación de Stretch Database para una base de datos](sql-server-stretch-database-enable-database.md).
* Para habilitar Stretch Database en otra **tabla**cuando Stretch ya está habilitado en la base de datos, consulte [Habilitación de Stretch Database para una tabla](sql-server-stretch-database-enable-table.md).

## <a name="see-also"></a>Otras referencias
[Limitaciones de Stretch Database](sql-server-stretch-database-limitations.md)

[Habilitación de Stretch Database para una base de datos](sql-server-stretch-database-enable-database.md)

[Habilitación de Stretch Database para una tabla](sql-server-stretch-database-enable-table.md)




<!--HONumber=Jan17_HO1-->


