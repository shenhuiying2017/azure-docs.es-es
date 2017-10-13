---
title: Uso de ReportViewer en un sitio web | Microsoft Docs
description: "En este tema se describe cómo crear un sitio web de Microsoft Azure con el control ReportViewer de Visual Studio que muestre un informe guardado en una máquina virtual de Microsoft Azure."
services: virtual-machines-windows
documentationcenter: na
author: guyinacube
manager: erikre
editor: monicar
tags: azure-service-management
ms.assetid: 78b76318-d9bf-48ef-9d9e-d1b7d8cf3042
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 01/11/2017
ms.author: asaxton
ms.openlocfilehash: c4f7c829e6fe3890342bd973185e679dd3ea2df5
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="use-reportviewer-in-a-web-site-hosted-in-azure"></a>Usar ReportViewer en un sitio web hospedado en Azure
> [!IMPORTANT] 
> Azure tiene dos modelos de implementación diferentes para crear recursos y trabajar con ellos: [Resource Manager y el clásico](../../../azure-resource-manager/resource-manager-deployment-model.md). En este artículo se trata el modelo de implementación clásico. Microsoft recomienda que las implementaciones más recientes usen el modelo del Administrador de recursos.

Puede crear un sitio web de Microsoft Azure con el control ReportViewer de Visual Studio que muestre un informe guardado en una máquina virtual de Microsoft Azure. El control ReportViewer se encuentra en una aplicación web que se crea con la plantilla de aplicación web ASP.NET.

> [!IMPORTANT]
> Las plantillas de aplicación web de MVC de ASP.NET no admiten el control ReportViewer.

Para incorporar ReportViewer en su sitio web de Microsoft Azure, debe completar las siguientes tareas.

* **Agregar** ensamblados al paquete de implementación
* **Configurar** autenticación y autorización
* **Publicar** la aplicación web ASP.NET en Azure

## <a name="prerequisites"></a>Requisitos previos
Revise la sección "Recomendación general y procedimientos recomendados" de [SQL Server Business Intelligence en máquinas virtuales de Azure](../classic/ps-sql-bi.md).

> [!NOTE]
> Los controles ReportViewer se incluyen con Visual Studio, Standard Edition o superior. Si usa Web Developer Express Edition, debe instalar el [MICROSOFT REPORT VIEWER 2012 RUNTIME](https://www.microsoft.com/download/details.aspx?id=35747) para usar las características de tiempo de ejecución de ReportViewer.
> 
> No se admite el ReportViewer configurado en modo de procesamiento local en Microsoft Azure.

## <a name="adding-assemblies-to-the-deployment-package"></a>Adición de ensamblados al paquete de implementación
Cuando la aplicación ASP.NET se hospeda localmente, los ensamblados de ReportViewer normalmente se instalan directamente en la caché global de ensamblados (GAC) del servidor IIS durante la instalación de Visual Studio y la aplicación puede obtener acceso a ellos directamente. Sin embargo, cuando hospeda su aplicación ASP.NET en la nube, Microsoft Azure no permite que se instale nada en la GAC, por lo que debe asegurarse de que los ensamblados de ReportViewer están disponibles localmente para la aplicación. Puede ello, agregue referencias a los ensamblados en el proyecto y configúrelos para que se copien localmente.

En el modo de procesamiento remoto, el control ReportViewer usa los siguientes ensamblados:

* **Microsoft.ReportViewer.WebForms.dll**: contiene el código de ReportViewer, que se necesita para usar el ReportViewer en la página. Al colocar un control ReportViewer en una página ASP.NET del proyecto, se agrega una referencia para este ensamblado al proyecto.
* **Microsoft.ReportViewer.Common.dll**: contiene las clases usadas por el control ReportViewer en tiempo de ejecución. No se agrega automáticamente al proyecto.

### <a name="to-add-a-reference-to-microsoftreportviewercommon"></a>Para agregar una referencia a Microsoft.ReportViewer.Common
* Haga clic con el botón derecho en el nodo **Referencias** del proyecto y seleccione **Agregar referencia**, el ensamblado en la pestaña de .NET y haga clic en **Aceptar**.

### <a name="to-make-the-assemblies-locally-accessible-by-your-aspnet-application"></a>Para hacer que los ensamblados estén localmente disponibles para su aplicación ASP.NET
1. En la carpeta **Referencias** , haga clic en el ensamblado Microsoft.ReportViewer.Common, para que sus propiedades aparezcan en el panel Propiedades.
2. En el panel Propiedades, establezca **Copia local** en verdadero.
3. Repita los pasos 1 y 2 para Microsoft.ReportViewer.WebForms.

### <a name="to-get-reportviewer-language-pack"></a>Para obtener el paquete de idioma de ReportViewer
1. Instale el paquete redistribuible de Microsoft Report Viewer 2012 Runtime adecuado desde el [Centro de descarga de Microsoft](http://go.microsoft.com/fwlink/?LinkId=317386).
2. Seleccione el idioma en la lista desplegable y la página le dirigirá a la página del centro de descarga correspondiente.
3. Haga clic en **Descargar** para iniciar la descarga de ReportViewerLP.exe.
4. Después de descargar ReportViewerLP.exe, haga clic en **Ejecutar** para instalar inmediatamente o haga clic en **Guardar** para guardarlo en el equipo. Si hace clic en **Guardar**, recuerde el nombre de la carpeta donde se guarda el archivo.
5. Encuentre la carpeta donde guardó el archivo. Haga clic con el botón secundario en ReportViewerLP.exe, haga clic en **Ejecutar como administrador**, y luego en **Sí**.
6. Después de ejecutar ReportViewerLP.exe, verá que en la ruta c:\windows\assembly se encuentran los archivos de recursos **Microsoft.ReportViewer.Webforms.Resources** y **Microsoft.ReportViewer.Common.Resources**.

### <a name="to-configure-for-localized-reportviewer-control"></a>Para configurar para el control ReportViewer localizado
1. Descargue e instale el paquete redistribuible de Microsoft Report Viewer 2012 Runtime siguiendo las instrucciones especificadas anteriormente.
2. Cree la carpeta <language> en el proyecto y copie allí los archivos de ensamblados de recursos asociados. Los archivos de ensamblados de recursos que se va a copiar son: **Microsoft.ReportViewer.Webforms.Resources.dll** y **Microsoft.ReportViewer.Common.Resources.dll**. Seleccione los archivos de ensamblados de recursos y, en el panel Propiedades, establezca **Copiar en el directorio de resultados** en "**Copiar siempre**".
3. Establezca la referencia cultural y la referencia cultural de la interfaz de usuario para el proyecto web. Para obtener más información sobre cómo establecer la referencia cultural y la referencia cultural de la interfaz de usuario para una página web ASP.NET, vea [Cómo establecer la referencia cultural y la referencia cultural de la interfaz de usuario para la globalización de páginas web de ASP.NET](http://go.microsoft.com/fwlink/?LinkId=237461).

## <a name="configuring-authentication-and-authorization"></a>Configuración de autenticación y autorización
El control ReportViewer debe usar las credenciales adecuadas para autenticarse con el servidor de informes y las credenciales deben estar autorizadas por el servidor de informes para obtener acceso a los informes que quiere. Para obtener información sobre la autenticación, vea las notas del producto [Control Visor de informes de Reporting Services y servidores de informes basados en máquinas virtuales de Microsoft Azure](https://msdn.microsoft.com/library/azure/dn753698.aspx).

## <a name="publish-the-aspnet-web-application-to-azure"></a>Publicar la aplicación web ASP.NET en Azure
Para obtener instrucciones sobre cómo publicar una aplicación web ASP.NET en Azure, consulte [Cómo migrar y publicar una aplicación web en Azure desde Visual Studio](../../../vs-azure-tools-migrate-publish-web-app-to-cloud-service.md) e [Introducción a aplicaciones web y ASP.NET](../../../app-service/app-service-web-get-started-dotnet.md).

> [!IMPORTANT]
> Si el comando Agregar proyecto de implementación de Azure o Agregar  proyecto de servicio de nube de Azure no aparece en el menú contextual del Explorador de soluciones, puede que necesite cambiar el marco de trabajo de destino del proyecto a .NET Framework 4.
> 
> Los dos comandos ofrecen esencialmente la misma funcionalidad. Uno u otro comando aparecerán en el menú contextual en función de qué versión del SDK de Microsoft Azure haya instalado.
> 
> 

## <a name="resources"></a>Recursos
[Informes de Microsoft](http://go.microsoft.com/fwlink/?LinkId=205399)

[Business Intelligence de SQL Server en Máquinas virtuales de Azure](../classic/ps-sql-bi.md)

[Usar PowerShell para crear una máquina virtual de Azure con un servidor de informes en modo nativo](../classic/ps-sql-report.md)

