---
title: "Tutorial: Integración de Azure Active Directory con Skillport | Microsoft Docs"
description: "Aprenda a configurar el inicio de sesión único entre Azure Active Directory y Skillport."
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: 4df349b2-a73f-4b88-a077-ec0fbfc26527
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/24/2017
ms.author: jeedes
ms.openlocfilehash: 3b14c40b402ae1877f6bfb267c137c9a051b8225
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-skillport"></a>Tutorial: Integración de Azure Active Directory con Skillport

En este tutorial, aprenderá a integrar Skillport con Azure Active Directory (Azure AD).

La integración de Skillport con Azure AD le proporciona las siguientes ventajas:

- Puede controlar en Azure AD quién tiene acceso a Skillport.
- Puede permitir que los usuarios inicien sesión automáticamente en Skillport (inicio de sesión único) con sus cuentas de Azure AD.
- Puede administrar sus cuentas en una ubicación central: el nuevo Azure Portal.

Si desea saber más sobre la integración de aplicaciones SaaS con Azure AD, consulte [¿Qué es el acceso a aplicaciones y el inicio de sesión único con Azure Active Directory?](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Requisitos previos

Para configurar la integración de Azure AD con Skillport, necesita los siguientes elementos:

- Una suscripción de Azure AD
- Una suscripción habilitada para el inicio de sesión único en Skillport

> [!NOTE]
> Para probar los pasos de este tutorial, no se recomienda el uso de un entorno de producción.

Para probar los pasos de este tutorial, debe seguir estas recomendaciones:

- No use el entorno de producción, salvo que sea necesario.
- Si no dispone de un entorno de prueba de Azure AD, puede obtener una versión de prueba de un mes [aquí](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descripción del escenario
En este tutorial, puede probar el inicio de sesión único de Azure AD en un entorno de prueba. La situación descrita en este tutorial consta de dos bloques de creación principales:

1. Adición de Skillport desde la galería
2. Configuración y comprobación del inicio de sesión único de Azure AD

## <a name="adding-skillport-from-the-gallery"></a>Adición de Skillport desde la galería
Para configurar la integración de Skillport en Azure AD, es preciso agregar Skillport desde la galería a la lista de aplicaciones SaaS administradas.

**Para agregar Skillport desde la galería, realice los pasos siguientes:**

1. En el panel de navegación izquierdo de **[Azure Portal](https://portal.azure.com)**, haga clic en el icono de **Azure Active Directory**. 

    ![Active Directory][1]

2. Vaya a **Aplicaciones empresariales**. A continuación, vaya a **Todas las aplicaciones**.

    ![Aplicaciones][2]
    
3. Haga clic en el botón **Nueva aplicación** en la parte superior del cuadro de diálogo.

    ![Aplicaciones][3]

4. En el cuadro de búsqueda, escriba **Skillport**.

    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-skillport-tutorial/tutorial_skillport_search.png)

5. En el panel de resultados, seleccione **Skillport** y luego haga clic en el botón **Agregar** para agregar la aplicación.

    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-skillport-tutorial/tutorial_skillport_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configuración y comprobación del inicio de sesión único de Azure AD
En esta sección, podrá configurar y probar el inicio de sesión único de Azure AD con Skillport con un usuario de prueba llamado "Britta Simon".

Para que el inicio de sesión único funcione, Azure AD debe saber cuál es el usuario homólogo de Skillport para un usuario de Azure AD. Es decir, es necesario establecer una relación de vínculo entre un usuario de Azure AD y el usuario relacionado de Skillport.

Esta relación de vínculo se establece mediante la asignación del valor del **nombre de usuario** en Azure AD como el valor del **nombre de usuario** en Skillport.

Para configurar y probar el inicio de sesión único de Azure AD con Skillport, es preciso completar los siguientes bloques de creación:

1. **[Configuración del inicio de sesión único de Azure AD](#configuring-azure-ad-single-sign-on)** : para permitir a los usuarios usar esta característica.
2. **[Creación de un usuario de prueba de Azure AD](#creating-an-azure-ad-test-user)** : para probar el inicio de sesión único de Azure AD con Britta Simon.
3. **[Creación de un usuario de prueba de Skillport](#creating-a-skillport-test-user)**: el objetivo es tener un homólogo de Britta Simon en Skillport que esté vinculado a la representación del usuario en Azure AD.
4. **[Asignación del usuario de prueba de Azure AD](#assigning-the-azure-ad-test-user)** : para permitir que Britta Simon use el inicio de sesión único de Azure AD.
5. **[Testing Single Sign-On](#testing-single-sign-on)** : para comprobar si funciona la configuración.

### <a name="configuring-azure-ad-single-sign-on"></a>Configuración del inicio de sesión único de Azure AD

En esta sección, habilitará el inicio de sesión único de Azure AD en Azure Portal y lo configurará en la aplicación Skillport.

**Para configurar el inicio de sesión único de Azure AD con Skillport, siga los pasos siguientes:**

1. En la página de integración de la aplicación **Skillport** de Azure Portal, haga clic en **Inicio de sesión único**.

    ![Configurar inicio de sesión único][4]

2. En el cuadro de diálogo **Inicio de sesión único**, en **Modo** seleccione **Inicio de sesión basado en SAML** para habilitar el inicio de sesión único.
 
    ![Configurar inicio de sesión único](./media/active-directory-saas-skillport-tutorial/tutorial_skillport_samlbase.png)

3. En la sección **Dominio y direcciones URL de Skillport**, lleve a cabo los pasos siguientes:

    ![Configurar inicio de sesión único](./media/active-directory-saas-skillport-tutorial/tutorial_skillport_url.png)

    a. En el cuadro de texto **URL de inicio de sesión**, escriba la dirección URL:
      
      Centro de datos de UE: `https://adfs.skillport.eu`
   
      Centro de datos de EE. UU.: `https://sso.skillport.com`

    b. En el cuadro de texto **Identificador**, escriba la dirección URL:
      
      Centro de datos de UE: `http://adfs.skillport.eu/adfs/services/trust`
   
      Centro de datos de EE. UU.: `https://sso.skillport.com`
   
    c. En el cuadro de texto **URL de respuesta**, escriba la siguiente dirección URL:
    
      Centro de datos de UE: ` https://adfs.skillport.eu/adfs/ls/`
    
      Centro de datos de EE. UU.: `https://sso.skillport.com/sp/ACS.saml2`
 
4. En la sección **Certificado de firma de SAML**, haga clic en **XML de metadatos** y luego guarde el archivo XML en el equipo.

    ![Configurar inicio de sesión único](./media/active-directory-saas-skillport-tutorial/tutorial_skillport_certificate.png) 

5. Haga clic en el botón **Guardar** .

    ![Configurar inicio de sesión único](./media/active-directory-saas-skillport-tutorial/tutorial_general_400.png)

6. Para configurar el inicio de sesión único en **Skillport**, debe enviar el archivo **XML de metadatos** descargado al [equipo de soporte técnico de Skillport](https://www.skillsoft.com/contact.asp). Ellos se encargarán de configurarlo para establecer la conexión de SSO de SAML correctamente en ambos lados.

### <a name="creating-an-azure-ad-test-user"></a>Creación de un usuario de prueba de Azure AD
El objetivo de esta sección es crear un usuario de prueba en Azure Portal llamado "Britta Simon".

![Creación de un usuario de Azure AD][100]

**Siga estos pasos para crear un usuario de prueba en Azure AD:**

1. En el panel de navegación izquierdo de **Azure Portal**, haga clic en el icono de **Azure Active Directory**.

    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-skillport-tutorial/create_aaduser_01.png) 

2. Vaya a **Usuarios y grupos** y haga clic en **Todos los usuarios** para mostrar la lista de usuarios.
    
    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-skillport-tutorial/create_aaduser_02.png) 

3. En la parte superior del diálogo, haga clic en **Agregar** para abrir el diálogo **Usuario**.
 
    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-skillport-tutorial/create_aaduser_03.png) 

4. En la página de diálogo **Usuario**, realice los siguientes pasos:
 
    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-skillport-tutorial/create_aaduser_04.png) 

    a. En el cuadro de texto **Nombre**, escriba **BrittaSimon**.

    b. En el cuadro de texto **Nombre de usuario**, escriba la **dirección de correo electrónico** de Britta Simon.

    c. Seleccione **Mostrar contraseña** y anote el valor del cuadro **Contraseña**.

    d. Haga clic en **Crear**.
 
### <a name="creating-a-skillport-test-user"></a>Creación de un usuario de prueba de Skillport

Para crear el usuario de prueba de Skillport, debe ponerse en contacto con el [equipo de soporte técnico de Skillport](https://www.skillsoft.com/contact.asp) ya que disponen de varios escenarios empresariales acordes con los requisitos del usuario final. Después de mantener conversaciones con los usuarios, procederán a configurarlo.  

### <a name="assigning-the-azure-ad-test-user"></a>Asignación del usuario de prueba de Azure AD

En esta sección, habilitará a Britta Simon para que use el inicio de sesión único de Azure concediéndole acceso a Skillport.

![Asignar usuario][200] 

**Para asignar a Britta Simon a Skillport, realice los pasos siguientes:**

1. En Azure Portal, abra la vista de aplicaciones, vaya a la vista de directorio y vaya a **Aplicaciones empresariales**. Luego, haga clic en **Todas las aplicaciones**.

    ![Asignar usuario][201] 

2. En la lista de aplicaciones, seleccione **Skillport**.

    ![Configurar inicio de sesión único](./media/active-directory-saas-skillport-tutorial/tutorial_skillport_app.png) 

3. En el menú de la izquierda, haga clic en **Usuarios y grupos**.

    ![Asignar usuario][202] 

4. Haga clic en el botón **Agregar**. Después, seleccione **Usuarios y grupos** en el cuadro de diálogo **Agregar asignación**.

    ![Asignar usuario][203]

5. En el cuadro de diálogo **Usuarios y grupos**, seleccione **Britta Simon** en la lista de usuarios.

6. Haga clic en el botón **Seleccionar** del cuadro de diálogo **Usuarios y grupos**.

7. Haga clic en el botón **Asignar** del cuadro de diálogo **Agregar asignación**.
    
### <a name="testing-single-sign-on"></a>Prueba del inicio de sesión único 

En esta sección, probará la configuración de inicio de sesión único de Azure AD mediante el Panel de acceso.

Al hacer clic en el icono de Skillport en el Panel de acceso, debería iniciar sesión automáticamente en su aplicación Skillport.
Para más información sobre el Panel de acceso, consulte [Introducción al Panel de acceso](https://msdn.microsoft.com/library/dn308586). 

## <a name="additional-resources"></a>Recursos adicionales

* [Lista de tutoriales sobre cómo integrar aplicaciones SaaS con Azure Active Directory](active-directory-saas-tutorial-list.md)
* [¿Qué es el acceso a aplicaciones y el inicio de sesión único con Azure Active Directory?](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-skillport-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-skillport-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-skillport-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-skillport-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-skillport-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-skillport-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-skillport-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-skillport-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-skillport-tutorial/tutorial_general_203.png

