---
title: Configuración de las notificaciones de rol emitidas en el token SAML para aplicaciones empresariales en Azure Active Directory | Microsoft Docs
description: Aprenda a configurar las notificaciones de rol emitidas en el token SAML para aplicaciones empresariales en Azure Active Directory.
services: active-directory
documentationcenter: ''
author: jeevansd
manager: mtillman
editor: ''
ms.assetid: eb2b3741-3cde-45c8-b639-a636f3df3b74
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/20/2018
ms.author: jeedes
ms.custom: aaddev
ms.openlocfilehash: 5520781c9f687e5e32ad1d8c38922a456bb5e3ce
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 04/28/2018
---
# <a name="configuring-role-claim-issued-in-the-saml-token-for-enterprise-applications-in-azure-active-directory"></a>Configuración de las notificaciones de rol emitidas en el token SAML para aplicaciones empresariales en Azure Active Directory

Esta característica permite a los usuarios personalizar el tipo de las notificaciones de "roles" en el token de respuesta recibido tras la autorización de una aplicación con Azure AD.

## <a name="prerequisites"></a>requisitos previos
- Una suscripción a Azure AD con la configuración de directorio.
- Una suscripción habilitada para el inicio de sesión único.
- Debe configurar un inicio de sesión único con la aplicación.

## <a name="when-to-use-this-feature"></a>Cuándo usar esta característica

Si la aplicación espera que se pasen roles personalizados a la respuesta de SAML, debe usar esta característica. Esta característica permite crear tantos roles como sea necesario pasar desde Azure AD a la aplicación.

## <a name="steps-to-use-this-feature"></a>Pasos para usar esta característica

1. En el panel de navegación izquierdo de **[Azure Portal](https://portal.azure.com)**, haga clic en el icono de **Azure Active Directory**. 

    ![Botón Azure Active Directory][1]

2. Vaya a **Aplicaciones empresariales**. A continuación, vaya a **Todas las aplicaciones**.

    ![Hoja Aplicaciones empresariales][2]
    
3. Para agregar una nueva aplicación, haga clic en el botón **Nueva aplicación** de la parte superior del cuadro de diálogo.

    ![Botón Nueva aplicación][3]

4. En el cuadro de búsqueda, escriba el nombre de la aplicación, seleccione la aplicación en el panel de resultados y, después, haga clic en el botón **Agregar** para agregar la aplicación.

    ![Aplicación en la lista de resultados](./media/active-directory-enterprise-app-role-management/tutorial_app_addfromgallery.png)

5. Una vez agregada la aplicación, vaya a la página **Propiedades** y copie el **identificador de objeto**.

    ![Página de propiedades](./media/active-directory-enterprise-app-role-management/tutorial_app_properties.png)

6. Abra el sitio del [Probador de Azure AD Graph](https://developer.microsoft.com/graph/graph-explorer) en otra ventana.

    a. Inicie sesión en el sitio del Probador de Graph con las credenciales globales de administrador o coadministrador del inquilino.

    b. Cambie la versión a **beta** y recupere la lista de entidades de servicio desde el inquilino con la siguiente consulta:
    
     `https://graph.microsoft.com/beta/servicePrincipals`
        
    Si usa varios directorios, debe seguir este patrón `https://graph.microsoft.com/beta/contoso.com/servicePrincipals`.
    
    ![Cuadro de diálogo del Probador de Graph](./media/active-directory-enterprise-app-role-management/graph-explorer1-updated.png)
    
    c. En la lista de entidades de servicio que se capturan, seleccione la que debe modificar. También puede usar CTRL + F para buscar la aplicación en todas las entidades de servicio enumeradas. Busque el **identificador de objeto** que copió de la página Propiedades y use la consulta siguiente para ir a la entidad de servicio correspondiente.
    
    `https://graph.microsoft.com/beta/servicePrincipals/<objectID>`.

    d. Extraiga la propiedad appRoles del objeto de entidad de servicio.

    ![Cuadro de diálogo del Probador de Graph](./media/active-directory-enterprise-app-role-management/graph-explorer-approles.png)

    e. Ahora debe generar roles nuevos para la aplicación. Puede descargar Azure AD Role Generator [aquí](https://app.box.com/s/jw6m9p9ehmf4ut5jx8xhcw87cu09ml3y).

    f. Abra Azure AD Generator y realice los siguientes pasos:

    ![Azure AD Generator](./media/active-directory-enterprise-app-role-management/azure_ad_role_generator.png)
    
    Escriba el **nombre del rol**, la **descripción del rol** y el **valor del rol**. Haga clic en **Add** (Agregar) para agregar el rol.
    
    Después de agregar todos los roles necesarios, haga clic en **Generate** (Generar).
    
    Copie el contenido; para ello, haga clic en **Copy Content** (Copiar contenido).

    > [!NOTE] 
    > Asegúrese de que dispone del rol de usuario **msiam_access** y de que el identificador coincide con el rol generado.

    g. Vuelva al Probador de Graph. Cambie el método de **GET** a **PATCH**. Revise el objeto de entidad de servicio para obtener las propiedades appRoles deseadas al actualizar la propiedad appRoles con los valores copiados. Haga clic en **Run Query** (Ejecutar consulta).

    ![Cuadro de diálogo del Probador de Graph](./media/active-directory-enterprise-app-role-management/graph-explorer-patch.png)

    > [!NOTE]
    > A continuación, se muestra un ejemplo del objeto appRoles. 
    > 
    >   ```
    > {
    > "appRoles": [
    > {
    >   "allowedMemberTypes": [
    >   "User"
    >   ],
    >   "description": "msiam_access",
    >   "displayName": "msiam_access",
    >   "id": "7dfd756e-8c27-4472-b2b7-38c17fc5de5e",
    >   "isEnabled": true,
    >   "origin": "Application",
    >   "value": null
    > },
    > {
    >   "allowedMemberTypes": [
    >   "User"
    >   ],
    >   "description": "teacher",
    >   "displayName": "teacher",
    >   "id": "6478ffd2-5dbd-4584-b2ce-137390b09b60",
    >   "isEnabled": ,
    >   "origin": "ServicePrincipal",
    >   "value": "teacher"
    > }
    > ] 
    > }
    >
    >   ```

7. Una vez que haya revisado la entidad de servicio con más roles, podrán asignarse usuarios a los roles correspondientes. Para hacer esto, vaya al Portal y navegue hasta la aplicación correspondiente. A continuación, en la parte superior, haga clic en la pestaña **Usuarios y grupos**. Este proceso mostrará una lista de todos los usuarios o grupos.

    ![Configurar la adición del inicio de sesión único](./media/active-directory-enterprise-app-role-management/userrole.png)

    a. Para asignar un rol a cualquier usuario, seleccione el usuario o grupo determinado y haga clic en el botón **Asignar** en la parte inferior de la página.

    ![Configurar la adición del inicio de sesión único](./media/active-directory-enterprise-app-role-management/userandgroups.png)

    b. Al hacer clic aparece una ventana emergente para que pueda seleccionar un rol de entre los distintos roles que se definen para la entidad de servicio correspondiente.

    c. Elija el rol necesario y haga clic en Enviar.

8. Después de asignar roles a los usuarios, deberá actualizar la tabla **Atributos** para definir una asignación personalizada de la notificación de **rol**.

9. En la sección **Atributos de usuario** del cuadro de diálogo **Inicio de sesión único**, configure el atributo token de SAML como muestra la imagen y siga estos pasos:
    
    | Nombre del atributo | Valor de atributo |
    | -------------- | ----------------|    
    | Nombre de rol      | user.assignedrole |

    a. Haga clic en **Agregar atributo** para abrir el cuadro de diálogo **Agregar atributo**.

    ![Configurar la adición del inicio de sesión único](./media/active-directory-enterprise-app-role-management/tutorial_attribute_04.png)

    ![Configuración del atributo de inicio de sesión único](./media/active-directory-enterprise-app-role-management/tutorial_attribute_05.png)

    b. En el cuadro de texto **Nombre**, escriba el nombre que se muestra para la fila.

    c. En la lista **Valor**, seleccione el atributo que se muestra para esa fila.

    d. Deje **Espacio de nombres** en blanco.
    
    e. Haga clic en **Aceptar**.

10. Para probar la aplicación en inicio de sesión único iniciado por IDP, inicie sesión en el Panel de acceso (https://myapps.microsoft.com)) y haga clic en el icono de la aplicación. En el token SAML, debería ver todos los roles asignados para el usuario con el nombre de notificación que les dio.

## <a name="update-existing-role"></a>Actualización de un rol existente

1. Para actualizar un rol existente, realice los pasos siguientes:

    a. Abra el sitio del [Probador de Azure AD Graph](https://developer.microsoft.com/graph/graph-explorer) en otra ventana.

    b. Inicie sesión en el sitio del Probador de Graph con las credenciales globales de administrador o coadministrador del inquilino.
    
    c. Cambie la versión a **beta** y recupere la lista de entidades de servicio desde el inquilino con la siguiente consulta:
    
    `https://graph.microsoft.com/beta/servicePrincipals`
        
    Si usa varios directorios, debe seguir este patrón `https://graph.microsoft.com/beta/contoso.com/servicePrincipals`.
    
    ![Cuadro de diálogo del Probador de Graph](./media/active-directory-enterprise-app-role-management/graph-explorer1-updated.png)
    
    d. En la lista de entidades de servicio que se capturan, seleccione la que debe modificar. También puede usar CTRL + F para buscar la aplicación en todas las entidades de servicio enumeradas. Busque el **identificador de objeto** que copió de la página Propiedades y use la consulta siguiente para ir a la entidad de servicio correspondiente.
    
    `https://graph.microsoft.com/beta/servicePrincipals/<objectID>`.
    
    e. Extraiga la propiedad appRoles del objeto de entidad de servicio.
    
    ![Cuadro de diálogo del Probador de Graph](./media/active-directory-enterprise-app-role-management/graph-explorer-approles.png)
    
    f. Para actualizar el rol existente, siga los pasos siguientes:

    ![Cuadro de diálogo del Probador de Graph](./media/active-directory-enterprise-app-role-management/graph-explorer-patchupdate.png)
    
    * Cambie el método de **GET** a **PATCH**.

    * Copie los roles existentes de la aplicación y péguelos en el **cuerpo de la solicitud**.
    
    * Actualice el valor del rol reemplazando la **descripción del rol**, el **valor del rol** y el **nombre para mostrar del rol** según los requisitos de su organización.
    
    * Después de actualizar todos los roles necesarios, haga clic en **Run Query** (Ejecutar consulta).
        
## <a name="delete-existing-role"></a>Eliminación del rol existente

1. Para eliminar un rol existente, realice los pasos siguientes:

    a. Abra el sitio del [Probador de Azure AD Graph](https://developer.microsoft.com/graph/graph-explorer) en otra ventana.

    b. Inicie sesión en el sitio del Probador de Graph con las credenciales globales de administrador o coadministrador del inquilino.

    c. Cambie la versión a **beta** y recupere la lista de entidades de servicio desde el inquilino con la siguiente consulta:
    
    `https://graph.microsoft.com/beta/servicePrincipals`
    
    Si usa varios directorios, debe seguir este patrón `https://graph.microsoft.com/beta/contoso.com/servicePrincipals`.
    
    ![Cuadro de diálogo del Probador de Graph](./media/active-directory-enterprise-app-role-management/graph-explorer1-updated.png)
    
    d. En la lista de entidades de servicio que se capturan, seleccione la que debe modificar. También puede usar CTRL + F para buscar la aplicación en todas las entidades de servicio enumeradas. Busque el **identificador de objeto** que copió de la página Propiedades y use la consulta siguiente para ir a la entidad de servicio correspondiente.
     
    `https://graph.microsoft.com/beta/servicePrincipals/<objectID>`.
    
    e. Extraiga la propiedad appRoles del objeto de entidad de servicio.
    
    ![Cuadro de diálogo del Probador de Graph](./media/active-directory-enterprise-app-role-management/graph-explorer-approles.png)

    f. Para eliminar el rol existente, siga los pasos siguientes:

    ![Cuadro de diálogo del Probador de Graph](./media/active-directory-enterprise-app-role-management/graph-explorer-patchdelete.png)

    Cambie el método de **GET** a **PATCH**.

    Copie los roles existentes de la aplicación y péguelos en el **cuerpo de la solicitud**.
    
    Establezca el valor **IsEnabled** en **false** para el rol que quiere eliminar.

    Haga clic en **Run Query** (Ejecutar consulta).
    
    > [!NOTE] 
    > Asegúrese de que dispone del rol de usuario **msiam_access** y de que el identificador coincide con el rol generado.
    
    g. Una vez realizado el proceso arriba mencionado, mantenga el método como **PATCH** y pegue el contenido restante del rol en el **cuerpo de la solicitud** y haga clic en **Run Query** (Ejecutar consulta).
    
    ![Cuadro de diálogo del Probador de Graph](./media/active-directory-enterprise-app-role-management/graph-explorer-patchfinal.png)

    h. Después de ejecutar la consulta se eliminará el rol.
    
    > [!NOTE]
    > El rol debe estar deshabilitado para poder eliminarlo. 

## <a name="next-steps"></a>Pasos siguientes

Consulte la [documentación de la aplicación ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list) para conocer los pasos adicionales.

<!--Image references-->
<!--Image references-->

[1]: ./media/active-directory-enterprise-app-role-management/tutorial_general_01.png
[2]: ./media/active-directory-enterprise-app-role-management/tutorial_general_02.png
[3]: ./media/active-directory-enterprise-app-role-management/tutorial_general_03.png
[4]: ./media/active-directory-enterprise-app-role-management/tutorial_general_04.png
