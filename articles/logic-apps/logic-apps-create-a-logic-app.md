---
title: "Creación de flujos de trabajo con la primera aplicación de lógica de Azure | Microsoft Docs"
description: "Introducción a la conexión tanto de aplicaciones como de servicios de SaaS a su primera aplicación lógica"
author: jeffhollan
manager: anneta
editor: 
services: logic-apps
documentationcenter: 
ms.assetid: ce3582b5-9c58-4637-9379-75ff99878dcd
ms.service: logic-apps
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 01/25/2017
ms.author: jehollan
translationtype: Human Translation
ms.sourcegitcommit: 6e0ad6b5bec11c5197dd7bded64168a1b8cc2fdd
ms.openlocfilehash: 221f1b9f0985bbaf0553f6ca01f0f048b9976315
ms.lasthandoff: 03/28/2017


---
# <a name="create-a-new-logic-app-connecting-saas-services"></a>Creación de una nueva aplicación lógica mediante la conexión de servicios de SaaS
En este tema se muestra cómo, en solo unos minutos, puede comenzar a trabajar con [Azure Logic Apps](logic-apps-what-are-logic-apps.md). Le guiaremos a través de un flujo de trabajo simple que le permite enviar tweets interesantes a su correo electrónico.

Para utilizar este escenario necesitará:

* Una suscripción de Azure
* Una cuenta de Twitter
* Una cuenta de Outlook.com o de Office 365 Outlook

## <a name="create-a-new-logic-app-to-email-you-tweets"></a>Creación de una nueva aplicación lógica para enviarle tweets por correo electrónico

1. Inicie sesión en el [Portal de Azure](https://portal.azure.com). 

2. En el menú izquierdo, elija **Nuevo** > **Enterprise Integration** > **Logic App**.

    También puede elegir **Nuevo** y, después, en el cuadro de búsqueda escribir `logic app` y presionar Entrar. Elija **Logic App** > **Crear**.

3. Escriba un nombre para la aplicación lógica, seleccione su suscripción de Azure, cree o seleccione un grupo de recursos de Azure, seleccione una ubicación y elija **Crear**.

    Si selecciona **Anclar al panel**, la aplicación lógica se abrirá automáticamente después de la implementación.

4. Al abrir la aplicación lógica por primera vez, puede seleccionar una plantilla inicial.
Por ahora, haga clic en **Blank Logic App** (aplicación lógica en blanco) para generarla desde cero. 

5. El primer elemento que debe crear es el desencadenador. Este es el evento que inicia la aplicación lógica. En el cuadro de búsqueda, busque **twitter** y seleccione **Cuando se publica un tweet nuevo**. Inicie sesión con el nombre de usuario y la contraseña de su cuenta de Twitter.

6. Ahora escriba un término de búsqueda que desencadene su aplicación lógica.

   ![Búsqueda de Twitter](media/logic-apps-create-a-logic-app/twittersearch.png)

    **Frecuencia** e **Intervalo** determinan con qué frecuencia la aplicación lógica busca nuevos tweets y devuelve todos los tweets durante ese intervalo de tiempo.

7. Seleccione **Nuevo paso** y elija **Agregar una acción** o **Agregar una condición**.

    Al seleccionar **Agregar una acción**, para elegir una acción, puede buscar en los [conectores disponibles](../connectors/apis-list.md). 

8. En el cuadro de búsqueda, busque **outlook**y seleccione **Enviar un correo electrónico** para enviar un mensaje desde su cuenta de Outlook a cualquier dirección de correo electrónico especificada.

   ![Acciones](media/logic-apps-create-a-logic-app/actions.png)

9. Ahora tiene que rellenar los parámetros de su correo electrónico preferido: 

   ![Parámetros](media/logic-apps-create-a-logic-app/parameters.png)

10. Por último, puede seleccionar **Guardar** para activar la aplicación lógica.

## <a name="manage-your-logic-app-after-creation"></a>Administrar la aplicación lógica tras la creación

Ahora la aplicación lógica está en funcionamiento. Buscará periódicamente los tweets con el término de búsqueda especificado. Cuando encuentre un tweet coincidente, le enviará un correo electrónico. Por último, verá cómo deshabilitar la aplicación o ver cómo marcha.

1. Vaya al [Portal de Azure](https://portal.azure.com).

2. En el menú izquierdo, haga clic en **Más servicios**. En **Enterprise Integration**, elija **Logic Apps**. Seleccione la aplicación lógica.

    *    Para ver el estado de la aplicación, el historial de ejecución e información general, en el menú de la aplicación lógica, elija **Información general**. Si no encuentra los datos que espera, en la barra de comandos, elija **Actualizar**.

    *    Para editar la aplicación, en el menú de la aplicación lógica, elija **Diseñador de aplicación lógica**.

    *    Para desactivar temporalmente la aplicación, en el menú de la aplicación lógica, elija **Información general**. En la barra de comandos, haga clic en **Deshabilitar**.

    *    Para eliminar la aplicación, en el menú de la aplicación lógica, elija **Información general**. 
    En la barra de comandos, haga clic en **Eliminar**. Escriba el nombre de la aplicación lógica y elija **Eliminar**.

En menos de 5 minutos ha sido capaz de configurar una aplicación lógica sencilla que se ejecuta en la nube. Para obtener más información acerca del uso de las características de las aplicaciones lógicas, consulte [Uso de las características de aplicaciones lógicas]. Para obtener más información acerca de las definiciones de aplicación lógica, consulte [Creación de definiciones de aplicación lógica](../logic-apps/logic-apps-author-definitions.md).

<!-- Shared links -->
[Azure portal]: https://portal.azure.com
[Uso de las características de aplicaciones lógicas]: logic-apps-create-a-logic-app.md
