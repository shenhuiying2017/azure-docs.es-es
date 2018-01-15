---
title: "Autenticación entre servicios: Data Lake Store con Azure Active Directory | Microsoft Docs"
description: "Aprenda a realizar la autenticación entre servicios con Data Lake Store mediante Azure Active Directory."
services: data-lake-store
documentationcenter: 
author: nitinme
manager: jhubbard
editor: cgronlun
ms.service: data-lake-store
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 01/09/2018
ms.author: nitinme
ms.openlocfilehash: 0b3f19bb92d1eeb214150bf118d546cd1c67cd78
ms.sourcegitcommit: 9292e15fc80cc9df3e62731bafdcb0bb98c256e1
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/10/2018
---
# <a name="service-to-service-authentication-with-data-lake-store-using-azure-active-directory"></a>Autenticación entre servicios con Data Lake Store mediante Azure Active Directory
> [!div class="op_single_selector"]
> * [Autenticación de usuario final](data-lake-store-end-user-authenticate-using-active-directory.md)
> * [Autenticación entre servicios](data-lake-store-service-to-service-authenticate-using-active-directory.md)
> 
>  

Azure Data Lake Store usa Azure Active Directory para la autenticación. Antes de crear una aplicación que funcione con Azure Data Lake Store, debe decidir cómo autenticar la aplicación con Azure Active Directory (Azure AD). Las dos principales opciones disponibles son:

* Autenticación de usuario final 
* Autenticación entre servicios (este artículo) 

Con ambas opciones, la aplicación recibe un token de OAuth 2.0, que se adjunta a cada solicitud realizada a Azure Data Lake Store.

En este artículo se explica cómo crear una **aplicación web de Azure AD para la autenticación entre servicios**. Para obtener instrucciones sobre la configuración de la aplicación de Azure AD para la autenticación de usuario final, vea [Autenticación de usuario final con Data Lake Store mediante Azure Active Directory](data-lake-store-end-user-authenticate-using-active-directory.md).

## <a name="prerequisites"></a>Requisitos previos
* Una suscripción de Azure. Vea [Obtener evaluación gratuita de Azure](https://azure.microsoft.com/pricing/free-trial/).

## <a name="step-1-create-an-active-directory-web-application"></a>Paso 1: Crear una aplicación web de Active Directory

Cree y configure una aplicación web de Azure AD para la autenticación entre servicios con Azure Data Lake Store mediante Azure Active Directory. Para obtener instrucciones, vea cómo [crear una aplicación de Azure AD](../azure-resource-manager/resource-group-create-service-principal-portal.md).

Al seguir las instrucciones que aparecen en el vínculo anterior, asegúrese de seleccionar **Aplicación web/API** para el tipo de aplicación, como se muestra en la captura de pantalla siguiente:

![Crear aplicación web](./media/data-lake-store-authenticate-using-active-directory/azure-active-directory-create-web-app.png "Crear aplicación web")

## <a name="step-2-get-application-id-authentication-key-and-tenant-id"></a>Paso 2: Obtener el identificador de aplicación, la clave de autenticación y el identificador de inquilino
Al iniciar sesión mediante programación, necesita el identificador de la aplicación. Si esta se ejecuta con sus propias credenciales, también necesitará una clave de autenticación.

* Para obtener instrucciones sobre cómo recuperar el identificador y la clave de autenticación de la aplicación, consulte [Obtención del id. y la clave de autenticación de la aplicación](../azure-resource-manager/resource-group-create-service-principal-portal.md#get-application-id-and-authentication-key).

* Para obtener instrucciones sobre cómo recuperar el identificador de inquilino, consulte [Obtención del identificador de inquilino](../azure-resource-manager/resource-group-create-service-principal-portal.md#get-tenant-id).

## <a name="step-3-assign-the-azure-ad-application-to-the-azure-data-lake-store-account-file-or-folder"></a>Paso 3: Asignación de la aplicación de Azure AD al archivo o la carpeta de la cuenta de Azure Data Lake Store


1. Inicie sesión en [Azure Portal](https://portal.azure.com). Abra la cuenta de Azure Data Lake Store que desea asociar a la aplicación de Azure Active Directory que creó anteriormente.
2. En la hoja de su cuenta de Almacén de Data Lake, haga clic en **Explorador de datos**.
   
    ![Creación de directorios en una cuenta de Data Lake Store](./media/data-lake-store-authenticate-using-active-directory/adl.start.data.explorer.png "Creación de directorios en una cuenta de Data Lake Store")
3. En la hoja **Explorador de datos**, haga clic en el archivo o la carpeta para los que desea proporcionar acceso a la aplicación de Azure AD y, después, haga clic en **Acceso**. Para configurar el acceso a un archivo, debe hacer clic en la opción **Acceso** de la hoja de **vista previa de archivos**.
   
    ![Configuración de ACL en el sistema de archivos de Data Lake](./media/data-lake-store-authenticate-using-active-directory/adl.acl.1.png "Configuración de ACL en el sistema de archivos de Data Lake")
4. La hoja **Acceso** enumera el acceso estándar y el acceso personalizado ya asignados a la raíz. Haga clic en el icono **Agregar** para agregar las ACL de nivel personalizado.
   
    ![Lista de accesos estándar y personalizados](./media/data-lake-store-authenticate-using-active-directory/adl.acl.2.png "Lista de accesos estándar y personalizados")
5. Haga clic en el icono **Agregar** para abrir la hoja **Agregar acceso personalizado**. En esta hoja, haga clic en **Seleccionar usuario o grupo** y, después, en la hoja **Seleccionar usuario o grupo**, busque la aplicación de Azure Active Directory que creó antes. Si tiene muchos grupos en los que buscar, use el cuadro de texto de la parte superior para filtrar por nombre de grupo. Haga clic en el grupo que desee agregar y después en **Seleccionar**.
   
    ![Incorporación de un grupo](./media/data-lake-store-authenticate-using-active-directory/adl.acl.3.png "Incorporación de un grupo")
6. Haga clic en **Seleccionar permisos**, seleccione los permisos y si desea asignarlos como una ACL predeterminada, ACL de acceso o ambos. Haga clic en **OK**.
   
    ![Asignación de permisos a un grupo](./media/data-lake-store-authenticate-using-active-directory/adl.acl.4.png "Asignación de permisos a un grupo")
   
    Para obtener más información sobre los permisos de Data Lake Store y ACL y las ACL predeterminadas y de acceso, consulte [Control de acceso en Data Lake Store](data-lake-store-access-control.md).
7. En la hoja **Agregar acceso personalizado**, haga clic en **Aceptar**. El grupo recién agregado, con los permisos asociados, se enumeran en la hoja **Acceso** .
   
    ![Asignación de permisos a un grupo](./media/data-lake-store-authenticate-using-active-directory/adl.acl.5.png "Asignación de permisos a un grupo")

> [!NOTE]
> Si desea usar los SDK para crear una cuenta de Data Lake Store, debe asignar la aplicación web de Azure AD como un rol al grupo de recursos en el que crea la cuenta de Data Lake Store.
> 
>

## <a name="step-4-get-the-oauth-20-token-endpoint-only-for-java-based-applications"></a>Paso 4: Obtener el punto de conexión del token de OAuth 2.0 (solo para aplicaciones basadas en Java)

1. Inicie sesión en [Azure Portal](https://portal.azure.com) y haga clic en Active Directory en el panel izquierdo.

2. En el panel izquierdo, haga clic en **Registros de aplicaciones**.

3. En la parte superior de la hoja Registros de aplicaciones, haga clic en **Puntos de conexión**.

    ![Punto de conexión de token de OAuth](./media/data-lake-store-authenticate-using-active-directory/oauth-token-endpoint.png "Punto de conexión de token de OAuth")

4. En la lista de puntos de conexión, copie el correspondiente al token de OAuth 2.0.

    ![Punto de conexión de token de OAuth](./media/data-lake-store-authenticate-using-active-directory/oauth-token-endpoint-1.png "Punto de conexión de token de OAuth")   

## <a name="next-steps"></a>pasos siguientes
En este artículo ha creado creó una aplicación web de Azure AD y ha recopilado la información que necesita en las aplicaciones cliente que cree mediante SDK de .NET, Java, Python, API de REST, etc. Ya puede pasar a los artículos siguientes, en los que se explica cómo usar la aplicación nativa de Azure AD primero para autenticarse en Data Lake Store y luego realizar otras operaciones en el almacén.

* [Autenticación entre servicios con Data Lake Store mediante Java](data-lake-store-service-to-service-authenticate-java.md)
* [Autenticación entre servicios con Data Lake Store mediante SDK de .NET](data-lake-store-service-to-service-authenticate-net-sdk.md)
* [Autenticación entre servicios con Data Lake Store mediante Python](data-lake-store-service-to-service-authenticate-python.md)
* [Autenticación entre servicios con Data Lake Store mediante la API de REST](data-lake-store-service-to-service-authenticate-rest-api.md)


