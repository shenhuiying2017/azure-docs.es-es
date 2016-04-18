<properties
   pageTitle="API de Power BI Embedded .NET"
   description=""
   services="power-bi-embedded"
   documentationCenter=""
   authors="dvana"
   manager="NA"
   editor=""
   tags=""/>
<tags
   ms.service="power-bi-embedded"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="powerbi"
   ms.date="03/29/2016"
   ms.author="derrickv"/>

# API de Power BI Embedded .NET

La versión preliminar de **Microsoft Power BI Embedded** se centra principalmente en la exposición de la mayoría de las funcionalidades de la API de Power BI existentes como parte de un servicio de Azure con el que desarrollar las aplicaciones. Además, podrá aprovisionar, desarrollar e implementar los recursos necesarios y el contenido de Power BI mediante programación.

Puede usar la **API de Power BI** para crear y administrar áreas de trabajo de contenido de Power BI. Con la API, puede:

  - Importar un archivo de Power BI Desktop (PBIX) en un área de trabajo con una clave basada en la autenticación.
  - Modificar cadenas de conexión de un conjunto de datos.
  - Obtener **informes** para integrarlos en su propia aplicación.
  - Establecer las credenciales de un conjunto de datos para poder comunicarse con el origen de datos correcto.
  - Para obtener más información sobre la **API de Power BI**, consulte [Get started with Microsoft Power BI Embedded preview (Introducción a la versión preliminar de Microsoft Power BI Embedded)](power-bi-embedded-get-started.md).

Consulte el [Material de referencia de Power BI en MSDN](https://msdn.microsoft.com/library/mt669800.aspx).

Estas son algunas de las clases y métodos que se usan en el **ejemplo de Power BI Embedded**:

## Espacio de nombres Microsoft.PowerBI.Api.Beta Namespace

### Clase ReportsExtensions
|Nombre|Descripción
|---|---
|[GetReports(IReports, String, String)](https://msdn.microsoft.com/library/microsoft.powerbi.api.beta.reportsextensions.getreports.aspx)|Obtiene una lista de los informes disponibles en el área de trabajo especificado
|[GetReportsAsync(IReports, String, String, CancellationToken)](https://msdn.microsoft.com/library/microsoft.powerbi.api.beta.reportsextensions.getreportsasync.aspx)|Obtiene una lista de los informes disponibles en el área de trabajo especificado

## Espacio de nombres Microsoft.PowerBI.Security

### Métodos de PowerBIToken
|Nombre| Descripción
|---|---
|[CreateDevToken(String, Guid)](https://msdn.microsoft.com/library/mt670215.aspx)|Crea un token para el desarrollador con una expiración predeterminada que se usa para acceder a los servicios de la plataforma de Power BI.
|[CreateProvisionToken(String)](https://msdn.microsoft.com/library/mt670218.aspx)|Crea un token de aprovisionamiento con una expiración predeterminada que se usa para administrar áreas de trabajo en una colección de área de trabajo

## Consulte también

- [Qué es Microsoft Power BI Embedded](power-bi-embedded-what-is-power-bi-embedded.md)
- [Get started with Microsoft Power BI Embedded preview (Introducción a la versión preliminar de Microsoft Power BI Embedded)](power-bi-embedded-get-started.md)

<!---HONumber=AcomDC_0406_2016-->