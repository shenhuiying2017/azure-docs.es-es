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
ms.openlocfilehash: cccab530e86373fee8a78b42c8cba532b05c1bab
ms.sourcegitcommit: 5ac112c0950d406251551d5fd66806dc22a63b01
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/23/2018
---
# <a name="get-started-with-storage-explorer-preview"></a>Introducción al Explorador de Storage (versión preliminar)
## <a name="overview"></a>Información general
El Explorador de Azure Storage (versión preliminar) es una aplicación independiente que permite trabajar fácilmente con datos de Azure Storage en Windows, macOS y Linux. En este artículo aprenderá las diferentes maneras de conectarse a cuentas de Azure Storage y de administrarlas.

![Explorador de Microsoft Azure Storage (versión preliminar)][15]

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

1. En el Explorador de Storage (versión preliminar), seleccione **Configuración de la cuenta de Azure**.

    ![Configuración de la cuenta de Azure][0]

2. En el panel izquierdo se muestran todas las cuentas de Microsoft en las que ha iniciado sesión. Para conectarse a otra cuenta, seleccione **Agregar una cuenta** y siga las instrucciones para iniciar sesión con una cuenta de Microsoft que esté asociada a una suscripción activa de Azure por lo menos.

    >[!NOTE]
    >Actualmente no se admite la conexión a una nube de Azure nacional (como Azure Alemania, Azure Government y Azure China mediante inicio de sesión). Consulte la sección "Conexión a almacenamiento externo" para saber cómo conectarse a cuentas de Azure Storage nacionales.

3. Después de haber iniciado sesión correctamente con una cuenta de Microsoft, el panel izquierdo se llenará con las suscripciones de Azure asociadas a dicha cuenta. Elija las suscripciones de Azure con las que desea trabajar y luego seleccione **Aplicar**. (Al seleccionar **Todas las suscripciones** , se alterna entre todas o ninguna de las suscripciones de Azure que aparecen).

    ![Selección de suscripciones de Azure][3]  
    El panel izquierdo muestra ahora las cuentas de almacenamiento asociadas a las suscripciones de Azure seleccionadas.

    ![Suscripciones de Azure seleccionadas][4]

## <a name="connect-to-an-azure-stack-subscription"></a>Conexión a una suscripción de Azure Stack

Para más información sobre cómo conectarse a una suscripción de Azure Stack, consulte [Conexión de Storage Explorer a una suscripción de Azure Stack](azure-stack/user/azure-stack-storage-connect-se.md).

## <a name="work-with-local-development-storage"></a>Trabajo con el almacenamiento de desarrollo local
Con el Explorador de Storage (versión preliminar), puede trabajar con el almacenamiento local mediante el Emulador de Azure Storage. Este enfoque le permite escribir código y realizar pruebas de almacenamiento sin necesidad de tener una cuenta de almacenamiento implementada en Azure (ya que el Emulador de Azure Storage emula dicha cuenta).

> [!NOTE]
> El Emulador de Azure Storage actualmente solo se admite para Windows.
>
>

1. En el panel izquierdo del Explorador de Storage (versión preliminar), expanda el nodo **Local and Attached (Local y asociados)** > **Cuentas de almacenamiento** > **(Desarrollo)**.

    ![Nodo de desarrollo local][21]

2. Si aún no ha instalado el Emulador de Azure Storage, se le solicita que lo haga a través de una barra de información. Si se muestra la barra de información, seleccione **Descargar la última versión** e instale el emulador.

    ![Mensaje para descargar el emulador de Azure Storage][22]

3. Después de que el emulador se ha instalado, puede crear tablas, colas y blobs locales, y trabajar con ellos. Para aprender a trabajar con cada tipo de cuenta de almacenamiento, seleccione uno de los siguientes vínculos:

    * [Administración de recursos de Azure Blob Storage](vs-azure-tools-storage-explorer-blobs.md)
    * Administración de recursos de almacenamiento de recurso compartido de archivos de Azure; *próximamente*
    * Administración de recursos de Azure Queue Storage; *próximamente*
    * Administración de recursos de Azure Table Storage; *próximamente*

## <a name="attach-or-detach-an-external-storage-account"></a>Conexión a almacenamiento externo
Con el Explorador de Storage (versión preliminar), puede conectarse a cuentas de almacenamiento externas, lo que facilita el uso compartido de las cuentas de almacenamiento. En esta sección se explica cómo asociar (y desasociar) cuentas de almacenamiento externo.

### <a name="get-the-storage-account-credentials"></a>Obtención de las credenciales de la cuenta de almacenamiento
Para compartir una cuenta de almacenamiento externa, su propietario debe obtener primero las credenciales (nombre y clave de cuenta) de esta, y compartir dicha información con la persona que desea asociar a dicha cuenta (externa). Puede obtener las credenciales de la cuenta de almacenamiento mediante el portal de Azure haciendo lo siguiente:

1. Inicie sesión en el [Azure Portal](https://portal.azure.com).

2. Seleccione **Examinar**.

3. Seleccione **Cuentas de almacenamiento**.

4. En la hoja **Cuentas de almacenamiento**, seleccione la cuenta de almacenamiento deseada.

5. En la hoja **Configuración** de la cuenta de almacenamiento elegida, seleccione **Claves de acceso**.

    ![Opción de teclas de acceso][5]

6. En la hoja **Claves de acceso**, copie los valores de **Nombre de cuenta de almacenamiento** y **key1** para usarlos en la conexión a la cuenta de almacenamiento.

    ![Claves de acceso][6]

### <a name="attach-to-an-external-storage-account"></a>Asociación de una cuenta de almacenamiento externo
Para la asociación a una cuenta de almacenamiento externa, se necesitan el nombre y la clave de la misma. En la sección "Obtención de las credenciales de la cuenta de almacenamiento" se explica cómo obtener estos valores desde el portal de Azure. Sin embargo, en el portal, la clave de cuenta se denomina **key1**. Por tanto, cuando el Explorador de Storage (versión preliminar) solicite una clave de cuenta, escriba el valor **key1**.

1. En el Explorador de Storage (versión preliminar), seleccione **Connect to Azure storage** (Conectar con Azure Storage).

    ![Opción de conexión a Almacenamiento de Azure][23]

2. En el cuadro de diálogo **Connect to Azure storage** (Conectar con Azure Storage), especifique la clave de cuenta (el valor **key1** del portal de Azure) y seleccione **Siguiente**.

    > [!NOTE]
    > Puede escribir la cadena de conexión de almacenamiento desde una cuenta de almacenamiento en una nube de Azure nacional. Por ejemplo, para conectarse a las cuentas de almacenamiento de Azure Alemania, escriba cadenas de conexión similares a las siguientes: 
    >
    >* DefaultEndpointsProtocol=https
    >* AccountName=cawatest03
    >* AccountKey=<storage_account_key>
    >* EndpointSuffix=core.cloudapi.de
    
    >Puede obtener la cadena de conexión desde el portal de Azure tal y como se describe en la sección "Obtención de las credenciales de la cuenta de almacenamiento".

    ![Cuadro de diálogo Connect to Azure storage (Conectar con Azure Storage)][24]

3. En el cuadro de diálogo **Attach External Storage** (Asociar almacenamiento externo), escriba el nombre de la cuenta de almacenamiento en el cuadro **Nombre de la cuenta**, especifique los demás valores deseados y luego seleccione **Siguiente**.

    ![Cuadro de diálogo Attach External Storage (Asociar almacenamiento externo)][8]

4. En el cuadro de diálogo **Connection Summary** (Resumen de conexiones), compruebe la información. Si desea cambiar algo, seleccione **Atrás** y vuelva a escribir la configuración deseada. 

5. Seleccione **Conectar**.

6. Una vez conectado correctamente, se muestra la cuenta de almacenamiento externa con el texto **(Externa)** junto al nombre de esta.

    ![Resultado de la conexión a una cuenta de almacenamiento externo][9]

### <a name="detach-from-an-external-storage-account"></a>Desasociación de una cuenta de almacenamiento externo
1. Haga clic con el botón derecho en la cuenta de almacenamiento externa que desea desconectar y luego seleccione **Desconectar**.

    ![Opción para desasociar del almacenamiento][10]

2. En el mensaje de confirmación, seleccione **Sí** para confirmar la desasociación de la cuenta de almacenamiento externa.

## <a name="attach-a-storage-account-by-using-an-sas"></a>Asociación de una cuenta de almacenamiento mediante una SAS
Una [SAS](storage/common/storage-dotnet-shared-access-signature-part-1.md) permite al administrador de una suscripción de Azure conceder acceso temporal a una cuenta de almacenamiento sin tener que proporcionar las credenciales de suscripción de Azure.

Para ilustrar este escenario, supongamos que el Usuario A es administrador de una suscripción de Azure y desea permitir que el Usuario B acceda a una cuenta de almacenamiento durante un tiempo limitado con determinados permisos:

1. El Usuario A genera una SAS (que consiste en una cadena de conexión para la cuenta de almacenamiento) durante un período de tiempo específico y con los permisos deseados.

2. El Usuario A comparte la SAS con la persona que desea tener acceso a la cuenta de almacenamiento (en nuestro ejemplo el Usuario B).  

3. El Usuario B usa el Explorador de Storage (versión preliminar) para conectarse a la cuenta que pertenece al Usuario A, con la SAS proporcionada.

### <a name="get-an-sas-for-the-account-you-want-to-share"></a>Obtención de una SAS para la cuenta que se desea compartir
1. En el Explorador de Storage (versión preliminar), haga clic con el botón derecho en la cuenta de almacenamiento que quiere compartir y seleccione **Get Shared Access Signature**(Obtener firma de acceso compartido).

    ![Opción de menú contextual para obtener la SAS][13]

2. En el cuadro de diálogo **Firma de acceso compartido**, especifique el período y los permisos que desee para la cuenta y luego seleccione **Crear**.

    ![Cuadro de diálogo Get SAS (Obtener SAS)][14]  
    Se abre el cuadro de diálogo **Firma de acceso compartido**, que muestra la SAS.

3. Junto a la **cadena de conexión**, seleccione **Copiar** para copiarla en el Portapapeles y, a continuación, seleccione **Cerrar**.

### <a name="attach-to-the-shared-account-by-using-the-sas"></a>Conexión a la cuenta compartida mediante la SAS
1. En el Explorador de Storage (versión preliminar), seleccione **Connect to Azure storage** (Conectar con Azure Storage).

    ![Opción de conexión a Almacenamiento de Azure][23]

2. En el cuadro de diálogo **Connect to Azure storage** (Conectar con Azure Storage), especifique la cadena de conexión y seleccione **Siguiente**.

    ![Cuadro de diálogo Connect to Azure storage (Conectar con Azure Storage)][24]

3. En el cuadro de diálogo **Connection Summary** (Resumen de conexiones), compruebe la información. Para realizar cambios, seleccione **Atrás** y luego escriba los valores de configuración deseados. 

4. Seleccione **Conectar**.

5. Después de conectarse, la cuenta de almacenamiento se muestra con **(SAS)** anexada al nombre de cuenta que ha proporcionado.

    ![Resultado de conectarse a una cuenta mediante SAS][17]

## <a name="attach-a-service-by-using-an-sas"></a>Asociación de un servicio mediante una SAS
En la sección "Asociación de una cuenta de almacenamiento mediante una SAS" se explica cómo el administrador de una suscripción de Azure puede conceder acceso temporal a una cuenta de almacenamiento mediante la generación y el uso compartido de una SAS para dicha cuenta. De igual forma, se puede generar una SAS para un servicio específico (contenedor de blobs, cola o tabla) dentro de una cuenta de almacenamiento.  

### <a name="generate-an-sas-for-the-service-that-you-want-to-share"></a>Generación de una SAS para el servicio que se desea compartir
En este contexto, un servicio puede ser un contenedor de blobs, una cola o una tabla. Para generar la SAS para un servicio enumerado, consulte:

* [Get the SAS for a blob container (Obtención de la SAS para un contenedor de blobs)](vs-azure-tools-storage-explorer-blobs.md#get-the-sas-for-a-blob-container)
* Obtención de la SAS para un recurso compartido de archivos; *próximamente*
* Obtención de la SAS para una cola; *próximamente*
* Obtención de la SAS para una tabla; *próximamente*

### <a name="attach-to-the-shared-account-service-by-using-the-sas"></a>Conexión al servicio de la cuenta de almacenamiento mediante la SAS
1. En el Explorador de Storage (versión preliminar), seleccione **Connect to Azure storage** (Conectar con Azure Storage).

    ![Opción de conexión a Almacenamiento de Azure][23]

2. En el cuadro de diálogo **Connect to Azure storage** (Conectar con Azure Storage), especifique el identificador URI de SAS y luego seleccione **Siguiente**.

    ![Cuadro de diálogo Connect to Azure storage (Conectar con Azure Storage)][24]

3. En el cuadro de diálogo **Connection Summary** (Resumen de conexiones), compruebe la información. Para realizar cambios, seleccione **Atrás** y luego escriba los valores de configuración deseados. 

4. Seleccione **Conectar**.

5. Una vez conectado, el servicio aparecerá en el nodo **(Service SAS)** [(SAS de servicio)].

    ![Resultado de la conexión a un servicio compartido mediante una SAS][20]

## <a name="connect-to-an-azure-cosmos-db-account-by-using-a-connection-string"></a>Conexión a una cuenta de Azure Cosmos DB mediante una cadena de conexión
Además de administrar cuentas de Azure Cosmos DB a través de una suscripción de Azure, una manera alternativa de conectarse a Azure Cosmos DB es usar una cadena de conexión. Siga estos pasos para conectarse usando una cadena de conexión.

1. Busque **Local and Attached** (Locales y adjuntas) en el árbol de la izquierda, haga clic con el botón derecho en **Cuentas de Azure Cosmos DB**, elija **Connect to Azure Cosmos DB...** (Conectar a Azure Cosmos DB).

    ![Conexión a Azure Cosmos DB mediante una cadena de conexión][33]

2. Elija la API de Azure Cosmos DB, pegue su **cadena de conexión** y, a continuación, haga clic en **Aceptar** para conectarse a la cuenta de Azure Cosmos DB. Para más información sobre cómo recuperar la cadena de conexión, vea [Obtener la cadena de conexión](https://docs.microsoft.com/azure/cosmos-db/manage-account#get-the--connection-string).

    ![connection-string][32]

## <a name="search-for-storage-accounts"></a>Búsqueda de cuentas de almacenamiento
Si tiene una larga lista de cuentas de almacenamiento, puede usar el cuadro de búsqueda en la parte superior del panel izquierdo para encontrar rápidamente una cuenta de almacenamiento concreta.

A medida que vaya escribiendo en el cuadro de búsqueda, el panel izquierdo mostrará solo las cuentas de almacenamiento que coincidan con el valor de búsqueda que haya escrito hasta ese momento. Por ejemplo, en la pantalla siguiente se muestra una búsqueda de todas las cuentas de almacenamiento cuyo nombre contenga **tarcher**:

![Búsqueda de cuenta de almacenamiento][11]

## <a name="next-steps"></a>pasos siguientes
* [Administración de recursos de Azure Blob Storage con el Explorador de Storage (versión preliminar)](vs-azure-tools-storage-explorer-blobs.md)
* [Administración de Azure Cosmos DB en el Explorador de Azure Storage (versión preliminar)](./cosmos-db/storage-explorer.md)

[0]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/settings-icon.png
[1]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/add-account-link.png
[3]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/subscriptions-list.png
[4]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/storage-accounts-list.png
[5]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/access-keys.png
[6]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/access-keys-copy.png
[8]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/attach-external-storage-dlg.png
[9]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/external-storage-account.png
[10]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/detach-external-storage.png
[11]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/storage-account-search.png
[12]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/detach-external-storage-confirmation.png
[13]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/get-sas-context-menu.png
[14]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/get-sas-dlg1.png
[15]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/mase.png
[17]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/attach-account-using-sas-finished.png
[20]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/attach-service-using-sas-finished.png
[21]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/local-storage-drop-down.png
[22]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/download-storage-emulator.png
[23]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/connect-to-azure-storage-icon.png
[24]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/connect-to-azure-storage-next.png
[25]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/add-certificate-azure-stack.png
[26]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/export-root-cert-azure-stack.png
[27]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/import-azure-stack-cert-storage-explorer.png
[28]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/select-target-azure-stack.png
[29]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/add-azure-stack-account.png
[30]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/select-accounts-azure-stack.png
[31]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/azure-stack-storage-account-list.png
[32]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/connection-string.PNG
[33]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/connect-to-db-by-connection-string.PNG
