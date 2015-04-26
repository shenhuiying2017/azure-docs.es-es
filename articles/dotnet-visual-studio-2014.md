<properties 
	pageTitle="Instalación de Azure SDK 2.4 para CTP2 de Visual Studio 14" 
	description="Instalar Azure SDK 2.4 y Visual Studio 14 CTP2" 
	services="visual-studio-online" 
	documentationCenter=".net" 
	authors="kempb" 
	manager="douge" 
	editor="tglee"/>

<tags 
	ms.service="visual-studio-online" 
	ms.workload="multiple" 
	ms.tgt_pltfrm="na" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="02/02/2015" 
	ms.author="kempb"/>

# Instalación de Azure SDK 2.4 para CTP de Visual Studio "14"

Para instalar Azure SDK 2.4 para .NET con CTP de Visual Studio "14", siga estos pasos. Este procedimiento instala SDK, herramientas básicas y herramientas extendidas para el desarrollo de Azure con CTP de Visual Studio "14" y no debe usarse con ninguna otra versión de Visual Studio.

**Nota**: Azure SDK 2.4 no es compatible con CPT1 de Visual Studio "14".

Para instalar Azure SDK 2.4 para .NET, siga estos pasos:

1. Instale el [CTP de Visual Studio "14"](http://go.microsoft.com/fwlink/p/?LinkId=400776) más reciente.

2. Instale cada uno de los componentes del SDK de Azure mediante los vínculos de la siguiente lista, en este orden. Elija la versión x86 o x64 de cada uno de los siguientes componentes.

       <ul>
        <li>Herramientas de creación de Azure: <a href="http://go.microsoft.com/fwlink/p/?LinkId=400892">MicrosoftAzureAuthoringTools-x86.msi</a> o <a href="http://go.microsoft.com/fwlink/p/?LinkId=400893">MicrosoftAzureAuthoringTools-x64.msi</a>.</li>
       <li>Emulador de proceso de Azure: <a href="http://go.microsoft.com/fwlink/p/?LinkId=400894">MicrosoftAzureEmulator-x86.exe</a> o <a href="http://go.microsoft.com/fwlink/p/?LinkId=400895">MicrosoftAzureEmulator-x64.exe</a>.</li>
       <li>Las bibliotecas de cliente de Azure: <a href="http://go.microsoft.com/fwlink/p/?LinkId=400896">MicrosoftAzureLibsForNet-x86.msi</a> o <a href="http://go.microsoft.com/fwlink/p/?LinkId=400897">MicrosoftAzureLibsForNet-x64.msi</a>.</li>
       <li>El emulador de almacenamiento: <a href="http://go.microsoft.com/fwlink/p/?LinkId=400904">MicrosoftAzureStorageEmulator.msi</a>.                            Si recibe una advertencia sobre las bases de datos locales SQL, instale SQL Server LocalDB 11.0 desde <a href="http://go.microsoft.com/fwlink/p/?LinkId=400778">esta ubicación</a> para x86 o desde <a href="http://go.microsoft.com/fwlink/p/?LinkId=400779">esta ubicación</a> para x64.</li><li> Herramientas de Azure para Visual Studio: <a href="http://go.microsoft.com/fwlink/p/?LinkId=400903">WindowsAzureTools.vs140.exe</a>.</li></ul>

## Problemas conocidos

1. Si instala CPT2 de Visual Studio "14" en una máquina que tiene instalado Visual Studio 2013, no podrá iniciar servicios móviles en CTP2 de Visual Studio "14". Para solucionar este problema, agregue una  referencia a los siguientes ensamblados en el proyecto de servicios móviles:

	* packages/Microsoft.Data.OData.5.6.0/lib/net40/Microsoft.Data.OData.dll
	* packages/Microsoft.Data.Edm.5.6.0/lib/net40/Microsoft.Data.Edm.dll

2. La depuración remota para Sitios web y Servicios móviles de Azure no funciona en CTP2 de Visual Studio "14".

## Notas de la versión

Lea las [notas de la versión](http://go.microsoft.com/fwlink/?LinkId=507517) para Azure SDK 2.4.

<!--HONumber=46--> 

<!--HONumber=46--> 
