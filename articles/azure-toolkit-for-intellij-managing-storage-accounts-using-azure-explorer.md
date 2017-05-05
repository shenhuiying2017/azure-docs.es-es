---
title: Administrar cuentas de almacenamiento mediante Azure Explorer para IntelliJ | Microsoft Docs
description: "Obtenga información sobre cómo administrar las cuentas de Azure Storage mediante Azure Explorer para IntelliJ."
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
ms.openlocfilehash: 22930bf1d37d7b6039bd02792cdd70ec321d6fa7
ms.lasthandoff: 04/22/2017


---

# <a name="managing-storage-accounts-using-the-azure-explorer-for-intellij"></a>Administración de cuentas de almacenamiento mediante Azure Explorer para IntelliJ

Azure Explorer, que forma parte del kit de herramientas de Azure para IntelliJ, proporciona a los desarrolladores de Java una solución fácil de usar para la administración de cuentas de almacenamiento en su cuenta de Azure desde el IDE de IntelliJ.

[!INCLUDE [azure-toolkit-for-intellij-prerequisites](../includes/azure-toolkit-for-intellij-prerequisites.md)]

[!INCLUDE [azure-toolkit-for-intellij-show-azure-explorer](../includes/azure-toolkit-for-intellij-show-azure-explorer.md)]

## <a name="creating-a-storage-account-in-intellij"></a>Creación de una cuenta de almacenamiento en IntelliJ

Los siguientes pasos le guiarán por los pasos necesarios para crear una cuenta de almacenamiento mediante Azure Explorer.

1. Inicie sesión en su cuenta de Azure siguiendo los pasos del artículo [Sign In Instructions for the Azure Toolkit for Eclipse] (Instrucciones de inicio de sesión del kit de herramientas de Azure para Eclipse).

1. En la ventana de herramientas de **Azure Explorer**, expanda el nodo **Azure**, haga clic con el botón derecho en **Cuentas de almacenamiento** y, después, haga clic en **Crear cuenta de almacenamiento**.
   ![Menú Crear cuenta de almacenamiento][CS01]

1. Cuando aparezca el cuadro de diálogo **Crear cuenta de almacenamiento**, especifique las opciones siguientes: ![Cuadro de diálogo Crear una nueva cuenta de almacenamiento][CS02]

   a. **Nombre**: especifica el nombre para la nueva cuenta de almacenamiento.

   b. **Tipo de cuenta**: especifica el tipo de cuenta de almacenamiento que se va a crear, por ejemplo "Blob Storage". (Para más información, vea [Acerca de las cuentas de Azure Storage]).

   c. **Rendimiento**: especifica la oferta de cuenta de almacenamiento que quiere usar del publicador seleccionado; por ejemplo "Premium". (Para más información, vea [Objetivos de escalabilidad y rendimiento de Azure Storage]).

   d. **Replicación**: especifica la replicación para la cuenta de almacenamiento, por ejemplo en "Redundancia de zona". (Para más información, vea [Replicación de Azure Storage]).

   e. **Suscripción**: especifica la suscripción de Azure que quiere usar para la nueva cuenta de almacenamiento.

   f. **Ubicación**: especifica la ubicación donde se creará la cuenta de almacenamiento, por ejemplo "Oeste de EE. UU.".

   g. **Grupo de recursos**: especifica el grupo de recursos para la máquina virtual; debe elegir una de las siguientes opciones:
      * **Crear nuevo**: especifica que quiere crear un nuevo grupo de recursos.
      * **Usar existente**: especifica que se va a elegir de una lista de grupos de recursos asociados a la cuenta de Azure.

1. Cuando haya especificado todas las opciones anteriores, haga clic en **Aceptar**.

## <a name="creating-a-storage-container-in-intellij"></a>Creación de un contenedor de almacenamiento en IntelliJ

Los siguientes pasos le servirán de guía para crear un contenedor de almacenamiento mediante Azure Explorer.

1. En Azure Explorer, haga clic con el botón derecho en la cuenta de almacenamiento donde quiera crear un contenedor y, después, haga clic en **Crear contenedor de blobs**.
   ![Menú Crear contenedor de almacenamiento][CC01]

1. Cuando aparezca el cuadro de diálogo **Crear contenedor de blobs**, especifique el nombre para el contenedor y, después, haga clic en **Aceptar**. Para más información sobre la nomenclatura de contenedores de almacenamiento, vea [Asignar nombres y hacer referencia a contenedores, blobs y metadatos]. ![Cuadro de diálogo Crear contenedor de almacenamiento][CC02]

## <a name="deleting-a-storage-container-in-intellij"></a>Eliminación de un contenedor de almacenamiento en IntelliJ

Para eliminar un contenedor de almacenamiento mediante Azure Explorer, siga estos pasos:

1. En Azure Explorer, haga clic con el botón derecho en el contenedor de almacenamiento y, después, haga clic en **Eliminar**.
   ![Menú Eliminar contenedor de almacenamiento][DC01]

1. Haga clic en **Sí** cuando se le pida que elimine el contenedor de almacenamiento.
   ![Cuadro de diálogo Eliminar contenedor de almacenamiento][DC02]

## <a name="deleting-a-storage-account-in-intellij"></a>Eliminación de una cuenta de almacenamiento en IntelliJ

Para eliminar una cuenta de almacenamiento mediante Azure Explorer, siga estos pasos:

1. En la ventana de herramientas de **Azure Explorer**, haga clic con el botón derecho en la cuenta de almacenamiento y seleccione **Eliminar**.
   ![Menú Eliminar cuenta de almacenamiento][DS01]

1. Haga clic en **Sí** cuando se le pida que elimine la cuenta de almacenamiento.
   ![Cuadro de diálogo Eliminar cuenta de almacenamiento][DS02]

## <a name="see-also"></a>Otras referencias
Para más información sobre las cuentas de Azure Storage, tamaños y precios, vea los siguientes vínculos:

* [Introducción a Microsoft Azure Storage]
* [Acerca de las cuentas de Azure Storage]
* Tamaños de cuentas de Azure Storage
   * [Tamaños de las cuentas de almacenamiento de Windows en Azure]
   * [Sizes for Linux storage accounts in Azure] (Tamaños de las cuentas de almacenamiento de Linux en Azure)
* Precios de las cuentas de Azure Storage
   * [Precios de cuentas de almacenamiento de Windows]
   * [Precios de cuentas de almacenamiento de Linux]

Para más información acerca de los kits de herramientas de Azure para los IDE de Java, vea los siguientes vínculos:

* [Kit de herramientas de Azure para Eclipse]
  * [Novedades del kit de herramientas de Azure para Eclipse]
  * [Instalación del Kit de herramientas de Azure para Eclipse]
  * [Sign In Instructions for the Azure Toolkit for Eclipse]
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
[Sign In Instructions for the Azure Toolkit for Eclipse]: ./azure-toolkit-for-eclipse-sign-in-instructions.md
[Instrucciones de inicio de sesión del kit de herramientas de Azure para IntelliJ]: ./azure-toolkit-for-intellij-sign-in-instructions.md
[Novedades del kit de herramientas de Azure para Eclipse]: ./azure-toolkit-for-eclipse-whats-new.md
[Novedades del kit de herramientas de Azure para IntelliJ]: ./azure-toolkit-for-intellij-whats-new.md

[Centro para desarrolladores de Java de Azure]: https://azure.microsoft.com/develop/java/
[Java Tools for Visual Studio Team Services]: https://java.visualstudio.com/

[Introducción a Microsoft Azure Storage]: /azure/storage/storage-introduction
[Acerca de las cuentas de Azure Storage]: /azure/storage/storage-create-storage-account
[Replicación de Azure Storage]: /azure/storage/storage-redundancy
[Objetivos de escalabilidad y rendimiento de Azure Storage]: /azure/storage/storage-scalability-targets
[Asignar nombres y hacer referencia a contenedores, blobs y metadatos]: http://go.microsoft.com/fwlink/?LinkId=255555

[Tamaños de las cuentas de almacenamiento de Windows en Azure]: /azure/virtual-machines/virtual-machines-windows-sizes
[Sizes for Linux storage accounts in Azure]: /azure/virtual-machines/virtual-machines-linux-sizes (Tamaños de las cuentas de almacenamiento de Linux en Azure)
[Precios de cuentas de almacenamiento de Windows]: /pricing/details/virtual-machines/windows/
[Precios de cuentas de almacenamiento de Linux]: /pricing/details/virtual-machines/linux/

<!-- IMG List -->

[CS01]: ./media/azure-toolkit-for-intellij-managing-storage-accounts-using-azure-explorer/CS01.png
[CS02]: ./media/azure-toolkit-for-intellij-managing-storage-accounts-using-azure-explorer/CS02.png
[CC01]: ./media/azure-toolkit-for-intellij-managing-storage-accounts-using-azure-explorer/CC01.png
[CC02]: ./media/azure-toolkit-for-intellij-managing-storage-accounts-using-azure-explorer/CC02.png

[DS01]: ./media/azure-toolkit-for-intellij-managing-storage-accounts-using-azure-explorer/DS01.png
[DS02]: ./media/azure-toolkit-for-intellij-managing-storage-accounts-using-azure-explorer/DS02.png
[DC01]: ./media/azure-toolkit-for-intellij-managing-storage-accounts-using-azure-explorer/DC01.png
[DC02]: ./media/azure-toolkit-for-intellij-managing-storage-accounts-using-azure-explorer/DC02.png

