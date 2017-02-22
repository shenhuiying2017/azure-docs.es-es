---
title: "Autenticación entre servicios: Data Lake Store con Azure Active Directory | Microsoft Docs"
description: "Aprenda a realizar la autenticación entre servicios con Data Lake Store mediante Azure Active Directory."
services: data-lake-store
documentationcenter: 
author: nitinme
manager: jhubbard
editor: cgronlun
ms.assetid: 820b7c5d-4863-4225-9bd1-df4d8f515537
ms.service: data-lake-store
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 01/10/2017
ms.author: nitinme
translationtype: Human Translation
ms.sourcegitcommit: 9019a4115e81a7d8f1960098b1138cd437a0460b
ms.openlocfilehash: dac6c9f3be7b4535f8cb30a9ec0c1e398ca5ff28


---
# <a name="service-to-service-authentication-with-data-lake-store-using-azure-active-directory"></a>Autenticación entre servicios con Data Lake Store mediante Azure Active Directory
> [!div class="op_single_selector"]
> * [Autenticación entre servicios](data-lake-store-authenticate-using-active-directory.md)
> * [Autenticación de usuario final](data-lake-store-end-user-authenticate-using-active-directory.md)
> 
> 

Azure Data Lake Store usa Azure Active Directory para la autenticación. Antes de crear una aplicación que funcione con Azure Data Lake Store o Azure Data Lake Analytics, debe determinar cómo desea autenticar la aplicación con Azure Active Directory (Azure AD). Las dos principales opciones disponibles son:

* Autenticación de usuario final 
* Autenticación entre servicios (este artículo) 

Con ambas opciones, la aplicación recibe un token de OAuth 2.0 que se adjunta a cada solicitud realizada a Azure Data Lake Store o Azure Data Lake Analytics.

En este artículo se habla de cómo crear una aplicación web de Azure AD para la autenticación entre servicios. Para obtener instrucciones sobre la configuración de la aplicación de Azure AD para la autenticación entre servicios, vea [Autenticación de usuario final con Data Lake Store mediante Azure Active Directory](data-lake-store-end-user-authenticate-using-active-directory.md).

## <a name="prerequisites"></a>Requisitos previos
* Una suscripción de Azure. Consulte [How to get Azure Free trial for testing Hadoop in HDInsight (Obtención de una versión de prueba gratuita de Azure para probar Hadoop en HDInsight)](https://azure.microsoft.com/pricing/free-trial/).

## <a name="create-an-active-directory-application"></a>Creación de una aplicación de Active Directory
En esta sección aprenderemos a crear y configurar una aplicación web de Azure AD para la autenticación entre servicios con Azure Data Lake Store mediante Azure Active Directory. Tenga en cuenta que, al crear una "aplicación de Active Directory", lo que se crea es una entidad de servicio, y no una aplicación o código.

### <a name="step-1-create-an-azure-active-directory-application"></a>Paso 1: Crear una aplicación de Azure Active Directory
1. Inicie sesión en su cuenta de Azure a través del [portal clásico](https://manage.windowsazure.com/).
2. Seleccione **Active Directory** en el panel izquierdo.
   
     ![seleccionar Active Directory](./media/data-lake-store-authenticate-using-active-directory/active-directory.png)
3. Seleccione el directorio de Active Directory que desea usar para crear la nueva aplicación. Si tiene más de uno, suele ser preferible crear la aplicación en el directorio en el que reside la suscripción. Solo puede conceder acceso a recursos de la suscripción para aplicaciones que se encuentren en el mismo directorio que su suscripción.  
   
     ![elegir directorio](./media/data-lake-store-authenticate-using-active-directory/active-directory-details.png)
4. Para ver las aplicaciones en su directorio, haga clic en **Aplicaciones**.
   
     ![ver aplicaciones](./media/data-lake-store-authenticate-using-active-directory/view-applications.png)
5. Si no ha creado una aplicación en ese directorio previamente, debería ver algo similar a la siguiente imagen. Haga clic en **AGREGAR UNA APLICACIÓN**.
   
     ![Agregar una aplicación](./media/data-lake-store-authenticate-using-active-directory/create-application.png)
   
     O bien, haga clic en **Agregar** en el panel inferior.
   
     ![agregar](./media/data-lake-store-authenticate-using-active-directory/add-icon.png)
6. Dé un nombre a la aplicación y seleccione el tipo al que esta pertenece. Para este tutorial, cree una **APLICACIÓN WEB Y/O API WEB** y haga clic en el botón Siguiente.
   
     ![aplicación de nombre](./media/data-lake-store-authenticate-using-active-directory/tell-us-about-your-application.png)

    > [!TIP]
    > Proporcione un nombre para la aplicación que sea más fácil de buscar. Más adelante en este tutorial buscará esta aplicación y la asignará a una cuenta de almacén de Data Lake Store.
    > 
    > 

7. Rellene las propiedades de la aplicación. Para **DIRECCIÓN URL DE INICIO DE SESIÓN**, proporcione el identificador URI para un sitio web que describe la aplicación. No se valida la existencia del sitio web. 
   Para **URI DE ID. DE APLICACIÓN**, proporcione el URI que identifica la aplicación.
   
     ![propiedades de la aplicación](./media/data-lake-store-authenticate-using-active-directory/app-properties.png)
   
    Haga clic en la marca de verificación para finalizar el asistente y crear la aplicación.

### <a name="step-2-get-client-id-client-secret-and-token-endpoint"></a>Paso 2: Obtención del identificador de cliente, el secreto de cliente y el punto de conexión de token
Al iniciar sesión mediante programación, necesitará el identificador de la aplicación. Si esta se ejecuta con sus propias credenciales, también precisará una clave de autenticación.

1. Haga clic en la pestaña **Configurar** para configurar la contraseña de la aplicación.
   
     ![configurar aplicación](./media/data-lake-store-authenticate-using-active-directory/application-configure.png)
2. Copie el valor de **ID. DE CLIENTE**.
   
     ![ID. DE CLIENTE](./media/data-lake-store-authenticate-using-active-directory/client-id.png)
3. Si la aplicación se ejecuta con sus propias credenciales, desplácese hacia abajo, hasta la sección **Claves**, y seleccione cuánto tiempo desea que la contraseña sea válida.
   
     ![claves](./media/data-lake-store-authenticate-using-active-directory/create-key.png)
4. Seleccione **Guardar** para crear la clave.
   
    ![Guardar](./media/data-lake-store-authenticate-using-active-directory/save-icon.png)
   
    Se muestra la clave guardada, y tiene la posibilidad de copiarla. No podrá recuperar la clave más tarde, por lo que debe copiarla ahora.
   
    ![clave guardada](./media/data-lake-store-authenticate-using-active-directory/save-key.png)
5. Recupere el punto de conexión de token seleccionando **Ver extremos** en la parte inferior de la pantalla y recuperando el valor del **punto de conexión de token de OAuth 2.0**, tal y como se muestra a continuación.  
   
    ![id. de inquilino](./media/data-lake-store-authenticate-using-active-directory/save-tenant.png)

#### <a name="note-down-the-following-properties-that-you-will-need-for-the-next-steps"></a>Anote las siguientes propiedades, ya que las necesitará en los pasos siguientes:
1. Nombre del identificador de la aplicación web que creó anteriormente en el paso 1.6
2. Identificador del cliente que recuperó anteriormente en el paso 2.2
3. Clave que creó anteriormente en el paso 2.4
4. Identificador del inquilino que recuperó anteriormente en el paso 2.5

### <a name="step-3-assign-the-azure-ad-application-to-the-azure-data-lake-store-account-file-or-folder-only-for-service-to-service-authentication"></a>Paso 3: Asignación de la aplicación de Azure AD al archivo o la carpeta de la cuenta de Azure Data Lake Store (solo para la autenticación entre servicios)
1. Inicie sesión en el nuevo [Azure Portal](https://portal.azure.com) y abra la cuenta de Azure Data Lake Store que desea asociar a la aplicación de Azure Active Directory que creó anteriormente.
2. En la hoja de su cuenta de Almacén de Data Lake, haga clic en **Explorador de datos**.
   
    ![Creación de directorios en una cuenta de Data Lake Store](./media/data-lake-store-authenticate-using-active-directory/adl.start.data.explorer.png "Creación de directorios en una cuenta de Data Lake Store")
3. En la hoja **Explorador de datos**, haga clic en el archivo o la carpeta para los que desea proporcionar acceso a la aplicación de Azure AD y, después, haga clic en **Acceso**. Para configurar el acceso a un archivo, debe hacer clic en la opción **Acceso** de la hoja de **vista previa de archivos**.
   
    ![Configuración de ACL en el sistema de archivos de Data Lake](./media/data-lake-store-authenticate-using-active-directory/adl.acl.1.png "Configuración de ACL en el sistema de archivos de Data Lake")
4. La hoja **Acceso** enumera el acceso estándar y el acceso personalizado ya asignados a la raíz. Haga clic en el icono **Agregar** para agregar las ACL de nivel personalizado.
   
    ![Lista de accesos estándar y personalizados](./media/data-lake-store-authenticate-using-active-directory/adl.acl.2.png "Lista de accesos estándar y personalizados")
5. Haga clic en el icono **Agregar** para abrir la hoja **Agregar acceso personalizado**. En esta hoja, haga clic en **Seleccionar usuario o grupo** y, después, en la hoja **Seleccionar usuario o grupo**, busque la aplicación de Azure Active Directory que creó antes. Si tiene muchos grupos en los que buscar, use el cuadro de texto en la parte superior para filtrar según el nombre del grupo. Haga clic en el grupo que desee agregar y después en **Seleccionar**.
   
    ![Incorporación de un grupo](./media/data-lake-store-authenticate-using-active-directory/adl.acl.3.png "Incorporación de un grupo")
6. Haga clic en **Seleccionar permisos**, seleccione los permisos y si desea asignarlos como una ACL predeterminada, ACL de acceso o ambos. Haga clic en **Aceptar**.
   
    ![Asignación de permisos a un grupo](./media/data-lake-store-authenticate-using-active-directory/adl.acl.4.png "Asignación de permisos a un grupo")
   
    Para obtener más información sobre los permisos de Data Lake Store y ACL y las ACL predeterminadas y de acceso, consulte [Control de acceso en Data Lake Store](data-lake-store-access-control.md).
7. En la hoja **Agregar acceso personalizado**, haga clic en **Aceptar**. El grupo recién agregado, con los permisos asociados, se mostrará ahora en la hoja **Acceso** .
   
    ![Asignación de permisos a un grupo](./media/data-lake-store-authenticate-using-active-directory/adl.acl.5.png "Asignación de permisos a un grupo")    

## <a name="next-steps"></a>Pasos siguientes
En este artículo creó una aplicación web de Azure AD y recopiló la información que necesita en las aplicaciones cliente que cree mediante SDK con .NET, SDK de Java, etc. Ahora puede continuar en los artículos siguientes, que hablan de cómo usar la aplicación web de Azure AD para autenticarse primero en Data Lake Store y luego realizar otras operaciones en el almacén.

* [Introducción al Almacén de Azure Data Lake mediante SDK de .NET](data-lake-store-get-started-net-sdk.md)
* [Introducción a Azure Data Lake Store con el SDK de Java](data-lake-store-get-started-java-sdk.md)

En este artículo, ha recorrido los pasos necesarios para crear una entidad de seguridad de un usuario y ejecutarla en su aplicación. Para más información, puede consultar los siguientes artículos:
* [Uso de PowerShell para crear una entidad de servicio](https://docs.microsoft.com/en-us/azure/azure-resource-manager/resource-group-authenticate-service-principal)
* [Uso de certificados para la autenticación de la entidad de servicio](https://docs.microsoft.com/en-us/azure/azure-resource-manager/resource-group-authenticate-service-principal#create-service-principal-with-certificate)
* [Otros métodos de autenticación de Azure AD](https://docs.microsoft.com/en-us/azure/active-directory/active-directory-authentication-scenarios)





<!--HONumber=Jan17_HO4-->


