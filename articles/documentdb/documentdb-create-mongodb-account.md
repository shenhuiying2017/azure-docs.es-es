---
redirect_url: https://docs.microsoft.com/azure/documentdb/documentdb-create-account
ROBOTS: NOINDEX, NOFOLLOW
ms.translationtype: Human Translation
ms.sourcegitcommit: 71fea4a41b2e3a60f2f610609a14372e678b7ec4
ms.openlocfilehash: 4b2ae4a8d3b93b5c27e180a5875ae9e90483068a
ms.contentlocale: es-es
ms.lasthandoff: 05/10/2017



---

# <a name="create-an-azure-cosmos-db-account-with-mongodb-api"></a>Creación de una cuenta de Azure Cosmos DB con MongoDB API
Las bases de datos de Azure Cosmos DB se pueden usar ahora como almacenes de datos para aplicaciones escritas para MongoDB. Para usar esta funcionalidad, necesita una cuenta de Azure y una cuenta de Azure Cosmos DB. Este tutorial le guía a través del proceso de creación de una cuenta de Azure Cosmos DB para su uso con aplicaciones de MongoDB. 

Puede crear una cuenta de Azure Cosmos DB compatible con MongoDB mediante Azure Portal o la CLI de Azure con plantillas de Azure Resource Manager. En este artículo se muestra cómo crear una cuenta de Azure Cosmos DB compatible con MongoDB mediante Azure Portal. Para crear una cuenta mediante la CLI de Azure con Azure Resource Manager, vea [Automate Azure Cosmos DB account management using Azure CLI 2.0](documentdb-automation-resource-manager-cli.md) (Automatización de la administración de cuentas de Azure DocumentDB con la CLI de Azure 2.0).

## <a name="prerequisite"></a>Requisito previo
Una cuenta de Azure. Si no tiene una cuenta de Azure, cree ahora una [cuenta de Azure gratuita](https://azure.microsoft.com/free/).
## <a name="create-an-azure-cosmos-db-account"></a>Creación de una cuenta de Azure Cosmos DB

1. En un explorador de Internet, inicie sesión en [Azure Portal](https://portal.azure.com).
2. En el panel de navegación izquierdo, haga clic en **Azure Cosmos DB**.

    ![Captura de pantalla de la navegación a la izquierda del Portal, resaltando la entrada de DocumentDB NoSQL](./media/documentdb-create-mongodb-account/portalleftnav.png)

3. O bien, haga clic en **Más servicios >**, escriba **DocumentDB** en la barra de búsqueda superior y haga clic en **Azure Cosmos DB**.

    ![Captura de pantalla de la hoja Más servicios, búsqueda de la entrada DocumentDB NoSQL](./media/documentdb-create-mongodb-account/more-services-search.PNG)

4. En la parte superior de la hoja **Azure Cosmos DB**, haga clic en **+ Agregar** en la barra de acciones superior.

    ![Captura de pantalla del botón Agregar en la hoja de recursos de Cosmos DB](./media/documentdb-create-mongodb-account/add-documentdb-account.png)

5. En la hoja **Azure Cosmos DB account** (Cuenta de Azure Cosmos DB), especifique la configuración que quiera para la cuenta.

   ![Captura de pantalla de la nueva hoja Azure Cosmos DB compatible con protocolo de MongoDB.](./media/documentdb-create-mongodb-account/create-documentdb-mongodb-account.PNG)

    - En el cuadro **Id.**, escriba un nombre para identificar la cuenta.  Cuando se valida el **Id.**, aparece una marca de verificación verde en el cuadro **Id**. El valor de **Id.** se convierte en el nombre de host dentro del URI. El **Id.** solo puede contener minúsculas, números y el carácter "-"; debe tener entre 3 y 50 caracteres. Tenga en cuenta que *documents.azure.com* se anexa al nombre del punto de conexión que elija; su resultado se convertirá en el punto de conexión de la cuenta.

    - Como **API**, seleccione **MongoDB**. Así especifica la API de comunicación que quiere usar para interactuar con la base de datos de Azure Cosmos DB.

    - En **Suscripción**, seleccione la suscripción de Azure que desee utilizar para la cuenta. Si la cuenta tiene una sola suscripción, dicha cuenta se selecciona de manera predeterminada.

    - En **Grupo de recursos**, seleccione o cree un grupo de recursos para la cuenta.  De forma predeterminada, se elegirá un grupo de recursos existente en la suscripción de Azure.  Sin embargo, puede elegir crear un nuevo grupo de recursos al que desea agregar la cuenta. Para más información, consulte [Uso del Portal de Azure para administrar los recursos de Azure](../azure-portal/resource-group-portal.md).

    - Use **Ubicación** para especificar la ubicación geográfica en la que se hospedará la cuenta.

6. Una vez que las nuevas opciones de la cuenta estén configuradas, haga clic en **Crear**.  La cuenta puede tardar unos minutos en crearse.

   Puede supervisar su progreso en el centro de notificaciones.  

   ![Captura de pantalla del centro de notificaciones en la que se muestra la cuenta de Azure Cosmos DB que se está creando](./media/documentdb-create-mongodb-account/create-documentdb-mongodb-deployment-status.png)  

7. Para acceder a la nueva cuenta, haga clic en **Azure Cosmos DB** en el menú izquierdo. En la lista de DocumentDB y DocumentDB normal con cuentas compatibles con el protocolo Mongo, haga clic en el nombre de la cuenta nueva.
8. La cuenta de Azure Cosmos DB ya está lista para usarse con la aplicación de MongoDB.

   ![Captura de pantalla de la hoja de cuenta predeterminada](./media/documentdb-create-mongodb-account/defaultaccountblade.png)

## <a name="next-steps"></a>Pasos siguientes
* Obtenga información acerca de cómo [conectar](documentdb-connect-mongodb-account.md) una cuenta de DocumentDB con soporte de protocolo para MongoDB.

