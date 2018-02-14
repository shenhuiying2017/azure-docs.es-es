---
title: "Introducción al Explorador de Storage (versión preliminar) | Microsoft Docs"
description: "Administración de recursos de Azure Storage con el Explorador de Storage (versión preliminar)"
services: storage
documentationcenter: na
author: cawa
manager: paulyuk
editor: 
ms.assetid: 1ed0f096-494d-49c4-ab71-f4164ee19ec8
ms.service: storage
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/17/2017
ms.author: cawa
ms.openlocfilehash: d2b93eec9d3ac575e771bceb0ac45823254c142d
ms.sourcegitcommit: e19742f674fcce0fd1b732e70679e444c7dfa729
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 02/01/2018
---
# <a name="get-started-with-storage-explorer-preview"></a>Introducción al Explorador de Storage (versión preliminar)
## <a name="overview"></a>Información general
El Explorador de Azure Storage (versión preliminar) es una aplicación independiente que permite trabajar fácilmente con datos de Azure Storage en Windows, macOS y Linux. En este artículo aprenderá varias maneras de conectarse a cuentas de Azure Storage y de administrarlas.

![Explorador de Microsoft Azure Storage (versión preliminar)][0]

## <a name="prerequisites"></a>requisitos previos
* [Descargue e instale el Explorador de Storage (versión preliminar)](http://www.storageexplorer.com)

## <a name="connect-to-a-storage-account-or-service"></a>Conexión a una cuenta de almacenamiento o servicio
El Explorador de Storage (versión preliminar) proporciona varias maneras de conectar con las cuentas de almacenamiento. Por ejemplo, puede:
* Conectar con las cuentas de almacenamiento asociadas a las suscripciones de Azure.
* Conectar con las cuentas de almacenamiento y los servicios que se comparten desde otras suscripciones de Azure.
* Conectar con el almacenamiento local, y administrarlo, mediante el Emulador de Azure Storage. 

Además, puede trabajar con cuentas de almacenamiento en nubes de Azure globales y nacionales:

* [Conexión a una suscripción de Azure](#connect-to-an-azure-subscription): administre los recursos de almacenamiento que pertenecen a su suscripción de Azure.
* [Trabajo con el almacenamiento de desarrollo local](#work-with-local-development-storage): administre el almacenamiento local mediante el Emulador de Azure Storage.
* [Conexión a almacenamiento externo](#attach-or-detach-an-external-storage-account): administre los recursos de almacenamiento que pertenecen a otra suscripción de Azure o que se encuentran en nubes de Azure nacionales mediante el nombre, la clave y los puntos de conexión de la cuenta de almacenamiento.
* [Asociación de una cuenta de almacenamiento mediante una SAS](#attach-storage-account-using-sas): administre recursos de almacenamiento que pertenecen a otra suscripción de Azure mediante una firma de acceso compartido (SAS).
* [Asociación de un servicio mediante una SAS](#attach-service-using-sas): administre un servicio de almacenamiento específico (contenedor de blobs, cola o tabla) que pertenece a otra suscripción de Azure mediante una SAS.
* [Conexión a una cuenta de Azure Cosmos DB mediante una cadena de conexión](#connect-to-an-azure-cosmos-db-account-by-using-a-connection-string): administración de cuenta Cosmos DB mediante una cadena de conexión.

## <a name="connect-to-an-azure-subscription"></a>Conexión a una suscripción de Azure
> [!NOTE]
> Si aún no tiene ninguna cuenta de Azure, puede [registrarse para una evaluación gratuita](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A261C142F) o [activar las ventajas de suscriptor de Visual Studio](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F).
>
>

1. En el Explorador de Storage (versión preliminar), seleccione **Manage Accounts** (Administrar cuentas) para ir a **Account Management Panel** (Panel de administración de cuentas).

    ![Administración de cuentas][1]

2. En el panel izquierdo se muestran ahora todas las cuentas de Azure en las que ha iniciado sesión. Para conectarse a otra cuenta, seleccione **Add an account** (Agregar una cuenta).

3. Si quiere iniciar sesión en una nube nacional o en una instancia de Azure Stack, haga clic en la lista desplegable **Azure environment** (Entorno de Azure) para seleccionar qué nube de Azure quiere usar. Cuando haya elegido el entorno, haga clic en el botón **Sign in...** (Iniciar sesión...). Si va a iniciar sesión en Azure Stack, consulte [Conexión del Explorador de Storage a una suscripción de Azure Stack](azure-stack/user/azure-stack-storage-connect-se.md) para más información.

    ![Opción de inicio de sesión][2]

3. Después de iniciar sesión correctamente con una cuenta de Azure, la cuenta y las suscripciones de Azure asociadas con esa cuenta se agregan al panel izquierdo. Seleccione las suscripciones de Azure con las que quiere trabajar y, luego, seleccione **Apply** (Aplicar) (al seleccionar **All subscriptions:** (Todas las suscripciones:) se alterna entre seleccionar todas o ninguna de las suscripciones de Azure enumeradas).

    ![Selección de suscripciones de Azure][3]

    El panel izquierdo muestra ahora las cuentas de almacenamiento asociadas a las suscripciones de Azure seleccionadas.

    ![Suscripciones de Azure seleccionadas][4]

## <a name="work-with-local-development-storage"></a>Trabajo con el almacenamiento de desarrollo local
Con el Explorador de Storage (versión preliminar), puede trabajar con el almacenamiento local mediante el Emulador de Azure Storage. Este enfoque le permite simular el trabajo con Azure Storage sin necesidad de tener una cuenta de almacenamiento implementada en Azure, ya que el Emulador de Azure Storage emula dicha cuenta.

> [!NOTE]
> El Emulador de Azure Storage actualmente solo se admite para Windows.
>
>

> [!NOTE]
> No admite recursos compartidos de archivos.
>
>

1. En el panel izquierdo del Explorador de Storage (versión preliminar), expanda el nodo **(Local and Attached)** >  (Local y asociada) **Storage Accounts** >  (Cuentas de almacenamiento) **(Development)** >  (Desarrollo) **Blob Containers** (Contenedores de blobs).

    ![Nodo de desarrollo local][5]

2. Si aún no ha instalado el Emulador de Azure Storage, se le solicita que lo haga en una barra de información. Si se muestra la barra de información, seleccione **Descargar la última versión** e instale el emulador.

    ![Mensaje para descargar el emulador de Azure Storage][6]

3. Después de que el emulador se ha instalado, puede crear tablas, colas y blobs locales, y trabajar con ellos. Para aprender a trabajar con cada tipo de cuenta de almacenamiento, consulte las siguientes guías:

    * [Administración de recursos de Azure Blob Storage](vs-azure-tools-storage-explorer-blobs.md)
    * Administración de recursos de almacenamiento de recurso compartido de archivos de Azure; *próximamente*
    * Administración de recursos de Azure Queue Storage; *próximamente*
    * Administración de recursos de Azure Table Storage; *próximamente*

## <a name="attach-or-detach-an-external-storage-account"></a>Conexión a almacenamiento externo
Con el Explorador de Storage (versión preliminar), puede conectarse a cuentas de almacenamiento externas, lo que facilita el uso compartido de las cuentas de almacenamiento. En esta sección se explica cómo asociar (y desasociar) cuentas de almacenamiento externo.

### <a name="get-the-storage-account-credentials"></a>Obtención de las credenciales de la cuenta de almacenamiento
Para compartir una cuenta de almacenamiento externa, su propietario debe obtener primero las credenciales (nombre y clave de cuenta) de esta, y compartir dicha información con la persona que desea asociar a dicha cuenta. Puede obtener las credenciales de la cuenta de almacenamiento mediante Azure Portal siguiendo estos pasos:

1. Inicie sesión en el [Azure Portal](https://portal.azure.com).

2. Seleccione **Examinar**.

3. Seleccione **Cuentas de almacenamiento**.

4. En la lista de **Cuentas de almacenamiento**, seleccione la cuenta de almacenamiento deseada.

5. En **Configuración**, seleccione **Claves de acceso**.

    ![Opción de teclas de acceso][7]

6. Copie el **nombre de la cuenta de almacenamiento** y **key1**.

    ![Claves de acceso][8]

### <a name="attach-to-an-external-storage-account"></a>Asociación de una cuenta de almacenamiento externo
Para la asociación a una cuenta de almacenamiento externa, se necesitan el nombre y la clave de la misma. En la sección "Obtención de las credenciales de la cuenta de almacenamiento" se explica cómo obtener estos valores desde el portal de Azure. Sin embargo, en el portal, la clave de cuenta se denomina **key1**. Por tanto, cuando el Explorador de Storage (versión preliminar) solicite una clave de cuenta, escriba el valor **key1**.

1. En Explorador de Storage (versión preliminar), abra el **cuadro de diálogo de conexión**.

    ![Opción de conexión a Almacenamiento de Azure][9]

2. En el **cuadro de diálogo de conexión**, elija **Use a storage account name and key** (Usar un nombre y una clave de cuenta de almacenamiento).

    ![Opción para agregar con el nombre y la clave][10]

3. Pegue el nombre de la cuenta en el cuadro de texto **Account name** (Nombre de cuenta) y pegue la clave de la cuenta (el valor **key1** de Azure Portal) en el cuadro de texto **Account key** (Clave de cuenta) y luego seleccione **Next** (Siguiente).

    ![Página de nombre y clave][11]

    > [!NOTE]
    > Para usar un nombre y una clave de una nube nacional, use la lista desplegable **Storage endpoints domain:** (Dominio de puntos de conexión de almacenamiento) para seleccionar el dominio de puntos de conexión adecuado: 
    >
    >

4. En el cuadro de diálogo **Connection Summary** (Resumen de conexiones), compruebe la información. Si desea cambiar algo, seleccione **Atrás** y vuelva a escribir la configuración deseada. 

5. Seleccione **Conectar**.

6. Cuando la cuenta de almacenamiento se haya asociado correctamente, se muestra con **(External)** (Externa) anexado a su nombre.

    ![Resultado de la conexión a una cuenta de almacenamiento externo][12]

### <a name="detach-from-an-external-storage-account"></a>Desasociación de una cuenta de almacenamiento externo
1. Haga clic con el botón derecho en la cuenta de almacenamiento externa que desea desconectar y luego seleccione **Desconectar**.

    ![Opción para desasociar del almacenamiento][13]

2. En el mensaje de confirmación, seleccione **Sí** para confirmar la desasociación de la cuenta de almacenamiento externa.

## <a name="attach-a-storage-account-by-using-a-shared-access-signature-sas"></a>Asociación de una cuenta de almacenamiento mediante una firma de acceso compartido (SAS)
Una firma de acceso compartido, o [SAS](storage/common/storage-dotnet-shared-access-signature-part-1.md), permite al administrador de una suscripción de Azure conceder temporalmente acceso a una cuenta de almacenamiento sin tener que proporcionar credenciales de suscripción de Azure.

Para ilustrar este escenario, supongamos que el Usuario A es administrador de una suscripción de Azure y desea permitir que el Usuario B acceda a una cuenta de almacenamiento durante un tiempo limitado con determinados permisos:

1. El Usuario A genera una cadena de conexión de SAS durante un período específico y con los permisos deseados.

2. El Usuario A comparte esta cadena con la persona (Usuario B, en este ejemplo) que quiere acceder a la cuenta de almacenamiento.  

3. El Usuario B usa el Explorador de Storage (versión preliminar) para conectarse a la cuenta que pertenece al Usuario A, con la SAS proporcionada.

### <a name="generate-a-sas-connection-string-for-the-account-you-want-to-share"></a>Generación de una cadena de conexión de SAS para la cuenta que quiere compartir
1. En el Explorador de Storage (versión preliminar), haga clic con el botón derecho en la cuenta de almacenamiento que quiere compartir y seleccione **Get Shared Access Signature**(Obtener firma de acceso compartido).

    ![Opción de menú contextual para obtener la SAS][14]

2. En el cuadro de diálogo **Generate Shared Access Signature** (Generar firma de acceso compartido), especifique la franja de tiempo y los permisos que quiere para la cuenta y, luego, haga clic en el botón **Create** (Crear).

    ![Cuadro de diálogo Get SAS (Obtener SAS)][15]  

3. Junto al cuadro de texto **Connection String** (Cadena de conexión), seleccione **Copy** (Copiar) para copiarla en el portapapeles y, luego, haga clic en **Close** (Cerrar).

### <a name="attach-to-a-storage-account-by-using-a-sas-connection-string"></a>Asociación de una cuenta de almacenamiento mediante una cadena de conexión de SAS
1. En el Explorador de Storage (versión preliminar), abra el **cuadro de diálogo de conexión**.

    ![Opción de conexión a Almacenamiento de Azure][9]

2. En el **cuadro de diálogo de conexión**, elija **Use a connection string or shared access signature URI** (Usar una cadena de conexión o un URI de firma de acceso compartido) y, luego, haga clic en **Next** (Siguiente).

    ![Cuadro de diálogo Connect to Azure storage (Conectar con Azure Storage)][16]

3. Elija **Use a connection string** (Usar una cadena de conexión) y pegue la cadena de conexión en el campo **Connection string:** (Cadena de conexión). Haga clic en el botón **Next** (Siguiente).

    ![Cuadro de diálogo Connect to Azure storage (Conectar con Azure Storage)][17]

4. En el cuadro de diálogo **Connection Summary** (Resumen de conexiones), compruebe la información. Para realizar cambios, seleccione **Atrás** y luego escriba los valores de configuración deseados. 

5. Seleccione **Conectar**.

6. Cuando la cuenta de almacenamiento se ha asociado correctamente, se muestra con **(SAS)** anexado a su nombre.

    ![Resultado de conectarse a una cuenta mediante SAS][18]

## <a name="attach-a-service-by-using-a-shared-access-signature-sas"></a>Asociación de un servicio mediante una firma de acceso compartido (SAS)
En la sección "Asociación de una cuenta de almacenamiento mediante una SAS" se explica cómo el administrador de una suscripción de Azure puede conceder acceso temporal a una cuenta de almacenamiento mediante la generación y el uso compartido de una SAS para dicha cuenta. Igualmente, se puede generar una SAS para un servicio específico (contenedor de blobs, cola, tabla o recurso compartido de archivos) dentro de una cuenta de almacenamiento.  

### <a name="generate-an-sas-for-the-service-that-you-want-to-share"></a>Generación de una SAS para el servicio que se desea compartir
En este contexto, un servicio puede ser un contenedor de blobs, una cola, una tabla o un recurso compartido de archivos. Para generar la SAS para un servicio enumerado, consulte:

* [Get the SAS for a blob container (Obtención de la SAS para un contenedor de blobs)](vs-azure-tools-storage-explorer-blobs.md#get-the-sas-for-a-blob-container)
* Obtención de la SAS para un recurso compartido de archivos; *próximamente*
* Obtención de la SAS para una cola; *próximamente*
* Obtención de la SAS para una tabla; *próximamente*

### <a name="attach-to-the-shared-account-service-by-using-a-sas-uri"></a>Asociación del servicio de cuenta compartida mediante un URI de SAS
1. En el Explorador de Storage (versión preliminar), abra el **cuadro de diálogo de conexión**.

    ![Opción de conexión a Almacenamiento de Azure][9]

2. En el **cuadro de diálogo de conexión**, elija **Use a connection string or shared access signature URI** (Usar una cadena de conexión o un URI de firma de acceso compartido) y, luego, haga clic en **Next** (Siguiente).

    ![Cuadro de diálogo Connect to Azure storage (Conectar con Azure Storage)][16]

3. Elija **Use a SAS URI** (Usar un URI de SAS) y pegue el URI en el campo **URI:**. Haga clic en el botón **Next** (Siguiente).

    ![Cuadro de diálogo Connect to Azure storage (Conectar con Azure Storage)][19]

3. En el cuadro de diálogo **Connection Summary** (Resumen de conexiones), compruebe la información. Para realizar cambios, seleccione **Atrás** y luego escriba los valores de configuración deseados. 

4. Seleccione **Conectar**.

5. Después de que el servicio se ha asociado correctamente, se muestra en el nodo **(SAS-Attached Services)** (Servicios asociados mediante SAS).

    ![Resultado de la conexión a un servicio compartido mediante una SAS][20]

## <a name="connect-to-an-azure-cosmos-db-account-by-using-a-connection-string"></a>Conexión a una cuenta de Azure Cosmos DB mediante una cadena de conexión
Además de administrar cuentas de Azure Cosmos DB a través de una suscripción de Azure, una manera alternativa de conectarse a Azure Cosmos DB es usar una cadena de conexión. Siga estos pasos para conectarse usando una cadena de conexión.

1. Busque **Local and Attached** (Locales y adjuntas) en el árbol de la izquierda, haga clic con el botón derecho en **Cuentas de Azure Cosmos DB**, elija **Connect to Azure Cosmos DB...** (Conectar a Azure Cosmos DB).

    ![Conexión a Azure Cosmos DB mediante una cadena de conexión][21]

2. Elija la API de Azure Cosmos DB, pegue su **cadena de conexión** y, a continuación, haga clic en **Aceptar** para conectarse a la cuenta de Azure Cosmos DB. Para más información sobre cómo recuperar la cadena de conexión, vea [Obtener la cadena de conexión](https://docs.microsoft.com/azure/cosmos-db/manage-account#get-the--connection-string).

    ![connection-string][22]

## <a name="search-for-storage-accounts"></a>Búsqueda de cuentas de almacenamiento
Si necesita encontrar un recurso de almacenamiento y no sabe dónde está, puede usar el cuadro de búsqueda en la parte superior del panel izquierdo para buscar el recurso.

A medida que escribe en el cuadro de búsqueda, en el panel izquierdo se muestran todos los recursos que coinciden con el valor de búsqueda especificado hasta ese punto. Por ejemplo, en la siguiente captura de pantalla se muestra una búsqueda de **puntos de conexión**:

![Búsqueda de cuenta de almacenamiento][23]

> [!NOTE]
> Use **Account Management Panel** (Panel de administración de cuentas) para deseleccionar las suscripciones que no contengan el elemento que busca para mejorar el tiempo de ejecución de la búsqueda. También puede hacer clic con el botón derecho en un nodo y seleccionar **Search From Here** (Buscar desde aquí) para comenzar a buscar desde un nodo específico.
>
>

## <a name="next-steps"></a>pasos siguientes
* [Administración de recursos de Azure Blob Storage con el Explorador de Storage (versión preliminar)](vs-azure-tools-storage-explorer-blobs.md)
* [Administración de Azure Cosmos DB en el Explorador de Azure Storage (versión preliminar)](./cosmos-db/storage-explorer.md)

[0]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/Overview.png
[1]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/ManageAccounts.png
[2]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/ConnectDialog-SignInSelected.png
[3]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/AccountPanel.png
[4]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/SubscriptionNode.png
[5]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/DevelopmentNode.png
[6]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/EmulatorNotInstalled.png
[7]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/PortalAccessKeys.png
[8]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/AccessKeys.png
[9]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/ConnectDialog.png
[10]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/ConnectDialog-AddWithKeySelected.png
[11]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/ConnectDialog-NameAndKeyPage.png
[12]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/AttachedWithKeyAccount.png
[13]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/AttachedWithKeyAccount-Detach.png
[14]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/GetSharedAccessSignature.png
[15]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/SharedAccessSignatureDialog.png
[16]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/ConnectDialog-WithConnStringOrSASSelected.png
[17]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/ConnectDialog-ConnStringOrSASPage-1.png
[18]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/AttachedWithSASAccount.png
[19]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/ConnectDialog-ConnStringOrSASPage-2.png
[20]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/ServiceAttachedWithSAS.png
[21]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/connect-to-db-by-connection-string.png
[22]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/connection-string.png
[23]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/Search.png
