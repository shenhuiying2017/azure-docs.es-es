---
title: "Tutorial: integración de Azure Active Directory con EasyTerritory | Microsoft Docs"
description: "Obtenga información sobre cómo configurar el inicio de sesión único entre Azure Active Directory e EasyTerritory."
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.assetid: d29b362d-e986-4f67-8ff2-e158e49353aa
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/25/2017
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 77ab362fb0c00017f28c326cbe43f69eb39a3f76
ms.openlocfilehash: 95a6f808cdd0dd398949d22f95094e906331a1a7


---
# <a name="tutorial-azure-active-directory-integration-with-easyterritory"></a>Tutorial: Integración de Azure Active Directory con EasyTerritory

En este tutorial, obtendrá información sobre cómo integrar EasyTerritory con Azure Active Directory (Azure AD).

La integración de EasyTerritory con Azure AD le proporciona las siguientes ventajas:

- Puede controlar en Azure AD quién tiene acceso a EasyTerritory.
- Puede permitir que los usuarios inicien sesión automáticamente en EasyTerritory (inicio de sesión único) con sus cuentas de Azure AD.
- Puede administrar sus cuentas en una ubicación central: el Portal de administración de Azure.

Si desea obtener más información sobre la integración de aplicaciones SaaS con Azure AD, vea [Qué es el acceso a las aplicaciones y el inicio de sesión único en Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Requisitos previos

Para configurar la integración de Azure AD con EasyTerritory, necesita los siguientes elementos:

- Una suscripción de Azure AD
- Una suscripción habilitada para el inicio de sesión único en EasyTerritory


> [!NOTE]
> Para probar los pasos de este tutorial, no se recomienda el uso de un entorno de producción.


Para probar los pasos de este tutorial, debe seguir estas recomendaciones:

- No debe usar el entorno de producción, a menos que sea necesario.
- Si no dispone de un entorno de prueba de Azure AD, puede obtener una versión de prueba de un mes [aquí](https://azure.microsoft.com/pricing/free-trial/).


## <a name="scenario-description"></a>Descripción del escenario
En este tutorial, puede probar el inicio de sesión único de Azure AD en un entorno de prueba. La situación descrita en este tutorial consta de dos bloques de creación principales:

1. Agregar EasyTerritory desde la galería
2. Configuración y comprobación del inicio de sesión único de Azure AD


## <a name="adding-easyterritory-from-the-gallery"></a>Agregar EasyTerritory desde la galería
Para configurar la integración de EasyTerritory en Azure AD, deberá agregar EasyTerritory desde la galería a la lista de aplicaciones SaaS administradas.

**Para agregar EasyTerritory desde la galería, siga estos pasos:**

1. En el panel de navegación izquierdo del **[Portal de administración de Azure](https://portal.azure.com)**, haga clic en el icono de **Azure Active Directory**. 

    ![Active Directory][1]

2. Vaya a **Aplicaciones empresariales**. A continuación, vaya a **Todas las aplicaciones**.

    ![Aplicaciones][2]
    
3. Haga clic en el botón **Agregar** situado en la parte superior del cuadro de diálogo.

    ![Aplicaciones][3]

4. En el cuadro de búsqueda, escriba **EasyTerritory**.

    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-easyterritory-tutorial/tutorial_easyterritory_001.png)

5. En el panel de resultados, seleccione **EasyTerritory** y luego haga clic en el botón **Agregar** para agregar la aplicación.

    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-easyterritory-tutorial/tutorial_easyterritory_0001.png)


##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configuración y comprobación del inicio de sesión único de Azure AD
En esta sección, podrá configurar y probar el inicio de sesión único de Azure AD con EasyTerritory con un usuario de prueba denominado "Britta Simon".

Para que el inicio de sesión único funcione, Azure AD debe saber cuál es el usuario homólogo de EasyTerritory para un usuario de Azure AD. Es decir, es necesario establecer una relación de vínculo entre un usuario de Azure AD y el usuario relacionado de EasyTerritory.

Esta relación de vínculo se establece asignando el valor del **nombre de usuario** en Azure AD como el valor del **nombre de usuario** en EasyTerritory.

Para configurar y probar el inicio de sesión único de Azure AD con EasyTerritory, es preciso completar los siguientes bloques de creación:

1. **[Configuring Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)** : para permitir a los usuarios usar esta característica.
2. **[Creación de un usuario de prueba de Azure AD](#creating-an-azure-ad-test-user)** : para probar el inicio de sesión único de Azure AD con Britta Simon.
3. **[Creación de un usuario de prueba de EasyTerritory](#creating-an-easyterritory-test-user)**: para tener un homólogo de Britta Simon en EasyTerritory que esté vinculado a su representación en Azure AD.
4. **[Asignación del usuario de prueba de Azure AD](#assigning-the-azure-ad-test-user)** : para permitir que Britta Simon use el inicio de sesión único de Azure AD.
5. **[Testing Single Sign-On](#testing-single-sign-on)** : para comprobar si funciona la configuración.

### <a name="configuring-azure-ad-single-sign-on"></a>Configuración del inicio de sesión único de Azure AD

En esta sección, habilite el inicio de sesión único de Azure AD en el Portal de administración de Azure y configure el inicio de sesión único en la aplicación EasyTerritory.

**Para configurar el inicio de sesión único de Azure AD con EasyTerritory, realice los pasos siguientes:**

1. En el Portal de administración de Azure, en la página de integración de la aplicación **EasyTerritory**, haga clic en **Inicio de sesión único**.

    ![Configurar inicio de sesión único][4]

2. En el cuadro de diálogo **Inicio de sesión único**, en **Modo**, seleccione **Inicio de sesión basado en SAML** para habilitar el inicio de sesión único.
 
    ![Configurar inicio de sesión único](./media/active-directory-saas-easyterritory-tutorial/tutorial_easyterritory_01.png)

3. En la sección **Dominio y direcciones URL de EasyTerritory**, si quiere configurar la aplicación en **IDP initiated mode** (Modo iniciado por IDP), realice los siguientes pasos:

    ![Configurar inicio de sesión único](./media/active-directory-saas-easyterritory-tutorial/tutorial_easyterritory_02.png)

    a. En el cuadro de texto **Identificador**, escriba una dirección URL con el siguiente patrón: `https://apps.easyterritory.com/<tenant id>/DEV/`
    
    b. En el cuadro de texto **URL de respuesta**, escriba una dirección URL con el siguiente patrón: `https://apps.easyterritory.com/<tenant id>/DEV/AuthServices/Acs`.
    
4. Si quiere configurar la aplicación en **SP initiated mode** (Modo iniciado por SP), en la sección **Dominio y direcciones URL de EasyTerritory**, realice los siguientes pasos:
    
    ![Configurar inicio de sesión único](./media/active-directory-saas-easyterritory-tutorial/tutorial_easyterritory_03.png)

    a. Haga clic en la opción **Mostrar configuración avanzada de URL**.

    b. En el cuadro de texto **URL de inicio de sesión**, escriba una dirección URL con el siguiente patrón: `https://<company name>.easyterritory.com/`.

    > [!NOTE] 
    > Tenga en cuenta que estos no son valores reales. Tendrá que actualizar estos valores con la dirección URL de inicio de sesión, el identificador y la dirección URL de respuesta reales. Póngase en contacto con [equipo de soporte técnico de EasyTerritory](mailto:sales@easyterritory.com) para obtener esos valores.

5. En la sección **Certificado de firma de SAML**, haga clic en **Crear nuevo certificado**.

    ![Configurar inicio de sesión único](./media/active-directory-saas-easyterritory-tutorial/tutorial_easyterritory_04.png)     

6. En el cuadro de diálogo **Crear nuevo certificado**, haga clic en el icono del calendario y seleccione una valor en **Fecha de expiración**. Luego haga clic en el botón **Guardar**.

    ![Configurar inicio de sesión único](./media/active-directory-saas-easyterritory-tutorial/tutorial_general_300.png)

7. En la sección **Certificado de firma de SAML**, seleccione **Make new certificate active** (Activar el nuevo certificado) y haga clic en el botón **Guardar**.

    ![Configurar inicio de sesión único](./media/active-directory-saas-easyterritory-tutorial/tutorial_easyterritory_05.png)

8. En la ventana emergente **Rollover certificate** (Certificado de sustitución), haga clic en **Aceptar**.

    ![Configurar inicio de sesión único](./media/active-directory-saas-easyterritory-tutorial/tutorial_general_400.png)

9. En la sección **Certificado de firma de SAML**, haga clic en **XML de metadatos** y luego guarde el archivo de metadatos en el equipo.

    ![Configurar inicio de sesión único](./media/active-directory-saas-easyterritory-tutorial/tutorial_easyterritory_06.png) 

10. Para configurar SSO para su aplicación, póngase en contacto con el [equipo de soporte técnico de EasyTerritory](mailto:sales@easyterritory.com) y proporcione los **metadatos** descargados. 


### <a name="creating-an-azure-ad-test-user"></a>Creación de un usuario de prueba de Azure AD
El objetivo de esta sección es crear un usuario de prueba en el Portal de administración de Azure llamado Britta Simon.

![Creación de un usuario de Azure AD][100]

**Siga estos pasos para crear un usuario de prueba en Azure AD:**

1. En el panel de navegación izquierdo del **Portal de administración de Azure**, haga clic en el icono de **Azure Active Directory**.

    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-easyterritory-tutorial/create_aaduser_01.png) 

2. Vaya a **Usuarios y grupos** y haga clic en **Todos los usuarios** para mostrar la lista de usuarios.
    
    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-easyterritory-tutorial/create_aaduser_02.png) 

3. En la parte superior del diálogo, haga clic en **Agregar** para abrir el diálogo **Usuario**.
 
    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-easyterritory-tutorial/create_aaduser_03.png) 

4. En la página de diálogo **Usuario**, realice los siguientes pasos:
 
    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-easyterritory-tutorial/create_aaduser_04.png) 

    a. En el cuadro de texto **Nombre**, escriba **BrittaSimon**.

    b. En el cuadro de texto **Nombre de usuario**, escriba la **dirección de correo electrónico** de Britta Simon.

    c. Seleccione **Mostrar contraseña** y anote el valor del cuadro **Contraseña**.

    d. Haga clic en **Crear**. 



### <a name="creating-an-easyterritory-test-user"></a>Creación de un usuario de prueba en EasyTerritory

En esta sección, creará una usuaria llamada Britta Simon en EasyTerritory. Trabaje con el [equipo de soporte técnico de EasyTerritory](mailto:sales@easyterritory.com) para agregar los usuarios a la plataforma de EasyTerritory.


### <a name="assigning-the-azure-ad-test-user"></a>Asignación del usuario de prueba de Azure AD

En esta sección, habilitará a Britta Simon para que use el inicio de sesión único de Azure concediéndole acceso a EasyTerritory.

![Asignar usuario][200] 

**Para asignar Britta Simon a EasyTerritory, siga estos pasos:**

1. En el Portal de administración de Azure, abra la vista de aplicaciones, vaya a la vista de directorio y seleccione **Aplicaciones empresariales**. Después, haga clic en **Todas las aplicaciones**.

    ![Asignar usuario][201] 

2. En la lista de aplicaciones, seleccione **EasyTerritory**.

    ![Configurar inicio de sesión único](./media/active-directory-saas-easyterritory-tutorial/tutorial_easyterritory_50.png) 

3. En el menú de la izquierda, haga clic en **Usuarios y grupos**.

    ![Asignar usuario][202] 

4. Haga clic en el botón **Agregar**. Después, seleccione **Usuarios y grupos** en el cuadro de diálogo **Agregar asignación**.

    ![Asignar usuario][203]

5. En el cuadro de diálogo **Usuarios y grupos**, seleccione **Britta Simon** en la lista de usuarios.

6. Haga clic en el botón **Seleccionar** del cuadro de diálogo **Usuarios y grupos**.

7. Haga clic en el botón **Asignar** del cuadro de diálogo **Agregar asignación**.
    


### <a name="testing-single-sign-on"></a>Prueba del inicio de sesión único 

En esta sección, probará la configuración de inicio de sesión único de Azure AD mediante el Panel de acceso.

Al hacer clic en el icono de EasyTerritory en el panel de acceso, debe iniciar sesión automáticamente en su aplicación de EasyTerritory.


## <a name="additional-resources"></a>Recursos adicionales

* [Lista de tutoriales sobre cómo integrar aplicaciones SaaS con Azure Active Directory](active-directory-saas-tutorial-list.md)
* [¿Qué es el acceso a aplicaciones y el inicio de sesión único con Azure Active Directory?](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-easyterritory-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-easyterritory-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-easyterritory-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-easyterritory-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-easyterritory-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-easyterritory-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-easyterritory-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-easyterritory-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-easyterritory-tutorial/tutorial_general_203.png


<!--HONumber=Feb17_HO1-->


