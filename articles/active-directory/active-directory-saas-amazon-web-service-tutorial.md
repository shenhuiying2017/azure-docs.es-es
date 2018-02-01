---
title: "Tutorial: Integración de Azure Active Directory con Amazon Web Services (AWS) | Microsoft Docs"
description: "Obtenga información sobre cómo configurar el inicio de sesión único entre Azure Active Directory y Amazon Web Services (AWS)."
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
ms.date: 1/16/2017
ms.author: jeedes
ms.openlocfilehash: 8d77215fd2923e22a9cc87e469cb135d035d22d9
ms.sourcegitcommit: 2a70752d0987585d480f374c3e2dba0cd5097880
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/19/2018
---
# <a name="tutorial-azure-active-directory-integration-with-amazon-web-services-aws"></a>Tutorial: integración de Azure Active Directory con Amazon Web Service (AWS)

En este tutorial, obtendrá información sobre cómo integrar Amazon Web Services (AWS) con Azure Active Directory (Azure AD).

La integración de Amazon Web Services (AWS) con Azure AD proporciona las siguientes ventajas:

- Puede controlar en Azure AD quién tiene acceso a Amazon Web Services (AWS).
- Puede permitir que los usuarios inicien sesión automáticamente en Amazon Web Services (AWS) (inicio de sesión único) con sus cuentas de Azure AD.
- Puede administrar sus cuentas en una ubicación central: Azure Portal.

Si desea saber más sobre la integración de aplicaciones SaaS con Azure AD, consulte [¿Qué es el acceso a aplicaciones y el inicio de sesión único con Azure Active Directory?](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>requisitos previos

Para configurar la integración de Azure AD con Amazon Web Services (AWS), necesita los siguientes elementos:

- Una suscripción de Azure AD
- Un suscripción habilitada para el inicio de sesión único en Amazon Web Services (AWS)

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

    ![Amazon Web Services (AWS) en la lista de resultados](./media/active-directory-saas-amazon-web-service-tutorial/tutorial_amazonwebservices(aws)_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configuración y prueba del inicio de sesión único en Azure AD

En esta sección, podrá configurar y probar el inicio de sesión único de Azure AD con Amazon Web Services (AWS) con un usuario de prueba llamado "Britta Simon".

Para que el inicio de sesión único funcione, Azure AD debe saber cuál es el usuario homólogo de Amazon Web Services (AWS) para un usuario de Azure AD. Es decir, es necesario establecer una relación de vínculo entre un usuario de Azure AD y el usuario relacionado de Amazon Web Services (AWS).

En Amazon Web Services (AWS), asigne el valor de **nombre de usuario** de Azure AD como valor de **Nombre de usuario** para establecer la relación de vínculo.

Para configurar y probar el inicio de sesión único de Azure AD con Amazon Web Services (AWS), es preciso completar los siguientes bloques de creación:

1. **[Configuración del inicio de sesión único de Azure AD](#configure-azure-ad-single-sign-on)**: para que los usuarios puedan usar esta característica.
2. **[Creación de un usuario de prueba de Azure AD](#create-an-azure-ad-test-user)**, para probar el inicio de sesión único de Azure AD con Britta Simon.
3. **[Creación de un usuario de prueba de Amazon Web Services (AWS)](#create-an-amazon-web-services-aws-test-user)**: para tener un homólogo de Britta Simon en Amazon Web Services (AWS) que esté vinculado a la representación del usuario en Azure AD.
4. **[Asignación del usuario de prueba de Azure AD](#assign-the-azure-ad-test-user)**, para permitir que Britta Simon use el inicio de sesión único de Azure AD.
5. **[Prueba del inicio de sesión único](#test-single-sign-on)**: para comprobar si la configuración funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configuración del inicio de sesión único de Azure AD

En esta sección, habilitará el inicio de sesión único de Azure AD en Azure Portal y configurará el inicio de sesión único en la aplicación de Amazon Web Services (AWS).

**Para configurar el inicio de sesión único de Azure AD con Amazon Web Services (AWS), realice los pasos siguientes:**

1. En Azure Portal, en la página de integración de la aplicación de **Amazon Web Services (AWS)**, haga clic en **Inicio de sesión único**.

    ![Vínculo Configurar inicio de sesión único][4]

2. En el cuadro de diálogo **Inicio de sesión único**, en **Modo** seleccione **Inicio de sesión basado en SAML** para habilitar el inicio de sesión único.
 
    ![Cuadro de diálogo Inicio de sesión único](./media/active-directory-saas-amazon-web-service-tutorial/tutorial_amazonwebservices(aws)_samlbase.png)

3. En la sección **Dominio y direcciones URL de Amazon Web Services (AWS)**, el usuario no tiene que realizar ningún paso ya que la aplicación se ha integrado previamente con Azure.

    ![Información de dominio y direcciones URL de inicio de sesión único de Amazon Web Services (AWS)](./media/active-directory-saas-amazon-web-service-tutorial/tutorial_amazonwebservices(aws)_url.png)

4. La aplicación de software de Amazon Web Services (AWS) espera las aserciones de SAML en un formato concreto. Configure las siguientes notificaciones para esta aplicación. Puede administrar los valores de estos atributos en la sección "**Atributos de usuario**" de la página de integración de aplicaciones. La siguiente captura de pantalla le muestra un ejemplo de esto.

    ![Configurar el atributo de inicio de sesión único](./media/active-directory-saas-amazon-web-service-tutorial/tutorial_amazonwebservices(aws)_attribute.png)   

5. En la sección **Atributos de usuario** del cuadro de diálogo **Inicio de sesión único**, configure el atributo Token SAML como muestra la imagen anterior y realice los siguientes pasos:
    
    | Nombre del atributo  | Valor de atributo | Espacio de nombres |
    | --------------- | --------------- | --------------- |
    | RoleSessionName | user.userprincipalname | https://aws.amazon.com/SAML/Attributes |
    | Rol            | user.assignedroles |  https://aws.amazon.com/SAML/Attributes |
    
    >[!TIP]
    >Debe configurar el aprovisionamiento de usuarios en Azure AD para capturar todos los roles de la consola de AWS. Consulte los pasos de aprovisionamiento a continuación.

    a. Haga clic en **Agregar atributo** para abrir el cuadro de diálogo **Agregar atributo**.

    ![Configurar la opción de agregar el inicio de sesión único](./media/active-directory-saas-amazon-web-service-tutorial/tutorial_attribute_04.png)

    ![Configurar la opción de agregar el atributo del inicio de sesión único](./media/active-directory-saas-amazon-web-service-tutorial/tutorial_attribute_05.png)

    b. En el cuadro de texto **Nombre**, escriba el nombre que se muestra para la fila.

    c. En la lista **Valor**, seleccione el atributo que se muestra para esa fila.

    d. En el cuadro de texto **Espacio de nombres**, escriba el valor del espacio de nombres que se muestra para esa fila.
    
    d. Haga clic en **Aceptar**.

6. En la sección **Certificado de firma de SAML**, haga clic en **XML de metadatos** y luego guarde el archivo de metadatos en el equipo.

    ![Vínculo de descarga del certificado](./media/active-directory-saas-amazon-web-service-tutorial/tutorial_amazonwebservices(aws)_certificate.png) 

7. Haga clic en el botón **Guardar** .

    ![Botón Configurar inicio de sesión único](./media/active-directory-saas-amazon-web-service-tutorial/tutorial_general_400.png)

8. En otra ventana del explorador, inicie sesión en su sitio de la compañía de Amazon Web Services (AWS) como administrador.

9. Haga clic en **Página principal de la consola**.
   
    ![Configurar página principal de inicio de sesión único][11]

10. Haga clic en **Administración de identidades y acceso**. 
   
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

14. Haga clic en **Roles** y, después, en **Crear nuevo rol**. 
    
    ![Configurar roles de inicio de sesión único][16]

15. En el cuadro de diálogo **Set Role Name** (Establecer nombre de rol), realice los siguientes pasos: 
    
    ![Configurar nombre de inicio de sesión único][17] 

    a. En el cuadro de texto **Nombre de rol**, escriba un nombre de rol (por ejemplo, *TestUser*). 

    b. Haga clic en **Siguiente paso**.

16. En el cuadro de diálogo **Select Role Type** (Seleccionar tipo de rol), realice los siguientes pasos: 
    
    ![Configurar tipo de rol de inicio de sesión único][18] 

    a. Seleccione **Rol de acceso del proveedor de identidades**. 

    b. En la sección **Conceder acceso de inicio de sesión único web (WebSSO)** a los proveedores de SAML, haga clic en **Seleccionar**.

17. En el cuadro de diálogo **Establecer confianza** , realice los siguientes pasos:  
    
    ![Configurar confianza de inicio de sesión único][19] 

    a. Como proveedor de SAML, seleccione el proveedor de SAML que ha creado antes (por ejemplo, *WAAD*). 
  
    b. Haga clic en **Siguiente paso**.

18. En el diálogo **Verify Role Trust** (Comprobar la confianza del rol), haga clic en **Paso siguiente**. 
    
    ![Configurar confianza de rol de inicio de sesión único][32]

19. En el diálogo **Attach Policy** (Adjuntar directiva), haga clic en **Paso siguiente**.  
    
    ![Configurar directiva de inicio de sesión único][33]

20. En el cuadro de diálogo **Revisar** , realice los pasos siguientes:   
    
    ![Configurar revisión de inicio de sesión único][34] 

    a. Haga clic en **Crear rol**.

    b. Cree tantos roles como sea necesario y asígnelos al proveedor de identidades.

21. Use credenciales de cuenta de servicio de AWS para obtener los roles de cuenta de AWS en aprovisionamiento de usuarios de Azure AD. Para ello, abra la página principal de la consola de AWS.

22. Haga clic en **Servicios** -> **Seguridad, identidad y cumplimiento** -> **IAM**.

    ![obtención de roles de la cuenta de AWS](./media/active-directory-saas-amazon-web-service-tutorial/fetchingrole1.png)

23. Seleccione la pestaña **Tabs** (Directivas) en la sección IAM.

    ![obtención de roles de la cuenta de AWS](./media/active-directory-saas-amazon-web-service-tutorial/fetchingrole2.png)

24. Haga clic en **Create policy** (Crear directiva) para crear una nueva directiva.

    ![Creación de una nueva directiva](./media/active-directory-saas-amazon-web-service-tutorial/fetchingrole3.png)
 
25. Cree su propia directiva para obtener todos los roles de las cuentas de AWS con estos pasos:

    ![Creación de una nueva directiva](./media/active-directory-saas-amazon-web-service-tutorial/policy1.png)

    a. En la sección **"Crear directiva"**, haga clic en la pestaña **"JSON"**.

    b. En el documento de la directiva, agregue el siguiente JSON.
    
    ```
    
    {

    "Version": "2012-10-17",

    "Statement": [

    {

    "Effect": "Allow",
        
    "Action": [
        
    "iam: ListRoles"
        
    ],

    "Resource": "*"

    }

    ]

    }
    
    ```

    c. Haga clic en el botón **Revisar directiva** para validar la directiva.

    ![Definición de una nueva directiva](./media/active-directory-saas-amazon-web-service-tutorial/policy5.png)

26. Defina la **directiva nueva** con estos pasos:

    ![Definición de una nueva directiva](./media/active-directory-saas-amazon-web-service-tutorial/policy2.png)

    a. En **Policy Name** (Nombre de la directiva), especifique **AzureAD_SSOUserRole_Policy**.

    b. En **Description** (Descripción), puede describir la directiva como **Esta directiva permitirá obtener los roles de cuentas de AWS**.
    
    c. Haga clic en el botón **"Crear directiva"**.
        
27. Cree una nueva cuenta de usuario en el servicio IAM de AWS mediante los pasos siguientes:

    a. Haga clic en el panel de navegación **Users** (Usuarios) en la consola de IAM de AWS.

    ![Definición de una nueva directiva](./media/active-directory-saas-amazon-web-service-tutorial/policy3.png)
    
    b. Haga clic en el botón **Add user** (Agregar usuario) para crear un nuevo usuario.

    ![Agregar usuario](./media/active-directory-saas-amazon-web-service-tutorial/policy4.png)

    c. En la sección **Add user** (Agregar usuario), lleve a cabo estos pasos:
    
    ![Agregar usuario](./media/active-directory-saas-amazon-web-service-tutorial/adduser1.png)
    
    * Escriba el nombre de usuario como **AzureADRoleManager**.
    
    * En el tipo de acceso, seleccione la opción **Programmatic access** (Acceso mediante programación). De este modo, el usuario puede llamar a las API y obtener los roles de la cuenta de AWS.
    
    * Haga clic en el botón **Next Permissions** (Permisos siguientes) en la esquina inferior derecha.

28. Ahora cree una nueva directiva para este usuario mediante los pasos siguientes:

    ![Agregar usuario](./media/active-directory-saas-amazon-web-service-tutorial/adduser2.png)
    
    a. Haga clic en el botón **Attach existing policies directly** (Asociar directivas existentes directamente).

    b. Busque la directiva recién creada en la sección de filtro **AzureAD_SSOUserRole_Policy**.
    
    c. Seleccione la **directiva** y, a continuación, haga clic en el botón **Next: Review** (Siguiente: Revisión).

29. Revise la directiva del usuario asociado mediante los pasos siguientes:

    ![Agregar usuario](./media/active-directory-saas-amazon-web-service-tutorial/adduser3.png)
    
    a. Revise el nombre de usuario, el tipo de acceso y la directiva asociada al usuario.
    
    b. Haga clic en el botón **Create user** (Crear usuario) situado en la esquina inferior derecha para crear el usuario.

30. Descargue las credenciales de usuario de un usuario mediante estos pasos:

    ![Agregar usuario](./media/active-directory-saas-amazon-web-service-tutorial/adduser4.png)
    
    a. Copie el **identificador de la clave de acceso** y la **clave de acceso secreta** del usuario.
    
    b. Escriba estas credenciales en la sección de aprovisionamiento de usuarios de Azure AD para obtener los roles de la consola de AWS.
    
    c. Haga clic en el botón **Close** (Cerrar) en la parte inferior.

31. Vaya a la sección **Aprovisionamiento de usuarios** de la aplicación Amazon Web Services en el Portal de administración de Azure AD.

    ![Agregar usuario](./media/active-directory-saas-amazon-web-service-tutorial/provisioning.png)

32. Escriba la **clave de acceso** y la **clave secreta** en los campos **Secreto de cliente** y **Token secreto** respectivamente.

    ![Agregar usuario](./media/active-directory-saas-amazon-web-service-tutorial/provisioning1.png)
    
    a. Escriba la clave de acceso de usuario de AWS en el campo **Secreto de cliente**.
    
    b. Escriba el secreto de usuario de AWS en el campo **Token secreto**.
    
    c. Haga clic en el botón **Probar conexión**. Al hacerlo, debería poder probar correctamente esta conexión.

    d. Haga clic en el botón **Guardar** situado en la parte superior para guardar la configuración.
 
33. Ahora, asegúrese de establecer el estado de aprovisionamiento en **Activado** en la sección de configuración, para lo que debe activar el conmutador y, a continuación, hacer clic en el botón **Guardar** situado en la parte superior.

    ![Agregar usuario](./media/active-directory-saas-amazon-web-service-tutorial/provisioning2.png)

> [!TIP]
> Ahora puede leer una versión resumida de estas instrucciones dentro de [Azure Portal](https://portal.azure.com) mientras configura la aplicación.  Después de agregar esta aplicación desde la sección **Active Directory > Aplicaciones empresariales**, simplemente haga clic en la pestaña **Inicio de sesión único** y acceda a la documentación insertada a través de la sección **Configuración** de la parte inferior. Puede leer más sobre la característica de documentación insertada aquí: [Vista previa: Administración de inicio de sesión único para aplicaciones empresariales en el nuevo Azure Portal]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="create-an-azure-ad-test-user"></a>Creación de un usuario de prueba de Azure AD

El objetivo de esta sección es crear un usuario de prueba en Azure Portal llamado "Britta Simon".

   ![Creación de un usuario de prueba de Azure AD][100]

**Siga estos pasos para crear un usuario de prueba en Azure AD:**

1. En el panel izquierdo de Azure Portal, haga clic en el botón **Azure Active Directory**.

    ![Botón Azure Active Directory](./media/active-directory-saas-amazon-web-service-tutorial/create_aaduser_01.png)

2. Para mostrar la lista de usuarios, vaya a **Usuarios y grupos** y, luego, haga clic en **Todos los usuarios**.

    ![Vínculos "Usuarios y grupos" y "Todos los usuarios"](./media/active-directory-saas-amazon-web-service-tutorial/create_aaduser_02.png)

3. En la parte superior del cuadro de diálogo **Todos los usuarios**, haga clic en **Agregar** para abrir el cuadro de diálogo **Agregar**.

    ![Botón Agregar](./media/active-directory-saas-amazon-web-service-tutorial/create_aaduser_03.png)

4. En el cuadro de diálogo **Usuario** , realice los pasos siguientes:

    ![Cuadro de diálogo Usuario](./media/active-directory-saas-amazon-web-service-tutorial/create_aaduser_04.png)

    a. En el cuadro **Nombre**, escriba **BrittaSimon**.

    b. En el cuadro de texto **Nombre de usuario**, escriba la dirección de correo electrónico del usuario Britta Simon.

    c. Active la casilla **Mostrar contraseña** y, después, anote el valor que se muestra en el cuadro **Contraseña**.

    d. Haga clic en **Create**(Crear).
 
### <a name="create-an-amazon-web-services-aws-test-user"></a>Creación de un usuario de prueba de Amazon Web Services (AWS)

El objetivo de esta sección es crear un usuario llamado Britta Simon en Amazon Web Services (AWS). Amazon Web Services (AWS) no necesita que se cree un usuario en su sistema para el inicio de sesión único, por lo que no es necesario realizar ninguna acción aquí.

### <a name="assign-the-azure-ad-test-user"></a>Asignación del usuario de prueba de Azure AD

En esta sección, habilitará a Britta Simon para que use el inicio de sesión único de Azure concediéndole acceso a Amazon Web Services (AWS).

![Asignación de rol de usuario][200] 

**Para asignar Britta Simon a Amazon Web Services (AWS), realice los pasos siguientes:**

1. En Azure Portal, abra la vista de aplicaciones, navegue a la vista de directorio y vaya a **Aplicaciones empresariales**. Luego haga clic en **Todas las aplicaciones**.

    ![Asignar usuario][201] 

2. En la lista de aplicaciones, seleccione **Amazon Web Services (AWS)**.

    ![En la lista de aplicaciones, seleccione el vínculo de Amazon Web Services (AWS).](./media/active-directory-saas-amazon-web-service-tutorial/tutorial_amazonwebservices(aws)_app.png)  

3. En el menú de la izquierda, haga clic en **Usuarios y grupos**.

    ![Vínculo "Usuarios y grupos"][202]

4. Haga clic en el botón **Agregar**. Después, seleccione **Usuarios y grupos** en el cuadro de diálogo **Agregar asignación**.

    ![Panel Agregar asignación][203]

5. En el cuadro de diálogo **Usuarios y grupos**, seleccione **Britta Simon** en la lista de usuarios.

6. Haga clic en el botón **Seleccionar** del cuadro de diálogo **Usuarios y grupos**.

7. Haga clic en el botón **Asignar** del cuadro de diálogo **Agregar asignación**.
    
### <a name="test-single-sign-on"></a>Prueba de inicio de sesión único

En esta sección, probará la configuración de inicio de sesión único de Azure AD mediante el Panel de acceso.

Al hacer clic en el icono de Amazon Web Services (AWS) en el panel de acceso, automáticamente iniciará sesión en su aplicación de Amazon Web Services (AWS).
Para más información sobre el Panel de acceso, consulte [Introducción al Panel de acceso](active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Recursos adicionales

* [Lista de tutoriales sobre cómo integrar aplicaciones SaaS con Azure Active Directory](active-directory-saas-tutorial-list.md)
* [¿Qué es el acceso a aplicaciones y el inicio de sesión único con Azure Active Directory?](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-amazon-web-service-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-amazon-web-service-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-amazon-web-service-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-amazon-web-service-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-amazon-web-service-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-amazon-web-service-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-amazon-web-service-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-amazon-web-service-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-amazon-web-service-tutorial/tutorial_general_203.png
[11]: ./media/active-directory-saas-amazon-web-service-tutorial/ic795031.png
[12]: ./media/active-directory-saas-amazon-web-service-tutorial/ic795032.png
[13]: ./media/active-directory-saas-amazon-web-service-tutorial/ic795033.png
[14]: ./media/active-directory-saas-amazon-web-service-tutorial/ic795034.png
[15]: ./media/active-directory-saas-amazon-web-service-tutorial/ic795035.png
[16]: ./media/active-directory-saas-amazon-web-service-tutorial/ic795022.png
[17]: ./media/active-directory-saas-amazon-web-service-tutorial/ic795023.png
[18]: ./media/active-directory-saas-amazon-web-service-tutorial/ic795024.png
[19]: ./media/active-directory-saas-amazon-web-service-tutorial/ic795025.png
[32]: ./media/active-directory-saas-amazon-web-service-tutorial/ic7950251.png
[33]: ./media/active-directory-saas-amazon-web-service-tutorial/ic7950252.png
[35]: ./media/active-directory-saas-amazon-web-service-tutorial/tutorial_amazonwebservices_provisioning.png
[34]: ./media/active-directory-saas-amazon-web-service-tutorial/ic7950253.png
[36]: ./media/active-directory-saas-amazon-web-service-tutorial/tutorial_amazonwebservices_securitycredentials.png
[37]: ./media/active-directory-saas-amazon-web-service-tutorial/tutorial_amazonwebservices_securitycredentials_continue.png
[38]: ./media/active-directory-saas-amazon-web-service-tutorial/tutorial_amazonwebservices_createnewaccesskey.png
[39]: ./media/active-directory-saas-amazon-web-service-tutorial/tutorial_amazonwebservices_provisioning_automatic.png
[40]: ./media/active-directory-saas-amazon-web-service-tutorial/tutorial_amazonwebservices_provisioning_testconnection.png
[41]: ./media/active-directory-saas-amazon-web-service-tutorial/tutorial_amazonwebservices_provisioning_on.png

