---
title: "Usar Visual Studio en una máquina virtual de Azure | Microsoft Docs"
description: "Usar Visual Studio en una máquina virtual de Azure."
services: virtual-machines-windows
documentationcenter: virtual-machines
author: PhilLee-MSFT
manager: sacalla
editor: tysonn
tags: azure-resource-manager
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.prod: vs-devops-alm
ms.date: 01/30/2018
ms.author: phillee
keywords: visualstudio
ms.openlocfilehash: 813022f1778e2c7f3174e11192b845c2c33ad219
ms.sourcegitcommit: 9d317dabf4a5cca13308c50a10349af0e72e1b7e
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 02/01/2018
---
# <a id="top"> </a> Imágenes de Visual Studio en Azure
Usar Visual Studio en una máquina virtual (VM) preconfigurada de Azure es la forma más fácil y rápida de crear un entorno de desarrollo que funcione correctamente desde cero.  En [Azure Marketplace](https://portal.azure.com/) encontrará varias imágenes del sistema con distintas configuraciones de Visual Studio. Solo tiene que iniciar una VM y comenzar a trabajar.

¿Acaba de llegar a Azure? [Creación de una cuenta de Azure gratis](https://azure.microsoft.com/free).

## <a name="what-configurations-and-versions-are-available"></a>¿Qué configuraciones y versiones están disponibles?
En Azure Marketplace encontrará imágenes de las versiones principales más recientes: Visual Studio 2017 y Visual Studio 2015.  Igualmente, en cada versión principal podrá ver la versión de lanzamiento original (también conocida como "RTW") y las "últimas" versiones actualizadas.  En cada una de estas versiones encontrará las ediciones Visual Studio Enterprise y Visual Studio Community.

|               Versión de lanzamiento              |          Ediciones            |    Versión del producto    |
|:------------------------------------------:|:----------------------------:|:---------------------:|
| Visual Studio 2017: la más reciente (versión 15.5) |    Enterprise, Community     |     Versión 15.5.3    |
|         Visual Studio 2017: RTW           |    Enterprise, Community     |     Versión 15.0.7    |
|   Visual Studio 2015: la más reciente (Update 3)   |    Enterprise, Community     | Versión 14.0.25431.01 |
|         Visual Studio 2015 - RTW           | Ninguna (servicio de mantenimiento expirado) |          ---          |

> [!NOTE]
> De acuerdo con la directiva de mantenimiento de Microsoft, el servicio de mantenimiento de la versión de lanzamiento original (también conocida como "RTW") de Visual Studio 2015 ya no está activo.  Por este motivo, Visual Studio 2015 Update 3 es la única versión que se ofrece en la línea de productos de Visual Studio 2015.

Para obtener más información, consulte la [Directiva de mantenimiento de Visual Studio](https://www.visualstudio.com/en-us/productinfo/vs-servicing-vs).

## <a name="what-features-are-installed"></a>¿Qué características están instaladas?
Cada imagen contiene el conjunto de características recomendado para esa edición de Visual Studio.  Por lo general, la instalación incluye:

* Todas las cargas de trabajo disponibles, incluidos los componentes opcionales recomendados para esas cargas de trabajo
* SDK de .NET 4.6.2 y .NET 4.7, paquetes de destino y herramientas de desarrollo
* Visual F#
* Extensión de GitHub para Visual Studio
* Herramientas de LINQ to SQL

Esta es la línea de comandos que se usa para instalar Visual Studio al compilar las imágenes:

   * vs_enterprise.exe --allWorkloads --includeRecommended --passive ^
   * add Microsoft.Net.Component.4.7.SDK ^
   * add Microsoft.Net.Component.4.7.TargetingPack ^ 
   * add Microsoft.Net.Component.4.6.2.SDK ^
   * add Microsoft.Net.Component.4.6.2.TargetingPack ^
   * add Microsoft.Net.ComponentGroup.4.7.DeveloperTools ^
   * add Microsoft.VisualStudio.Component.FSharp ^
   * add Component.GitHub.VisualStudio ^
   * add Microsoft.VisualStudio.Component.LinqToSql

Si las imágenes no incluyen la característica de Visual Studio que necesita, envíe un comentario mediante la herramienta para crear comentarios que se encuentra en la esquina superior derecha de la página.

## <a name="what-size-vm-should-i-choose"></a>¿Qué tamaño de VM debería elegir?
Es muy fácil aprovisionar una máquina virtual nueva y Azure le ofrece un amplio abanico de tamaños para ella.  Igual que sucede cuando compra cualquier tipo de hardware, lo que le interesa es conseguir una buena relación entre el rendimiento y el costo.  Dado que Visual Studio es una eficaz aplicación multiproceso, es recomendable que la VM tenga un tamaño que pueda incluir al menos dos procesadores y 7 GB de memoria.  Para obtener más información acerca de los tamaños más recientes de máquinas virtuales, consulte [Tamaños de las máquinas virtuales Windows en Azure](https://docs.microsoft.com/en-us/azure/virtual-machines/windows/sizes).

Además, gracias a Azure no estará limitado a su primera elección: puede volver a equilibrar la decisión inicial cambiando el tamaño de VM.  También puede aprovisionar una nueva VM con un tamaño más adecuado o puede cambiar el tamaño de la VM existente en otro hardware subyacente.  Para obtener más información, consulte [Cambio de tamaño de una máquina virtual Windows](https://docs.microsoft.com/en-us/azure/virtual-machines/windows/resize-vm).

## <a name="after-i-get-the-vm-running-then-what"></a>Ya he puesto en marcha la VM, ¿ahora qué?
Visual Studio sigue el modelo "traiga su propia licencia" en Azure.  Así pues, igual que sucede en la instalación de hardware propietario, uno de los primeros pasos es obtener una licencia para la instalación de Visual Studio.  Puede desbloquear Visual Studio iniciando sesión con una cuenta de Microsoft que esté asociada con una suscripción a Visual Studio, o mediante la clave de producto que se proporciona con la compra inicial.  Para obtener más información, consulte [Iniciar sesión en Visual Studio](https://docs.microsoft.com/en-us/visualstudio/ide/signing-in-to-visual-studio) y [Cómo desbloquear Visual Studio](https://docs.microsoft.com/en-us/visualstudio/ide/how-to-unlock-visual-studio).

## <a name="after-i-build-out-the-dev-vm-how-do-i-save-capture-it-for-future-or-team-use"></a>Después de compilar la VM de desarrollo, ¿cómo puedo guardarla (capturarla) para usarla más adelante o para que la use mi equipo?

Hay una amplia gama de entornos de desarrollo y, si quiere compilar uno de los entornos más complejos, esto le supondrá un costo significativo.  Sin embargo, con independencia de la configuración del entorno, Azure le permite conservar su inversión fácilmente; para ello, se guarda o captura la VM que ya esté configurada como "imagen base" para que usted o su equipo puedan usarla en un futuro.  A continuación, al arrancar la nueva VM, debe aprovisionarla desde la imagen base, en vez de usar la imagen de Marketplace.

Resumiendo: deberá usar la herramienta de preparación del sistema y apagar la VM que se esté ejecutando. A continuación, *capture (figura 1)* la VM como una imagen mediante la interfaz de usuario de Azure Portal.  Azure guardará el archivo `.vhd` que contiene la imagen en la cuenta de almacenamiento de su elección.  A continuación, la nueva imagen se muestra como un recurso de imagen en la lista de recursos de la suscripción.

<img src="media/using-visual-studio-vm/capture-vm.png" alt="Capture an image through the Azure portal’s UI" style="border:3px solid Silver; display: block; margin: auto;"><center>*(Figura 1) Capturar una imagen mediante la interfaz de usuario de Azure Portal.*</center>

Para obtener más información, consulte [Capturar una VM en una imagen](https://docs.microsoft.com/en-us/azure/virtual-machines/windows/capture-image-resource).

  **Aviso:** no olvide usar la herramienta de preparación del sistema para la VM.  Si se salta este paso, Azure no podrá aprovisionar la VM desde la imagen.

> [!NOTE]
> Recuerde que almacenar las imágenes le supondrá cierto costo de tipo incremental, pero este es insignificante en comparación con el costo de mano de obra que le supondría recompilar la VM desde cero (es decir, una VM para cada persona del equipo que necesite una).  Por ejemplo, puede crear y almacenar una imagen de 127 GB durante un mes para que la usen todos los miembros del equipo y solo le costará una pequeña cantidad de dinero.  Este costo es insignificante si lo comparamos con las horas que debe invertir cada empleado para compilar y validar un cuadro de desarrollo que esté configurado correctamente y que se pueda usar de forma individual.

Además, las tareas o tecnologías dedicadas al desarrollo necesitan más escalado, como las variedades referentes a la configuración de desarrollo y a la configuración de varias máquinas.  Puede usar Azure DevTest Labs para crear _recetas_ que automaticen la creación de la "imagen maestra" y que administren las directivas que usará el equipo al ejecutar VM.  Si quiere obtener más información acerca de DevTest Labs, consulte [Uso de Azure DevTest Labs para desarrolladores](https://docs.microsoft.com/en-us/azure/devtest-lab/devtest-lab-developer-lab).

## <a name="next-steps"></a>pasos siguientes
Ahora que ya conoce las imágenes preconfiguradas de Visual Studio, el siguiente paso es crear una nueva VM:

* [Cree una máquina virtual Windows en Azure Portal](quick-create-portal.md)
* [Información general sobre las máquinas virtuales Windows](overview.md)
