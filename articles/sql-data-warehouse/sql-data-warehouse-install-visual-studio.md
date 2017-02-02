---
title: "Instalación de Visual Studio y SSDT para SQL Data Warehouse | Microsoft Docs"
description: "Instalación de herramientas de desarrollo de Visual Studio y SQL Server Data Tools (SSDT) para Almacenamiento de datos SQL de Azure"
services: sql-data-warehouse
documentationcenter: NA
author: barbkess
manager: jhubbard
editor: 
ms.assetid: 0ed9b406-9b42-4fe6-b963-fe0a5b48aac1
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.date: 10/31/2016
ms.author: barbkess
translationtype: Human Translation
ms.sourcegitcommit: dcda8b30adde930ab373a087d6955b900365c4cc
ms.openlocfilehash: 9c44f417d465aedcbbc731757282e18985b0435c


---
# <a name="install-visual-studio-2015-and-ssdt-for-sql-data-warehouse"></a>Instalación de Visual Studio 2015 y SSDT para Almacenamiento de datos SQL
Para desarrollar aplicaciones para Almacenamiento de datos SQL, se recomienda usar Visual Studio 2015 junto con la versión más reciente de SQL Server Data Tools (SSDT).  También se admite Visual Studio 2013 Update 5 con SSDT por compatibilidad con versiones anteriores.  

Con Visual Studio con SSDT, podrá usar el Explorador de objetos de SQL Server para explorar visualmente tablas, vistas, procedimientos almacenados y muchos más objetos en su instancia de Almacenamiento de datos SQL, así como para ejecutar consultas.

> [!NOTE]
> Almacenamiento de datos SQL no es compatible aún con proyectos de base de datos de Visual Studio.  Esta característica se agregará en una versión futura.
> 
> 

## <a name="step-1-install-visual-studio-2015"></a>Paso 1: Instalación de Visual Studio 2015
Siga estos vínculos para descargar e instalar Visual Studio 2015. Si ya tiene Visual Studio 2013 o 2015 instalado, vaya directamente al paso 2 para instalar SSDT.

1. [Descargue Visual Studio 2015][Download Visual Studio 2015].
2. Siga la guía [Instalar Visual Studio][Installing Visual Studio] en MSDN y elija las configuraciones predeterminadas.

## <a name="step-2-install-ssdt"></a>Paso 2: Instalación de SSDT
Para instalar SSDT para Visual Studio, simplemente busque una actualización de SSDT desde dentro de Visual Studio siguiendo estos pasos.

1. En Visual Studio, haga clic en **Herramientas** / **Extensiones y actualizaciones...** / **Actualizaciones**.
2. Seleccione **Actualizaciones de productos** y busque la actualización **Microsoft SQL Server Update para herramientas de base de datos**.

Si no hay ninguna actualización, debería tener la última versión instalada.  Para confirmar que SSDT está instalado, haga clic en **Ayuda** / **Acerca de Microsoft Visual Studio** y busque SQL Server Data Tools en la lista.  La versión más reciente de SSDT es 14.0.60525.0.  Si la opción para instalar no está disponible en Visual Studio, también puede visitar la página [Descargar las últimas herramientas de datos SQL Server][SSDT Download] para descargar e instalar SSDT manualmente.

## <a name="next-steps"></a>Pasos siguientes
Ahora que tiene la versión más reciente de SSDT, ya puede [conectarse][connect] a su instancia de Almacenamiento de datos SQL.

<!--Anchors-->

<!--Image references-->

<!--Articles-->
[connect]: ./sql-data-warehouse-query-visual-studio.md

<!--Other-->
[Download Visual Studio 2015]: https://www.visualstudio.com/downloads/
[Installing Visual Studio]: https://msdn.microsoft.com/library/e2h7fzkw.aspx
[SSDT Download]: https://msdn.microsoft.com/library/mt204009.aspx



<!--HONumber=Dec16_HO2-->


