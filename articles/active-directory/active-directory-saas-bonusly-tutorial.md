---
title: "Tutorial: Integración de Azure Active Directory con Bonusly | Microsoft Docs"
description: "Obtenga información sobre cómo configurar el inicio de sesión único entre Azure Active Directory y Bonusly."
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.assetid: 42875d53-0769-4520-a6af-7308b5240d6b
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/07/2017
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: af00ed428dda846a06466df1e30ee7e8a7e5758c
ms.openlocfilehash: c160829519a14a55e76a2ecedcb286663dc89d65
ms.lasthandoff: 02/15/2017


---
# <a name="tutorial-azure-active-directory-integration-with-bonusly"></a>Tutorial: Integración de Azure Active Directory con Bonusly

En este tutorial, obtendrá información sobre cómo integrar Bonusly con Azure Active Directory (Azure AD).

La integración de Bonusly con Azure AD le proporciona las siguientes ventajas:

- Puede controlar en Azure AD quién tiene acceso a Bonusly.
- Puede permitir que los usuarios inicien sesión automáticamente en Bonusly (inicio de sesión único) con sus cuentas de Azure AD.
- Puede administrar sus cuentas en una ubicación central: el Portal de administración de Azure.

Si desea obtener más información sobre la integración de aplicaciones SaaS con Azure AD, vea [Qué es el acceso a las aplicaciones y el inicio de sesión único en Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Requisitos previos

Para configurar la integración de Azure AD con Bonusly, necesita los siguientes elementos:

- Una suscripción de Azure AD
- Una suscripción habilitada para el inicio de sesión único en Bonusly


> [!NOTE]
> Para probar los pasos de este tutorial, no se recomienda el uso de un entorno de producción.


Para probar los pasos de este tutorial, debe seguir estas recomendaciones:

- No debe usar el entorno de producción, a menos que sea necesario.
- Si no dispone de un entorno de prueba de Azure AD, puede obtener una versión de prueba de un mes [aquí](https://azure.microsoft.com/pricing/free-trial/).


## <a name="scenario-description"></a>Descripción del escenario
En este tutorial, puede probar el inicio de sesión único de Azure AD en un entorno de prueba. La situación descrita en este tutorial consta de dos bloques de creación principales:

1. Adición de Bonusly desde la galería
2. Configuración y comprobación del inicio de sesión único de Azure AD


## <a name="adding-bonusly-from-the-gallery"></a>Adición de Bonusly desde la galería
Para configurar la integración de Bonusly en Azure AD, debe agregar Bonusly desde la galería a la lista de aplicaciones SaaS administradas.

**Para agregar Bonusly desde la galería, realice los pasos siguientes:**

1. En el panel de navegación izquierdo del **[Portal de administración de Azure](https://portal.azure.com)**, haga clic en el icono de **Azure Active Directory**. 

    ![Active Directory][1]

2. Vaya a **Aplicaciones empresariales**. A continuación, vaya a **Todas las aplicaciones**.

    ![Aplicaciones][2]
    
3. Haga clic en el botón **Agregar** situado en la parte superior del cuadro de diálogo.

    ![Aplicaciones][3]

4. En el cuadro de búsqueda, escriba **Bonusly**.

    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-bonusly-tutorial/tutorial_bonusly_001.png)

5. En el panel de resultados, seleccione **Bonusly** y luego haga clic en el botón **Agregar** para agregar la aplicación.

    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-bonusly-tutorial/tutorial_bonusly_0001.png)


##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configuración y comprobación del inicio de sesión único de Azure AD
En esta sección, puede configurar y probar el inicio de sesión único de Azure AD con Bonusly con un usuario de prueba llamado "Britta Simon".

Para que el inicio de sesión único funcione, Azure AD debe saber cuál es el usuario homólogo de Bonusly para un usuario de Azure AD. Es decir, es necesario establecer una relación de vínculo entre un usuario de Azure AD y el usuario relacionado de Bonusly.

Para establecer esta relación de vínculo, se asigna el valor del **nombre de usuario** de Azure AD como el valor del **nombre de usuario** de Bonusly.

Para configurar y probar el inicio de sesión único de Azure AD con Bonusly, es preciso completar los siguientes bloques de creación:

1. **[Configuring Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)** : para permitir a los usuarios usar esta característica.
2. **[Creación de un usuario de prueba de Azure AD](#creating-an-azure-ad-test-user)** : para probar el inicio de sesión único de Azure AD con Britta Simon.
3. **[Creación de un usuario de prueba de Bonusly](#creating-a-bonusly-test-user)**: para tener un homólogo de Britta Simon en Bonusly que esté vinculado a su representación en Azure AD.
4. **[Asignación del usuario de prueba de Azure AD](#assigning-the-azure-ad-test-user)** : para permitir que Britta Simon use el inicio de sesión único de Azure AD.
5. **[Testing Single Sign-On](#testing-single-sign-on)** : para comprobar si funciona la configuración.

### <a name="configuring-azure-ad-single-sign-on"></a>Configuración del inicio de sesión único de Azure AD

En esta sección, habilita el inicio de sesión único de Azure AD en el Portal de administración de Azure y configura el inicio de sesión único en la aplicación Bonusly.

**Para configurar el inicio de sesión único de Azure AD con Bonusly, realice los pasos siguientes:**

1. En el Portal de administración de Azure, en la página de integración de la aplicación **Bonusly**, haga clic en **Inicio de sesión único**.

    ![Configurar inicio de sesión único][4]

2. En el cuadro de diálogo **Inicio de sesión único**, en **Modo**, seleccione **Inicio de sesión basado en SAML** para habilitar el inicio de sesión único.
 
    ![Configurar inicio de sesión único](./media/active-directory-saas-bonusly-tutorial/tutorial_bonusly_01.png)

3. En la sección **Dominio y direcciones URL de Bonusly**, si quiere configurar la aplicación en **IDP initiated mode** (Modo iniciado por ID), realice los siguientes pasos:

    ![Configurar inicio de sesión único](./media/active-directory-saas-bonusly-tutorial/tutorial_bonusly_02.png)

    a. En el cuadro de texto **Identificador**, escriba el valor como `bonusly`.
    
    b. En el cuadro de texto **URL de respuesta**, escriba una dirección URL con el siguiente patrón: `https://bonus.ly/saml/<APP-ID>/consume`.
    
4. Si quiere configurar la aplicación en **SP initiated mode** (Modo iniciado por SP), en la sección **Dominio y direcciones URL de Bonusly**, realice los siguientes pasos:
    
    ![Configurar inicio de sesión único](./media/active-directory-saas-bonusly-tutorial/tutorial_bonusly_03.png)

    a. Haga clic en la opción **Mostrar configuración avanzada de URL**.

    b. En el cuadro de texto **URL de inicio de sesión**, escriba una dirección URL con el siguiente patrón: `https://bonus.ly/saml/<your_subdomain>/consume`.

    > [!NOTE] 
    > Tenga en cuenta que estos no son valores reales. Tendrá que actualizar estos valores con la dirección URL de inicio de sesión, el identificador y la dirección URL de respuesta reales. Aquí le recomendamos que utilice el valor de cadena único en el identificador. Póngase en contacto con el [equipo de soporte técnico de Bonusly](mailto:sales@easyterritory.com) para obtener estos valores.

5. En la sección **Certificado de firma de SAML**, haga clic en **Crear nuevo certificado**.

    ![Configurar inicio de sesión único](./media/active-directory-saas-bonusly-tutorial/tutorial_bonusly_04.png)     

6. En el cuadro de diálogo **Crear nuevo certificado**, haga clic en el icono del calendario y seleccione una valor en **Fecha de expiración**. Luego haga clic en el botón **Guardar**.

    ![Configurar inicio de sesión único](./media/active-directory-saas-bonusly-tutorial/tutorial_general_300.png)

7. En la sección **Certificado de firma de SAML**, seleccione **Make new certificate active** (Activar el nuevo certificado) y haga clic en el botón **Guardar**.

    ![Configurar inicio de sesión único](./media/active-directory-saas-bonusly-tutorial/tutorial_bonusly_05.png)

8. En la ventana emergente **Rollover certificate** (Certificado de sustitución), haga clic en **Aceptar**.

    ![Configurar inicio de sesión único](./media/active-directory-saas-bonusly-tutorial/tutorial_general_400.png)

9. En la sección **Certificado de firma de SAML**, haga clic en **Certificado (Base64)** y, luego, guarde el archivo de certificado en el equipo.

    ![Configurar inicio de sesión único](./media/active-directory-saas-bonusly-tutorial/tutorial_bonusly_06.png) 

10. En la sección **Configuración de Bonusly**, haga clic en **Configurar Bonusly** para abrir la ventana **Configurar inicio de sesión**.

    ![Configurar inicio de sesión único](./media/active-directory-saas-bonusly-tutorial/tutorial_bonusly_07.png) 

    ![Configurar inicio de sesión único](./media/active-directory-saas-bonusly-tutorial/tutorial_bonusly_08.png)

11. En otra ventana del explorador web, inicie sesión como administrador en el sitio de la compañía de Bonusly.

12. En la barra de herramientas de la parte superior, haga clic en **Configuración** y seleccione **Integraciones y aplicaciones**.

    ![Configuración del inicio de sesión único en la aplicación](./media/active-directory-saas-bonusly-tutorial/tutorial_bonusly_002.png)

13. En **Inicio de sesión único**, seleccione **SAML**.

14. En la página de diálogo **SAML** , realice los pasos siguientes:

    ![Configuración del inicio de sesión único en la aplicación](./media/active-directory-saas-bonusly-tutorial/tutorial_bonusly_003.png)

    a. En el cuadro de texto **IdP SSO target URL** (Dirección URL de destino de SSO de IdP), coloque el valor de **SAML Single Sign-On Service URL** (Dirección URL del servicio de inicio de sesión único de SAML) en la ventana de configuración de aplicaciones de Azure AD.

    b. En el cuadro de texto **IdP Login URL** (Dirección URL de inicio de sesión de IdP), coloque el valor de **SAML Single Sign-On Service URL** (Dirección URL del servicio de inicio de sesión único de SAML) en la ventana de configuración de aplicaciones de Azure AD.

    c. En el cuadro de texto **IdP Issuer** (Emisor de IdP), coloque el valor de **SAML Entity ID** (ID de entidad de SAML) de la ventana de configuración de aplicaciones de Azure AD.

    d. Copie el valor de huella digital del certificado descargado y luego péguelo en el cuadro de texto **Cert Fingerprint** (Huella digital del certificado).

    e. Haga clic en **Guardar**.

    > [!NOTE] 
    > Para obtener más información, consulte [Recuperación del valor de huella digital de un certificado](https://www.youtube.com/watch?v=YKQF266SAxI&feature=youtu.be).



### <a name="creating-an-azure-ad-test-user"></a>Creación de un usuario de prueba de Azure AD
El objetivo de esta sección es crear un usuario de prueba en el Portal de administración de Azure llamado Britta Simon.

![Creación de un usuario de Azure AD][100]

**Siga estos pasos para crear un usuario de prueba en Azure AD:**

1. En el panel de navegación izquierdo del **Portal de administración de Azure**, haga clic en el icono de **Azure Active Directory**.

    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-bonusly-tutorial/create_aaduser_01.png) 

2. Vaya a **Usuarios y grupos** y haga clic en **Todos los usuarios** para mostrar la lista de usuarios.
    
    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-bonusly-tutorial/create_aaduser_02.png) 

3. En la parte superior del diálogo, haga clic en **Agregar** para abrir el diálogo **Usuario**.
 
    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-bonusly-tutorial/create_aaduser_03.png) 

4. En la página de diálogo **Usuario**, realice los siguientes pasos:
 
    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-bonusly-tutorial/create_aaduser_04.png) 

    a. En el cuadro de texto **Nombre**, escriba **BrittaSimon**.

    b. En el cuadro de texto **Nombre de usuario**, escriba la **dirección de correo electrónico** de Britta Simon.

    c. Seleccione **Mostrar contraseña** y anote el valor del cuadro **Contraseña**.

    d. Haga clic en **Create**(Crear). 



### <a name="creating-a-bonusly-test-user"></a>Creación de un usuario de prueba en Bonusly

Para permitir que los usuarios de Azure AD inicien sesión en Bonusly, tienen que aprovisionarse en Bonusly.
En el caso de Bonusly, el aprovisionamiento es una tarea manual.

####<a name="to-provision-a-user-account-perform-the-following-steps"></a>Para aprovisionar una cuenta de usuario, realice estos pasos:

1. Inicie sesión en su sitio de la compañía de Bonusly como administrador.

2. Haga clic en **Configuración**.

    ![Nuevo usuario](./media/active-directory-saas-bonusly-tutorial/tutorial_bonusly_010.png "Nuevo usuario")

3. Haga clic en la pestaña **Usuarios y bonificaciones** .

    ![Nuevo usuario](./media/active-directory-saas-bonusly-tutorial/tutorial_bonusly_011.png "Nuevo usuario")

4. Haga clic en **Administrar usuarios**.

    ![Nuevo usuario](./media/active-directory-saas-bonusly-tutorial/tutorial_bonusly_012.png "Nuevo usuario")

5. Haga clic en **Agregar usuario**.

    ![Nuevo usuario](./media/active-directory-saas-bonusly-tutorial/tutorial_bonusly_013.png "Nuevo usuario")

6. En la sección **Add User** (Agregar usuario), realice estos pasos:

    ![Nuevo usuario](./media/active-directory-saas-bonusly-tutorial/tutorial_bonusly_014.png "Nuevo usuario")

    a. En el cuadro de texto **Nombre**, escriba **Britta**.  

    b. En el cuadro de texto **Apellidos**, escriba **Simon**.

    c. En el cuadro de texto **Correo electrónico**, escriba la dirección de correo electrónico de la cuenta de Britta Simon.

    d. Haga clic en **Guardar**.

    > [!NOTE] 
    > El titular de la cuenta de AAD recibirá un mensaje de correo electrónico con un vínculo para confirmar la cuenta antes de que se active. Puede usar cualquier otra API o herramienta de creación de cuentas de usuario de Bonus.ly que proporcione Bonus.ly para aprovisionar cuentas de usuario de AAD.



### <a name="assigning-the-azure-ad-test-user"></a>Asignación del usuario de prueba de Azure AD

En esta sección, habilita a Britta Simon para que use el inicio de sesión único de Azure concediéndole acceso a Bonusly.

![Asignar usuario][200] 

**Para asignar Britta Simon a Bonusly, realice los pasos siguientes:**

1. En el Portal de administración de Azure, abra la vista de aplicaciones, vaya a la vista de directorio y seleccione **Aplicaciones empresariales**. Después, haga clic en **Todas las aplicaciones**.

    ![Asignar usuario][201] 

2. En la lista de aplicaciones, seleccione **Bonusly**.

    ![Configurar inicio de sesión único](./media/active-directory-saas-bonusly-tutorial/tutorial_bonusly_50.png) 

3. En el menú de la izquierda, haga clic en **Usuarios y grupos**.

    ![Asignar usuario][202] 

4. Haga clic en el botón **Agregar**. Después, seleccione **Usuarios y grupos** en el cuadro de diálogo **Agregar asignación**.

    ![Asignar usuario][203]

5. En el cuadro de diálogo **Usuarios y grupos**, seleccione **Britta Simon** en la lista de usuarios.

6. Haga clic en el botón **Seleccionar** del cuadro de diálogo **Usuarios y grupos**.

7. Haga clic en el botón **Asignar** del cuadro de diálogo **Agregar asignación**.
    


### <a name="testing-single-sign-on"></a>Prueba del inicio de sesión único 

En esta sección, probará la configuración de inicio de sesión único de Azure AD mediante el Panel de acceso.

Al hacer clic en el icono de Bonusly en el panel de acceso, debería iniciar sesión automáticamente en su aplicación Bonusly.


## <a name="additional-resources"></a>Recursos adicionales

* [Lista de tutoriales sobre cómo integrar aplicaciones SaaS con Azure Active Directory](active-directory-saas-tutorial-list.md)
* [¿Qué es el acceso a aplicaciones y el inicio de sesión único con Azure Active Directory?](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-bonusly-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-bonusly-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-bonusly-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-bonusly-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-bonusly-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-bonusly-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-bonusly-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-bonusly-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-bonusly-tutorial/tutorial_general_203.png
