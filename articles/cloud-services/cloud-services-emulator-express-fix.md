---
title: "Configuración de Emulator Express para depurar aplicaciones de Cloud Services en Visual Studio | Microsoft Docs"
description: "Explica cómo instalar el paquete redistribuible de C++ para habilitar Emulator Express en Visual Studio"
services: cloud-services
documentationcenter: 
author: cawa
manager: paulyuk
editor: 
ms.assetid: 22b20f7a-23f4-4f7f-b536-3bf1e01adcd1
ms.service: cloud-services
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 11/02/2016
ms.author: cawa
translationtype: Human Translation
ms.sourcegitcommit: 165ab7363efaf90eaab41098f71e2f1b846c346e
ms.openlocfilehash: 05d672dcb1335c617bb8d8cae43947bcd5e9ab3d

---
# <a name="use-emulator-express-to-debug-cloud-services-application-in-vs-2017"></a>Uso de Emulator Express para depurar una aplicación de Cloud Services en VS 2017
Este artículo explica cómo usar Emulator Express para depurar aplicaciones de Cloud Services en VS 2017.

## <a name="background-context"></a>Información contextual
Emulator Express se utiliza de forma predeterminada para la depuración de roles web y de trabajo de Cloud Services en Visual Studio. Esta configuración se especifica en la página de propiedades del proyecto de Cloud Services.

![Apertura de las propiedades del proyecto][0]

![Emulator Express se selecciona como predeterminado][1]

Emulator Express necesita el paquete [Paquete redistribuible de C++][Paquete redistribuible de C++] para Visual Studio. Actualmente no está instalado con la carga de trabajo de Azure. Al presionar F5 para depurar las aplicaciones de Cloud Services, Visual Studio preguntará por la instalación de este componente y continuará con la depuración.

![Pregunta para instalar el paquete redistribuible de C++][2]

Haga click en Sí para instalar el paquete redistribuible de C++.

![Instalación del paquete redistribuible de C++][3]

Presione F5 de nuevo para iniciar sesiones de depuración.

![Iniciar la depuración][4]

![Depuración correcta][5]

> Nota: La instalación del paquete redistribuible de Visual C++ se realiza una sola vez. Si se actualiza desde una versión anterior del SDK de Azure y ya tiene instalado Emulator Express, no se encontrará con este problema.
> 
> 

## <a name="manual-workaround"></a>Solución alternativa manual
También puede instalar el [Paquete redistribuible de C++][Paquete redistribuible de C++] manualmente y se aplicará el mismo efecto que cuando lo instaló Visual Studio en el sistema.

[vcredist_x86.exe][vcredist_x86.exe]

[vcredist_x64.exe][vcredist_x64.exe]

## <a name="next-steps"></a>Pasos siguientes
Más información sobre cómo usar Azure Computer Emulator para depurar las aplicaciones de Cloud Services en Visual Studio: [Uso de Emulator Express para ejecutar y depurar un servicio en la nube en un sistema local][Uso de Emulator Express para ejecutar y depurar un servicio en la nube en un sistema local]

[Paquete redistribuible de C++]:https://www.microsoft.com/en-us/download/details.aspx?id=30679
[vcredist_x86.exe]:https://download.microsoft.com/download/1/6/B/16B06F60-3B20-4FF2-B699-5E9B7962F9AE/VSU_4/vcredist_x86.exe
[vcredist_x64.exe]:https://download.microsoft.com/download/1/6/B/16B06F60-3B20-4FF2-B699-5E9B7962F9AE/VSU_4/vcredist_x64.exe
[Uso de Emulator Express para ejecutar y depurar un servicio en la nube en un sistema local]:https://azure.microsoft.com/en-us/documentation/articles/vs-azure-tools-emulator-express-debug-run/

[0]: ./media/cloud-services-emulator-express-fix/vs-05.png
[1]: ./media/cloud-services-emulator-express-fix/vs-06.png
[2]: ./media/cloud-services-emulator-express-fix/vs-01.png
[3]: ./media/cloud-services-emulator-express-fix/vs-02.png
[4]: ./media/cloud-services-emulator-express-fix/vs-03.png
[5]: ./media/cloud-services-emulator-express-fix/vs-04.png



<!--HONumber=Nov16_HO3-->


