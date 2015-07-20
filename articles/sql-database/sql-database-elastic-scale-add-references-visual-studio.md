<properties 
	pageTitle="referencias de escalado elástico de Base de datos SQL de Azure a un proyecto de Visual Studio" 
	description="Cómo agregar referencias de .NET para las API de escala elástica a proyectos de Visual Studio mediante Nuget." 
	services="sql-database" 
	documentationCenter="" 
	manager="jeffreyg" 
	authors="sidneyh" 
	editor=""/>

<tags 
	ms.service="sql-database" 
	ms.workload="sql-database" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="04/16/2015" 
	ms.author="sidneyh"/>

# Incorporación de referencias de biblioteca de cliente de bases de datos elásticas a un proyecto de Visual Studio 

### Requisitos previos: 

- Instale la [Galería de extensiones NuGet Visual Studio](http://docs.nuget.org/docs/start-here/installing-nuget) para Visual Studio 

### Para agregar una referencia de biblioteca de cliente de bases de datos elástica en Visual Studio 

1. Abra un proyecto existente o cree un nuevo proyecto mediante el cuadro de diálogo Nuevo proyecto que se encuentra en **Archivo** --> **Nuevo** --> **Proyecto** 
2. En el Explorador de soluciones, haga doble clic en **Referencias** y seleccione **Administrar paquetes de NuGet**.
3. En el menú en el lado izquierdo de la ventana Administrar paquetes de NuGet, seleccione **En línea** y, a continuación, **nuget.org** o "Todo". 
4. En el cuadro de diálogo **Buscar en línea**, escriba **Base de datos elástica**, seleccione la **Biblioteca de cliente de bases de datos elásticas** y haga clic en **Instalar**.

	![Busque en línea][1]
4. Revise la licencia y haga clic en **Acepto**. 

La referencia de biblioteca de cliente se agregó al proyecto.

[AZURE.INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]

<!--Image references-->
[1]: ./media/sql-database-elastic-scale-add-references-visual-studio/search-online.png
<!--anchors-->

<!---HONumber=July15_HO2-->