---
title: "Managing Virtual Machines using the Azure Explorer for Eclipse (Administración de Virtual Machines mediante Azure Explorer para Eclipse) | Microsoft Docs"
description: "Obtenga información sobre cómo administrar máquinas virtuales de Azure mediante Azure Explorer para Eclipse."
services: 
documentationcenter: java
author: rmcmurray
manager: erikre
editor: 
ms.assetid: 
ms.service: multiple
ms.workload: na
ms.tgt_pltfrm: multiple
ms.devlang: Java
ms.topic: article
ms.date: 04/14/2017
ms.author: robmcm
translationtype: Human Translation
ms.sourcegitcommit: 9eafbc2ffc3319cbca9d8933235f87964a98f588
ms.openlocfilehash: f8631c5fe566762862f64b47843e154b68bb8031
ms.lasthandoff: 04/22/2017


---

# <a name="managing-virtual-machines-using-the-azure-explorer-for-eclipse"></a>Administración de Virtual Machines mediante Azure Explorer para Eclipse

Azure Explorer, que forma parte del kit de herramientas de Azure para Eclipse, proporciona a los desarrolladores de Java una solución fácil de usar para la administración de máquinas virtuales en su cuenta de Azure desde el IDE de Eclipse.

[!INCLUDE [azure-toolkit-for-eclipse-prerequisites](../includes/azure-toolkit-for-eclipse-prerequisites.md)]

[!INCLUDE [azure-toolkit-for-eclipse-show-azure-explorer](../includes/azure-toolkit-for-eclipse-show-azure-explorer.md)]

## <a name="creating-a-virtual-machine-in-eclipse"></a>Creación de una máquina virtual en Eclipse

Los siguientes pasos le guían en la creación de una cuenta de almacenamiento mediante Azure Explorer.

1. Inicie sesión en su cuenta de Azure siguiendo los pasos del artículo [Sign In Instructions for the Azure Toolkit for Eclipse] (Instrucciones de inicio de sesión del kit de herramientas de Azure para Eclipse).

1. En la vista **Azure Explorer**, expanda el nodo **Azure**, haga clic con el botón derecho en **Virtual Machines** y, a continuación, haga clic en **Crear máquina virtual**.
   ![Menú Crear VM][CR01]

1. Cuando aparezca el asistente para **Crear nueva máquina virtual**, elija su suscripción y luego haga clic en **Siguiente**.
   ![Asistente para Crear nueva máquina virtual][CR02]

1. En la siguiente pantalla del asistente, especifique las opciones que se muestran a continuación y, después, haga clic en **Siguiente**:  ![Asistente para Crear nueva máquina virtual][CR03]

   a. **Ubicación**: especifica la ubicación donde se creará la máquina virtual, por ejemplo "westus".

   b. **Publicador**: especifica el publicador que creó la imagen que se usará para crear la máquina virtual; por ejemplo, "Microsoft".

   c. **Oferta**: especifica la oferta de máquina virtual que quiere usar del publicador seleccionado; por ejemplo, "JDK".

   d. **SKU**: especifica la *referencia de almacén (SKU)* que quiere usar de la oferta seleccionada; por ejemplo, "JDK_8".

   e. **N.º de versión**: especifica la versión que quiere usar de la SKU seleccionada.

1. En la siguiente pantalla del asistente, especifique las opciones que se muestran a continuación y, después, haga clic en **Siguiente**: ![Asistente para Crear nueva máquina virtual][CR04]

   a. **Nombre de máquina virtual**: especifica el nombre de la nueva máquina virtual, que debe comenzar con una letra y contener solo letras, números y guiones.

   b. **Tamaño**: especifica el número de núcleos y la memoria que se asignará para la máquina virtual.

   c. **Nombre de usuario**: especifica la cuenta de administrador que se creará para administrar la máquina virtual.

   d. **Contraseña** y **Confirmar**: especifica la contraseña de la cuenta de administrador.

1. Cuando se encuentre en la última pantalla del asistente, especifique las siguientes opciones:

   a. **Grupo de recursos**: especifica el grupo de recursos para la máquina virtual; debe elegir una de las siguientes opciones:
      * **Crear nuevo**: especifica que quiere crear un nuevo grupo de recursos.
      * **Usar existente**: especifica que se va a elegir de una lista de grupos de recursos asociados a la cuenta de Azure.

   b. **Cuenta de almacenamiento**: especifica la cuenta de almacenamiento que se usará para almacenar la máquina virtual; puede elegir una cuenta de almacenamiento existente o crear una nueva cuenta. Si elige **&lt;&lt;Crear nuevo&gt;&gt;**, se mostrará el cuadro de diálogo siguiente:

      ![Cuadro de diálogo Crear nueva cuenta de almacenamiento][CR05]

   c. **Red virtual** y **subred**: especifica la red virtual y la subred a las que se conectará la máquina virtual; puede elegir una red y una subred existentes a fin de usarlas para la máquina virtual, o bien crear una red y una subred nuevas. Si elige **&lt;&lt;Crear nuevo&gt;&gt;**, se mostrará el cuadro de diálogo siguiente:<br/>

      ![Cuadro de diálogo Crear una nueva red virtual][CR06]

   d. **Dirección IP pública**: especifica una dirección IP orientada hacia el exterior para la máquina virtual; puede decidir crear una nueva dirección IP o elegir **(Ninguno)** si la máquina virtual va a carecer de dirección IP pública.

   e. **Grupo de seguridad de red**: especifica un firewall de redes opcional que usará la máquina virtual; puede elegir un firewall existente o **(Ninguno)** si la máquina virtual no va a utilizar un firewall de red.

   f. **Conjunto de disponibilidad**: especifica un conjunto de disponibilidad opcional al que puede pertenecer la máquina virtual; puede elegir un conjunto de disponibilidad existente, crear uno nuevo, o bien seleccionar **(Ninguno)** si la máquina virtual no va a pertenecer a ningún conjunto de disponibilidad.

1. Una vez que haya escrito todas las opciones enumeradas anteriormente, haga clic en **Finalizar**: ![Asistente para Crear nueva máquina virtual][CR07]

1. Cuando haya completado los pasos anteriores, se mostrará la nueva máquina virtual en la ventana de herramientas de Azure Explorer.
   ![Nueva máquina virtual][CR08]

## <a name="restarting-a-virtual-machine-in-eclipse"></a>Reinicio de una máquina virtual en Eclipse

Para reiniciar una máquina virtual mediante Azure Explorer en Eclipse, siga estos pasos:

1. En la vista **Azure Explorer**, haga clic con el botón derecho en la máquina virtual y elija **Reiniciar**.
   ![Reinicio de una máquina virtual][RE01]

1. Haga clic en **Sí** cuando se le solicite que reinicie la máquina virtual.
   ![Reinicio de una máquina virtual][RE02]

## <a name="shutting-down-a-virtual-machine-in-eclipse"></a>Apagado de una máquina virtual en Eclipse

Para apagar una máquina virtual en ejecución mediante Azure Explorer en Eclipse, siga estos pasos:

1. En la vista **Azure Explorer**, haga clic con el botón derecho en la máquina virtual y elija **Apagar**.
   ![Apagado de una máquina virtual][SH01]

1. Haga clic en **Sí** cuando se le solicite que apague la máquina virtual.
   ![Apagado de una máquina virtual][SH02]

## <a name="deleting-a-virtual-machine-in-eclipse"></a>Eliminación de una máquina virtual en Eclipse

Para eliminar una máquina virtual mediante Azure Explorer en Eclipse, siga estos pasos:

1. En la vista **Azure Explorer**, haga clic con el botón derecho en la máquina virtual y elija **Eliminar**.
   ![Eliminación de una máquina virtual][DE01]

1. Haga clic en **Sí** cuando se le solicite que elimine la máquina virtual.
   ![Eliminación de una máquina virtual][DE02]

## <a name="see-also"></a>Otras referencias
Para más información sobre los tamaños y los precios de máquinas virtuales de Azure, consulte los siguientes vínculos:

* Tamaños de máquina virtual de Azure
  * [Tamaños de las máquinas virtuales Windows en Azure]
  * [Tamaños de las máquinas virtuales Linux en Azure]
* Precios de máquina virtual de Azure
  * [Precios de máquinas virtuales Windows]
  * [Precios de máquinas virtuales Linux]

Para más información acerca de los kits de herramientas de Azure para los IDE de Java, vea los siguientes vínculos:

* [Kit de herramientas de Azure para Eclipse]
  * [Novedades del kit de herramientas de Azure para Eclipse]
  * [Instalación del Kit de herramientas de Azure para Eclipse]
  * [Sign In Instructions for the Azure Toolkit for Eclipse] (Instrucciones de inicio de sesión del kit de herramientas de Azure para Eclipse)
  * [Creación de una aplicación web Hello World para Azure en Eclipse]
* [Kit de herramientas de Azure para IntelliJ]
  * [Novedades del kit de herramientas de Azure para IntelliJ]
  * [Instalación del kit de herramientas de Azure para IntelliJ]
  * [Instrucciones de inicio de sesión del kit de herramientas de Azure para IntelliJ]
  * [Creación de una aplicación web Hello World para Azure en IntelliJ]

Para obtener más información sobre el uso de Azure con Java, vea el [Centro para desarrolladores de Java de Azure] y [Java Tools for Visual Studio Team Services] (Herramientas de Java para Visual Studio Team Services).

<!-- URL List -->

[Kit de herramientas de Azure para Eclipse]: ./azure-toolkit-for-eclipse.md
[Kit de herramientas de Azure para IntelliJ]: ./azure-toolkit-for-intellij.md
[Creación de una aplicación web Hello World para Azure en Eclipse]: ./app-service-web/app-service-web-eclipse-create-hello-world-web-app.md
[Creación de una aplicación web Hello World para Azure en IntelliJ]: ./app-service-web/app-service-web-intellij-create-hello-world-web-app.md
[Instalación del Kit de herramientas de Azure para Eclipse]: ./azure-toolkit-for-eclipse-installation.md
[Instalación del kit de herramientas de Azure para IntelliJ]: ./azure-toolkit-for-intellij-installation.md
[Sign In Instructions for the Azure Toolkit for Eclipse]: ./azure-toolkit-for-eclipse-sign-in-instructions.md (Instrucciones de inicio de sesión del kit de herramientas de Azure para Eclipse)
[Instrucciones de inicio de sesión del kit de herramientas de Azure para IntelliJ]: ./azure-toolkit-for-intellij-sign-in-instructions.md
[Novedades del kit de herramientas de Azure para Eclipse]: ./azure-toolkit-for-eclipse-whats-new.md
[Novedades del kit de herramientas de Azure para IntelliJ]: ./azure-toolkit-for-intellij-whats-new.md

[Centro para desarrolladores de Java de Azure]: https://azure.microsoft.com/develop/java/
[Java Tools for Visual Studio Team Services]: https://java.visualstudio.com/

[Tamaños de las máquinas virtuales Windows en Azure]: /azure/virtual-machines/virtual-machines-windows-sizes
[Tamaños de las máquinas virtuales Linux en Azure]: /azure/virtual-machines/virtual-machines-linux-sizes
[Precios de máquinas virtuales Windows]: /pricing/details/virtual-machines/windows/
[Precios de máquinas virtuales Linux]: /pricing/details/virtual-machines/linux/

<!-- IMG List -->

[RE01]: ./media/azure-toolkit-for-eclipse-managing-virtual-machines-using-azure-explorer/RE01.png
[RE02]: ./media/azure-toolkit-for-eclipse-managing-virtual-machines-using-azure-explorer/RE02.png

[SH01]: ./media/azure-toolkit-for-eclipse-managing-virtual-machines-using-azure-explorer/SH01.png
[SH02]: ./media/azure-toolkit-for-eclipse-managing-virtual-machines-using-azure-explorer/SH02.png

[DE01]: ./media/azure-toolkit-for-eclipse-managing-virtual-machines-using-azure-explorer/DE01.png
[DE02]: ./media/azure-toolkit-for-eclipse-managing-virtual-machines-using-azure-explorer/DE02.png

[CR01]: ./media/azure-toolkit-for-eclipse-managing-virtual-machines-using-azure-explorer/CR01.png
[CR02]: ./media/azure-toolkit-for-eclipse-managing-virtual-machines-using-azure-explorer/CR02.png
[CR03]: ./media/azure-toolkit-for-eclipse-managing-virtual-machines-using-azure-explorer/CR03.png
[CR04]: ./media/azure-toolkit-for-eclipse-managing-virtual-machines-using-azure-explorer/CR04.png
[CR05]: ./media/azure-toolkit-for-eclipse-managing-virtual-machines-using-azure-explorer/CR05.png
[CR06]: ./media/azure-toolkit-for-eclipse-managing-virtual-machines-using-azure-explorer/CR06.png
[CR07]: ./media/azure-toolkit-for-eclipse-managing-virtual-machines-using-azure-explorer/CR07.png
[CR08]: ./media/azure-toolkit-for-eclipse-managing-virtual-machines-using-azure-explorer/CR08.png

