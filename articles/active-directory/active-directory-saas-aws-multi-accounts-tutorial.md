---
title: 'Tutorial: Integración de Azure Active Directory con Amazon Web Services (AWS) para conectar varias cuentas | Microsoft Docs'
description: Aprenda a configurar el inicio de sesión único entre Azure AD y Amazon Web Services (AWS).
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 7561c20b-2325-4d97-887f-693aa383c7be
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/28/2018
ms.author: jeedes
ms.openlocfilehash: 929caab0aafdad24062e372e458f16a5f36cce73
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 04/18/2018
---
# <a name="tutorial-azure-active-directory-integration-with-multiple-amazon-web-services-aws-accounts"></a>Tutorial: Integración de Azure Active Directory con varias cuentas de Amazon Web Services (AWS)

En este tutorial, aprenderá a integrar Azure Active Directory (Azure AD) con varias cuentas de Amazon Web Services (AWS).

La integración de Amazon Web Services (AWS) con Azure AD proporciona las siguientes ventajas:

- Puede controlar en Azure AD quién tiene acceso a Amazon Web Services (AWS).
- Puede permitir que los usuarios inicien sesión automáticamente en Amazon Web Services (AWS) (inicio de sesión único) con sus cuentas de Azure AD.
- Puede administrar sus cuentas en una ubicación central: Azure Portal.

Si desea saber más sobre la integración de aplicaciones SaaS con Azure AD, consulte [¿Qué es el acceso a aplicaciones y el inicio de sesión único con Azure Active Directory?](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>requisitos previos

Para configurar la integración de Azure AD con Amazon Web Services (AWS), necesita los siguientes elementos:

- Una suscripción a Azure AD Básico o Premium
- Varias cuentas de inicio de sesión único habilitadas en Amazon Web Services (AWS)

> [!NOTE]
> Para probar los pasos de este tutorial, no se recomienda el uso de un entorno de producción.

Para probar los pasos de este tutorial, debe seguir estas recomendaciones:

- No use el entorno de producción, salvo que sea necesario.
- Si no dispone de un entorno de prueba de Azure AD, puede [obtener una versión de prueba durante un mes](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descripción del escenario
En este tutorial, puede probar el inicio de sesión único de Azure AD en un entorno de prueba. El escenario descrito en este tutorial consta de dos bloques de creación principales:

1. Adición de Amazon Web Services (AWS) desde la galería
2. Configuración y comprobación del inicio de sesión único de Azure AD

## <a name="adding-amazon-web-services-aws-from-the-gallery"></a>Adición de Amazon Web Services (AWS) desde la galería
Para configurar la integración de Amazon Web Services (AWS) en Azure AD, es preciso agregar Amazon Web Services (AWS) desde la galería a la lista de aplicaciones SaaS administradas.

**Para agregar Amazon Web Services (AWS) desde la galería, realice los pasos siguientes:**

1. En el panel de navegación izquierdo de **[Azure Portal](https://portal.azure.com)**, haga clic en el icono de **Azure Active Directory**. 

    ![Botón Azure Active Directory][1]

2. Vaya a **Aplicaciones empresariales**. A continuación, vaya a **Todas las aplicaciones**.

    ![Hoja Aplicaciones empresariales][2]
    
3. Para agregar una nueva aplicación, haga clic en el botón **Nueva aplicación** de la parte superior del cuadro de diálogo.

    ![Botón Nueva aplicación][3]

4. En el cuadro de búsqueda, escriba **Amazon Web Services (AWS)**, seleccione **Amazon Web Services (AWS)** en el panel de resultados y, a continuación, haga clic en el botón **Agregar** para agregar la aplicación.

    ![Amazon Web Services (AWS) en la lista de resultados](./media/active-directory-saas-aws-multi-accounts-tutorial/tutorial_amazonwebservices(aws)_addfromgallery.png)

5. Una vez agregada la aplicación, vaya a la página **Propiedades** y copie el **identificador de objeto**.

    ![Amazon Web Services (AWS) en la lista de resultados](./media/active-directory-saas-aws-multi-accounts-tutorial/tutorial_amazonwebservices(aws)_properties.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configuración y prueba del inicio de sesión único en Azure AD

En esta sección, podrá configurar y probar el inicio de sesión único de Azure AD con Amazon Web Services (AWS) con un usuario de prueba llamado "Britta Simon".

Para que el inicio de sesión único funcione, Azure AD debe saber cuál es el usuario homólogo de Amazon Web Services (AWS) para un usuario de Azure AD. Es decir, es necesario establecer una relación de vínculo entre un usuario de Azure AD y el usuario relacionado de Amazon Web Services (AWS).

En Amazon Web Services (AWS), asigne el valor de **nombre de usuario** de Azure AD como valor de **Nombre de usuario** para establecer la relación de vínculo.

Para configurar y probar el inicio de sesión único de Azure AD con Amazon Web Services (AWS), es preciso completar los siguientes bloques de creación:

1. **[Configuración del inicio de sesión único de Azure AD](#configure-azure-ad-single-sign-on)**: para que los usuarios puedan usar esta característica.
2. **[Prueba del inicio de sesión único](#test-single-sign-on)**: para comprobar si la configuración funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configuración del inicio de sesión único de Azure AD

En esta sección, habilitará el inicio de sesión único de Azure AD en Azure Portal y configurará el inicio de sesión único en la aplicación de Amazon Web Services (AWS).

**Para configurar el inicio de sesión único de Azure AD con Amazon Web Services (AWS), realice los pasos siguientes:**

1. En Azure Portal, en la página de integración de la aplicación de **Amazon Web Services (AWS)**, haga clic en **Inicio de sesión único**.

    ![Vínculo Configurar inicio de sesión único][4]

2. En el cuadro de diálogo **Inicio de sesión único**, en **Modo** seleccione **Inicio de sesión basado en SAML** para habilitar el inicio de sesión único.
 
    ![Cuadro de diálogo Inicio de sesión único](./media/active-directory-saas-aws-multi-accounts-tutorial/tutorial_amazonwebservices(aws)_samlbase.png)

3. En la sección **Dominio y direcciones URL de Amazon Web Services (AWS)**, el usuario no tiene que realizar ningún paso ya que la aplicación se ha integrado previamente con Azure.

    ![Información de dominio y direcciones URL de inicio de sesión único de Amazon Web Services (AWS)](./media/active-directory-saas-aws-multi-accounts-tutorial/tutorial_amazonwebservices(aws)_url.png)

4. La aplicación de software de Amazon Web Services (AWS) espera las aserciones de SAML en un formato concreto. Configure las siguientes notificaciones para esta aplicación. Puede administrar los valores de estos atributos en la sección "**Atributos de usuario**" de la página de integración de aplicaciones. La siguiente captura de pantalla le muestra un ejemplo de esto.

    ![Configuración del atributo de inicio de sesión único](./media/active-directory-saas-aws-multi-accounts-tutorial/tutorial_amazonwebservices(aws)_attribute.png)  

5. En la sección **Atributos de usuario** del cuadro de diálogo **Inicio de sesión único**, configure el atributo Token SAML como muestra la imagen anterior y realice los siguientes pasos:
    
    | Nombre del atributo  | Valor de atributo | Espacio de nombres |
    | --------------- | --------------- | --------------- |
    | RoleSessionName | user.userprincipalname | https://aws.amazon.com/SAML/Attributes |
    | Rol            | user.assignedroles |  https://aws.amazon.com/SAML/Attributes |
    
    >[!TIP]
    >Debe configurar el aprovisionamiento de usuarios en Azure AD para capturar todos los roles de la consola de AWS. Consulte los pasos de aprovisionamiento a continuación.

    a. Haga clic en **Agregar atributo** para abrir el cuadro de diálogo **Agregar atributo**.

    ![Configurar la opción de agregar el inicio de sesión único](./media/active-directory-saas-aws-multi-accounts-tutorial/tutorial_attribute_04.png)

    ![Configuración del atributo de inicio de sesión único](./media/active-directory-saas-aws-multi-accounts-tutorial/tutorial_attribute_05.png)

    b. En el cuadro de texto **Nombre**, escriba el nombre que se muestra para la fila.

    c. En la lista **Valor**, seleccione el atributo que se muestra para esa fila.

    d. En el cuadro de texto **Espacio de nombres**, escriba el valor del espacio de nombres que se muestra para esa fila.
    
    d. Haga clic en **Aceptar**.

6. En la sección **Certificado de firma de SAML**, haga clic en **XML de metadatos** y luego guarde el archivo de metadatos en el equipo.

    ![Vínculo de descarga del certificado](./media/active-directory-saas-aws-multi-accounts-tutorial/tutorial_amazonwebservices(aws)_certificate.png) 

7. Haga clic en el botón **Guardar** .

    ![Botón Configurar inicio de sesión único](./media/active-directory-saas-aws-multi-accounts-tutorial/tutorial_general_400.png)

8. En otra ventana del explorador, inicie sesión en su sitio de la compañía de Amazon Web Services (AWS) como administrador.

9. Haga clic en **AWS Home** (Página principal de AWS).
   
    ![Configurar página principal de inicio de sesión único][11]

10. Haga clic en **IAM** (Administración de identidades y acceso). 
   
    ![Configurar identidad de inicio de sesión único][12]

11. Haga clic en **Proveedores de identidades** y, después, en **Create Provider** (Crear proveedor). 
   
    ![Configurar proveedor de inicio de sesión único][13]

12. En la página de diálogo **Configure Provider** (Configurar proveedor), realice los pasos siguientes: 
   
    ![Configurar cuadro de diálogo de inicio de sesión único][14]
 
    a. En **Tipo de proveedor**, seleccione **SAML**.

    b. En el cuadro de texto **Provider Name** (Nombre de proveedor), escriba un nombre de proveedor (por ejemplo, *WAAD*).

    c. Para cargar el **archivo de metadatos** descargado de Azure Portal, haga clic en **Choose file** (Elegir archivo).

    d. Haga clic en **Siguiente paso**.

13. En la página de diálogo **Verify Provider Information** (Comprobar la información del proveedor), haga clic en **Crear**. 
    
    ![Configurar verificación de inicio de sesión único][15]

14. Haga clic en **Roles** y, después, en **Crear rol**. 
    
    ![Configurar roles de inicio de sesión único][16]

15. En la página **Crear rol**, realice los pasos siguientes:  
    
    ![Configurar confianza de inicio de sesión único][19] 

    a. Seleccione **SAML 2.0 federation** (Federación de SAML 2.0) en **Select type of trusted entity** (Seleccionar tipo de entidad de confianza).

    b. En la sección **Choose a SAML 2.0 Provider** (Elegir un proveedor de SAML 2.0), seleccione **SAML provider** (Proveedor de SAML) que ha creado anteriormente (por ejemplo: *WAAD*).

    c. Seleccione **Allow programmatic and AWS Management Console access** (Permitir acceso mediante programación y a consola de AWS Management Console).
  
    d. Haga clic en **Next: Permissions** (Siguiente: permisos).

16. En el cuadro de diálogo **Attach Permissions Policies** (Adjuntar directivas de permisos), haga clic en **Next: Review** (Siguiente: revisión).  
    
    ![Configurar directiva de inicio de sesión único][33]

17. En el cuadro de diálogo **Revisar** , realice los pasos siguientes:   
    
    ![Configurar revisión de inicio de sesión único][34] 

    a. En el cuadro de texto **Role name** (Nombre de rol), escriba su nombre de rol.

    b. En el cuadro de texto **Role description**, escriba la descripción.

    a. Haga clic en **Crear rol**.

    b. Cree tantos roles como sea necesario y asígnelos al proveedor de identidades.

18. Cerrar la sesión de la cuenta de AWS actual e inicie sesión con la cuenta en la que desee configurar el inicio de sesión único con Azure AD.

19. Realice los pasos 9 a 17 para crear varios roles que desee configurar en esta cuenta. Si tiene más de dos cuentas, realice los mismos pasos para todas las cuentas para crear roles en ellas.

20. Una vez que se crean todos los roles en las cuentas, se muestran en la lista **Roles** de dichas cuentas.

    ![Instalación de roles](./media/active-directory-saas-aws-multi-accounts-tutorial/tutorial_amazonwebservices(aws)_listofroles.png)

21. Es necesario capturar los valores de ARN de rol y Entidades de confianza de los roles, que necesitamos para asignar manualmente con la aplicación de Azure AD. 

22. Haga clic en los roles para copiar los valores de **ARN de rol** y **Entidades de confianza**. Estos valores son necesarios para todos los roles que se deben crear en Azure AD.

    ![Instalación de roles](./media/active-directory-saas-aws-multi-accounts-tutorial/tutorial_amazonwebservices(aws)_role_summary.png)
 
23. Realice el paso anterior en todos los roles de todas las cuentas y almacenar todas ellas en el formato **ARN de rol,Entidades de confianza** en el Bloc de notas. 

24. Abra el sitio del [Probador de Azure AD Graph](https://developer.microsoft.com/graph/graph-explorer) en otra ventana.

    a. Inicie sesión en el sitio del Probador de Graph con las credenciales de administrador o coadministrador globales del inquilino.

    b. Debe tener permisos suficientes para crear los roles. Haga clic en **Modificar permisos** para obtener los permisos necesarios. 

    ![Cuadro de diálogo del Probador de Graph](./media/active-directory-saas-aws-multi-accounts-tutorial/graph-explorer-new9.png)

    c. Seleccione los siguientes permisos de la lista (si no los tiene ya) y haga clic en "Modificar permisos" 

    ![Cuadro de diálogo del Probador de Graph](./media/active-directory-saas-aws-multi-accounts-tutorial/graph-explorer-new10.png)

    d. En este se le pedirá que vuelva a iniciar la sesión y acepte el consentimiento. Después de aceptar el consentimiento, habrá iniciado sesión en el Probador de Graph de nuevo.

    e. En la lista desplegable de la versión, cambie a **beta**. Para capturar todas las entidades de seguridad de su inquilino, utilice la siguiente consulta:
    
     `https://graph.microsoft.com/beta/servicePrincipals`
        
    Si usa varios directorios, a continuación, puede usar el siguiente patrón, que contiene el dominio principal `https://graph.microsoft.com/beta/contoso.com/servicePrincipals`
    
    ![Cuadro de diálogo del Probador de Graph](./media/active-directory-saas-aws-multi-accounts-tutorial/graph-explorer-new1.png)
    
    f. En la lista de entidades de servicio que se capturan, seleccione la que debe modificar. También puede usar CTRL + F para buscar la aplicación en todas las entidades de servicio enumeradas. Para usar la siguiente consulta, utilice el valor de **Id. de objeto** que ha copiado de la página Propiedades de Azure AD para acceder a entidad de servicio correspondiente.
    
    `https://graph.microsoft.com/beta/servicePrincipals/<objectID>`.

    ![Cuadro de diálogo del Probador de Graph](./media/active-directory-saas-aws-multi-accounts-tutorial/graph-explorer-new2.png)

    g. Extraiga la propiedad appRoles del objeto de entidad de servicio. 

    ![Cuadro de diálogo del Probador de Graph](./media/active-directory-saas-aws-multi-accounts-tutorial/graph-explorer-new3.png)

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
            "id": "7dfd756e-8c27-4472-b2b7-38c17fc5de5e",
            "isEnabled": true,
            "origin": "Application",
            "value": null
        },
        {
            "allowedMemberTypes": [
                "User"
            ],
            "description": "Admin,WAAD",
            "displayName": "Admin,WAAD",
            "id": "4aacf5a4-f38b-4861-b909-bae023e88dde",
            "isEnabled": true,
            "origin": "ServicePrincipal",
            "value": "arn:aws:iam::12345:role/Admin,arn:aws:iam::12345:saml-provider/WAAD"
        },
        {
            "allowedMemberTypes": [
                "User"
            ],
            "description": "Auditors,WAAD",
            "displayName": "Auditors,WAAD",
            "id": "bcad6926-67ec-445a-80f8-578032504c09",
            "isEnabled": true,
            "origin": "ServicePrincipal",
            "value": "arn:aws:iam::12345:role/Auditors,arn:aws:iam::12345:saml-provider/WAAD"
        }    ]
    }
    ```

    > [!Note]
    > Solo puede agregar nuevos roles además de **msiam_access** para la operación de revisión. Además, puede agregar tantos roles como desee según las necesidades de su organización. Azure AD enviará el **valor** de estos roles como valor de notificación en la respuesta de SAML.
    
    j. Vuelva al explorador de Graph y cambie el método de **GET** a **PATCH**. Revise el objeto de entidad de servicio para obtener los roles deseados mediante la actualización de la propiedad appRoles de manera similar a la que aparece anteriormente en el ejemplo. Haga clic en **Ejecutar consulta** para ejecutar la operación de revisión. Un mensaje de operación correcta confirma la creación del rol en la aplicación de Amazon Web Services.

    ![Cuadro de diálogo del Probador de Graph](./media/active-directory-saas-aws-multi-accounts-tutorial/graph-explorer-new11.png)

25. Una vez que haya revisado la entidad de servicio con más roles, podrá asignar usuarios o grupos a los roles correspondientes. Para hacer esto, vaya al portal y navegue hasta la aplicación Amazon Web Services. En la parte superior, haga clic en la pestaña **Usuarios y grupos**. 

26. Se recomienda crear grupos nuevos para cada rol de AWS, con el fin de que pueda asignar dicho rol concreto en ese grupo. Tenga en cuenta que se trata de una asignación uno a uno de un grupo a un rol. Luego, puede agregar los miembros que pertenecen al grupo.

27. Una vez que se crean los grupos, seleccione el grupo y asígnelo a la aplicación. 

    ![Configurar la adición del inicio de sesión único](./media/active-directory-saas-aws-multi-accounts-tutorial/graph-explorer-new5.png)

28. Para asignar el rol al grupo, seleccione el rol y haga clic en el botón **Asignar** de la parte inferior de la página.

    ![Configurar la adición del inicio de sesión único](./media/active-directory-saas-aws-multi-accounts-tutorial/graph-explorer-new6.png)

> [!Note]
> Tenga en cuenta que debe actualizar la sesión en Azure Portal para ver los nuevos roles.

### <a name="test-single-sign-on"></a>Prueba de inicio de sesión único

En esta sección, probará la configuración de inicio de sesión único de Azure AD mediante el Panel de acceso.

Al hacer clic en el icono de Amazon Web Services (AWS) en el panel de acceso, debería acceder a la página de la aplicación Amazon Web Services (AWS) con la opción para seleccionar el rol.

![Configurar la adición del inicio de sesión único](./media/active-directory-saas-aws-multi-accounts-tutorial/tutorial_amazonwebservices(aws)_test_screen.png)

También puede comprobar la respuesta de SAML para ver los roles que se usan como notificaciones.

![Configurar la adición del inicio de sesión único](./media/active-directory-saas-aws-multi-accounts-tutorial/tutorial_amazonwebservices(aws)_test_saml.png)

Para más información sobre el Panel de acceso, consulte [Introducción al Panel de acceso](active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Recursos adicionales

* [Lista de tutoriales sobre cómo integrar aplicaciones SaaS con Azure Active Directory](active-directory-saas-tutorial-list.md)
* [¿Qué es el acceso a aplicaciones y el inicio de sesión único con Azure Active Directory?](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-aws-multi-accounts-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-aws-multi-accounts-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-aws-multi-accounts-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-aws-multi-accounts-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-aws-multi-accounts-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-aws-multi-accounts-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-aws-multi-accounts-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-aws-multi-accounts-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-aws-multi-accounts-tutorial/tutorial_general_203.png
[11]: ./media/active-directory-saas-aws-multi-accounts-tutorial/ic795031.png
[12]: ./media/active-directory-saas-aws-multi-accounts-tutorial/ic795032.png
[13]: ./media/active-directory-saas-aws-multi-accounts-tutorial/ic795033.png
[14]: ./media/active-directory-saas-aws-multi-accounts-tutorial/ic795034.png
[15]: ./media/active-directory-saas-aws-multi-accounts-tutorial/ic795035.png
[16]: ./media/active-directory-saas-aws-multi-accounts-tutorial/ic795022.png
[17]: ./media/active-directory-saas-aws-multi-accounts-tutorial/ic795023.png
[18]: ./media/active-directory-saas-aws-multi-accounts-tutorial/ic795024.png
[19]: ./media/active-directory-saas-aws-multi-accounts-tutorial/ic795025.png
[32]: ./media/active-directory-saas-aws-multi-accounts-tutorial/ic7950251.png
[33]: ./media/active-directory-saas-aws-multi-accounts-tutorial/ic7950252.png
[35]: ./media/active-directory-saas-aws-multi-accounts-tutorial/tutorial_amazonwebservices_provisioning.png
[34]: ./media/active-directory-saas-aws-multi-accounts-tutorial/ic7950253.png
[36]: ./media/active-directory-saas-aws-multi-accounts-tutorial/tutorial_amazonwebservices_securitycredentials.png
[37]: ./media/active-directory-saas-aws-multi-accounts-tutorial/tutorial_amazonwebservices_securitycredentials_continue.png
[38]: ./media/active-directory-saas-aws-multi-accounts-tutorial/tutorial_amazonwebservices_createnewaccesskey.png
[39]: ./media/active-directory-saas-aws-multi-accounts-tutorial/tutorial_amazonwebservices_provisioning_automatic.png
[40]: ./media/active-directory-saas-aws-multi-accounts-tutorial/tutorial_amazonwebservices_provisioning_testconnection.png
[41]: ./media/active-directory-saas-aws-multi-accounts-tutorial/tutorial_amazonwebservices_provisioning_on.png

