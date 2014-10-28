<properties linkid="dotnet-visual-studio-2014" urlDisplayName="Visual Studio 14 CTP2" pageTitle="Installing the Azure SDK 2.4 for Visual Studio 14 CTP2" metaKeywords="Visual Studio, Azure SDK" description="Install Azure SDK 2.4 and Visual Studio 14 CTP2" metaCanonical="" services="" documentationCenter="" title="Installing Azure SDK 2.4 for Visual Studio 14 CTP2" authors="ghogen" solutions="" manager="" editor="" />

<tags ms.service="multiple" ms.workload="multiple" ms.tgt_pltfrm="na" ms.devlang="dotnet" ms.topic="article" ms.date="01/01/1900" ms.author="ghogen"></tags>

## Instalación de Azure SDK 2.4 para CTP de Visual Studio "14"

Para instalar Azure SDK 2.4 para .NET con CTP de Visual Studio "14", siga estos pasos. Este procedimiento instala SDK, herramientas básicas y herramientas extendidas para el desarrollo de Azure con CTP de Visual Studio "14" y no debe usarse con ninguna otra versión de Visual Studio.

**Nota**: Azure SDK 2.4 no es compatible con CPT1 de Visual Studio "14".

Para instalar Azure SDK 2.4 para .NET, siga estos pasos:

1.  Instale el último [CTP de Visual Studio "14"][CTP de Visual Studio "14"].

2.  Instale cada uno de los componentes del SDK de Azure mediante los vínculos de la siguiente lista, en este orden. Elija la versión x86 o x64 de cada uno de los siguientes componentes.

    -   Herramientas de creación de Azure: [WindowsAzureAuthoringTools-x86.msi][WindowsAzureAuthoringTools-x86.msi] o [WindowsAzureAuthoringTools-x64.msi][WindowsAzureAuthoringTools-x64.msi].
    -   Emulador de equipos de Azure: [WindowsAzureEmulator-x86.exe][WindowsAzureEmulator-x86.exe] o [WindowsAzureEmulator-x64.exe][WindowsAzureEmulator-x64.exe].
    -   Las bibliotecas de cliente de Azure: [WindowsAzureLibsForNet-x86.msi][WindowsAzureLibsForNet-x86.msi] o [WindowsAzureLibsForNet-x64.msi][WindowsAzureLibsForNet-x64.msi].
    -   El emulador de almacenamiento: [WindowsAzureStorageEmulator.msi][WindowsAzureStorageEmulator.msi]. Si recibe una advertencia sobre las bases de datos locales SQL, instale SQL Server LocalDB 11.0 desde [esta ubicación][esta ubicación] para x86 o desde [esta ubicación][1] para x64.
    -   Herramientas de Azure para Visual Studio: [WindowsAzureTools.vs140.exe][WindowsAzureTools.vs140.exe].

    </p>

## Problemas conocidos

1.  Si instala CPT2 de Visual Studio "14" en una máquina que tiene instalado Visual Studio 2013, no podrá iniciar servicios móviles en CTP2 de Visual Studio "14". Para solucionar este problema, agregue una referencia a los siguientes ensamblados en el proyecto de servicios móviles:

    -   packages/Microsoft.Data.OData.5.6.0/lib/net40/Microsoft.Data.OData.dll
    -   packages/Microsoft.Data.Edm.5.6.0/lib/net40/Microsoft.Data.Edm.dll

2.  La depuración remota para Sitios web y Servicios móviles de Azure no funciona en CTP2 de Visual Studio "14".

## Notas de la versión

Lea las [notas de la versión][notas de la versión] para Azure SDK 2.4.

  [CTP de Visual Studio "14"]: http://go.microsoft.com/fwlink/p/?LinkId=400776
  [WindowsAzureAuthoringTools-x86.msi]: http://go.microsoft.com/fwlink/p/?LinkId=400892
  [WindowsAzureAuthoringTools-x64.msi]: http://go.microsoft.com/fwlink/p/?LinkId=400893
  [WindowsAzureEmulator-x86.exe]: http://go.microsoft.com/fwlink/p/?LinkId=400894
  [WindowsAzureEmulator-x64.exe]: http://go.microsoft.com/fwlink/p/?LinkId=400895
  [WindowsAzureLibsForNet-x86.msi]: http://go.microsoft.com/fwlink/p/?LinkId=400896
  [WindowsAzureLibsForNet-x64.msi]: http://go.microsoft.com/fwlink/p/?LinkId=400897
  [WindowsAzureStorageEmulator.msi]: http://go.microsoft.com/fwlink/p/?LinkId=400904
  [esta ubicación]: http://go.microsoft.com/fwlink/p/?LinkId=400778
  [1]: http://go.microsoft.com/fwlink/p/?LinkId=400779
  [WindowsAzureTools.vs140.exe]: http://go.microsoft.com/fwlink/p/?LinkId=400903
  [notas de la versión]: http://go.microsoft.com/fwlink/?LinkId=507517
