---
title: " Creación de una cuenta de Azure Media Services con Azure Portal | Microsoft Docss"
description: "Este tutorial le guiará por los pasos que hay que seguir para crear una cuenta de Servicios multimedia de Azure con el Portal de Azure."
services: media-services
documentationcenter: 
author: Juliako
manager: erikre
editor: 
ms.assetid: c551e158-aad6-47b4-931e-b46260b3ee4c
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 01/10/2017
ms.author: juliako
translationtype: Human Translation
ms.sourcegitcommit: f6d6b7b1051a22bbc865b237905f8df84e832231
ms.openlocfilehash: 01c1297a6b107f91d8e6e73f17d76499d67849de


---
# <a name="create-an-azure-media-services-account-using-the-azure-portal"></a>Creación de una cuenta de Azure Media Services mediante Azure Portal
> [!div class="op_single_selector"]
> * [Portal](media-services-portal-create-account.md)
> * [PowerShell](media-services-manage-with-powershell.md)
> * [REST](https://docs.microsoft.com/rest/api/media/mediaservice)
> 
> [!NOTE]
> Para completar este tutorial, deberá tener una cuenta de Azure. Para obtener más información, consulte [Evaluación gratuita de Azure](https://azure.microsoft.com/pricing/free-trial/). 
> 
> 

El Portal de Azure proporciona una forma de crear rápidamente una cuenta de Servicios multimedia de Azure (AMS). Puede usar la cuenta para obtener acceso a Servicios multimedia que le permiten almacenar, cifrar, codificar, administrar y transmitir contenido multimedia en Azure. En el momento en el que crea una cuenta de Servicios multimedia, también crea una cuenta de almacenamiento asociada (o usa una ya existente) en la misma región geográfica que la cuenta de Servicios multimedia.

En este artículo se explican algunos conceptos comunes y se muestra cómo crear una cuenta de Servicios multimedia con el Portal de Azure.

## <a name="concepts"></a>Conceptos
El acceso a Servicios multimedia requiere dos cuentas asociadas:

* Una cuenta de Servicios multimedia. La cuenta le proporciona acceso a un conjunto de Servicios multimedia basados en la nube que están disponibles en Azure. Una cuenta de Servicios multimedia no almacena el contenido multimedia real, sino que almacena metadatos sobre el contenido multimedia y trabajos de procesamiento multimedia en su cuenta. En el momento en el que cree la cuenta, seleccione una región de Servicios multimedia disponible. La región que selecciona es un centro de datos que almacena los registros de metadatos para su cuenta.
  
    Entre las regiones de Servicios multimedia (AMS) disponibles se incluyen las siguientes: Europa del Norte, Europa occidental, Oeste de EE. UU., Este de EE. UU., Sudeste de Asia, Asia oriental, Oeste de Japón, Este de Japón. Servicios multimedia no usa grupos de afinidad.
  
    AMS ya está disponible en los centros de datos siguientes: sur de Brasil, India occidental, sur de India e India central. Ahora se puede usar Azure Portal para crear cuentas de Media Services y realizar varias tareas que se describen aquí. Sin embargo, Codificación en directo no está habilitado en estos centros de datos. Además, no todos los tipos de unidades reservadas de codificación están disponibles en estos centros de datos.
  
  * Sur de Brasil: solo están disponibles las unidades reservadas de codificación Estándar y Básica.
  * India occidental, sur de la India: 
* Una cuenta de almacenamiento de Azure. Las cuentas de almacenamiento deben ubicarse en la misma región geográfica que la cuenta de Media Services. Cuando cree una cuenta de Servicios multimedia, podrá elegir una cuenta de almacenamiento existente en la misma región o crear una nueva cuenta de almacenamiento en la misma región. Si elimina una cuenta de Servicios multimedia, no se eliminarán los blobs de la cuenta de almacenamiento relacionada.

## <a name="create-an-ams-account"></a>Creación de una cuenta de AMS
Los pasos de esta sección muestran cómo crear una cuenta de AMS.

1. Inicie sesión en el [Portal de Azure](https://portal.azure.com/).
2. Haga clic en **+Nuevo** > **Web y móvil** > **Servicios multimedia**.
   
    ![Creación de Servicios multimedia](./media/media-services-create-account/media-services-new1.png)
3. En **CREAR CUENTA DE SERVICIOS MULTIMEDIA** especifique los valores obligatorios.
   
    ![Creación de Servicios multimedia](./media/media-services-create-account/media-services-new3.png)
   
   1. En **Nombre de la cuenta**, especifique el nombre de la cuenta nueva de AMS. El nombre de la cuenta de Servicios multimedia debe estar compuesto de números o letras en minúscula, sin espacios y con una longitud de entre 3 y 24 caracteres.
   2. En Suscripción, seleccione entre las diferentes suscripciones de Azure a las que tiene acceso.
   3. En **Grupo de recursos**seleccione el recurso nuevo o uno ya existente.  Un grupo de recursos es una colección de recursos que comparten ciclos de vida, permisos y directivas. Obtenga más información [aquí](../azure-resource-manager/resource-group-overview.md#resource-groups).
   4. En **Ubicación**, seleccione la región geográfica que se usará para almacenar los registros de medios y de metadatos para la cuenta de Media Services. Esta región se utilizará para procesar y transmitir contenido multimedia. Solo las regiones de Servicios multimedia disponibles aparecen en la lista desplegable. 
   5. En **Cuenta de almacenamiento**, seleccione una cuenta de almacenamiento para proporcionar almacenamiento de blobs del contenido multimedia desde la cuenta de Servicios multimedia. Puede seleccionar una cuenta de almacenamiento existente de la misma región geográfica que la cuenta de Servicios multimedia o crearla. Se crea una nueva cuenta de almacenamiento en la misma región. Las reglas para los nombres de cuenta de almacenamiento son las mismas que para las cuentas de Servicios multimedia.
      
       Puede obtener más información acerca del almacenamiento [aquí](../storage/storage-introduction.md).
   6. Seleccione **Anclar al panel** para ver el progreso de la implementación de la cuenta.
4. Haga clic en **Crear** en la parte inferior del formulario.
   
    Cuando la cuenta se crea correctamente, se carga la página de información general. En la tabla de puntos de conexión de streaming, la cuenta tendrá el punto de conexión de streaming predeterminado en el estado **Stopped** (Detenido). 

    >[!NOTE]
    >Cuando se crea la cuenta de AMS, se agrega un punto de conexión de streaming **predeterminado** a la cuenta en estado **Stopped** (Detenido). Para iniciar la transmisión del contenido y aprovechar el empaquetado dinámico y el cifrado dinámico, el punto de conexión de streaming desde el que va a transmitir el contenido debe estar en estado **Running** (En ejecución). 
   
    ![Configuración de Servicios multimedia](./media/media-services-create-account/media-services-settings.png)
   
    Para administrar la cuenta de AMS (por ejemplo, para cargar vídeos, codificar recursos, supervisar el progreso del trabajo) use la ventana **Configuración** .

## <a name="manage-keys"></a>Administrar claves
Necesita el nombre de cuenta y la información de la clave principal para obtener acceso mediante programación a la cuenta de Servicios multimedia.

1. En el Portal de Azure, seleccione la cuenta. 
   
    Aparecerá la ventana **Configuración** a la derecha. 
2. En la ventana **Configuración**, seleccione **Claves**. 
   
    Aparece la ventana **Administrar claves** que muestra el nombre de la cuenta y la clave principal y la secundaria. 
3. Pulse el botón Copiar para copiar los valores.
   
    ![Claves de Servicios multimedia](./media/media-services-create-account/media-services-keys.png)

## <a name="next-steps"></a>Pasos siguientes
Ya puede cargar archivos en su cuenta de AMS. Para más información, consulte [Upload files into a Media Services account using the Azure portal](media-services-portal-upload-files.md)(Carga de archivos en una cuenta de Servicios multimedia desde el Portal de Azure).

## <a name="media-services-learning-paths"></a>Rutas de aprendizaje de Servicios multimedia
[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Envío de comentarios
[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]




<!--HONumber=Jan17_HO2-->


