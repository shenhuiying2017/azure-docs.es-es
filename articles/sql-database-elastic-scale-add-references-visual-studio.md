<properties title="Add Azure SQL DB Elastic Scale References to a Visual Studio Project" pageTitle="Incorporación de referencias de escalado elástico de Base de datos SQL de Azure a un proyecto de Visual Studio" description="How to add .NET references for Elastic Scale APIs to Visual Studio projects using Nuget." metaKeywords="Azure SQL Database, elastic scale, Nuget references" services="sql-database" documentationCenter="" manager="jhubbard" authors="sidneyh@microsoft.com"/>

<tags ms.service="sql-database" ms.workload="sql-database" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="10/02/2014" ms.author="sidneyh" />

#Eliminación de Incorporación de referencias de escalado elástico de Base de datos SQL de Azure a un proyecto de Visual Studio 

###Requisitos previos: 

- Instale la [Galería de extensiones NuGet Visual Studio](http://docs.nuget.org/docs/start-here/installing-nuget) para Visual Studio 

###Para agregar una referencia de escalado elástico en Visual Studio 

1. Cree un proyecto nuevo a través del cuadro de diálogo Nuevo proyecto ubicado en **Archivo** --> **Nuevo** --> **Proyecto** 
2. En el Explorador de soluciones, haga clic con el botón secundario en **Referencias** y seleccione **Administrar paquetes de NuGet**
3. En el menú del lateral izquierdo de la ventana Administrar paquetes de NuGet, seleccione **En línea** y, a continuación, **nuget.org** o "Todo" 
4. En el cuadro de diálogo **Buscar en línea**, escriba **Escalado elástico**, seleccione las **bibliotecas cliente de escalado elástico** y haga clic en **Instalar**.
4. Revise la licencia y haga clic en **Acepto**. 

Las referencias de escalado automático de Base de datos SQL de Azure ya se han agregado al proyecto. 

[AZURE.INCLUDE [elastic-scale-include](../includes/elastic-scale-include.md)]
