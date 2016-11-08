---
title: Instalación de Visual Studio y SSDT para Almacenamiento de datos SQL | Microsoft Docs
description: Instalación de herramientas de desarrollo de Visual Studio y SQL Server Data Tools (SSDT) para Almacenamiento de datos SQL de Azure
services: sql-data-warehouse
documentationcenter: NA
author: sonyam
manager: barbkess
editor: ''

ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: get-started-article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.date: 08/16/2016
ms.author: sonyama;barbkess

---
# Instalación de Visual Studio 2015 y SSDT para Almacenamiento de datos SQL
Para desarrollar aplicaciones para Almacenamiento de datos SQL, se recomienda usar Visual Studio 2015 junto con la versión más reciente de SQL Server Data Tools (SSDT). También se admite Visual Studio 2013 Update 5 con SSDT por compatibilidad con versiones anteriores.

Con Visual Studio con SSDT, podrá usar el Explorador de objetos de SQL Server para explorar visualmente tablas, vistas, procedimientos almacenados y muchos más objetos en su instancia de Almacenamiento de datos SQL, así como para ejecutar consultas.

> [!NOTE]
> Almacenamiento de datos SQL no es compatible aún con proyectos de base de datos de Visual Studio. Esta característica se agregará en una versión futura.
> 
> 

## Paso 1: Instalación de Visual Studio 2015
Siga estos vínculos para descargar e instalar Visual Studio 2015. Si ya tiene Visual Studio 2013 o 2015 instalado, vaya directamente al paso 2 para instalar SSDT.

1. [Descargue Visual Studio 2015][Descargue Visual Studio 2015].
2. Siga la guía [Instalar Visual Studio][Instalar Visual Studio] en MSDN y elija las configuraciones predeterminadas.

## Paso 2: Instalación de SSDT
Para instalar SSDT para Visual Studio, simplemente busque una actualización de SSDT desde dentro de Visual Studio siguiendo estos pasos.

1. En Visual Studio, haga clic en **Herramientas**, **Extensiones y actualizaciones** y **Actualizaciones**.
2. Seleccione **Actualizaciones de productos** y busque la actualización **Microsoft SQL Server Update para herramientas de base de datos**.

Si no hay ninguna actualización, debería tener la última versión instalada. Para confirmar que SSDT está instalado, haga clic en **Ayuda** y **Acerca de Microsoft Visual Studio**, y busque SQL Server Data Tools en la lista. La versión más reciente de SSDT es 14.0.60525.0. Si la opción para instalar no está disponible en Visual Studio, también puede visitar la página [Descargar las últimas herramientas de datos SQL Server][Descargar las últimas herramientas de datos SQL Server] para descargar e instalar SSDT manualmente.

## Pasos siguientes
Ahora que tiene la versión más reciente de SSDT, ya puede [conectarse][conectarse] a su instancia de Almacenamiento de datos SQL.

<!--Anchors-->

<!--Image references-->

<!--Articles-->
[conectarse]: ./sql-data-warehouse-query-visual-studio.md

<!--Other-->
[Descargue Visual Studio 2015]: https://www.visualstudio.com/downloads/
[Instalar Visual Studio]: https://msdn.microsoft.com/library/e2h7fzkw.aspx
[Descargar las últimas herramientas de datos SQL Server]: https://msdn.microsoft.com/library/mt204009.aspx

<!---HONumber=AcomDC_0817_2016-->