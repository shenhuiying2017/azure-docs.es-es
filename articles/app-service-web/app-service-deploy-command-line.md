---
title: "Automatización de la implementación de la aplicación de Azure con las herramientas de la línea de comandos | Microsoft Docs"
description: "Descubra información sobre cómo puede implementar la aplicación de Azure desde la línea de comandos."
services: app-service
documentationcenter: 
author: cephalin
manager: erikre
editor: 
ms.assetid: 8b65980c-eb75-44a2-8e0f-f9eb9e617d16
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/05/2017
ms.author: cephalin
ms.translationtype: HT
ms.sourcegitcommit: 12c20264b14a477643a4bbc1469a8d1c0941c6e6
ms.openlocfilehash: 2cc0c3a1afd42d1c04e8a220b66bcc6179d7ff33
ms.contentlocale: es-es
ms.lasthandoff: 09/07/2017

---
# <a name="automate-deployment-of-your-azure-app-with-command-line-tools"></a>Automatización de la implementación de la aplicación de Azure con las herramientas de la línea de comandos
Puede automatizar la implementación de las aplicaciones de Azure con las herramientas de línea de comandos. En este artículo se muestran algunas herramientas disponibles y vínculos útiles para que compruebe cómo se utilizan en el flujo de trabajo de implementación. 

## <a name="msbuild"></a>Automatización de la implementación con MSBuild
Si usa el [IDE de Visual Studio](#vs) para desarrollo, puede usar [MSBuild](http://msbuildbook.com/) para automatizar todo lo que puede hacer en el IDE. Puede configurar MSBuild para usar [Web Deploy](#webdeploy) o [FTP/FTPS](#ftp) para copiar archivos. Web Deploy también puede automatizar muchas otras tareas relacionadas con la implementación, como implementar bases de datos.

Para obtener más información acerca de la implementación de la línea de comandos con MSBuild, consulte los siguientes recursos:

* [Implementación web de ASP.NET con Visual Studio: Implementación de línea de comandos](http://www.asp.net/mvc/tutorials/deployment/visual-studio-web-deployment/command-line-deployment). El décimo tutorial de una serie sobre la implementación en Azure con Visual Studio. Muestra cómo usar la línea de comandos para la implementación después de haber configurado perfiles de publicación en Visual Studio.
* [Dentro de Microsoft Build Engine: Uso de MSBuild y Team Foundation Build](http://msbuildbook.com/). Copia impresa de un libro que incluye capítulos sobre cómo usar MSBuild para la implementación.

## <a name="powershell"></a>Automatización de la implementación con Windows PowerShell
Desde [Windows PowerShell](http://msdn.microsoft.com/library/dd835506.aspx)puede ejecutar funciones de implementación MSBuild o FTP. Si lo hace, también puede usar una recopilación de cmdlets de Windows PowerShell que hacen que sea fácil llamar a la API REST de administración de Azure.

Para obtener más información, consulte los siguientes recursos:

* [Aprovisionamiento e implementación predecibles de microservicios en Azure](app-service-deploy-complex-application-predictably.md)
* <seg>
  [Creación de aplicaciones reales en la nube con Azure: automatizar todo](http://asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/automate-everything).</seg> Capítulo de un libro electrónico que explica cómo la aplicación de ejemplo que aparece en el libro electrónico usa scripts de Windows PowerShell para crear un entorno de prueba de Azure e implementar en él. Consulte la sección [Recursos](http://asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/automate-everything#resources) para ver vínculos a documentación adicional de Azure PowerShell.
* [Uso de scripts de Windows PowerShell para publicar en entornos de prueba y desarrollo](../vs-azure-tools-publishing-using-powershell-scripts.md). Cómo usar scripts de implementación de Windows PowerShell que genera Visual Studio.

## <a name="api"></a>Automatización de la implementación con la API de administración de .NET
Puede escribir código de C# para realizar funciones de MSBuild o FTP para implementación. Si lo hace, puede tener acceso a la API REST de administración de Azure para realizar funciones de administración de sitios.

Para obtener más información, consulte el siguiente recurso:

* [Automatización de todo con las bibliotecas de administración de Azure y .NET](http://www.hanselman.com/blog/PennyPinchingInTheCloudAutomatingEverythingWithTheWindowsAzureManagementLibrariesAndNET.aspx). Introducción a la API de administración .NET y vínculos a más documentación.

## <a name="cli"></a>Implementación desde la interfaz de la línea de comandos de Azure (CLI de Azure)
Puede usar la línea de comandos en máquinas Windows, Mac o Linux para implementar mediante FTP. Si lo hace, también puede tener acceso a la API de administración REST de Azure con la CLI de Azure.

Para obtener más información, consulte el siguiente recurso:

* [Herramientas de línea de comandos de Azure](https://azure.microsoft.com/downloads/). Página de portal en Azure.com para obtener información sobre la herramienta de línea de comandos.

## <a name="webdeploy"></a>Implementación desde la línea de comandos de Web Deploy
[Web Deploy](http://www.iis.net/downloads/microsoft/web-deploy) es el software de Microsoft para la implementación en IIS que no solo ofrece características inteligentes de sincronización de archivos, sino que también puede realizar o coordinar muchas otras tareas relacionadas con la implementación que no se pueden automatizar cuando usa FTP. Por ejemplo, Web Deploy puede implementar una base de datos nueva o actualizaciones de base de datos junto con su aplicación web. Web Deploy también puede minimizar el tiempo que se requiere para actualizar un sitio existente, dado que puede copiar de manera inteligente solo los archivos modificados. Microsoft Visual Studio y Team Foundation Server cuentan con compatibilidad integrada para Web Deploy, pero solo puede usar Web Deploy directamente desde la línea de comandos para automatizar la implementación. Los comandos de Web Deploy son muy poderosos, pero la curva de aprendizaje puede ser pronunciada.

## <a name="more-resources"></a>Más recursos
Otra opción de implementación para la automatización de la línea de comandos es usar un servicio basado en la nube como [Octopus Deploy](http://en.wikipedia.org/wiki/Octopus_Deploy). Para obtener más información, consulte [Deploy ASP.NET applications to Azure Web Sites](https://octopusdeploy.com/blog/deploy-aspnet-applications-to-azure-websites)(Implementación de aplicaciones ASP.NET en Sitios web de Azure).

Para obtener más información sobre herramientas de línea de comandos, vea el siguiente recurso:

* [Aplicaciones web simples: implementación](https://azure.microsoft.com/blog/2014/07/28/simple-azure-websites-deployment/). Blog de David Ebbo sobre una herramienta que escribió para facilitar el uso de Web Deploy.
* [Herramienta de implementación web](http://technet.microsoft.com/library/dd568996). Documentación oficial sobre el sitio de Microsoft TechNet. Información antigua, pero sigue siendo un buen lugar para comenzar.
* [Uso de Web Deploy](http://www.iis.net/learn/publish/using-web-deploy). Documentación oficial sobre el sitio de Microsoft IIS.NET. También es información antigua, pero es un buen lugar para comenzar.
* [Implementación web de ASP.NET con Visual Studio: Implementación de línea de comandos](http://www.asp.net/mvc/tutorials/deployment/visual-studio-web-deployment/command-line-deployment). MSBuild es el motor de compilación que emplea Visual Studio y que también se puede usar desde la línea de comandos para implementar aplicaciones web en Web Apps. Este tutorial forma parte de una serie que se refiere principalmente a la implementación de Visual Studio.


