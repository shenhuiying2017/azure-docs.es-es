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
ms.openlocfilehash: 3acfa51351ac49456f5f9fcac8aa4f4f339b9ea3
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 04/18/2018
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

    b. Debe tener permisos suficientes para crear los roles. Haga clic en **Modificar permisos** para obtener los permisos necesarios. 

    ![Cuadro de diálogo del Probador de Graph](./media/active-directory-enterprise-app-role-management/graph-explorer-new9.png)

    c. Seleccione los siguientes permisos de la lista (si no los tiene ya) y haga clic en "Modificar permisos" 

    ![Cuadro de diálogo del Probador de Graph](./media/active-directory-enterprise-app-role-management/graph-explorer-new10.png)

    d. En este se le pedirá que vuelva a iniciar la sesión y acepte el consentimiento. Después de aceptar el consentimiento, habrá iniciado sesión en el sistema de nuevo.

    e. Cambie la versión a **beta** y recupere la lista de entidades de servicio desde el inquilino con la siguiente consulta:
    
     `https://graph.microsoft.com/beta/servicePrincipals`
        
    Si usa varios directorios, debe seguir este patrón `https://graph.microsoft.com/beta/contoso.com/servicePrincipals`.
    
    ![Cuadro de diálogo del Probador de Graph](./media/active-directory-enterprise-app-role-management/graph-explorer-new1.png)
    
    f. En la lista de entidades de servicio que se capturan, seleccione la que debe modificar. También puede usar CTRL + F para buscar la aplicación en todas las entidades de servicio enumeradas. Busque el **identificador de objeto** que copió de la página Propiedades y use la consulta siguiente para ir a la entidad de servicio correspondiente.
    
    `https://graph.microsoft.com/beta/servicePrincipals/<objectID>`.

    ![Cuadro de diálogo del Probador de Graph](./media/active-directory-enterprise-app-role-management/graph-explorer-new2.png)

    g. Extraiga la propiedad appRoles del objeto de entidad de servicio. 

    ![Cuadro de diálogo del Probador de Graph](./media/active-directory-enterprise-app-role-management/graph-explorer-new3.png)

    > [!Note]
    > Si va a usar la aplicación personalizada (que no procede de la galería), podrá ver los dos roles predeterminados: usuario y msiam_access. Si usa la aplicación de la galería, msiam_access es el único rol predeterminado. No es necesario realizar ningún cambio en los roles predeterminados.

    h. Ahora debe generar roles nuevos para la aplicación. 

    i. En el JSON que aparece a continuación, se muestra un ejemplo del objeto appRoles. Cree un objeto similar para agregar los roles que desee para su aplicación. 

    ```
    {
       "appRoles": [
        {
            "allowedMemberTypes": [
                "User"
            ],
            "description": "msiam_access",
            "displayName": "msiam_access",
            "id": "b9632174-c057-4f7e-951b-be3adc52bfe6",
            "isEnabled": true,
            "origin": "Application",
            "value": null
        },
        {
            "allowedMemberTypes": [
                "User"
            ],
            "description": "Administrators Only",
            "displayName": "Admin",
            "id": "4f8f8640-f081-492d-97a0-caf24e9bc134",
            "isEnabled": true,
            "origin": "ServicePrincipal",
            "value": "Administrator"
        }
    ],
    }
    ```
    > [!Note]
    > Solo puede agregar nuevos roles además de **msiam_access** para la operación de revisión. Además, puede agregar tantos roles como desee según las necesidades de su organización. Azure AD enviará el **valor** de estos roles como valor de notificación en la respuesta de SAML.
    
    j. Vuelva al explorador de Graph y cambie el método de **GET** a **PATCH**. Revise el objeto de entidad de servicio para obtener los roles deseados mediante la actualización de la propiedad appRoles de manera similar a la que aparece anteriormente en el ejemplo. Haga clic en **Ejecutar consulta** para ejecutar la operación de revisión. Un mensaje confirma la creación del rol.

    ![Cuadro de diálogo del Probador de Graph](./media/active-directory-enterprise-app-role-management/graph-explorer-new11.png)

7. Una vez que haya revisado la entidad de servicio con más roles, podrá asignar usuarios a los roles correspondientes. Para hacer esto, vaya al portal y navegue hasta la aplicación correspondiente. En la parte superior, haga clic en la pestaña **Usuarios y grupos**. Esta acción muestra todos los usuarios y grupos que ya están asignados a la aplicación. Puede agregar nuevos usuarios al nuevo rol y también puede seleccionar el usuario existente y hacer clic en **Editar** para cambiar el rol.

    ![Configurar la adición del inicio de sesión único](./media/active-directory-enterprise-app-role-management/graph-explorer-new5.png)

     Para asignar el rol a cualquier usuario, seleccione el nuevo rol y haga clic en el botón **Asignar** de la parte inferior de la página.

    ![Configurar la adición del inicio de sesión único](./media/active-directory-enterprise-app-role-management/graph-explorer-new6.png)

    > [!Note]
    > Tenga en cuenta que debe actualizar la sesión en Azure Portal para ver los nuevos roles.

8. Después de asignar roles a los usuarios, deberá actualizar la tabla **Atributos** para definir una asignación personalizada de la notificación de **rol**.

9. En la sección **Atributos de usuario** del cuadro de diálogo **Inicio de sesión único**, configure el atributo token de SAML como muestra la imagen y siga estos pasos:
    
    | Nombre del atributo | Valor de atributo |
    | -------------- | ----------------|    
    | Nombre de rol      | user.assignedrole |

    a. Haga clic en **Agregar atributo** para abrir el cuadro de diálogo **Agregar atributo**.

    ![Configurar la adición del inicio de sesión único](./media/active-directory-enterprise-app-role-management/tutorial_attribute_04.png)

    ![Configuración del atributo de inicio de sesión único](./media/active-directory-enterprise-app-role-management/tutorial_attribute_05.png)

    b. En el cuadro de texto **Nombre**, escriba el nombre de atributo según sea necesario. En este ejemplo hemos usado **Nombre del rol** como nombre de la notificación.

    c. En la lista **Valor**, seleccione el atributo que se muestra para esa fila.

    d. Deje **Espacio de nombres** en blanco.
    
    e. Haga clic en **Aceptar**.

10. Para probar la aplicación en inicio de sesión único iniciado por IDP, inicie sesión en el Panel de acceso (https://myapps.microsoft.com)) y haga clic en el icono de la aplicación. En el token SAML, debería ver todos los roles asignados para el usuario con el nombre de notificación que les dio.

## <a name="update-existing-role"></a>Actualización de un rol existente

Para actualizar un rol existente, realice los pasos siguientes:

1. Abra el [Explorador de Azure AD Graph](https://developer.microsoft.com/graph/graph-explorer).

2. Inicie sesión en el sitio del Probador de Graph con las credenciales globales de administrador o coadministrador del inquilino.
    
3. Cambie la versión a **beta** y recupere la lista de entidades de servicio desde el inquilino con la siguiente consulta:
    
    `https://graph.microsoft.com/beta/servicePrincipals`
    
    Si usa varios directorios, debe seguir este patrón `https://graph.microsoft.com/beta/contoso.com/servicePrincipals`.

    ![Cuadro de diálogo del Probador de Graph](./media/active-directory-enterprise-app-role-management/graph-explorer-new1.png)
    
4. En la lista de entidades de servicio que se capturan, seleccione la que debe modificar. También puede usar CTRL + F para buscar la aplicación en todas las entidades de servicio enumeradas. Busque el **identificador de objeto** que copió de la página Propiedades y use la consulta siguiente para ir a la entidad de servicio correspondiente.
    
    `https://graph.microsoft.com/beta/servicePrincipals/<objectID>`.

    ![Cuadro de diálogo del Probador de Graph](./media/active-directory-enterprise-app-role-management/graph-explorer-new2.png)
    
5. Extraiga la propiedad appRoles del objeto de entidad de servicio.
    
    ![Cuadro de diálogo del Probador de Graph](./media/active-directory-enterprise-app-role-management/graph-explorer-new3.png)
    
6. Para actualizar el rol existente, siga los pasos siguientes:

    ![Cuadro de diálogo del Probador de Graph](./media/active-directory-enterprise-app-role-management/graph-explorer-patchupdate.png)
    
    * Cambie el método de **GET** a **PATCH**.

    * Copie los roles existentes y péguelos en el **cuerpo de la solicitud**.

    * Actualice el valor del rol mediante la actualización de la **descripción del rol**, el **valor del rol** o el **nombre para mostrar del rol** según sea necesario.

    * Después de actualizar todos los roles necesarios, haga clic en **Run Query** (Ejecutar consulta).
        
## <a name="delete-existing-role"></a>Eliminación del rol existente

Para eliminar un rol existente, realice los pasos siguientes:

1. Abra el sitio de [Probador de Azure AD Graph](https://developer.microsoft.com/graph/graph-explorer) en otra ventana.

2. Inicie sesión en el sitio del Probador de Graph con las credenciales globales de administrador o coadministrador del inquilino.

3. Cambie la versión a **beta** y recupere la lista de entidades de servicio desde el inquilino con la siguiente consulta:
    
    `https://graph.microsoft.com/beta/servicePrincipals`
    
    Si usa varios directorios, debe seguir este patrón `https://graph.microsoft.com/beta/contoso.com/servicePrincipals`.
    
    ![Cuadro de diálogo del Probador de Graph](./media/active-directory-enterprise-app-role-management/graph-explorer-new1.png)
    
4. En la lista de entidades de servicio que se capturan, seleccione la que debe modificar. También puede usar CTRL + F para buscar la aplicación en todas las entidades de servicio enumeradas. Busque el **identificador de objeto** que copió de la página Propiedades y use la consulta siguiente para ir a la entidad de servicio correspondiente.
     
    `https://graph.microsoft.com/beta/servicePrincipals/<objectID>`.

    ![Cuadro de diálogo del Probador de Graph](./media/active-directory-enterprise-app-role-management/graph-explorer-new2.png)
    
5. Extraiga la propiedad appRoles del objeto de entidad de servicio.
    
    ![Cuadro de diálogo del Probador de Graph](./media/active-directory-enterprise-app-role-management/graph-explorer-new7.png)

6. Para eliminar el rol existente, siga los pasos siguientes:

    ![Cuadro de diálogo del Probador de Graph](./media/active-directory-enterprise-app-role-management/graph-explorer-new8.png)

    * Cambie el método de **GET** a **PATCH**.

    * Copie los roles existentes de la aplicación y péguelos en el **cuerpo de la solicitud**.
        
    * Establezca el valor **IsEnabled** en **false** para el rol que quiere eliminar.

    * Haga clic en **Ejecutar consulta**.
    
    > [!NOTE] 
    > Asegúrese de que dispone del rol de usuario **msiam_access** y de que el identificador coincide con el rol generado.
    
7. Una vez que el rol está deshabilitado, elimine ese bloque de roles de la sección appRoles, mantenga el método en **PATCH** y haga clic en **Ejecutar consulta**.
    
8. Después de ejecutar la consulta se eliminará el rol.
    
    > [!NOTE]
    > El rol debe estar deshabilitado para poder eliminarlo. 

## <a name="next-steps"></a>Pasos siguientes

Consulte la [documentación de la aplicación ](https://docs.microsoft.com/en-us/azure/active-directory/active-directory-saas-tutorial-list) para conocer los pasos adicionales.

<!--Image references-->
<!--Image references-->

[1]: ./media/active-directory-enterprise-app-role-management/tutorial_general_01.png
[2]: ./media/active-directory-enterprise-app-role-management/tutorial_general_02.png
[3]: ./media/active-directory-enterprise-app-role-management/tutorial_general_03.png
[4]: ./media/active-directory-enterprise-app-role-management/tutorial_general_04.png
