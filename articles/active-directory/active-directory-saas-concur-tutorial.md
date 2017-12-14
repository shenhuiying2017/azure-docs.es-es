---
title: "Tutorial: integración de Azure Active Directory con Concur | Microsoft Docs"
description: "Aprenda a configurar el inicio de sesión único entre Azure Active Directory y Concur."
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: 1eee0a5d-24fa-4986-9aef-3c543cfe3296
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/16/2017
ms.author: jeedes
ms.openlocfilehash: 7619b09f24cf63f86871c975584254636292d7f3
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-concur"></a>Tutorial: Integración de Azure Active Directory con Concur

En este tutorial, aprenderá a integrar Concur con Azure Active Directory (Azure AD).

Integrar Concur con Azure AD le proporciona las siguientes ventajas:

- En Azure AD se puede controlar quién tiene acceso a Concur
- Puede permitir que los usuarios inicien sesión automáticamente en Concur (Inicio de sesión único) con sus cuentas de Azure AD
- Puede administrar sus cuentas en una ubicación central: el nuevo Azure Portal.

Si desea saber más sobre la integración de aplicaciones SaaS con Azure AD, consulte [¿Qué es el acceso a aplicaciones y el inicio de sesión único con Azure Active Directory?](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Requisitos previos

Para configurar la integración de Azure AD con Concur, necesita los siguientes elementos:

- Una suscripción de Azure AD
- Una suscripción habilitada para el inicio de sesión único en Concur

> [!NOTE]
> Para probar los pasos de este tutorial, no se recomienda el uso de un entorno de producción.

Para probar los pasos de este tutorial, debe seguir estas recomendaciones:

- No use el entorno de producción, salvo que sea necesario.
- Si no dispone de un entorno de prueba de Azure AD, puede obtener una versión de prueba de un mes [aquí](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descripción del escenario
En este tutorial, puede probar el inicio de sesión único de Azure AD en un entorno de prueba. La situación descrita en este tutorial consta de dos bloques de creación principales:

1. Agregar Concur desde la galería
2. Configuración y comprobación del inicio de sesión único de Azure AD

>[!NOTE]
>La configuración de la suscripción a Concur para un SSO federado mediante SAML es una tarea independiente. Para llevarla a cabo, debe ponerse en contacto con el [equipo de soporte al cliente de Concur](https://www.concur.co.in/contact). 

## <a name="adding-concur-from-the-gallery"></a>Agregar Concur desde la galería
Para configurar la integración de Concur en Azure AD, debe agregar Concur desde la galería a la lista de aplicaciones SaaS administradas.

**Para agregar Concur desde la galería, realice los pasos siguientes:**

1. En el panel de navegación izquierdo de **[Azure Portal](https://portal.azure.com)**, haga clic en el icono de **Azure Active Directory**. 

    ![Active Directory][1]

2. Vaya a **Aplicaciones empresariales**. A continuación, vaya a **Todas las aplicaciones**.

    ![Aplicaciones][2]
    
3. Para agregar una nueva aplicación, haga clic en el botón **Nueva aplicación** de la parte superior del cuadro de diálogo.

    ![Aplicaciones][3]

4. En el cuadro de búsqueda, escriba **Concur**.

    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-concur-tutorial/tutorial_concur_search.png)

5. En el panel de resultados, seleccione **Concur** y luego haga clic en el botón **Agregar** para agregar la aplicación.

    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-concur-tutorial/tutorial_concur_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configuración y comprobación del inicio de sesión único de Azure AD
En esta sección, puede configurar y probar el inicio de sesión único de Azure AD con Concur con un usuario de prueba llamado "Britta Simon".

Para que el inicio de sesión único funcione, Azure AD debe saber cuál es el usuario homólogo de Concur para un usuario de Azure AD. Es decir, es necesario establecer una relación de vínculo entre un usuario de Azure AD y el usuario relacionado de Concur.

Para establecer esta relación de vínculo, se asigna el valor del **nombre de usuario** en Azure AD como el valor de **Username** (Nombre de usuario) en Concur.

Para configurar y probar el inicio de sesión único de Azure AD con Concur, es preciso completar los siguientes bloques de creación:

1. **[Configuración del inicio de sesión único de Azure AD](#configuring-azure-ad-single-sign-on)** : para permitir a los usuarios usar esta característica.
2. **[Creación de un usuario de prueba de Azure AD](#creating-an-azure-ad-test-user)** : para probar el inicio de sesión único de Azure AD con Britta Simon.
3. **[Creación de un usuario de prueba de Concur](#creating-a-concur-test-user)**: para tener un homólogo de Britta Simon en Concur que esté vinculado a la representación de Azure AD del usuario.
4. **[Asignación del usuario de prueba de Azure AD](#assigning-the-azure-ad-test-user)** : para permitir que Britta Simon use el inicio de sesión único de Azure AD.
5. **[Testing Single Sign-On](#testing-single-sign-on)** : para comprobar si funciona la configuración.

### <a name="configuring-azure-ad-single-sign-on"></a>Configuración del inicio de sesión único de Azure AD

En esta sección, habilitará el inicio de sesión único de Azure AD en Azure Portal y configurará el inicio de sesión único en la aplicación Concur.

**Para configurar el inicio de sesión único de Azure AD con Concur, realice los pasos siguientes:**

1. En Azure Portal, en la página de integración de la aplicación **Concur**, haga clic en **Inicio de sesión único**.

    ![Configurar inicio de sesión único][4]

2. En el cuadro de diálogo **Inicio de sesión único**, en **Modo** seleccione **Inicio de sesión basado en SAML** para habilitar el inicio de sesión único.
 
    ![Configurar inicio de sesión único](./media/active-directory-saas-concur-tutorial/tutorial_concur_samlbase.png)

3. En la sección **Dominio y direcciones URL de Concur**, lleve a cabo los pasos siguientes:

    ![Configurar inicio de sesión único](./media/active-directory-saas-concur-tutorial/tutorial_concur_url.png)

    a. En el cuadro de texto **URL de inicio de sesión**, escriba el valor con el siguiente patrón: `https://www.concursolutions.com/UI/SSO/<OrganizationId>`

    b. En el cuadro de texto **Identificador**, escriba una dirección URL con el siguiente patrón: `https://<customer-domain>.concursolutions.com`

    > [!NOTE] 
    > Estos valores no son reales. Actualice estos valores con la dirección URL y el identificador reales de inicio de sesión. Póngase en contacto con el [equipo de soporte al cliente de Concur](https://www.concur.co.in/contact) para obtenerlos. 

4. En la sección **Certificado de firma de SAML**, haga clic en **XML de metadatos** y luego guarde el archivo XML en el equipo.

    ![Configurar inicio de sesión único](./media/active-directory-saas-concur-tutorial/tutorial_concur_certificate.png) 

5. Haga clic en el botón **Guardar**.

    ![Configurar el inicio de sesión único](./media/active-directory-saas-concur-tutorial/tutorial_general_400.png)
<CS>

6. Para configurar el inicio de sesión único en el lado de **Concur**, es preciso enviar los datos descargados de **XML de metadatos** al equipo de soporte técnico de Concur. Dicho equipo lo configura para establecer la conexión de SSO de SAML correctamente en ambos lados.

  >[!NOTE]
  >La configuración de la suscripción a Concur para un SSO federado mediante SAML es una tarea independiente. Para llevarla a cabo, debe ponerse en contacto con el [equipo de soporte al cliente de Concur](https://www.concur.co.in/contact). 
  
<CE>

> [!TIP]
> Ahora puede leer una versión resumida de estas instrucciones dentro de [Azure Portal](https://portal.azure.com) mientras configura la aplicación.  Después de agregar esta aplicación desde la sección **Active Directory > Aplicaciones empresariales**, simplemente haga clic en la pestaña **Inicio de sesión único** y acceda a la documentación insertada a través de la sección **Configuración** de la parte inferior. Puede leer más sobre la característica de documentación insertada aquí: [Vista previa: Administración de inicio de sesión único para aplicaciones empresariales en el nuevo Azure Portal]( https://go.microsoft.com/fwlink/?linkid=845985)

### <a name="creating-an-azure-ad-test-user"></a>Creación de un usuario de prueba de Azure AD
El objetivo de esta sección es crear un usuario de prueba en Azure Portal llamado "Britta Simon".

![Creación de un usuario de Azure AD][100]

**Siga estos pasos para crear un usuario de prueba en Azure AD:**

1. En el panel de navegación izquierdo de **Azure Portal**, haga clic en el icono de **Azure Active Directory**.

    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-concur-tutorial/create_aaduser_01.png) 

2. Para mostrar la lista de usuarios, vaya a **Usuarios y grupos** y haga clic en **Todos los usuarios**.
    
    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-concur-tutorial/create_aaduser_02.png) 

3. Para abrir el cuadro de diálogo **Usuario**, haga clic en **Agregar** en la parte superior del cuadro de diálogo.
 
    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-concur-tutorial/create_aaduser_03.png) 

4. En la página de diálogo **Usuario**, realice los siguientes pasos:
 
    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-concur-tutorial/create_aaduser_04.png) 

    a. En el cuadro de texto **Nombre**, escriba **BrittaSimon**.

    b. En el cuadro de texto **Nombre de usuario**, escriba la **dirección de correo electrónico** de Britta Simon.

    c. Seleccione **Mostrar contraseña** y anote el valor del cuadro **Contraseña**.

    d. Haga clic en **Crear**.
 
### <a name="creating-a-concur-test-user"></a>Creación de un usuario de prueba de Concur

La aplicación admite el aprovisionamiento de usuarios justo a tiempo y, tras la autenticación, los usuarios se crean automáticamente en la aplicación.

### <a name="assigning-the-azure-ad-test-user"></a>Asignación del usuario de prueba de Azure AD

En esta sección, habilitará a Britta Simon para que use el inicio de sesión único de Azure concediéndole acceso a Concur.

![Asignar usuario][200] 

**Para asignar Britta Simon a Concur, realice los pasos siguientes:**

1. En Azure Portal, abra la vista de aplicaciones, vaya a la vista de directorio y vaya a **Aplicaciones empresariales**. Luego, haga clic en **Todas las aplicaciones**.

    ![Asignar usuario][201] 

2. En la lista de aplicaciones, seleccione **Concur**.

    ![Configurar inicio de sesión único](./media/active-directory-saas-concur-tutorial/tutorial_concur_app.png) 

3. En el menú de la izquierda, haga clic en **Usuarios y grupos**.

    ![Asignar usuario][202] 

4. Haga clic en el botón **Agregar**. Después, seleccione **Usuarios y grupos** en el cuadro de diálogo **Agregar asignación**.

    ![Asignar usuario][203]

5. En el cuadro de diálogo **Usuarios y grupos**, seleccione **Britta Simon** en la lista de usuarios.

6. Haga clic en el botón **Seleccionar** del cuadro de diálogo **Usuarios y grupos**.

7. Haga clic en el botón **Asignar** del cuadro de diálogo **Agregar asignación**.
    
### <a name="testing-single-sign-on"></a>Prueba del inicio de sesión único 

En esta sección, probará la configuración de inicio de sesión único de Azure AD mediante el Panel de acceso.

Al hacer clic en el icono de Concur del Panel de acceso, debería entrar en la página de inicio de sesión de la aplicación Concur.
Para más información sobre el Panel de acceso, consulte [Introducción al Panel de acceso](active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Recursos adicionales

* [Lista de tutoriales sobre cómo integrar aplicaciones SaaS con Azure Active Directory](active-directory-saas-tutorial-list.md)
* [¿Qué es el acceso a aplicaciones y el inicio de sesión único con Azure Active Directory?](active-directory-appssoaccess-whatis.md)
* [Configuración del aprovisionamiento de usuarios](active-directory-saas-concur-provisioning-tutorial.md)



<!--Image references-->

[1]: ./media/active-directory-saas-concur-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-concur-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-concur-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-concur-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-concur-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-concur-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-concur-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-concur-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-concur-tutorial/tutorial_general_203.png

