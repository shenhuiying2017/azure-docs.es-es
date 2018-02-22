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
ms.openlocfilehash: 0ff14365323d66a101e5847d7959045c3f20dea2
ms.sourcegitcommit: 059dae3d8a0e716adc95ad2296843a45745a415d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 02/09/2018
---
# <a name="tutorial-azure-active-directory-integration-with-amazon-web-services-aws"></a>Tutorial: integración de Azure Active Directory con Amazon Web Service (AWS)

En este tutorial, obtendrá información sobre cómo integrar Amazon Web Services (AWS) con Azure Active Directory (Azure AD).

La integración de Amazon Web Services (AWS) con Azure AD proporciona las siguientes ventajas:

- Puede controlar en Azure AD quién tiene acceso a Amazon Web Services (AWS).
- Puede permitir que los usuarios inicien sesión automáticamente en Amazon Web Services (AWS) con sus cuentas de Azure AD.
- Puede administrar sus cuentas en una ubicación central: Azure Portal.

Para más información sobre la integración de aplicaciones SaaS con Azure AD, consulte [¿Qué es el acceso a aplicaciones y el inicio de sesión único con Azure Active Directory?](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>requisitos previos

Para configurar la integración de Azure AD con Amazon Web Services (AWS), necesita los siguientes elementos:

- Una suscripción de Azure AD
- Un suscripción habilitada para el inicio de sesión único en Amazon Web Services (AWS)

> [!NOTE]
> Para probar los pasos de este tutorial, no se recomienda el uso de un entorno de producción.

Para probar los pasos de este tutorial, siga estas recomendaciones:

- No use el entorno de producción, salvo que sea necesario.
- Si no dispone de un entorno de prueba de Azure AD, puede [obtener una evaluación gratuita durante un mes](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descripción del escenario
En este tutorial, puede probar el inicio de sesión único de Azure AD en un entorno de prueba. El escenario descrito en este tutorial consta de dos bloques de creación principales:

1. Adición de Amazon Web Services (AWS) desde la galería
2. Configuración y comprobación del inicio de sesión único de Azure AD

## <a name="add-amazon-web-services-aws-from-the-gallery"></a>Adición de Amazon Web Services (AWS) desde la galería
Para configurar la integración de Amazon Web Services (AWS) en Azure AD, es preciso agregar Amazon Web Services (AWS) desde la galería a la lista de aplicaciones SaaS administradas.

**Para agregar Amazon Web Services (AWS) desde la galería, realice los pasos siguientes:**

1. En el panel de navegación izquierdo de **[Azure Portal](https://portal.azure.com)**, seleccione el icono de **Azure Active Directory**. 

    ![Botón Azure Active Directory][1]

2. Vaya a **Aplicaciones empresariales**. A continuación, vaya a **Todas las aplicaciones**.

    ![Hoja Aplicaciones empresariales][2]
    
3. Para agregar una nueva aplicación, seleccione el botón **Nueva aplicación** en la parte superior del cuadro de diálogo.

    ![Botón Nueva aplicación][3]

4. En el cuadro de búsqueda, escriba **Amazon Web Services (AWS)**. En el panel de resultados, seleccione **Amazon Web Services (AWS)** y, después, haga clic en el botón **Agregar** para agregar la aplicación.

    ![Amazon Web Services (AWS) en la lista de resultados](./media/active-directory-saas-amazon-web-service-tutorial/tutorial_amazonwebservices(aws)_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configuración y prueba del inicio de sesión único en Azure AD

En esta sección, podrá configurar y probar el inicio de sesión único de Azure AD con Amazon Web Services (AWS) con un usuario de prueba llamado "Britta Simon".

Para que el inicio de sesión único funcione, Azure AD debe saber cuál es el usuario homólogo de Amazon Web Services (AWS) para un usuario de Azure AD. Es decir, es necesario establecer un vínculo entre un usuario de Azure AD y el usuario relacionado de Amazon Web Services (AWS).

Para establecer el vínculo, el valor de **Nombre de usuario** en Amazon Web Services (AWS) tiene que coincidir con el valor de **Nombre de usuario** en Azure AD. 

Para configurar y probar el inicio de sesión único de Azure AD con Amazon Web Services (AWS), complete los siguientes bloques de creación:

1. [Configuración del inicio de sesión único de Azure AD](#configure-azure-ad-single-sign-on), para permitir que los usuarios utilicen esta característica.
2. [Creación de un usuario de prueba de Azure AD](#create-an-azure-ad-test-user), para probar el inicio de sesión único de Azure AD con Britta Simon.
3. [Creación de un usuario de prueba de Amazon Web Services (AWS)](#create-an-amazon-web-services-aws-test-user): para tener un homólogo de Britta Simon en Amazon Web Services (AWS) que esté vinculado a la representación del usuario en Azure AD.
4. [Asignación del usuario de prueba de Azure AD](#assign-the-azure-ad-test-user), para permitir que Britta Simon use el inicio de sesión único de Azure AD.
5. [Prueba del inicio de sesión único](#test-single-sign-on) para comprobar que la configuración funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configuración del inicio de sesión único de Azure AD

En esta sección, habilitará el inicio de sesión único de Azure AD en Azure Portal y configurará el inicio de sesión único en la aplicación de Amazon Web Services (AWS).

**Para configurar el inicio de sesión único de Azure AD con Amazon Web Services (AWS), realice los pasos siguientes:**

1. En Azure Portal, en la página de integración de la aplicación **Amazon Web Services (AWS)**, seleccione **Inicio de sesión único**.

    ![Vínculo Configurar inicio de sesión único][4]

2. En el cuadro de diálogo **Inicio de sesión único**, en la lista desplegable **Modo**, seleccione **Inicio de sesión basado en SAML** para habilitar el inicio de sesión único.
 
    ![Cuadro de diálogo Inicio de sesión único](./media/active-directory-saas-amazon-web-service-tutorial/tutorial_amazonwebservices(aws)_samlbase.png)

3. En la sección **Dominio y direcciones URL de Amazon Web Services (AWS)**, el usuario no tiene que realizar ningún paso ya que la aplicación se ha integrado previamente con Azure.

    ![Información de dominio y direcciones URL de inicio de sesión único de Amazon Web Services (AWS)](./media/active-directory-saas-amazon-web-service-tutorial/tutorial_amazonwebservices(aws)_url.png)

4. La aplicación de software Amazon Web Services (AWS) espera las aserciones de SAML en un formato concreto. Configure las siguientes notificaciones para esta aplicación. Puede administrar los valores de estos atributos en la sección **Atributos de usuario** de la página de integración de aplicaciones. En la siguiente captura de pantalla se muestra un ejemplo:

    ![Configuración de inicio de sesión único: atributo](./media/active-directory-saas-amazon-web-service-tutorial/tutorial_amazonwebservices(aws)_attribute.png)  

5. En la sección **Atributos de usuario** del cuadro de diálogo **Inicio de sesión único**, configure el atributo token SAML como muestra la imagen anterior y luego realice los pasos siguientes:
    
    | Nombre del atributo  | Valor de atributo | Espacio de nombres |
    | --------------- | --------------- | --------------- |
    | RoleSessionName | user.userprincipalname | https://aws.amazon.com/SAML/Attributes |
    | Rol            | user.assignedroles |  https://aws.amazon.com/SAML/Attributes |
    
    >[!TIP]
    >Configure el aprovisionamiento de usuarios en Azure AD para capturar todos los roles de la consola de Amazon Web Services (AWS). Consulte los siguientes pasos de aprovisionamiento.

    a. Seleccione **Agregar atributo** para abrir el cuadro de diálogo **Agregar atributo**.

    ![Configuración de inicio de sesión único: atributo](./media/active-directory-saas-amazon-web-service-tutorial/tutorial_attribute_04.png)

    ![Configuración de inicio de sesión único: atributo](./media/active-directory-saas-amazon-web-service-tutorial/tutorial_attribute_05.png)

    b. En el cuadro **Nombre**, escriba el nombre del atributo que se muestra para esa fila.

    c. En la lista **Valor**, escriba el valor del atributo que se muestra para esa fila.

    d. En el cuadro **Espacio de nombres**, escriba el valor del espacio de nombres que se muestra para esa fila.
    
    d. Seleccione **Aceptar**.

6. En la sección **Certificado de firma de SAML**, seleccione **XML de metadatos**. Luego, guarde el archivo de metadatos en el equipo.

    ![El vínculo de descarga del certificado](./media/active-directory-saas-amazon-web-service-tutorial/tutorial_amazonwebservices(aws)_certificate.png) 

7. Seleccione **Guardar**.

    ![Botón Guardar de Configuración de inicio de sesión único](./media/active-directory-saas-amazon-web-service-tutorial/tutorial_general_400.png)

8. En otra ventana del explorador, inicie sesión en su sitio de la compañía de Amazon Web Services (AWS) como administrador.

9. Seleccione **Console Home** (Página principal de consola).
   
    ![Configuración de inicio de sesión único: página principal][11]

10. Seleccione **Identity and Access Management** (Administración de identidades y accesos). 
   
    ![Configuración de inicio de sesión único: identidad][12]

11. Seleccione **Identity Providers** (Proveedores de identidades). A continuación, seleccione **Create Provider** (Crear proveedor). 
   
    ![Configuración de inicio de sesión único: proveedor][13]

12. En el cuadro de diálogo **Configure Provider** (Configurar proveedor), realice los siguientes pasos: 
   
    ![Cuadro de diálogo de configuración del inicio de sesión único][14]
 
    a. En **Provider Type** (Tipo de proveedor), seleccione **SAML**.

    b. En el cuadro **Provider Name** (Nombre de proveedor), escriba un nombre de proveedor (por ejemplo, *WAAD*).

    c. Para cargar el **archivo de metadatos** descargado de Azure Portal, seleccione **Choose file** (Elegir archivo).

    d. Seleccione **Next Step** (Siguiente paso).

13. En el cuadro de diálogo **Verify Provider Information** (Comprobar la información del proveedor), seleccione **Create** (Crear). 
    
    ![Configuración de inicio de sesión único: comprobación][15]

14. Seleccione **Roles**. A continuación, seleccione **Create New Role** (Crear nuevo rol). 
    
    ![Configuración de inicio de sesión único: roles][16]

15. En el cuadro de diálogo **Set Role Name** (Establecer nombre de rol), realice los siguientes pasos: 
    
    ![Configuración de inicio de sesión único: nombre][17] 

    a. En el cuadro de texto **Role Name** (Nombre de rol), escriba un nombre de rol (por ejemplo, *TestUser*). 

    b. Seleccione **Next Step** (Siguiente paso).

16. En el cuadro de diálogo **Select Role Type** (Seleccionar tipo de rol), realice los siguientes pasos: 
    
    ![Configuración de inicio de sesión único: tipo de rol][18] 

    a. Seleccione **Rol de acceso del proveedor de identidades**. 

    b. En la sección **Conceder acceso de inicio de sesión único web (WebSSO)** a los proveedores de SAML, haga clic en **Seleccionar**.

17. En el cuadro de diálogo **Establish Trust** (Establecer confianza), realice los siguientes pasos:  
    
    ![Configuración de inicio de sesión único: confianza][19] 

    a. Seleccione el proveedor de SAML que creó anteriormente (por ejemplo: *WAAD*). 
  
    b. Seleccione **Next Step** (Siguiente paso).

18. En el cuadro de diálogo **Verify Role Trust** (Comprobar confianza del rol), seleccione **Next Step** (Siguiente paso). 
    
    ![Configurar confianza de rol de inicio de sesión único][32]

19. En el cuadro de diálogo **Attach Policy** (Asociar directiva), seleccione **Next Step** (Siguiente paso).  
    
    ![Configurar directiva de inicio de sesión único][33]

20. En el cuadro de diálogo **Review** (Revisar), realice los siguientes pasos:   
    
    ![Configuración de inicio de sesión único: revisar][34] 

    a. Seleccione **Create Role** (Crear rol).

    b. Cree tantos roles como sea necesario y asígnelos al proveedor de identidades.

21. Use las credenciales de la cuenta de servicio de Amazon Web Services (AWS) para capturar los roles de la cuenta de Amazon Web Services (AWS) en el aprovisionamiento de usuarios de Azure AD. Para iniciar esta tarea, abra la página principal de la consola de Amazon Web Services (AWS).

22. Seleccione **Services** >  (Servicios)**Security, Identity & Compliance** >  (Seguridad, identidad y cumplimiento) **IAM**.

    ![Captura de los roles de la cuenta de Amazon Web Services (AWS)](./media/active-directory-saas-amazon-web-service-tutorial/fetchingrole1.png)

23. En la sección IAM, seleccione la pestaña **Policies** (Directivas).

    ![Captura de los roles de la cuenta de Amazon Web Services (AWS)](./media/active-directory-saas-amazon-web-service-tutorial/fetchingrole2.png)

24. Para crear una nueva directiva, seleccione **Create policy** (Crear directiva).

    ![Creación de una nueva directiva](./media/active-directory-saas-amazon-web-service-tutorial/fetchingrole3.png)
 
25. Para crear su propia directiva y capturar todos los roles de las cuentas de Amazon Web Services (AWS), siga estos pasos:

    ![Creación de una nueva directiva](./media/active-directory-saas-amazon-web-service-tutorial/policy1.png)

    a. En la sección **Create policy** (Crear directiva), seleccione la pestaña **JSON**.

    b. En el documento de directiva, agregue el siguiente JSON:
    
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

    c. Para validar la directiva, seleccione el botón **Review Policy** (Revisar directiva).

    ![Definición de una nueva directiva](./media/active-directory-saas-amazon-web-service-tutorial/policy5.png)

26. Para definir la **nueva directiva**, siga estos pasos:

    ![Definición de una nueva directiva](./media/active-directory-saas-amazon-web-service-tutorial/policy2.png)

    a. En **Policy Name** (Nombre de la directiva), especifique **AzureAD_SSOUserRole_Policy**.

    b. Puede proporcionar la siguiente **descripción** para la directiva: **esta directiva le permitirá capturar los roles de las cuentas de AWS**.
    
    c. Seleccione el botón **Create Policy** (Crear directiva).
        
27. Para crear una nueva cuenta de usuario en el servicio IAM de Amazon Web Services (AWS), siga estos pasos:

    a. Seleccione **Users** (Usuarios) en la consola de IAM de Amazon Web Services (AWS).

    ![Definición de una nueva directiva](./media/active-directory-saas-amazon-web-service-tutorial/policy3.png)
    
    b. Para crear un nuevo usuario, seleccione el botón **Add user** (Agregar usuario).

    ![Agregar usuario](./media/active-directory-saas-amazon-web-service-tutorial/policy4.png)

    c. En la sección **Add user** (Agregar usuario), realice los siguientes pasos:
    
    ![Agregar usuario](./media/active-directory-saas-amazon-web-service-tutorial/adduser1.png)
    
    * Escriba **AzureADRoleManager** en el cuadro de nombre de usuario.
    
    * Para el tipo de acceso, seleccione la opción **Programmatic access** (Acceso mediante programación). De este modo, el usuario puede llamar a las API y capturar los roles de la cuenta de Amazon Web Services (AWS).
    
    * Seleccione el botón **Next Permissions** (Siguiente permiso) de la esquina inferior derecha.

28. Cree una nueva directiva para este usuario mediante los pasos siguientes:

    ![Agregar usuario](./media/active-directory-saas-amazon-web-service-tutorial/adduser2.png)
    
    a. Seleccione el botón **Attach existing policies directly** (Asociar directivas existentes directamente).

    b. Busque la directiva recién creada en la sección de filtro **AzureAD_SSOUserRole_Policy**.
    
    c. Seleccione la **directiva**. A continuación, seleccione el botón **Next: Review** (Siguiente: revisar).

29. Revise la directiva del usuario asociado mediante los pasos siguientes:

    ![Agregar usuario](./media/active-directory-saas-amazon-web-service-tutorial/adduser3.png)
    
    a. Revise el nombre de usuario, el tipo de acceso y la directiva asociados al usuario.
    
    b. Para crear el usuario, seleccione el botón **Create user** (Crear usuario) en la esquina inferior derecha.

30. Descargue las credenciales de un usuario mediante los pasos siguientes:

    ![Agregar usuario](./media/active-directory-saas-amazon-web-service-tutorial/adduser4.png)
    
    a. Copie el **identificador de la clave de acceso** y la **clave de acceso secreta** del usuario.
    
    b. Escriba estas credenciales en la sección de aprovisionamiento de usuarios de Azure AD para capturar los roles de la consola de Amazon Web Services (AWS).
    
    c. Seleccione el botón **Close** (Cerrar) en la esquina inferior derecha.

31. Vaya a la sección **User Provisioning** (Aprovisionamiento de usuarios) de la aplicación Amazon Web Services (AWS) en el Portal de administración de Azure AD.

    ![Agregar usuario](./media/active-directory-saas-amazon-web-service-tutorial/provisioning.png)

32. Escriba la **clave de acceso** y la **clave secreta** en los campos **Secreto de cliente** y **Token secreto** respectivamente.

    ![Agregar usuario](./media/active-directory-saas-amazon-web-service-tutorial/provisioning1.png)
    
    a. Escriba la clave de acceso de usuario de Amazon Web Services (AWS) en el campo **clientsecret** (secreto de cliente).
    
    b. Escriba el secreto de usuario de Amazon Web Services (AWS) en el campo **Secret Token** (Token secreto).
    
    c. Seleccione el botón **Test Connection** (Prueba de conexión). Debería poder probar esta conexión correctamente.

    d. Seleccione el botón **Save** (Guardar) de la parte superior para guardar la configuración.
 
33. Asegúrese de que el estado de aprovisionamiento en **Settings** (Configuración) es **On** (Activado). Para ello, seleccione **On** (Activado) y luego el botón **Save** (Guardar) en la parte superior.

    ![Agregar usuario](./media/active-directory-saas-amazon-web-service-tutorial/provisioning2.png)

> [!TIP]
> Ahora puede leer una versión resumida de estas instrucciones dentro de [Azure Portal](https://portal.azure.com) mientras configura la aplicación. Después de agregar esta aplicación desde la sección **Active Directory > Enterprise Applications (Aplicaciones empresariales)**, seleccione la pestaña **Single Sign-On** (Inicio de sesión único). A continuación, consulte la documentación insertada en la sección **Configuración** en la parte inferior. Puede leer más sobre la característica de documentación insertada en la [documentación insertada de Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985).
> 

### <a name="create-an-azure-ad-test-user"></a>Creación de un usuario de prueba de Azure AD

El objetivo de esta sección es crear un usuario de prueba en Azure Portal llamado "Britta Simon".

   ![Creación de un usuario de prueba de Azure AD][100]

**Siga estos pasos para crear un usuario de prueba en Azure AD:**

1. En el panel izquierdo de Azure Portal, seleccione el botón **Azure Active Directory**.

    ![Botón Azure Active Directory](./media/active-directory-saas-amazon-web-service-tutorial/create_aaduser_01.png)

2. Para mostrar la lista de usuarios, vaya a **Usuarios y grupos**. Después, seleccione **Todos los usuarios**.

    ![Vínculos "Usuarios y grupos" y "Todos los usuarios"](./media/active-directory-saas-amazon-web-service-tutorial/create_aaduser_02.png)

3. En la parte superior del cuadro de diálogo **Todos los usuarios**, seleccione **Agregar** para abrir el cuadro de diálogo **Usuario**.

    ![Botón Agregar](./media/active-directory-saas-amazon-web-service-tutorial/create_aaduser_03.png)

4. En el cuadro de diálogo **Usuario**, realice los pasos siguientes:

    ![El cuadro de diálogo Usuario](./media/active-directory-saas-amazon-web-service-tutorial/create_aaduser_04.png)

    a. En el cuadro **Nombre**, escriba **BrittaSimon**.

    b. En el cuadro de texto **Nombre de usuario**, escriba la dirección de correo electrónico del usuario Britta Simon.

    c. Active la casilla **Mostrar contraseña** y, después, anote el valor que se muestra en el cuadro **Contraseña**.

    d. Seleccione **Crear**.
 
### <a name="create-an-amazon-web-services-aws-test-user"></a>Creación de un usuario de prueba de Amazon Web Services (AWS)

El objetivo de esta sección es crear un usuario llamado Britta Simon en Amazon Web Services (AWS). Amazon Web Services (AWS) no necesita que se cree un usuario en su sistema para el inicio de sesión único, por lo que no es necesario realizar ninguna acción aquí.

### <a name="assign-the-azure-ad-test-user"></a>Asignación del usuario de prueba de Azure AD

En esta sección, habilitará a Britta Simon para que use el inicio de sesión único de Azure, para lo cual le concederá acceso a Amazon Web Services (AWS).

![Asignación de rol de usuario][200] 

**Para asignar a Britta Simon a Amazon Web Services (AWS), realice los pasos siguientes:**

1. En Azure Portal, abra la vista de aplicaciones. Vaya a la vista de directorio y, a continuación, seleccione **Enterprise applications** (Aplicaciones empresariales). A continuación, seleccione **Todas las aplicaciones**.

    ![Asignar usuario][201] 

2. En la lista de aplicaciones, seleccione **Amazon Web Services (Amazon Web Services (AWS)**.

    ![El vínculo de Amazon Web Services (AWS) en la lista de aplicaciones](./media/active-directory-saas-amazon-web-service-tutorial/tutorial_amazonwebservices(aws)_app.png)  

3. En el menú de la izquierda, seleccione **Usuarios y grupos**.

    ![Vínculo "Usuarios y grupos"][202]

4. Seleccione el botón **Agregar**. Después, en el cuadro de diálogo **Agregar asignación**, seleccione **Usuarios y grupos**.

    ![Panel Agregar asignación][203]

5. En el cuadro de diálogo **Usuarios y grupos**, seleccione **Britta Simon**.

6. En el cuadro de diálogo **Usuarios y grupos**, haga clic en el botón **Seleccionar**. 

7. En el cuadro de diálogo **Agregar asignación**, haga clic en el botón **Asignar**.
    
### <a name="test-single-sign-on"></a>Prueba de inicio de sesión único

En esta sección, probará la configuración de inicio de sesión único de Azure AD mediante el panel de acceso.

Al seleccionar el icono de Amazon Web Services (AWS) en el panel de acceso, automáticamente iniciará sesión en su aplicación de Amazon Web Services (AWS). Para más información sobre el panel de acceso, consulte [Introducción al panel de acceso](active-directory-saas-access-panel-introduction.md). 

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

