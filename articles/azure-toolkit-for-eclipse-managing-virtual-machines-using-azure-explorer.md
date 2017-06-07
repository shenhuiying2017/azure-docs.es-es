---
title: "Administración de máquinas virtuales con Azure Explorer para Eclipse | Microsoft Docs"
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
ms.translationtype: Human Translation
ms.sourcegitcommit: fc4172b27b93a49c613eb915252895e845b96892
ms.openlocfilehash: 9784e8af9c530078afee06f08a23403a44b0762f
ms.contentlocale: es-es
ms.lasthandoff: 05/12/2017


---

# <a name="manage-virtual-machines-by-using-the-azure-explorer-for-eclipse"></a>Administración de máquinas virtuales con Azure Explorer para Eclipse

Azure Explorer, que forma parte del kit de herramientas de Azure para Eclipse, proporciona a los desarrolladores de Java una solución fácil de usar para la administración de máquinas virtuales en su cuenta de Azure desde el entorno de desarrollo integrado de Eclipse (IDE).

[!INCLUDE [azure-toolkit-for-eclipse-prerequisites](../includes/azure-toolkit-for-eclipse-prerequisites.md)]

[!INCLUDE [azure-toolkit-for-eclipse-show-azure-explorer](../includes/azure-toolkit-for-eclipse-show-azure-explorer.md)]

## <a name="create-a-virtual-machine-in-eclipse"></a>Creación de una máquina virtual en Eclipse

Para crear una máquina virtual con Azure Explorer, haga lo siguiente:

1. Inicie sesión en su cuenta de Azure siguiendo los pasos del artículo [Instrucciones de inicio de sesión del kit de herramientas de Azure para Eclipse].

2. En la vista **Azure Explorer**, expanda el nodo **Azure**, haga clic con el botón derecho en **Virtual Machines** y, luego, en **Crear máquina virtual**.

   ![El comando Crear máquina virtual][CR01]  
   El Asistente **para crear nueva máquina virtual** se abrirá.

3. En la ventana **Elegir una suscripción**, seleccione su suscripción y, luego, haga clic en **Siguiente**.

   ![Ventana Elegir una suscripción][CR02]

4. En la ventana **Seleccionar una imagen de máquina virtual**, escriba la siguiente información:

   * **Ubicación**: especifica la ubicación donde se creará la máquina virtual (por ejemplo, *oeste de EE. UU.*).

   * **Publicador**: especifica el publicador que creó la imagen que se usará para crear la máquina virtual (por ejemplo, *Microsoft*).

   * **Oferta**: especifica la oferta de máquina virtual que quiere usar del publicador seleccionado (por ejemplo, *JDK*).

   * **SKU**: especifica la referencia de almacén (SKU) que quiere usar de la oferta seleccionada (por ejemplo, *JDK_8*).

   * **N.º de versión**: especifica la versión que quiere usar de la SKU seleccionada.

    ![La ventana Seleccionar una imagen de máquina virtual][CR03]

5. Haga clic en **Siguiente**.

6. En la pantalla **Configuración básica de máquina virtual**, escriba la siguiente información:

   * **Nombre de máquina virtual**: especifica el nombre de la nueva máquina virtual, que debe comenzar con una letra y contener solo letras, números y guiones.

   * **Tamaño**: especifica el número de núcleos y la memoria que se asignará para la máquina virtual.

   * **Nombre de usuario**: especifica la cuenta de administrador que se creará para administrar la máquina virtual.

   * **Contraseña** y **Confirmar**: especifica la contraseña de la cuenta de administrador.

    ![La ventana Configuración básica de máquina virtual][CR04]

7. Haga clic en **Siguiente**.

8. En la ventana **Crear una nueva cuenta de almacenamiento**, escriba la siguiente información:

   * **Grupo de recursos**: especifica el grupo de recursos para su máquina virtual. Seleccione una de las siguientes opciones:
      * **Crear nuevo**: especifica que quiere crear un nuevo grupo de recursos.
      * **Usar existente**: especifica que desea seleccionar un grupo de recursos que ya está asociado a la cuenta de Azure.

      ![Cuadro de diálogo Crear una nueva cuenta de almacenamiento][CR05]

   * **Cuenta de almacenamiento**: especifica la cuenta de almacenamiento que se usará para almacenar la máquina virtual. Puede usar una cuenta de almacenamiento existente o crear una nueva.

   * **Red virtual** y **subred**: especifica la red virtual y subred que se conectará la máquina virtual. Puede usar una red y subred existentes, o puede crear una nueva red y subred. Si selecciona **Crear nuevo**, se mostrará el cuadro de diálogo siguiente:

      ![Cuadro de diálogo Crear una red virtual nueva][CR06]

9. En la ventana **Recursos asociados**, escriba la siguiente información:

   * **Dirección IP pública**: especifica una dirección IP externa para la máquina virtual. Puede crear una dirección IP o, si la máquina virtual no tiene una dirección IP pública, puede seleccionar **(Ninguno)**.

   * **Grupo de seguridad de red**: especifica un firewall de red opcional para la máquina virtual. Puede seleccionar un firewall o, si la máquina virtual no utiliza un firewall, puede seleccionar **(Ninguno)**.

   * **Conjunto de disponibilidad**: especifica un conjunto de disponibilidad opcional al que puede pertenecer su máquina virtual. Puede seleccionar un conjunto de disponibilidad, crear uno o, si la máquina virtual no pertenece a un conjunto de disponibilidad, seleccionar **(Ninguno)**.

   ![La ventana Recursos asociados][CR07]

9. Haga clic en **Finalizar**  
  La nueva máquina virtual aparece en la ventana de la herramienta Azure Explorer.

   ![Nueva máquina virtual][CR08]

## <a name="restart-a-virtual-machine-in-eclipse"></a>Reinicio de una máquina virtual en Eclipse

Para reiniciar una máquina virtual mediante Azure Explorer en Eclipse, siga estos pasos:

1. En la vista **Azure Explorer**, haga clic con el botón derecho en la máquina virtual y elija **Reiniciar**.

   ![El comando de reinicio de máquina virtual][RE01]

2. En la ventana confirmación, haga clic en **Sí**.

   ![Ventana de confirmación de Reiniciar][RE02]

## <a name="shut-down-a-virtual-machine-in-eclipse"></a>Apagado de una máquina virtual en Eclipse

Para apagar una máquina virtual en funcionamiento con Azure Explorer en Eclipse, siga estos pasos:

1. En la vista **Azure Explorer**, haga clic con el botón derecho en la máquina virtual y elija **Apagar**.

   ![El comando de apagado de máquina virtual][SH01]

2. En la ventana confirmación, haga clic en **Sí**.

   ![Ventana de confirmación de apagado de máquina virtual][SH02]

## <a name="delete-a-virtual-machine-in-eclipse"></a>Eliminación de una máquina virtual en Eclipse

Para eliminar una máquina virtual mediante Azure Explorer en Eclipse, siga estos pasos:

1. En la vista **Azure Explorer**, haga clic con el botón derecho en la máquina virtual y elija **Eliminar**.

   ![El comando de eliminación de máquina virtual][DE01]

2. En la ventana confirmación, haga clic en **Sí**.

   ![Ventana de confirmación de eliminación de máquina virtual][DE02]

## <a name="next-steps"></a>Pasos siguientes
Para obtener más información sobre los tamaños y los precios de máquinas virtuales de Azure, consulte los siguientes vínculos:

* Tamaños de máquinas virtuales de Azure
  * [Tamaños de las máquinas virtuales Windows en Azure]
  * [Tamaños de las máquinas virtuales Linux en Azure]
* Precios de máquinas virtuales de Azure
  * [Precios de máquinas virtuales Windows]
  * [Precios de máquinas virtuales Linux]

Para obtener más información sobre los kits de herramientas de Azure para los IDE de Java, consulte los siguientes vínculos:

* [Kit de herramientas de Azure para Eclipse]
  * [Novedades del kit de herramientas de Azure para Eclipse]
  * [Instalación del Kit de herramientas de Azure para Eclipse]
  * [Instrucciones de inicio de sesión del kit de herramientas de Azure para Eclipse]
  * [Creación de una aplicación web Hello World para Azure en Eclipse]
* [Kit de herramientas de Azure para IntelliJ]
  * [Novedades del kit de herramientas de Azure para IntelliJ]
  * [Instalación del kit de herramientas de Azure para IntelliJ]
  * [Instrucciones de inicio de sesión del kit de herramientas de Azure para IntelliJ]
  * [Creación de una aplicación web Hello World para Azure en IntelliJ]

Para obtener más información sobre el uso de Azure con Java, vea el [Centro para desarrolladores de Java de Azure] y la página de [herramientas de Java para Visual Studio Team Services].

<!-- URL List -->

[Kit de herramientas de Azure para Eclipse]: ./azure-toolkit-for-eclipse.md
[Kit de herramientas de Azure para IntelliJ]: ./azure-toolkit-for-intellij.md
[Creación de una aplicación web Hello World para Azure en Eclipse]: ./app-service-web/app-service-web-eclipse-create-hello-world-web-app.md
[Creación de una aplicación web Hello World para Azure en IntelliJ]: ./app-service-web/app-service-web-intellij-create-hello-world-web-app.md
[Instalación del Kit de herramientas de Azure para Eclipse]: ./azure-toolkit-for-eclipse-installation.md
[Instalación del kit de herramientas de Azure para IntelliJ]: ./azure-toolkit-for-intellij-installation.md
[Instrucciones de inicio de sesión del kit de herramientas de Azure para Eclipse]: ./azure-toolkit-for-eclipse-sign-in-instructions.md
[Instrucciones de inicio de sesión del kit de herramientas de Azure para IntelliJ]: ./azure-toolkit-for-intellij-sign-in-instructions.md
[Novedades del kit de herramientas de Azure para Eclipse]: ./azure-toolkit-for-eclipse-whats-new.md
[Novedades del kit de herramientas de Azure para IntelliJ]: ./azure-toolkit-for-intellij-whats-new.md

[Centro para desarrolladores de Java de Azure]: https://azure.microsoft.com/develop/java/
[herramientas de Java para Visual Studio Team Services]: https://java.visualstudio.com/

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

