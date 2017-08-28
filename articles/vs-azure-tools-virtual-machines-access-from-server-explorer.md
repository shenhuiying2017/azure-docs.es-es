---
title: Acceso a Azure Virtual Machines desde el Explorador de servidores | Microsoft Docs
description: "Obtenga una visión general de cómo ver, crear y administrar Azure los máquinas virtuales (VM) en el Explorador de servidores de Visual Studio."
services: visual-studio-online
documentationcenter: na
author: kraigb
manager: ghogen
editor: 
ms.assetid: eb3afde6-ba90-4308-9ac1-3cc29da4ede0
ms.service: multiple
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: multiple
ms.date: 11/18/2016
ms.author: kraigb
ms.translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: 009b325054737b63d5bdaefbe005d6a613126f52
ms.contentlocale: es-es
ms.lasthandoff: 11/17/2016

---
# <a name="accessing-azure-virtual-machines-from-server-explorer"></a>Tener acceso a Máquinas virtuales de Azure desde el Explorador de servidores
Con el Explorador de servidores de Visual Studio puede mostrar información acerca de las máquinas virtuales hospedadas por Azure.

## <a name="accessing-virtual-machines-in-server-explorer"></a>Acceso a máquinas virtuales en el Explorador de servidores
Si tiene máquinas virtuales hospedadas por Azure, puede acceder a ellas en el Explorador de servidores. Primero debe iniciar una sesión en su suscripción de Azure para ver los servicios móviles. Para iniciar sesión, abra el menú contextual del nodo de Azure en el Explorador de servidores y elija **Conectar a Microsoft Azure**.

### <a name="to-get-information-about-your-virtual-machines"></a>Para obtener información acerca de las máquinas virtuales
1. En el Explorador de servidores, elija una máquina virtual y, a continuación, presione la tecla F4 para mostrar su ventana de propiedades.
   
    La siguiente tabla muestra las propiedades que están disponibles, pero son todas de solo lectura. Utilizar el [Portal de Azure clásico](http://go.microsoft.com/fwlink/?LinkID=213885)para cambiarlas.
   
   | Propiedad | Description |
   | --- | --- |
   | Nombre DNS |La dirección URL con la dirección de Internet de la máquina virtual. |
   | Environment |En el caso de una máquina virtual, el valor de esta propiedad siempre es Production. |
   | Nombre |El nombre de la máquina virtual. |
   | Tamaño |El tamaño de la máquina virtual, que refleja la cantidad de memoria y espacio en disco disponibles. Para obtener más información, consulte Procedimiento: creación de los tamaños de las máquinas virtuales. |
   | Estado |Los valores incluyen: Iniciando, Iniciado, Deteniéndose, Detenido y Recuperando estado. Si aparece Recuperando estado, el estado actual es desconocido. Los valores para esta propiedad son distintos de los que se usan en el [Portal de Azure clásico](http://go.microsoft.com/fwlink/?LinkID=213885). |
   | SubscriptionID |El Id. de suscripción de la cuenta de Azure. Esta información se puede mostrar en el [Portal de Azure clásico](http://go.microsoft.com/fwlink/?LinkID=213885) mediante la visualización de las propiedades de una suscripción. |
2. Seleccione un nodo de extremo y, a continuación, vea la ventana **Propiedades** .
3. La tabla siguiente describe las propiedades disponibles de los extremos, pero son de solo lectura. Para agregar o editar los puntos de conexión de una máquina virtual, use el [Portal de Azure clásico](http://go.microsoft.com/fwlink/?LinkID=213885). 
   
   | Propiedad | Descripción |
   | --- | --- |
   | Nombre |Un identificador para el extremo. |
   | Private Port |El puerto del acceso de red interno de la aplicación. |
   | Protocol |El protocolo que usa la capa de transporte para este extremo, TCP o UDP. |
   | Public Port |El puerto que se usa para el acceso público a la aplicación. |

## <a name="next-steps"></a>Pasos siguientes
Para obtener más información sobre los roles de Azure en Visual Studio, consulte [Uso de Escritorio de remoto con los roles de Azure](vs-azure-tools-remote-desktop-roles.md).


