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
ms.sourcegitcommit: ce1e0cfe7c7f52d521ef3318376af75331323083
ms.openlocfilehash: 178a57624345a4b3e5d73e64ce4ccf81b8b90a88


---
# <a name="create-a-new-logic-app-connecting-saas-services"></a>Creación de una nueva aplicación lógica mediante la conexión de servicios de SaaS
En este tema se muestra cómo, en solo unos minutos, puede comenzar a trabajar con [Azure Logic Apps](logic-apps-what-are-logic-apps.md). Le guiaremos a través de un flujo de trabajo simple que le permite enviar tweets interesantes a su correo electrónico.

Para utilizar este escenario necesitará:

* Una suscripción de Azure
* Una cuenta de Twitter
* Un buzón Outlook.com o uno alojado de Office 365

## <a name="create-a-new-logic-app-to-email-you-tweets"></a>Creación de una nueva aplicación lógica para enviarle tweets por correo electrónico
1. En el [panel del Portal de Azure](https://portal.azure.com), seleccione **Nuevo**. 
2. En la barra de búsqueda, indique 'aplicación lógica' y seleccione **Aplicación lógica**. También puede seleccionar **Nuevo**, **Web y móvil** y, después, **Aplicación lógica**. 
3. Escriba un nombre para la aplicación lógica y seleccione una ubicación, un grupo de recursos y, después, **Crear**.  Si selecciona **Anclar al panel** , la aplicación lógica se abrirá automáticamente una vez implementada.  
4. Después de abrir la aplicación lógica por primera vez puede seleccionar una plantilla inicial.  Por ahora, haga clic en **Blank Logic App** (aplicación lógica en blanco) para generarla desde cero. 
5. El primer elemento que debe crear es el desencadenador.  Este es el evento que iniciará la aplicación lógica.  Busque **twitter** en el cuadro de búsqueda del desencadenador y selecciónelo.
6. Ahora tendrá que escribir un término de búsqueda de desencadenamiento.  La **Frecuencia** y el **Intervalo** determinarán con qué frecuencia la aplicación lógica buscará nuevos tweets (y devolverá todos los tweets de ese intervalo de tiempo).
    ![Búsqueda de Twitter](media/logic-apps-create-a-logic-app/twittersearch.png)
7. Seleccione el botón **Nuevo paso** y elija **Agregar una acción** o **Agregar una condición**.
8. Al seleccionar **Agregar una acción**, para elegir una acción, puede buscar en los [conectores disponibles](../connectors/apis-list.md) . Por ejemplo, puede seleccionar **Outlook.com - Enviar correo electrónico** para enviar correo desde una dirección de outlook.com:  
    ![Acciones](media/logic-apps-create-a-logic-app/actions.png)
9. Ahora tiene que rellenar los parámetros de su correo electrónico preferido:  ![Parámetros](media/logic-apps-create-a-logic-app/parameters.png)
10. Por último, puede seleccionar **Guardar** para activar la aplicación lógica.

## <a name="manage-your-logic-app-after-creation"></a>Administrar la aplicación lógica tras la creación
Ahora la aplicación lógica está en funcionamiento. Buscará periódicamente los tweets con el término de búsqueda especificado. Cuando encuentre un tweet coincidente, le enviará un correo electrónico. Por último, verá cómo deshabilitar la aplicación o ver cómo marcha.

1. Vaya al [Portal de Azure](https://portal.azure.com)
2. Haga clic en **Examinar** en el lado izquierdo de la pantalla y seleccione **Aplicaciones lógicas**.
3. Haga clic en la nueva aplicación lógica que acaba de crear para ver información general y el estado actual.
4. Para editar la nueva aplicación lógica, haga clic en **Editar**.
5. Para desactivar la aplicación, haga clic en **Deshabilitar** en la barra de comandos.
6. Vea, ejecute y desencadene historias que supervisar cuando se ejecute la aplicación lógica.  Puede hacer clic en **Actualizar** para ver los datos más recientes.

En menos de 5 minutos ha sido capaz de configurar una aplicación lógica sencilla que se ejecuta en la nube. Para obtener más información acerca del uso de las características de las aplicaciones lógicas, consulte [Uso de las características de aplicaciones lógicas]. Para obtener más información acerca de las definiciones de aplicación lógica, consulte [Creación de definiciones de aplicación lógica](../logic-apps/logic-apps-author-definitions.md).

<!-- Shared links -->
[Azure portal]: https://portal.azure.com
[Uso de las características de aplicaciones lógicas]: logic-apps-create-a-logic-app.md



<!--HONumber=Jan17_HO4-->


