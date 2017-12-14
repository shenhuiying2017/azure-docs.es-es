---
title: "Tutorial: Integración de Azure Active Directory con Showpad | Microsoft Docs"
description: "Obtenga información sobre cómo configurar el inicio de sesión único entre Azure Active Directory y Showpad."
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: 48b6bee0-dbc5-4863-964d-75b25e517741
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/12/2017
ms.author: jeedes
ms.openlocfilehash: 3fa246fe3e6264189e6b727b970708f26a787020
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-showpad"></a>Tutorial: integración de Azure Active Directory con Showpad

En este tutorial, obtendrá información sobre cómo integrar Showpad con Azure Active Directory (Azure AD).

Integrar Showpad con Azure AD proporciona las siguientes ventajas:

- Puede controlar en Azure AD quién tiene acceso a Showpad.
- Puede permitir que los usuarios inicien sesión automáticamente en Showpad (inicio de sesión único) con sus cuentas de Azure AD.
- Puede administrar sus cuentas en una ubicación central: el nuevo Azure Portal.

Si desea saber más sobre la integración de aplicaciones SaaS con Azure AD, consulte [¿Qué es el acceso a aplicaciones y el inicio de sesión único con Azure Active Directory?](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Requisitos previos

Para configurar la integración de Azure AD con Showpad, se necesitan los siguientes elementos:

- Una suscripción de Azure AD
- Una suscripción habilitada para el inicio de sesión único en Showpad

> [!NOTE]
> Para probar los pasos de este tutorial, no se recomienda el uso de un entorno de producción.

Para probar los pasos de este tutorial, debe seguir estas recomendaciones:

- No use el entorno de producción, salvo que sea necesario.
- Si no dispone de un entorno de prueba de Azure AD, puede obtener una versión de prueba de un mes [aquí](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descripción del escenario
En este tutorial, puede probar el inicio de sesión único de Azure AD en un entorno de prueba. La situación descrita en este tutorial consta de dos bloques de creación principales:

1. Incorporación de Showpad desde la galería
2. Configuración y comprobación del inicio de sesión único de Azure AD

## <a name="adding-showpad-from-the-gallery"></a>Incorporación de Showpad desde la galería

Para configurar la integración de Showpad en Azure AD, deberá agregar Showpad desde la galería a la lista de aplicaciones SaaS administradas.

**Para agregar Showpad desde la galería, realice los pasos siguientes:**

1. En el panel de navegación izquierdo de **[Azure Portal](https://portal.azure.com)**, haga clic en el icono de **Azure Active Directory**. 

    ![Active Directory][1]

2. Vaya a **Aplicaciones empresariales**. A continuación, vaya a **Todas las aplicaciones**.

    ![Aplicaciones][2]
    
3. Para agregar una nueva aplicación, haga clic en el botón **Nueva aplicación** de la parte superior del cuadro de diálogo.

    ![Aplicaciones][3]

4. En el cuadro de búsqueda, escriba **Showpad**.

    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-showpad-tutorial/tutorial_showpad_search.png)

5. En el panel de resultados, seleccione **Showpad** y luego haga clic en el botón **Agregar** para agregar la aplicación.

    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-showpad-tutorial/tutorial_showpad_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configuración y comprobación del inicio de sesión único de Azure AD

En esta sección, podrá configurar y probar el inicio de sesión único de Azure AD con Showpad con un usuario de prueba llamado "Britta Simon".

Para que el inicio de sesión único funcione, Azure AD debe saber cuál es el usuario homólogo de Showpad para un usuario de Azure AD. Es decir, hay que establecer una relación de vínculo entre un usuario de Azure AD y el usuario relacionado de Showpad.

Para establecer la relación de vínculo, en Showpad, asigne el valor de **nombre de usuario** de Azure AD como valor de **nombre de usuario**.

Para configurar y probar el inicio de sesión único de Azure AD con Showpad, es preciso completar los siguientes bloques de creación:

1. **[Configuración del inicio de sesión único de Azure AD](#configuring-azure-ad-single-sign-on)** : para permitir a los usuarios usar esta característica.
2. **[Creación de un usuario de prueba de Azure AD](#creating-an-azure-ad-test-user)** : para probar el inicio de sesión único de Azure AD con Britta Simon.
3. **[Creación de un usuario de prueba de Showpad](#creating-a-showpad-test-user)**: para tener un homólogo de Britta Simon en Showpad que esté vinculado a su representación en Azure AD.
4. **[Asignación del usuario de prueba de Azure AD](#assigning-the-azure-ad-test-user)** : para permitir que Britta Simon use el inicio de sesión único de Azure AD.
5. **[Testing Single Sign-On](#testing-single-sign-on)** : para comprobar si funciona la configuración.

### <a name="configuring-azure-ad-single-sign-on"></a>Configuración del inicio de sesión único de Azure AD

En esta sección, habilitará el inicio de sesión único de Azure AD en Azure Portal y lo configurará en la aplicación Showpad.

**Para configurar el inicio de sesión único de Azure AD con Showpad, realice los pasos siguientes:**

1. En Azure Portal, en la página de integración de la aplicación **Showpad**, haga clic en **Inicio de sesión único**.

    ![Configurar inicio de sesión único][4]

2. En el cuadro de diálogo **Inicio de sesión único**, en **Modo** seleccione **Inicio de sesión basado en SAML** para habilitar el inicio de sesión único.
 
    ![Configurar inicio de sesión único](./media/active-directory-saas-showpad-tutorial/tutorial_showpad_samlbase.png)

3. En la sección **Dominio y direcciones URL de Showpad**, lleve a cabo los pasos siguientes:

    ![Configurar inicio de sesión único](./media/active-directory-saas-showpad-tutorial/tutorial_showpad_url.png)

    a. En el cuadro de texto **URL de inicio de sesión**, escriba una dirección URL con el siguiente patrón: `https://<comapany-name>.showpad.biz/login`.

    b. En el cuadro de texto **Identificador**, escriba una dirección URL con el siguiente patrón: `https://<company-name>.showpad.biz`

    > [!NOTE] 
    > Estos valores no son reales. Debe actualizarlos con la dirección URL y el identificador reales de inicio de sesión. Póngase en contacto con el [equipo de soporte técnico de Showpad](https://help.showpad.com) para obtener estos valores. 
 


4. En la sección **Certificado de firma de SAML**, haga clic en **XML de metadatos** y luego guarde el archivo de metadatos en el equipo.

    ![Configurar inicio de sesión único](./media/active-directory-saas-showpad-tutorial/tutorial_showpad_certificate.png) 

5. Haga clic en el botón **Guardar** .

    ![Configurar inicio de sesión único](./media/active-directory-saas-showpad-tutorial/tutorial_general_400.png)

6. Inicie la sesión en el inquilino de Showpad como administrador.

7. En el menú de la parte superior, haga clic en **Configuración**.
   
    ![Configuración del inicio de sesión único en la aplicación](./media/active-directory-saas-showpad-tutorial/tutorial_showpad_001.png) 

8. Vaya a **Inicio de sesión único** y haga clic en "**Habilitar**".
   
    ![Configuración del inicio de sesión único en la aplicación](./media/active-directory-saas-showpad-tutorial/tutorial_showpad_002.png)

9. En el cuadro de diálogo **Agregar un servicio de SAML 2.0** , realice los pasos siguientes:
   
    ![Configuración del inicio de sesión único en la aplicación](./media/active-directory-saas-showpad-tutorial/tutorial_showpad_003.png) 
   
    a. En el cuadro de texto **Nombre**, escriba el nombre del proveedor de identificadores (por ejemplo, el nombre de la compañía).
   
    b. Como **origen de metadatos**, seleccione **XML**.
   
    c. Copie el contenido del archivo XML de metadatos que ha descargado de Azure Portal y, a continuación, péguelo en el cuadro de texto **XML de metadatos**.
   
    d. Seleccione **Aprovisionar cuentas automáticamente para nuevos usuarios cuando inicien sesión**.
   
    e. Haga clic en **Enviar**.

> [!TIP]
> Ahora puede leer una versión resumida de estas instrucciones dentro de [Azure Portal](https://portal.azure.com) mientras configura la aplicación.  Después de agregar esta aplicación desde la sección **Active Directory > Aplicaciones empresariales**, simplemente haga clic en la pestaña **Inicio de sesión único** y acceda a la documentación insertada a través de la sección **Configuración** de la parte inferior. Puede leer más sobre la característica de documentación insertada aquí: [Vista previa: Administración de inicio de sesión único para aplicaciones empresariales en el nuevo Azure Portal]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>Creación de un usuario de prueba de Azure AD
El objetivo de esta sección es crear un usuario de prueba en Azure Portal llamado "Britta Simon".

![Creación de un usuario de Azure AD][100]

**Siga estos pasos para crear un usuario de prueba en Azure AD:**

1. En el panel de navegación izquierdo de **Azure Portal**, haga clic en el icono de **Azure Active Directory**.

    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-showpad-tutorial/create_aaduser_01.png) 

2. Para mostrar la lista de usuarios, vaya a **Usuarios y grupos** y haga clic en **Todos los usuarios**.
    
    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-showpad-tutorial/create_aaduser_02.png) 

3. Para abrir el cuadro de diálogo **Usuario**, haga clic en **Agregar** en la parte superior del cuadro de diálogo.
 
    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-showpad-tutorial/create_aaduser_03.png) 

4. En la página de diálogo **Usuario**, realice los siguientes pasos:
 
    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-showpad-tutorial/create_aaduser_04.png) 

    a. En el cuadro de texto **Nombre**, escriba **BrittaSimon**.

    b. En el cuadro de texto **Nombre de usuario**, escriba la **dirección de correo electrónico** de Britta Simon.

    c. Seleccione **Mostrar contraseña** y anote el valor del cuadro **Contraseña**.

    d. Haga clic en **Crear**.
 
### <a name="creating-a-showpad-test-user"></a>Creación de usuarios de prueba de Showpad

El objetivo de esta sección es crear un usuario llamado Britta Simon en Showpad. 

Showpad admite el aprovisionamiento Just-In-Time. Ya lo ha habilitado en **[Configuración del inicio de sesión único de Azure AD](#configuring-azure-ad-single-sign-on)**. 

No hay ningún elemento de acción para usted en esta sección. 

### <a name="assigning-the-azure-ad-test-user"></a>Asignación del usuario de prueba de Azure AD

En esta sección, concederá acceso a Britta Simon a Showpad para que use el inicio de sesión único de Azure.

![Asignar usuario][200] 

**Para asignar Britta Simon a Showpad, realice los pasos siguientes:**

1. En Azure Portal, abra la vista de aplicaciones, vaya a la vista de directorio y vaya a **Aplicaciones empresariales**. Luego, haga clic en **Todas las aplicaciones**.

    ![Asignar usuario][201] 

2. En la lista de aplicaciones, seleccione **Showpad**.

    ![Configurar inicio de sesión único](./media/active-directory-saas-showpad-tutorial/tutorial_showpad_app.png) 

3. En el menú de la izquierda, haga clic en **Usuarios y grupos**.

    ![Asignar usuario][202] 

4. Haga clic en el botón **Agregar**. Después, seleccione **Usuarios y grupos** en el cuadro de diálogo **Agregar asignación**.

    ![Asignar usuario][203]

5. En el cuadro de diálogo **Usuarios y grupos**, seleccione **Britta Simon** en la lista de usuarios.

6. Haga clic en el botón **Seleccionar** del cuadro de diálogo **Usuarios y grupos**.

7. Haga clic en el botón **Asignar** del cuadro de diálogo **Agregar asignación**.
    
### <a name="testing-single-sign-on"></a>Prueba del inicio de sesión único 

En esta sección, probará la configuración de inicio de sesión único de Azure AD mediante el Panel de acceso.

Al hacer clic en el icono de Showpad del Panel de acceso, debería iniciar sesión automáticamente en su aplicación Showpad.
Para más información sobre el Panel de acceso, consulte [Introducción al Panel de acceso](active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Recursos adicionales

* [Lista de tutoriales sobre cómo integrar aplicaciones SaaS con Azure Active Directory](active-directory-saas-tutorial-list.md)
* [¿Qué es el acceso a aplicaciones y el inicio de sesión único con Azure Active Directory?](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-showpad-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-showpad-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-showpad-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-showpad-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-showpad-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-showpad-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-showpad-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-showpad-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-showpad-tutorial/tutorial_general_203.png

