---
title: "Integración de una cuenta de una instancia de Azure Storage con la red CDN de Azure | Microsoft Docs"
description: "Aprenda a usar la red de entrega de contenido (CDN) de Azure para ofrecer contenido con un ancho de banda alto mediante el almacenamiento en caché de blobs de Almacenamiento de Azure."
services: cdn
documentationcenter: 
author: zhangmanling
manager: erikre
editor: 
ms.assetid: cbc2ff98-916d-4339-8959-622823c5b772
ms.service: cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/23/2017
ms.author: mazha
ms.translationtype: HT
ms.sourcegitcommit: 83f19cfdff37ce4bb03eae4d8d69ba3cbcdc42f3
ms.openlocfilehash: 511076935d06ed0908341044e37069e74530be49
ms.contentlocale: es-es
ms.lasthandoff: 08/21/2017

---
# <a name="integrate-an-azure-storage-account-with-azure-cdn"></a>Integración de una cuenta de una instancia de Azure Storage con la red CDN de Azure
CDN puede habilitarse para almacenar el contenido de la caché en Almacenamiento de Azure. Ofrece a los desarrolladores una solución global de entrega de contenido con alto ancho de banda; para ello, almacena en memoria caché los blobs y los contenidos estáticos de las instancias de proceso en nodos físicos ubicados en Estados Unidos, Europa, Asia, Australia y Sudamérica.

## <a name="step-1-create-a-storage-account"></a>Paso 1: Creación de una cuenta de almacenamiento
Use el siguiente procedimiento para crear una nueva cuenta de almacenamiento para una suscripción de Azure. Una cuenta de almacenamiento proporciona acceso a los servicios de almacenamiento de Azure. La cuenta de almacenamiento representa el máximo nivel de espacio de nombres para tener acceso a todos los componentes del servicio de almacenamiento de Azure: servicios BLOB, servicios Cola y servicios Tabla. Para más información, consulte [Introducción al Almacenamiento de Microsoft Azure](../storage/common/storage-introduction.md).

Para crear una cuenta de almacenamiento, debe ser administrador del servicio o coadministrador de la suscripción correspondiente.

> [!NOTE]
> Hay varios métodos que puede usar para crear una cuenta de almacenamiento, incluido el Portal de Azure y Powershell.  Para este tutorial, usaremos el Portal de Azure.  
> 
> 

**Para crear una cuenta de almacenamiento para una suscripción de Azure**

1. Inicie sesión en el [Portal de Azure](https://portal.azure.com).
2. En la esquina superior izquierda, seleccione **Nuevo**. En el cuadro de diálogo **Nuevo**, seleccione **Datos + Almacenamiento** y haga clic en **Cuenta de almacenamiento**.
    
    Aparece la hoja **Crear cuenta de almacenamiento** .   

    ![Crear cuenta de almacenamiento][create-new-storage-account]  

3. En el campo **Nombre** , escriba un nombre de subdominio. Esta entrada puede contener de 3 a 24 letras minúsculas y números.
   
    Este valor se convierte en el nombre del host dentro del URI que se ha usado para direccionar los recursos Blob, Cola o Tabla de la suscripción. Para dirigir un recurso contenedor en Blob service, debería usar un URI con el siguiente formato, en el que *&lt;StorageAccountLabel&gt;* hace referencia al valor que escribió en **Escriba una dirección URL**:
   
    http://*&lt;StorageAcountLabel&gt;*.blob.core.windows.net/*&lt;mycontainer&gt;*
   
    **Importante**: La etiqueta de URL forma el subdominio del URI de la cuenta de almacenamiento y debe ser única en todos los servicios hospedados en Azure.
   
    Este valor también se utiliza como nombre de esta cuenta de almacenamiento en el portal o en el acceso a esta cuenta mediante programación.
4. Deje los valores predeterminados de **Modelo de implementación**, **Tipo de cuenta**, **Rendimiento** y **Replicación**. 
5. Seleccione la **Suscripción** con la que se usará la cuenta de almacenamiento.
6. Seleccione o cree un **grupo de recursos**.  Para más información sobre los grupos de recursos, consulte [Información general de Azure Resource Manager](../azure-resource-manager/resource-group-overview.md#resource-groups).
7. Seleccione la ubicación para la cuenta de almacenamiento.
8. Haga clic en **Crear**. El proceso de creación de la cuenta de almacenamiento podría tardar varios minutos en completarse.

## <a name="step-2-enable-cdn-for-the-storage-account"></a>Paso 2: Habilitación de la red CDN para la cuenta de almacenamiento

Con la integración más reciente, ahora puede habilitar la red CDN para la cuenta de almacenamiento sin salir de la extensión del portal de almacenamiento. 

1. Seleccione la cuenta de almacenamiento, busque "CDN" o desplácese hacia abajo en el menú de navegación izquierdo y haga clic en "CDN de Azure".
    
    Aparece la hoja **CDN de Azure**.

    ![cdn enable navigation][cdn-enable-navigation]
    
2. Cree un nuevo punto de conexión. Para ello escriba la información necesaria
    - **Perfil de CDN**: puede crear un perfil o usar uno existente.
    - **Plan de tarifa**: si crea un nuevo perfil CDN, bastará con seleccionar un plan de tarifa.
    - **Nombre del punto de conexión de CDN**: escriba un nombre de punto de conexión de su elección.

    > [!TIP]
    > El punto de conexión de CDN creado utilizará el nombre de host de la cuenta de almacenamiento como origen de forma predeterminada.

    ![cdn new endpoint creation][cdn-new-endpoint-creation]

3. Después de su creación, se mostrará el nuevo punto de conexión en la lista de puntos de conexión anterior.

    ![cdn storage new endpoint][cdn-storage-new-endpoint]

> [!NOTE]
> También puede ir a la extensión de CDN de Azure para habilitar la red CDN. [Tutorial](#Tutorial-cdn-create-profile).
> 
> 

[!INCLUDE [cdn-create-profile](../../includes/cdn-create-profile.md)]  

## <a name="step-3-enable-additional-cdn-features"></a>Paso 3: Habilitación de características adicionales de CDN

En la hoja "CDN de Azure" de la cuenta de almacenamiento, haga clic en el punto de conexión de CDN de la lista para abrir la hoja de configuración de CDN. Puede habilitar características adicionales de CDN para la entrega como, por ejemplo, la compresión, la cadena de consulta o el filtrado geográfico. También puede agregar la asignación de dominios personalizada al punto de conexión de CDN y habilitar HTTPS de dominio personalizado.
    
![cdn storage cdn configuration][cdn-storage-cdn-configuration]

## <a name="step-4-access-cdn-content"></a>Paso 4: Acceso a su contenido de la red CDN
Para obtener acceso al contenido almacenado en la memoria caché de la red CDN, use la URL de la red CDN que se le ha proporcionado en el portal. La dirección del blob en caché será similar a la siguiente:

http://<*EndpointName*\>.azureedge.net/<*myPublicContainer*\>/<*BlobName*\>

> [!NOTE]
> Una vez que haya habilitado el acceso de la red CDN a una cuenta de almacenamiento, todos los objetos disponibles de forma pública se pueden almacenar en la memoria caché perimetral de la red CDN. Si modifica un objeto que está almacenado en la memoria caché de la red CDN actualmente, el nuevo contenido no estará disponible a través de la red CDN hasta que la red CDN actualice su contenido al cumplir el período de vida del contenido almacenado en caché.
> 
> 

## <a name="step-5-remove-content-from-the-cdn"></a>Paso 5: Eliminación de su contenido de la red CDN
Si no desea seguir almacenando un objeto en la memoria caché de la Red de entrega de contenido de Azure (CDN), puede realizar uno de los siguientes pasos:

* Puede crear un contenedor privado en vez de público. Vea [Administración del acceso de lectura anónimo a contenedores y blobs](../storage/blobs/storage-manage-access-to-resources.md) para más información.
* Puede deshabilitar o eliminar el punto de conexión de la red CDN con el Portal de administración.
* Puede modificar su servicio hospedado para no seguir respondiendo las solicitudes del objeto.

Un objeto que ya está almacenado en la memoria caché de la red CDN permanecerá almacenado en caché hasta que cumpla el período de vida del objeto o hasta que se purgue el punto de conexión. Al cumplir el período de vida, la red CDN comprobará si el punto de conexión de la red CDN sigue siendo válido y si el objeto sigue siendo accesible de forma anónima. Si no lo es, el objeto dejará de estar almacenado en caché.

## <a name="additional-resources"></a>Recursos adicionales
* [Asignación del contenido de la red CDN a un dominio personalizado](cdn-map-content-to-custom-domain.md)
* [Habilitar HTTPS para el dominio personalizado](cdn-custom-ssl.md)

[create-new-storage-account]: ./media/cdn-create-a-storage-account-with-cdn/CDN_CreateNewStorageAcct.png
[cdn-enable-navigation]: ./media/cdn-create-a-storage-account-with-cdn/cdn-storage-new-endpoint-creation.png
[cdn-storage-new-endpoint]: ./media/cdn-create-a-storage-account-with-cdn/cdn-storage-new-endpoint-list.png
[cdn-storage-cdn-configuration]: ./media/cdn-create-a-storage-account-with-cdn/cdn-storage-endpoint-configuration.png 

