---
title: "Tutorial: Integración de Azure Active Directory con WORKS MOBILE | Microsoft Docs"
description: "Aprenda a configurar el inicio de sesión único entre Azure Active Directory y WORKS MOBILE."
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.assetid: 725f32fd-d0ad-49c7-b137-1cc246bf85d7
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/24/2017
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: eeb56316b337c90cc83455be11917674eba898a3
ms.openlocfilehash: 8d1b3a49f15861d886822fa1a7328301e97ce37e
ms.lasthandoff: 04/03/2017


---
# <a name="tutorial-azure-active-directory-integration-with-works-mobile"></a>Tutorial: Integración de Azure Active Directory con WORKS MOBILE

En este tutorial, obtendrá información sobre cómo integrar WORKS MOBILE con Azure Active Directory (Azure AD).

La integración de WORKS MOBILE con Azure AD proporciona las siguientes ventajas:

- Puede controlar en Azure AD quién tiene acceso a WORKS MOBILE.
- Puede permitir que los usuarios inicien sesión automáticamente en WORKS MOBILE (inicio de sesión único) con sus cuentas de Azure AD.
- Puede administrar sus cuentas en una ubicación central: el Portal de administración de Azure.

Si desea obtener más información sobre la integración de aplicaciones SaaS con Azure AD, vea [Qué es el acceso a las aplicaciones y el inicio de sesión único en Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Requisitos previos

Para configurar la integración de Azure AD con WORKS MOBILE, necesita los siguientes elementos:

- Una suscripción de Azure AD
- Una suscripción habilitada para el inicio de sesión único en WORKS MOBILE

>[!NOTE]
>Para probar los pasos de este tutorial, no se recomienda el uso de un entorno de producción.
>

Para probar los pasos de este tutorial, debe seguir estas recomendaciones:

- No debe usar el entorno de producción, a menos que sea necesario.
- Si no dispone de un entorno de prueba de Azure AD, puede [obtener una versión de prueba durante un mes](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descripción del escenario
En este tutorial, puede probar el inicio de sesión único (SSO) de Azure AD en un entorno de prueba. 

La situación descrita en este tutorial consta de dos bloques de creación principales:

1. Incorporación de WORKS MOBILE desde la galería
2. Configuración y prueba del inicio de sesión único de Azure AD

## <a name="add-works-mobile-from-the-gallery"></a>Agregar WORKS MOBILE desde la galería
Para configurar la integración de WORKS MOBILE en Azure AD, deberá agregar WORKS MOBILE desde la galería a la lista de aplicaciones SaaS administradas.

**Para agregar WORKS MOBILE desde la galería, realice los pasos siguientes:**

1. En el panel de navegación izquierdo del **[Portal de administración de Azure](https://portal.azure.com)**, haga clic en el icono de **Azure Active Directory**. 

    ![Active Directory][1]
2. Vaya a **Aplicaciones empresariales**. A continuación, vaya a **Todas las aplicaciones**.

    ![Aplicaciones][2]
3. Haga clic en el botón **Agregar** situado en la parte superior del cuadro de diálogo.

    ![Aplicaciones][3]
4. En el cuadro de búsqueda, escriba **WORKS MOBILE**.

    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-worksmobile-tutorial/tutorial_worksmobile_001.png)

5. En el panel de resultados, seleccione **WORKS MOBILE** y luego haga clic en el botón **Agregar** para agregar la aplicación.

    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-worksmobile-tutorial/tutorial_worksmobile_0001.png)

##  <a name="configure-and-test-azure-ad-single-sign-on"></a>Configuración y prueba del inicio de sesión único en Azure AD
En esta sección, configurará y probará el SSO de Azure AD con WORKS MOBILE mediante un usuario de prueba llamado "Britta Simon".

Para que el inicio de sesión único funcione, Azure AD debe saber cuál es el usuario homólogo de WORKS MOBILE para un usuario de Azure AD. Es decir, es necesario establecer una relación de vínculo entre un usuario de Azure AD y el usuario relacionado de WORKS MOBILE.

Para establecer esta relación de vínculo, se asigna el valor del **nombre de usuario** en Azure AD como el valor del **nombre de usuario** en WORKS MOBILE.

Para configurar y probar el inicio de sesión único de Azure AD con WORKS MOBILE, es preciso completar los siguientes bloques de creación:

1. **[Configuración del inicio de sesión único de Azure AD](#configuring-azure-ad-single-sign-on)**: para permitir a los usuarios usar esta característica.
2. **[Creación de un usuario de prueba de Azure AD](#creating-an-azure-ad-test-user)** : para probar el inicio de sesión único de Azure AD con Britta Simon.
3. **[Creación de un usuario de prueba de WORKS MOBILE](#creating-a-works-mobile-test-user)**: para tener un homólogo de Britta Simon en WORKS MOBILE que esté vinculado a la representación de ella en Azure AD.
4. **[Asignación del usuario de prueba de Azure AD](#assigning-the-azure-ad-test-user)** : para permitir que Britta Simon use el inicio de sesión único de Azure AD.
5. **[Prueba del inicio de sesión único](#testing-single-sign-on)**: para comprobar si funciona la configuración.

### <a name="configure-azure-ad-single-sign-on"></a>Configuración del inicio de sesión único de Azure AD

En esta sección, habilitará el inicio de sesión único de Azure AD en el Portal de administración de Azure y configurará el inicio de sesión único en la aplicación WORKS MOBILE.

**Para configurar el inicio de sesión único de Azure AD con WORKS MOBILE, realice los pasos siguientes:**

1. En el nuevo Portal de administración de Azure, en la página de integración de la aplicación **WORKS MOBILE**, haga clic en **Inicio de sesión único**.

    ![Configurar inicio de sesión único][4]
2. En el cuadro de diálogo **Inicio de sesión único**, en **Modo**, seleccione **Inicio de sesión basado en SAML** para habilitar el inicio de sesión único.
 
    ![Configurar inicio de sesión único](./media/active-directory-saas-worksmobile-tutorial/tutorial_worksmobile_01.png)
3. En la sección **Dominio y direcciones URL de WORKS MOBILE**, lleve a cabo los pasos siguientes:

    ![Configurar inicio de sesión único](./media/active-directory-saas-worksmobile-tutorial/tutorial_worksmobile_02.png)
  1. En el cuadro de texto **URL de inicio de sesión**, escriba una dirección URL con el siguiente patrón: `https://<your-subdomain>.worksmobile.com/jp/myservice`.
  2. En el cuadro de texto **Identificador**, escriba el valor como `worksmobile.com`.

    >[!NOTE] 
    >Estos valores no son reales. Tendrá que actualizarlos con la dirección URL y el identificador reales de inicio de sesión. Aquí le recomendamos que utilice el valor de cadena único en el identificador. Póngase en contacto con el [equipo de soporte técnico de WORKS MOBILE](mailto:dl_ssoinfo@worksmobile.com) para obtener estos valores. 
    >
    >

4. En la sección **Certificado de firma de SAML**, haga clic en **Crear nuevo certificado**.

    ![Configurar inicio de sesión único](./media/active-directory-saas-worksmobile-tutorial/tutorial_worksmobile_03.png)     
5. En el cuadro de diálogo **Crear nuevo certificado**, haga clic en el icono del calendario y seleccione una valor en **Fecha de expiración**. Luego haga clic en el botón **Guardar**.

    ![Configurar inicio de sesión único](./media/active-directory-saas-worksmobile-tutorial/tutorial_general_300.png)
6. En la sección **Certificado de firma de SAML**, seleccione **Make new certificate active** (Activar el nuevo certificado) y haga clic en el botón **Guardar**.

    ![Configurar inicio de sesión único](./media/active-directory-saas-worksmobile-tutorial/tutorial_worksmobile_04.png)
7. En la ventana emergente **Rollover certificate** (Certificado de sustitución), haga clic en **Aceptar**.

    ![Configurar inicio de sesión único](./media/active-directory-saas-worksmobile-tutorial/tutorial_general_400.png)
8. En la sección **Certificado de firma de SAML**, haga clic en **Certificado (sin procesar)** y, a continuación, guarde el archivo de certificado en el equipo.

    ![Configurar inicio de sesión único](./media/active-directory-saas-worksmobile-tutorial/tutorial_worksmobile_05.png) 

9. En la sección **Configuración de WORKS MOBILE**, haga clic en **Configurar WORKS MOBILE** para abrir la ventana **Configurar inicio de sesión**.

    ![Configurar inicio de sesión único](./media/active-directory-saas-worksmobile-tutorial/tutorial_worksmobile_06.png) 

    ![Configurar inicio de sesión único](./media/active-directory-saas-worksmobile-tutorial/tutorial_worksmobile_07.png)

10. Para configurar el inicio de sesión único en su aplicación, póngase en contacto con el [equipo de soporte técnico de WORKS MOBILE](mailto:dl_ssoinfo@worksmobile.com) para proporcionarles lo siguiente: •  El **archivo del certificado** descargado •  La **URL de inicio de sesión único de SAML** •  El **Id. de entidad de SAML** •  La **URL de cierre de sesión**
  
### <a name="create-an-azure-ad-test-user"></a>Creación de un usuario de prueba de Azure AD
El objetivo de esta sección es crear un usuario de prueba en el Portal de administración de Azure llamado Britta Simon.

![Creación de un usuario de Azure AD][100]

**Siga estos pasos para crear un usuario de prueba en Azure AD:**

1. En el panel de navegación izquierdo del **Portal de administración de Azure**, haga clic en el icono de **Azure Active Directory**.

    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-worksmobile-tutorial/create_aaduser_01.png) 
2. Vaya a **Usuarios y grupos** y haga clic en **Todos los usuarios** para mostrar la lista de usuarios.
    
    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-worksmobile-tutorial/create_aaduser_02.png) 
3. En la parte superior del diálogo, haga clic en **Agregar** para abrir el diálogo **Usuario**.
 
    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-worksmobile-tutorial/create_aaduser_03.png) 
4. En la página de diálogo **Usuario**, realice los siguientes pasos:
 
    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-worksmobile-tutorial/create_aaduser_04.png) 
  1. En el cuadro de texto **Nombre**, escriba **BrittaSimon**.
  2. En el cuadro de texto **Nombre de usuario**, escriba la **dirección de correo electrónico** de Britta Simon.
  3. Seleccione **Mostrar contraseña** y anote el valor del cuadro **Contraseña**.
  4. Haga clic en **Crear**. 

### <a name="create-a-works-mobile-test-user"></a>Crear un usuario de prueba de WORKS MOBILE

En esta sección, creará un usuario llamado Britta Simon en WORKS MOBILE. Trabaje con el [equipo de soporte técnico de WORKS MOBILE](mailto:dl_ssoinfo@worksmobile.com) para agregar los usuarios a la plataforma de WORKS MOBILE.

### <a name="assign-the-azure-ad-test-user"></a>Asignación del usuario de prueba de Azure AD

En esta sección, habilitará a Britta Simon para que use el SSO de Azure concediéndole acceso a WORKS MOBILE.

![Asignar usuario][200] 

**Para asignar a Britta Simon a WORKS MOBILE, realice los pasos siguientes:**

1. En el Portal de administración de Azure, abra la vista de aplicaciones, vaya a la vista de directorio y seleccione **Aplicaciones empresariales**. Después, haga clic en **Todas las aplicaciones**.

    ![Asignar usuario][201] 

2. En la lista de aplicaciones, seleccione **WORKS MOBILE**.

    ![Configurar inicio de sesión único](./media/active-directory-saas-worksmobile-tutorial/tutorial_worksmobile_50.png) 

3. En el menú de la izquierda, haga clic en **Usuarios y grupos**.

    ![Asignar usuario][202] 

4. Haga clic en el botón **Agregar**. Después, seleccione **Usuarios y grupos** en el cuadro de diálogo **Agregar asignación**.

    ![Asignar usuario][203]

5. En el cuadro de diálogo **Usuarios y grupos**, seleccione **Britta Simon** en la lista de usuarios.
6. Haga clic en el botón **Seleccionar** del cuadro de diálogo **Usuarios y grupos**.
7. Haga clic en el botón **Asignar** del cuadro de diálogo **Agregar asignación**.
    
### <a name="test-single-sign-on"></a>Prueba de inicio de sesión único

En esta sección, probará la configuración de SSO de Azure AD mediante el panel de acceso.

Al hacer clic en el icono de WORKS MOBILE en el Panel de acceso, debería iniciar sesión automáticamente en su aplicación WORKS MOBILE.

## <a name="additional-resources"></a>Recursos adicionales

* [Lista de tutoriales sobre cómo integrar aplicaciones SaaS con Azure Active Directory](active-directory-saas-tutorial-list.md)
* [¿Qué es el acceso a aplicaciones y el inicio de sesión único con Azure Active Directory?](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-worksmobile-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-worksmobile-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-worksmobile-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-worksmobile-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-worksmobile-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-worksmobile-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-worksmobile-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-worksmobile-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-worksmobile-tutorial/tutorial_general_203.png
