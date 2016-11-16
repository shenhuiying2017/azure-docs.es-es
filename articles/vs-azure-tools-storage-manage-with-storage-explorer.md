---
title: "Introducción al Explorador de almacenamiento (versión preliminar) | Microsoft Docs"
description: "Administración de recursos de almacenamiento de Azure con el Explorador de almacenamiento (versión preliminar)"
services: storage
documentationcenter: na
author: TomArcher
manager: douge
editor: 
ms.assetid: 1ed0f096-494d-49c4-ab71-f4164ee19ec8
ms.service: storage
ms.devlang: multiple
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/17/2016
ms.author: tarcher
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: d5c003c9240d8625d2921666e1361ce0512138bb


---
# <a name="getting-started-with-storage-explorer-preview"></a>Introducción al Explorador de almacenamiento (versión preliminar)
## <a name="overview"></a>Información general
La versión preliminar del Explorador de almacenamiento de Microsoft Azure es una aplicación independiente que permite trabajar fácilmente con los datos de Almacenamiento de Azure en Windows, OS X y Linux. En este artículo aprenderá las diferentes maneras de conectarse a sus cuentas de Almacenamiento de Azure y de administrarlas.

![Explorador de almacenamiento de Microsoft Azure (versión preliminar)][15]

## <a name="prerequisites"></a>Requisitos previos
* [Descargue e instale el Explorador de almacenamiento (versión preliminar)](http://www.storageexplorer.com)

## <a name="connect-to-a-storage-account-or-service"></a>Conexión a una cuenta de almacenamiento o servicio
El Explorador de almacenamiento (versión preliminar) proporciona innumerables formas de conectar con las cuentas de almacenamiento. Esto incluye la conexión a cuentas de almacenamiento asociadas a las suscripciones de Azure, la conexión a cuentas de almacenamiento y servicios compartidos desde otras suscripciones de Azure e incluso la conexión al almacenamiento local y su administración mediante el Emulador de almacenamiento de Azure:

* [Conexión a una suscripción de Azure](#connect-to-an-azure-subscription) : administre recursos de almacenamiento que pertenezcan a su suscripción de Azure.
* [Trabajo con el almacenamiento de desarrollo local](#work-with-local-development-storage) : administre el almacenamiento local mediante el Emulador de almacenamiento de Azure. 
* [Conexión a almacenamiento externo](#attach-or-detach-an-external-storage-account) : administre los recursos de almacenamiento que pertenezcan a otra suscripción de Azure utilizando el nombre de cuenta y la clave de la cuenta de almacenamiento.
* [Conexión a una cuenta de almacenamiento mediante SAS](#attach-storage-account-using-sas) : administre los recursos de almacenamiento que pertenezcan a otra suscripción de Azure mediante una SAS.
* [Conexión de servicio mediante SAS](#attach-service-using-sas) : administre un servicio de almacenamiento específico (contenedor de blobs, cola o tabla) que pertenezca a otra suscripción de Azure mediante una SAS.

## <a name="connect-to-an-azure-subscription"></a>Conexión a una suscripción de Azure
> [!NOTE]
> Si aún no tiene ninguna cuenta de Azure, puede [registrarse para una evaluación gratuita](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A261C142F) o [activar las ventajas de suscriptor de Visual Studio](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F).
> 
> 

1. En el Explorador de almacenamiento (versión preliminar), seleccione **Configuración de la cuenta de Azure**. 
   
    ![Configuración de la cuenta de Azure][0]
2. Ahora, el panel izquierdo mostrará todas las cuentas de Microsoft en las que ha iniciado sesión. Para conectarse a otra cuenta, seleccione **Agregar una cuenta**y siga los cuadros de diálogo para iniciar sesión con una cuenta de Microsoft que esté asociada con al menos una suscripción activa de Azure.
   
    ![Agregar una cuenta][1]
3. Una vez que haya iniciado sesión correctamente con una cuenta de Microsoft, el panel izquierdo se llenará con las suscripciones de Azure asociadas a esa cuenta. Elija las suscripciones de Azure con las que desea trabajar y seleccione **Aplicar**. (Al seleccionar **Todas las suscripciones** , se alterna entre todas o ninguna de las suscripciones de Azure que aparecen).
   
    ![Selección de suscripciones de Azure][3]
4. El panel izquierdo mostrará ahora las cuentas de almacenamiento asociadas a las suscripciones de Azure seleccionadas.
   
    ![Suscripciones de Azure seleccionadas][4]

## <a name="work-with-local-development-storage"></a>Trabajo con el almacenamiento de desarrollo local
El Explorador de almacenamiento (versión preliminar) le permite trabajar con almacenamiento local mediante el Emulador de almacenamiento de Azure. Esto le permite escribir código y realizar pruebas de almacenamiento sin necesidad de tener una cuenta de almacenamiento implementada en Azure (ya que el Emulador de almacenamiento de Azure emula dicha cuenta).

> [!NOTE]
> El Emulador de almacenamiento de Azure actualmente solo se admite para Windows. 
> 
> 

1. En el panel izquierdo del Explorador de almacenamiento (versión preliminar), expanda el nodo **Local and Attached (Local y asociados)** > **Cuentas de almacenamiento** > **(Desarrollo)**.
   
    ![Nodo de desarrollo local][21]
2. Si aún no ha instalado el Emulador de almacenamiento de Azure, se solicitará que lo haga a través de una barra de información. Si se muestra la barra de información, seleccione **Descargar la última versión**e instale el emulador. 
   
    ![Mensaje para descargar el emulador de Almacenamiento de Azure][22]
3. Una vez instalado el emulador,podrá crear y trabajar con tablas, colas y blobs locales. Para informarse sobre cómo trabajar con cada tipo de cuenta de almacenamiento, seleccione a continuación el vínculo apropiado:
   
   * [Administración de recursos de Azure Blob Storage](vs-azure-tools-storage-explorer-blobs.md)
   * Administración de recursos de Almacenamiento de recurso compartido de archivos de Azure; *próximamente*
   * Administración de recursos de Almacenamiento en cola de Azure; *próximamente*
   * Administración de recursos de Almacenamiento de tablas de Azure; *próximamente*

## <a name="attach-or-detach-an-external-storage-account"></a>Conexión a almacenamiento externo
El Explorador de almacenamiento (versión preliminar) permite conectar cuentas de almacenamiento externo, lo que facilita el uso compartido de las cuentas de almacenamiento. En esta sección se explica cómo asociar (y desasociar) cuentas de almacenamiento externo.

### <a name="get-the-storage-account-credentials"></a>Obtención de las credenciales de la cuenta de almacenamiento
Para compartir una cuenta de almacenamiento externo, el propietario de la misma tiene que obtener primero las credenciales (nombre de cuenta y clave) de la cuenta, y compartir esa información con la persona que desea asociar a esa cuenta (externa). La obtención de las credenciales de cuenta de almacenamiento puede realizarse a través del Portal de Azure siguiendo estos pasos: 

1. Inicie sesión en el [Portal de Azure](https://portal.azure.com).
2. Seleccione **Examinar**.
3. Seleccione **Cuentas de almacenamiento**.
4. En la hoja **Cuentas de almacenamiento** , seleccione la cuenta de almacenamiento deseada.
5. En la hoja **Configuración** de la cuenta de almacenamiento elegida, seleccione **Claves de acceso**.
   
   ![Opción de teclas de acceso][5]
6. En la hoja **Claves de acceso**, copie los valores **NOMBRE DE LA CUENTA DE ALMACENAMIENTO** y **CLAVE 1** para usarlos en la conexión a la cuenta de almacenamiento. 
   
   ![Claves de acceso][6]

### <a name="attach-to-an-external-storage-account"></a>Asociación de una cuenta de almacenamiento externo
Para la asociación a una cuenta de almacenamiento externo, necesitará el nombre y la clave de la cuenta. En la sección *Obtención de las credenciales de la cuenta de almacenamiento* se explica cómo obtener estos valores desde el Portal de Azure. Sin embargo, tenga en cuenta que, en el portal, la clave de cuenta se denomina "clave 1" por lo que en el Explorador de almacenamiento (versión preliminar), cuando se solicita una clave de cuenta, deberá escribir (o pegar) el valor de "clave 1". 

1. En el Explorador de almacenamiento (versión preliminar), seleccione **Connect to Azure storage**(Conectar con el almacenamiento de Azure).
   
   ![Opción de conexión a Almacenamiento de Azure][23]
2. En el cuadro de diálogo **Connect to Azure storage** (Conectar con Azure Storage), especifique la clave de cuenta (el valor de "clave 1" de Azure Portal) y seleccione **Siguiente**.
   
   ![Cuadro de diálogo de conexión a Almacenamiento de Azure][24] 
3. En el cuadro de diálogo **Asociar almacenamiento externo**, escriba el nombre de la cuenta de almacenamiento en el cuadro **Nombre de la cuenta**, especifique los demás valores deseados y seleccione **Siguiente**. 
   
   ![Cuadro de diálogo de conexión a Almacenamiento externo][8]
4. En el cuadro de diálogo **Resumen de conexiones** , compruebe la información. Si desea cambiar algo, seleccione **Atrás** y vuelva a escribir la configuración deseada. Cuando haya terminado, seleccione **Conectar**.
5. Una vez conectado, se mostrará la cuenta de almacenamiento externo con el texto **(Externa)** junto al nombre de la cuenta. 
   
   ![Resultado de la conexión a una cuenta de almacenamiento externo][9]

### <a name="detach-from-an-external-storage-account"></a>Desasociación de una cuenta de almacenamiento externo
1. Haga clic con el botón derecho en la cuenta de almacenamiento externa que desea desasociar y, en el menú contextual, seleccione **Desconectar**.
   
   ![Opción para desasociar del almacenamiento][10]
2. Cuando aparezca el cuadro del mensaje de confirmación, seleccione **Sí** para confirmar la desasociación de la cuenta de almacenamiento externa.

## <a name="attach-storage-account-using-sas"></a>Conexión a una cuenta de almacenamiento mediante SAS
Una [SAS (firma de acceso compartido)](storage/storage-dotnet-shared-access-signature-part-1.md) proporciona al administrador de una suscripción de Azure la posibilidad de conceder acceso a una cuenta de almacenamiento de forma temporal, sin tener que proporcionar sus credenciales de suscripción de Azure. 

Para ilustrar este punto, supongamos que el Usuario A es administrador de una suscripción de Azure que desea permitir al Usuario B el acceso a una cuenta de almacenamiento durante un tiempo limitado con determinados permisos:

1. El Usuario A genera una SAS (que consiste en una cadena de conexión para la cuenta de almacenamiento) durante un período de tiempo específico y con los permisos deseados.
2. El Usuario A comparte la SAS con la persona que desea tener acceso a la cuenta de almacenamiento (en nuestro ejemplo el Usuario B).  
3. El Usuario B utiliza el Explorador de almacenamiento (versión preliminar) para asociarse a la cuenta de la que es propietario el Usuario A, usando la SAS proporcionada. 

### <a name="get-a-sas-for-the-account-you-want-to-share"></a>Obtención de una SAS para la cuenta que se desea compartir
1. En el Explorador de almacenamiento (versión preliminar), haga clic con el botón derecho en la cuenta de almacenamiento que desea compartir y, en el menú contextual, seleccione **Get Shared Access Signature**(Obtener firma de acceso compartido).
   
   ![Opción de menú contextual para obtener la SAS][13]
2. En el cuadro de diálogo **Firma de acceso compartido**, especifique el período y los permisos que desee para la cuenta y seleccione **Crear**.
   
    ![Cuadro de diálogo para obtener la SAS][14]
3. Aparecerá un segundo cuadro de diálogo **Firma de acceso compartido** , que muestra la SAS. Seleccione **Copiar** junto a la **Cadena de conexión** para copiarla en el Portapapeles. Seleccione **Cerrar** para salir del cuadro de diálogo.

### <a name="attach-to-the-shared-account-using-the-sas"></a>Asociación a la cuenta compartida con SAS
1. En el Explorador de almacenamiento (versión preliminar), seleccione **Connect to Azure storage**(Conectar con el almacenamiento de Azure).
   
   ![Opción de conexión a Almacenamiento de Azure][23]
2. En el cuadro de diálogo **Connect to Azure storage** (Conectar con Azure Storage), especifique la cadena de conexión y seleccione **Siguiente**.
   
   ![Cuadro de diálogo de conexión a Almacenamiento de Azure][24] 
3. En el cuadro de diálogo **Resumen de conexiones** , compruebe la información. Si desea cambiar algo, seleccione **Atrás** y vuelva a escribir la configuración deseada. Cuando haya terminado, seleccione **Conectar**.
4. Una vez se haya realizado la asociación, se mostrará la cuenta de almacenamiento externo con el texto (SAS) anexado al nombre de cuenta que ha proporcionado.
   
   ![Resultado de asociación a una cuenta mediante SAS][17]

## <a name="attach-service-using-sas"></a>Conexión de servicio mediante SAS
La sección [Conexión a una cuenta de almacenamiento mediante SAS](#attach-storage-account-using-sas) muestra cómo un administrador de suscripciones de Azure puede conceder acceso temporal a una cuenta de almacenamiento al generar (y compartir) una SAS para la cuenta de almacenamiento. De forma similar, se puede generar una SAS para un servicio específico (contenedor de blobs, cola o tabla) dentro de una cuenta de almacenamiento.  

### <a name="generate-a-sas-for-the-service-you-want-to-share"></a>Generación de una SAS para el servicio que se desea compartir
En este contexto, un servicio puede ser un contenedor de blobs, una cola o una tabla. Las siguientes secciones explican cómo generar la SAS para el servicio de lista:

* [Get the SAS for a blob container (Obtención de la SAS para un contenedor de blobs)](vs-azure-tools-storage-explorer-blobs.md#get-the-sas-for-a-blob-container)
* Obtención de la SAS para un recurso compartido de archivos; *próximamente*
* Obtención de la SAS para una cola; *próximamente*
* Obtención de la SAS para una tabla; *próximamente*

### <a name="attach-to-the-shared-account-service-using-the-sas"></a>Asociación al servicio de cuenta compartida con SAS
1. En el Explorador de almacenamiento (versión preliminar), seleccione **Connect to Azure storage**(Conectar con el almacenamiento de Azure).
   
   ![Opción de conexión a Almacenamiento de Azure][23]
2. En el cuadro de diálogo **Connect to Azure storage** (Conectar con Azure Storage), especifique el identificador URI de SAS y seleccione **Siguiente**.
   
   ![Cuadro de diálogo de conexión a Almacenamiento de Azure][24] 
3. En el cuadro de diálogo **Resumen de conexiones** , compruebe la información. Si desea cambiar algo, seleccione **Atrás** y vuelva a escribir la configuración deseada. Cuando haya terminado, seleccione **Conectar**.
4. Una vez asociado, el servicio aparecerá en el nodo **(Service SAS)** [(SAS de servicio)].
   
   ![Resultado de asociación a un servicio compartido mediante SAS][20]

## <a name="search-for-storage-accounts"></a>Búsqueda de cuentas de almacenamiento
Si tiene una larga lista de cuentas de almacenamiento, puede usar el cuadro de búsqueda en la parte superior del panel izquierdo para encontrar rápidamente una cuenta de almacenamiento concreta. 

A medida que vaya escribiendo en el cuadro de búsqueda, el panel izquierdo mostrará solo las cuentas de almacenamiento que coincidan con el valor de búsqueda que haya escrito hasta ese momento. La siguiente captura de pantalla es un ejemplo de cómo buscar todas las cuentas de almacenamiento cuyos nombres contienen el texto "tarcher".

![Búsqueda de cuenta de almacenamiento][11]

Para borrar la búsqueda, seleccione el botón **x** en el cuadro de búsqueda.

## <a name="next-steps"></a>Pasos siguientes
* [Administración de recursos de Azure Blob Storage con el Explorador de almacenamiento (versión preliminar)](vs-azure-tools-storage-explorer-blobs.md)

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



<!--HONumber=Nov16_HO2-->


