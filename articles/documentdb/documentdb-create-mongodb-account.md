---
title: "Creación de una cuenta de DocumentDB para su uso con aplicaciones de MongoDB | Microsoft Docs"
description: "Use este tutorial para obtener información acerca de cómo crear una cuenta de DocumentDB con compatibilidad de protocolo para MongoDB, ahora disponible en una versión preliminar."
keywords: "aplicaciones de mongodb apps, aplicación de mongodb"
services: documentdb
author: AndrewHoh
manager: jhubbard
editor: 
documentationcenter: 
ms.assetid: 413f3ae1-a68e-43be-b0d7-fa2987644f3e
ms.service: documentdb
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/24/2016
ms.author: anhoh
translationtype: Human Translation
ms.sourcegitcommit: 9b83a0878f45bf3a79d00ad77965308d95f33d28
ms.openlocfilehash: 797ee3b1209e5abf545f0614c408d94391a29c8b


---

# <a name="create-a-documentdb-account-for-use-with-mongodb-apps"></a>Creación de una cuenta de DocumentDB para su uso con aplicaciones de MongoDB
Las bases de datos de DocumentDB se pueden utilizar ahora como almacenes de datos para aplicaciones escritas para MongoDB. Para utilizar esta funcionalidad, necesita una cuenta de Azure y una cuenta de DocumentDB. Este tutorial le guía a través del proceso de creación de una cuenta de DocumentDB para su uso con aplicaciones de MongoDB. 

Puede crear una cuenta de DocumentDB compatible con MongoDB mediante Azure Portal o la CLI de Azure con las plantillas de Azure Resource Manager. En este artículo se muestra cómo crear una cuenta de DocumentDB compatible con MongoDB con Azure Portal. Para crear una cuenta usando la CLI de Azure con Azure Resource Manager, consulte el artículo [Automate DocumentDB with support for MongoDB account creation](documentdb-automation-resource-manager-cli.md#quick-create-documentdb-with-mongodb-api-account) (Automatización de la creación de cuentas de bases de datos de DocumentDB compatibles con MongoDB).

## <a name="prerequisite"></a>Requisito previo
Una cuenta de Azure. Si no tiene una cuenta de Azure, cree ahora una [cuenta de Azure gratuita](https://azure.microsoft.com/free/).
## <a name="create-a-documentdb-account"></a>de una cuenta de Base de datos de documentos

1. En un explorador de Internet, inicie sesión en [Azure Portal](https://portal.azure.com).
2. En el panel izquierdo, haga clic en **NoSQL (DocumentDB)**.

    ![Captura de pantalla de la navegación a la izquierda del Portal, resaltando la entrada de DocumentDB NoSQL](./media/documentdb-create-mongodb-account/portalleftnav.png)

3. O bien, haga clic en **Más servicios >**, escriba **DocumentDB** en la barra de búsqueda superior y haga clic en **NoSQL (DocumentDB)**.

    ![Captura de pantalla de la hoja Más servicios, búsqueda de la entrada DocumentDB NoSQL](./media/documentdb-create-mongodb-account/more-services-search.PNG)

4. En la parte superior de la hoja **NoSQL (DocumentDB)**, haga clic en **+ Agregar** en la barra de acción superior.

    ![Captura de pantalla del botón Agregar en la hoja de recursos de DocumentDB NoSQL](./media/documentdb-create-mongodb-account/add-documentdb-account.png)

5. En la hoja **Cuenta de DocumentDB** , especifique la configuración que desee para la cuenta.

   ![Captura de pantalla del nuevo DocumentDB con soporte de protocolo para la hoja de MongoDB.](./media/documentdb-create-mongodb-account/create-documentdb-mongodb-account.PNG)

    - En el cuadro **Id.**, escriba un nombre para identificar la cuenta.  Cuando se valida el **Id.**, aparece una marca de verificación verde en el cuadro **Id.**. El valor de **Id.** se convierte en el nombre de host dentro del URI. El **Id.** solo puede contener minúsculas, números y el carácter "-"; debe tener entre 3 y 50 caracteres. Tenga en cuenta que *documents.azure.com* se anexa al nombre del punto de conexión que elija; su resultado se convertirá en el punto de conexión de la cuenta.

    - Para **API de NoSQL**, seleccione **MongoDB**. Esto especifica la API de comunicación que desea usar para interactuar con la base de datos de DocumentDB.

    - En **Suscripción**, seleccione la suscripción de Azure que desee utilizar para la cuenta. Si la cuenta tiene una sola suscripción, dicha cuenta se selecciona de manera predeterminada.

    - En **Grupo de recursos**, seleccione o cree un grupo de recursos para la cuenta.  De forma predeterminada, se elegirá un grupo de recursos existente en la suscripción de Azure.  Sin embargo, puede elegir crear un nuevo grupo de recursos al que desea agregar la cuenta. Para más información, consulte [Uso del Portal de Azure para administrar los recursos de Azure](../azure-portal/resource-group-portal.md).

    - Use **Ubicación** para especificar la ubicación geográfica en la que se hospedará la cuenta.

6. Una vez que las nuevas opciones de la cuenta estén configuradas, haga clic en **Crear**.  La cuenta puede tardar unos minutos en crearse.

   Puede supervisar su progreso en el centro de notificaciones.  

   ![!Screen shot of the Notifications hub, showing that the DocumentDB account is being created5](./media/documentdb-create-mongodb-account/create-documentdb-mongodb-deployment-status.png)  

7. Para acceder a la nueva cuenta, haga clic en **DocumentDB (NoSQL)** en el menú izquierdo. En la lista de DocumentDB y DocumentDB normal con cuentas compatibles con el protocolo Mongo, haga clic en el nombre de la cuenta nueva.
8. Su cuenta de DocumentDB ahora está lista para usarse con la aplicación de MongoDB.

   ![Captura de pantalla de la hoja de cuenta predeterminada](./media/documentdb-create-mongodb-account/defaultaccountblade.png)

## <a name="next-steps"></a>Pasos siguientes
* Obtenga información acerca de cómo [conectar](documentdb-connect-mongodb-account.md) una cuenta de DocumentDB con soporte de protocolo para MongoDB.



<!--HONumber=Jan17_HO2-->


