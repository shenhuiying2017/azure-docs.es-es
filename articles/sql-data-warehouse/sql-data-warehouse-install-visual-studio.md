<properties
   pageTitle="Instalación de Visual Studio y SSDT para Almacenamiento de datos SQL | Microsoft Azure"
   description="Instalación de herramientas de desarrollo de Visual Studio y SQL Server Data Tools (SSDT) para Almacenamiento de datos SQL de Azure"
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="sonyam"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="05/16/2016"
   ms.author="sonyama;barbkess"/>

# Instalación de Visual Studio 2015 y SSDT para Almacenamiento de datos SQL

Para desarrollar aplicaciones para Almacenamiento de datos SQL, se recomienda usar Visual Studio 2015 junto con la versión más reciente de SQL Server Data Tools (SSDT). También se admite Visual Studio 2013 con SSDT.

Además, se necesita **la actualización de Microsoft SQL Server para herramientas de base de datos**, para ejecutar consultas desde el entorno de desarrollo integrado (IDE) de Visual Studio. Una vez instalada esta extensión, podrá ver los objetos de base de datos en el árbol del Explorador de objetos y ejecutar consultas en el Almacenamiento de datos SQL.

> [AZURE.NOTE] Almacenamiento de datos SQL no es compatible aún con proyectos de base de datos de Visual Studio. Esta característica se agregará en una versión futura.

## Paso 1: Instalación de Visual Studio 2015

Siga estos vínculos para descargar e instalar Visual Studio 2015. Si ya tiene Visual Studio 2013 o 2015 instalado, vaya directamente al paso 2 para instalar SSDT.

1. [Descargue Visual Studio 2015][] desde Visual Studio Team Services.
2. Siga la guía [Instalación de Visual Studio][] en MSDN para elegir las configuraciones predeterminadas.

## Paso 2: Instalación de SSDT

Para instalar SSDT para Visual Studio, simplemente busque una actualización de SSDT desde dentro de Visual Studio siguiendo estos pasos.

1. En Visual Studio haga clic en **Herramientas** / **Extensiones y actualizaciones…** / **Actualizaciones**
2. Seleccione **Actualizaciones de productos** y, a continuación, busque la **actualización de Microsoft SQL Server para herramientas de base de datos**

Si no hay ninguna actualización, tiene la última versión instalada. Para confirmar que SSDT está instalada, haga clic en **Ayuda** / **Acerca de Microsoft Visual Studio** y busque SQL Server Data Tools en la lista.

## Pasos siguientes

Ahora que tiene la versión más reciente de SSDT, está listo para [conectar][] la base de datos.

<!--Anchors-->

<!--Image references-->

<!--Articles-->
[conectar]: ./sql-data-warehouse-get-started-connect.md

<!--Other-->
[Descargue Visual Studio 2015]: https://www.visualstudio.com/downloads/
[Instalación de Visual Studio]: https://msdn.microsoft.com/library/e2h7fzkw.aspx

<!---HONumber=AcomDC_0518_2016-->