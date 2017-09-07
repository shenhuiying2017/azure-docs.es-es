---
title: "Instrucciones de inicio de sesión del kit de herramientas de Azure para IntelliJ"
description: "Obtenga información sobre cómo iniciar sesión en Microsoft Azure con el kit de herramientas de Azure para IntelliJ."
services: 
documentationcenter: java
author: rmcmurray
manager: routlaw
editor: 
ms.assetid: 
ms.service: multiple
ms.workload: na
ms.tgt_pltfrm: multiple
ms.devlang: Java
ms.topic: article
ms.date: 08/20/2017
ms.author: robmcm
ms.translationtype: HT
ms.sourcegitcommit: a16daa1f320516a771f32cf30fca6f823076aa96
ms.openlocfilehash: def5957b2ed58aee2e03c879ba9fd3d6c714e358
ms.contentlocale: es-es
ms.lasthandoff: 09/02/2017

---

# <a name="sign-in-instructions-for-the-azure-toolkit-for-intellij"></a>Instrucciones de inicio de sesión del kit de herramientas de Azure para IntelliJ

El kit de herramientas de Azure para IntelliJ proporciona dos métodos para iniciar sesión en su cuenta de Azure:

  * **Automatizado**: cree un archivo de credenciales que puede usar para iniciar sesión automáticamente en su cuenta de Azure.
  * **Interactivo**: escriba sus credenciales de Azure cada vez que inicie sesión su cuenta de Azure.

Los pasos descritos en las secciones siguientes explican cómo utilizar cada método.

[!INCLUDE [azure-toolkit-for-intellij-prerequisites](../includes/azure-toolkit-for-intellij-prerequisites.md)]

## <a name="sign-in-to-your-azure-account-automatically"></a>Inicio sesión en la cuenta de Azure de forma automática

Los siguientes pasos lo guiarán por el proceso de creación de un archivo de credenciales que contiene los datos de entidades de servicio. Cuando finalice este proceso, Eclipse usará el archivo de credenciales para iniciar sesión automáticamente en Azure cada vez que abra el proyecto.

1. Abra el proyecto con IntelliJ IDEA.

2. En el menú **Herramientas**, seleccione **Azure** y, luego, haga clic en **Inicio de sesión en Microsoft Azure**.

   ![El comando de inicio de sesión en Azure IntelliJ][A01]

3. En la ventana **Inicio de sesión en Microsoft Azure**, seleccione **Automatizado** y, luego, haga clic en **Nuevo**.

   ![La ventana Inicio de sesión en Microsoft Azure con el modo Automatizado seleccionado][A02]

4. En la ventana **Inicio de sesión en Microsoft Azure**, escriba sus credenciales de Azure y, luego, haga clic en **Iniciar sesión**.

   ![El cuadro de diálogo Inicio de sesión en Microsoft Azure][A03]

5. En la ventana **Create authentication files** (Crear archivos de autenticación), seleccione las suscripciones que desea utilizar, elija el directorio de destino y, luego, haga clic en **Iniciar**.

   ![La ventana Create authentication files (Crear archivos de autenticación)][A04]

6. En el cuadro de diálogo **Service Principal Creation Status** (Estado de creación de entidades de servicio), una vez que se hayan creado correctamente los archivos, haga clic en **Aceptar**.

   ![El cuadro de diálogo Service Principal Creation Status (Estado de creación de entidades de servicio)][A05]

7. En la ventana **Inicio de sesión en Microsoft Azure**, haga clic en **Iniciar sesión en**.

   ![Cuadro de diálogo Inicio de sesión en Microsoft Azure][A06]

8. En el cuadro de diálogo **Seleccionar suscripciones**, elija las suscripciones que desea usar y, luego, haga clic en **Aceptar**.

   ![Cuadro de diálogo Seleccionar suscripciones][A07]

## <a name="sign-out-of-your-azure-account-after-you-have-signed-in-automatically"></a>Cierre de sesión de la cuenta de Azure después de haber iniciado sesión de forma automática

Después de haber configurado la cuenta con los pasos anteriores, el kit de herramientas de Azure iniciará sesión automáticamente en su cuenta de Azure cada vez que reinicie IntelliJ IDEA. Sin embargo, para cerrar la sesión de su cuenta de Azure e impedir que el kit de herramientas de Azure inicie sesión automáticamente, siga estos pasos:

1. En IntelliJ IDEA, haga clic en **Herramientas**, seleccione **Azure** y, luego, haga clic en **Cierre de sesión en Microsoft Azure**.

   ![El comando de cierre de sesión en Azure IntelliJ][L01]

2. En la ventana confirmación **Cierre de sesión en Microsoft Azure**, haga clic en **Sí**.

   ![La ventana de confirmación Cierre de sesión en Microsoft Azure][L03]

## <a name="sign-in-to-your-azure-account-automatically-by-using-an-existing-credentials-file"></a>Inicio de sesión en su cuenta de Azure de forma automática mediante un archivo de credenciales

Si cierra la sesión de su cuenta de Azure mientras utiliza IntelliJ IDEA, debe usar un archivo de credenciales para iniciar sesión automáticamente en la cuenta. Para configurar el kit de herramientas de Azure para que Eclipse use un archivo de credenciales, realice lo siguiente:

1. Abra el proyecto con IntelliJ IDEA.

2. En el menú **Herramientas**, seleccione **Azure** y, luego, haga clic en **Inicio de sesión en Microsoft Azure**.

   ![El comando de inicio de sesión en Azure IntelliJ][A01]

3. En la ventana **Inicio de sesión en Microsoft Azure**, seleccione **Automatizado** y, luego, haga clic en **Examinar**.

   ![La ventana Inicio de sesión en Microsoft Azure con el modo Automatizado seleccionado][A02]

4. En el cuadro de diálogo **Select Authentication File** (Seleccionar archivo de autenticación), elija un archivo de credenciales creado anteriormente y, luego, haga clic en **Seleccionar**.

   ![El cuadro de diálogo Select Authentication File (Seleccionar archivo de autenticación)][A08]

5. En la ventana **Inicio de sesión en Microsoft Azure**, haga clic en **Iniciar sesión en**.

   ![La ventana Inicio de sesión en Microsoft Azure con el modo Automatizado seleccionado][A06]

6. En el cuadro de diálogo **Seleccionar suscripciones**, elija las suscripciones que desea usar y, luego, haga clic en **Aceptar**.

   ![Cuadro de diálogo Seleccionar suscripciones][A07]

## <a name="sign-in-to-your-azure-account-interactively"></a>Inicio de sesión en la cuenta de Azure de forma interactiva

En los pasos siguientes se explica cómo iniciar sesión en Azure especificando manualmente las credenciales de Azure:

1. Abra el proyecto con IntelliJ IDEA.

2. Haga clic en **Herramientas**, seleccione **Azure** y, luego, haga clic en **Inicio de sesión en Microsoft Azure**.

   ![El comando de inicio de sesión en Azure IntelliJ][I01]

3. En la ventana **Inicio de sesión en Microsoft Azure**, seleccione **Interactivo** y, luego, haga clic en **Iniciar sesión**.

   ![La ventana Inicio de sesión en Microsoft Azure con el modo Interactivo seleccionado][I02]

4. Cuando se muestre el cuadro de diálogo **Inicio de sesión en Microsoft Azure**, escriba sus credenciales de Azure y, luego, haga clic en **Iniciar sesión**.

   ![El cuadro de diálogo Inicio de sesión en Microsoft Azure][I03]

5. En el cuadro de diálogo **Seleccionar suscripciones**, elija las suscripciones que desea usar y, luego, haga clic en **Aceptar**.

   ![Cuadro de diálogo Seleccionar suscripciones][I04]

## <a name="sign-out-of-your-azure-account-after-you-have-signed-in-interactively"></a>Cierre de sesión de la cuenta de Azure al iniciar sesión de forma interactiva

Después de haber configurado la cuenta con los pasos anteriores, se cerrará automáticamente la sesión de su cuenta de Azure cada vez que reinicie IntelliJ IDEA. Sin embargo, si desea cerrar sesión de su cuenta de Azure *sin* tener que reiniciar IntelliJ IDEA, siga estos pasos.

1. En IntelliJ IDEA, haga clic en **Herramientas**, seleccione **Azure** y, luego, haga clic en **Cierre de sesión en Microsoft Azure**.

   ![El comando de cierre de sesión en Azure IntelliJ][L01]

2. En la ventana confirmación **Cierre de sesión en Microsoft Azure**, haga clic en **Sí**.

   ![La ventana de confirmación Cierre de sesión en Microsoft Azure][L02]

## <a name="next-steps"></a>Pasos siguientes

[!INCLUDE [azure-toolkit-additional-resources](../includes/azure-toolkit-additional-resources.md)]

<!-- URL List -->

[Azure Toolkit for Eclipse]: ./azure-toolkit-for-eclipse.md
[Azure Toolkit for IntelliJ]: ./azure-toolkit-for-intellij.md
[Create a Hello World Web App for Azure in Eclipse]: ./app-service-web/app-service-web-eclipse-create-hello-world-web-app.md
[Create a Hello World web app for Azure in IntelliJ]: ./app-service-web/app-service-web-intellij-create-hello-world-web-app.md
[Installing the Azure Toolkit for Eclipse]: ./azure-toolkit-for-eclipse-installation.md
[Installing the Azure Toolkit for IntelliJ]: ./azure-toolkit-for-intellij-installation.md
[Sign-in instructions for the Azure Toolkit for Eclipse]: ./azure-toolkit-for-eclipse-sign-in-instructions.md
[Sign-in instructions for the Azure Toolkit for IntelliJ]: ./azure-toolkit-for-intellij-sign-in-instructions.md
[What's new in the Azure Toolkit for Eclipse]: ./azure-toolkit-for-eclipse-whats-new.md
[What's new in the Azure Toolkit for IntelliJ]: ./azure-toolkit-for-intellij-whats-new.md

[Azure Java Developer Center]: https://azure.microsoft.com/develop/java/
[Java Tools for Visual Studio Team Services]: https://java.visualstudio.com/

<!-- IMG List -->

[I01]: ./media/azure-toolkit-for-intellij-sign-in-instructions/I01.png
[I02]: ./media/azure-toolkit-for-intellij-sign-in-instructions/I02.png
[I03]: ./media/azure-toolkit-for-intellij-sign-in-instructions/I03.png
[I04]: ./media/azure-toolkit-for-intellij-sign-in-instructions/I04.png

[A01]: ./media/azure-toolkit-for-intellij-sign-in-instructions/A01.png
[A02]: ./media/azure-toolkit-for-intellij-sign-in-instructions/A02.png
[A03]: ./media/azure-toolkit-for-intellij-sign-in-instructions/A03.png
[A04]: ./media/azure-toolkit-for-intellij-sign-in-instructions/A04.png
[A05]: ./media/azure-toolkit-for-intellij-sign-in-instructions/A05.png
[A06]: ./media/azure-toolkit-for-intellij-sign-in-instructions/A06.png
[A07]: ./media/azure-toolkit-for-intellij-sign-in-instructions/A07.png
[A08]: ./media/azure-toolkit-for-intellij-sign-in-instructions/A08.png

[L01]: ./media/azure-toolkit-for-intellij-sign-in-instructions/L01.png
[L02]: ./media/azure-toolkit-for-intellij-sign-in-instructions/L02.png
[L03]: ./media/azure-toolkit-for-intellij-sign-in-instructions/L03.png

