<properties 
   pageTitle="Notas de la versión de SDK de Azure para .NET 2.9" 
   description="Notas de la versión de SDK de Azure para .NET 2.9" 
   services="app-service\web" 
   documentationCenter=".net" 
   authors="Juliako" 
   manager="erikre" 
   editor=""/>

<tags
   ms.service="app-service"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="integration" 
   ms.date="03/29/2016"
   ms.author="juliako"/>


# Notas de la versión de SDK de Azure para .NET 2.9

##Información general

Este documento contiene las notas de la versión de SDK de Azure para la versión .NET 2.9.

Para obtener información detallada sobre las actualizaciones de esta publicación, consulte [Announcing the Visual Studio Azure Tools and SDK 2.9](https://azure.microsoft.com/blog/announcing-visual-studio-azure-tools-and-sdk-2-9/) (Presentación de Visual Studio Azure Tools y el SDK 2.9).

## SDK de Azure 2.9 para Visual Studio 2015 Update 2 y vista previa de Visual Studio "15"
 
Esta actualización incluye las correcciones de errores siguientes:

- Problema relacionado con la generación de clientes de la API de REST, en el que la cadena "Tipo desconocido" aparecía como nombre de la carpeta de generación de código o como nombre del espacio de nombres que se coloca en el código generado.
- Problema relacionado con WebJobs programados, en el que la información de autenticación no se transfería al proceso de aprovisionamiento del programador.

Esta actualización incluye la siguiente característica nueva:

- Compatibilidad con Servicios de aplicaciones secundarios en la pestaña "Servicios" del cuadro de diálogo de aprovisionamiento de Servicio de aplicaciones. 

##Azure Data Lake Tools para Visual Studio 2015 Update 2
 
Esta actualización incluye lo siguiente:

- **Azure Data Lake Tools** para Visual Studio se ha combinado en el SDK de Azure para .NET. La herramienta se instala automáticamente cuando se instala el SDK de Azure. 

	La herramienta se actualiza con frecuencia. Vaya [aquí](http://aka.ms/datalaketool) para obtener las actualizaciones.

- El **Explorador de servidores** ahora le permite verlo todo y crear algunas entidades de metadatos U-SQL. Para obtener más información, consulte [este blog]().


##Herramientas de HDInsight 

**Herramientas de HDInsight** para Visual Studio ahora admite HDInsight versión 3.3, incluida la visualización de gráficos Tez y otras correcciones de lenguaje.


##Administrador de recursos de Azure 

Esta versión agrega compatibilidad de [KeyVault](../resource-manager-keyvault-parameter.md) con plantillas de ARM.

##Consulte también

[Announcing the Visual Studio Azure Tools and SDK 2.9](https://azure.microsoft.com/blog/announcing-visual-studio-azure-tools-and-sdk-2-9/) (Presentación de Visual Studio Azure Tools y el SDK 2.9).

<!---HONumber=AcomDC_0330_2016-->