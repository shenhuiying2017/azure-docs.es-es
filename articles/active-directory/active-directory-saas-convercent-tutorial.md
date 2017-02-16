---
title: "Tutorial: Integración de Azure Active Directory con Convercent | Microsoft Docs"
description: "Aprenda a configurar el inicio de sesión único entre Azure Active Directory y Convercent."
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.assetid: f9c9d290-0e13-490b-b559-0be772d6a690
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/28/2016
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 6bbfc7e22e5cb70ed1466bda14831412aead76a6
ms.openlocfilehash: bacabf7fe51e3c417a93a54f043c14aa5d65b31d


---
# <a name="tutorial-azure-active-directory-integration-with-convercent"></a>Tutorial: Integración de Azure Active Directory con Convercent

En este tutorial, aprenderá a integrar Convercent con Azure Active Directory (Azure AD).

La integración de Convercent con Azure AD proporciona las siguientes ventajas:

- Puede controlar en Azure AD quién tiene acceso a Convercent.
- Puede permitir que los usuarios inicien sesión automáticamente en Convercent (inicio de sesión único) con sus cuentas de Azure AD.
- Puede administrar sus cuentas en una ubicación central: el nuevo portal de Azure.

Si desea obtener más información sobre la integración de aplicaciones SaaS con Azure AD, vea [Qué es el acceso a las aplicaciones y el inicio de sesión único en Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Requisitos previos

Para configurar la integración de Azure AD con Convercent, necesita los siguientes elementos:

- Una suscripción de Azure AD
- Una suscripción habilitada para el inicio de sesión único en Convercent


> [!NOTE]
> Para probar los pasos de este tutorial, no se recomienda el uso de un entorno de producción.


Para probar los pasos de este tutorial, debe seguir estas recomendaciones:

- No debe usar el entorno de producción, a menos que sea necesario.
- Si no dispone de un entorno de prueba de Azure AD, puede obtener una versión de prueba de un mes [aquí](https://azure.microsoft.com/pricing/free-trial/).


## <a name="scenario-description"></a>Descripción del escenario
En este tutorial, puede probar el inicio de sesión único de Azure AD en un entorno de prueba. La situación descrita en este tutorial consta de dos bloques de creación principales:

1. Agregar Convercent desde la galería
2. Configuración y comprobación del inicio de sesión único de Azure AD


## <a name="adding-convercent-from-the-gallery"></a>Agregar Convercent desde la galería
Para configurar la integración de Convercent en Azure AD, es preciso agregar Convercent desde la galería a la lista de aplicaciones SaaS administradas.

**Para agregar Convercent desde la galería, realice los pasos siguientes:**

1. En el panel de navegación izquierdo del **Portal de administración de Azure**, haga clic en el icono de **Azure Active Directory**. 

    ![Active Directory][1]

2. Vaya a **Aplicaciones empresariales**. A continuación, vaya a **All applications** (Todas las aplicaciones).

    ![Aplicaciones][2]
    
3. Haga clic en el botón **Agregar** en la parte superior del cuadro de diálogo.

    ![Aplicaciones][3]

4. En el cuadro de búsqueda, escriba **Convercent**.

    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-convercent-tutorial/tutorial_convercent_001.png)

5. En el panel de resultados, seleccione **Convercent** y luego haga clic en el botón **Agregar** para agregar la aplicación.

    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-convercent-tutorial/tutorial_convercent_0001.png)


##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configuración y comprobación del inicio de sesión único de Azure AD
En esta sección, configurará y probará el inicio de sesión único de Azure AD con Convercent con un usuario de prueba llamado "Britta Simon".

Para que el inicio de sesión único funcione, Azure AD debe saber cuál es el usuario homólogo de Convercent para un usuario de Azure AD. Es decir, es necesario establecer una relación de vínculo entre un usuario de Azure AD y el usuario relacionado de Convercent.

Esta relación de vínculo se establece asignando el valor del **nombre de usuario** en Azure AD como el valor del **Nombre de usuario** en Convercent.

Para configurar y probar el inicio de sesión único de Azure AD con Convercent, es preciso completar los siguientes bloques de creación:

1. **[Configuración del inicio de sesión único de Azure AD](#configuring-azure-ad-single-sign-on)** : para permitir a los usuarios usar esta característica.
2. **[Creación de un usuario de prueba de Azure AD](#creating-an-azure-ad-test-user)** : para probar el inicio de sesión único de Azure AD con Britta Simon.
3. **[Creación de un usuario de prueba de Convercent](#creating-a-works-mobile-test-user)** : para tener un homólogo de Britta Simon en Convercent que esté vinculado a la representación de ella en Azure AD.
4. **[Asignación del usuario de prueba de Azure AD](#assigning-the-azure-ad-test-user)** : para permitir que Britta Simon use el inicio de sesión único de Azure AD.
5. **[Testing Single Sign-On](#testing-single-sign-on)** : para comprobar si funciona la configuración.

### <a name="configuring-azure-ad-single-sign-on"></a>Configuración del inicio de sesión único de Azure AD

En esta sección, habilitará el inicio de sesión único de Azure AD en el nuevo portal de Azure y configurará el inicio de sesión único en la aplicación Convercent.

**Para configurar el inicio de sesión único de Azure AD con Convercent, realice los pasos siguientes:**

1. En el nuevo portal Azure, en la página de integración de la aplicación **Convercent**, haga clic en **Inicio de sesión único**.

    ![Configurar inicio de sesión único][4]

2. En la página de diálogo **Inicio de sesión único**, en **Modo**, seleccione **Inicio de sesión basado en SAML** para habilitar el inicio de sesión único.
 
    ![Configurar inicio de sesión único](./media/active-directory-saas-convercent-tutorial/tutorial_convercent_01.png)

3. En la sección **Dominio y direcciones URL de Convercent**, si quiere configurar la aplicación en **IDP initiated mode** (Modo iniciado por ID), realice los siguientes pasos:

    ![Configurar inicio de sesión único](./media/active-directory-saas-convercent-tutorial/tutorial_convercent_02.png)

    a. En el cuadro de texto **Identificador**, escriba: `https://sts.convercent.com/`

    b. Haga clic en **"Mostrar configuración avanzada de URL"**

    c. En el cuadro de texto **Estado de la retransmisión**, escriba: `https://app.convercent.com/`.
    
4. Si quiere configurar la aplicación en **SP initiated mode** (Modo iniciado por SP), en la sección **Dominio y direcciones URL de Convercent**, realice los siguientes pasos:
    
    ![Configurar inicio de sesión único](./media/active-directory-saas-convercent-tutorial/tutorial_convercent_03.png)

    a. En el cuadro de texto **URL de inicio de sesión**, escriba: `https://app.convercent.com/`

    > [!NOTE] 
    > Aquí le sugeriremos que use el identificador único especificado. Póngase en contacto con el [equipo de soporte técnico de Convercent](mailTo:support@convercent.com) para obtener este valor.

5. En la sección **Configuración de Convercent**, haga clic en **Configurar Convercent** para abrir el diálogo **Configurar inicio de sesión**. A continuación, haga clic en **SAML XML Metadata** (Metadatos XML de SAML) y luego guarde el archivo de metadatos en el equipo.

    ![Configurar inicio de sesión único](./media/active-directory-saas-convercent-tutorial/tutorial_convercent_04.png) 

    ![Configurar inicio de sesión único](./media/active-directory-saas-convercent-tutorial/tutorial_convercent_05.png)

6. Para configurar SSO para su aplicación, póngase en contacto con el [equipo de soporte técnico de Convercent](mailTo:support@convercent.com) y proporcione los **metadatos** descargados.

7. En el nuevo portal de Azure, haga clic en el botón **Guardar**.  
  

### <a name="creating-an-azure-ad-test-user"></a>Creación de un usuario de prueba de Azure AD
El objetivo de esta sección es crear un usuario de prueba en el nuevo portal de Azure llamado Britta Simon.

![Creación de un usuario de Azure AD][100]

**Siga estos pasos para crear un usuario de prueba en Azure AD:**

1. En el panel de navegación izquierdo del **Portal de administración de Azure**, haga clic en el icono de **Azure Active Directory**.

    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-convercent-tutorial/create_aaduser_01.png) 

2. Vaya a **Usuarios y grupos** y haga clic en **Todos los usuarios** para mostrar la lista de usuarios.
    
    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-convercent-tutorial/create_aaduser_02.png) 

3. En la parte superior del diálogo, haga clic en **Agregar** para abrir el diálogo **Usuario**.
 
    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-convercent-tutorial/create_aaduser_03.png) 

4. En la página del diálogo **Usuario**, realice los pasos siguientes:
 
    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-convercent-tutorial/create_aaduser_04.png) 

    a. En el cuadro de texto **Nombre**, escriba **BrittaSimon**.

    b. En el cuadro de texto **Nombre de usuario**, escriba la **dirección de correo electrónico** de BrittaSimon.

    c. Seleccione **Mostrar contraseña** y anote el valor de la **contraseña**.

    d. Haga clic en **Crear**. 



### <a name="creating-a-convercent-test-user"></a>Creación de un usuario de prueba de Convercent

En esta sección, creará un usuario llamado Britta Simon en Convercent. Trabaje con el [equipo de soporte técnico de Convercent](emailto:support@convercent.com) para agregar los usuarios a la plataforma de Convercent.


### <a name="assigning-the-azure-ad-test-user"></a>Asignación del usuario de prueba de Azure AD

En esta sección, habilitará a Britta Simon para que use el inicio de sesión único de Azure concediéndole acceso a Convercent.

![Asignar usuario][200] 

**Para asignar a Britta Simon a Convercent, realice los pasos siguientes:**

1. En el Portal de administración de Azure, abra la vista de aplicaciones, vaya a la vista de directorio y vaya a **Aplicaciones empresariales**. A continuación, haga clic en **All applications** (Todas las aplicaciones).

    ![Asignar usuario][201] 

2. En la lista de aplicaciones, seleccione **Convercent**.

    ![Configurar inicio de sesión único](./media/active-directory-saas-convercent-tutorial/tutorial_convercent_50.png) 

3. En el menú de la izquierda, haga clic en **Usuarios y grupos**.

    ![Asignar usuario][202] 

4. Haga clic en el botón **Agregar**. A continuación, seleccione **Usuarios y grupos** en el diálogo **Agregar asignación**.

    ![Asignar usuario][203]

5. En el diálogo **Usuarios y grupos**, seleccione **Britta Simon** en la lista de usuarios.

6. Haga clic en el botón **Seleccionar** del diálogo **Usuarios y grupos**.

7. Haga clic en el botón **Asignar** del diálogo **Agregar asignación**.
    


### <a name="testing-single-sign-on"></a>Prueba del inicio de sesión único 

En esta sección, probará la configuración de inicio de sesión único de Azure AD mediante el Panel de acceso.

Al hacer clic en el icono de Convercent en el Panel de acceso, debería iniciar sesión automáticamente en la aplicación Convercent.


## <a name="additional-resources"></a>Recursos adicionales

* [Lista de tutoriales sobre cómo integrar aplicaciones SaaS con Azure Active Directory](active-directory-saas-tutorial-list.md)
* [¿Qué es el acceso a aplicaciones y el inicio de sesión único con Azure Active Directory?](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-convercent-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-convercent-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-convercent-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-convercent-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-convercent-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-convercent-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-convercent-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-convercent-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-convercent-tutorial/tutorial_general_203.png


<!--HONumber=Jan17_HO1-->


