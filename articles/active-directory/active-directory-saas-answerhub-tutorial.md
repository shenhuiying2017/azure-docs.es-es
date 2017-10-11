---
title: "Tutorial: Integración de Azure Active Directory con AnswerHub | Microsoft Docs"
description: "Obtenga información sobre cómo configurar el inicio de sesión único entre Azure Active Directory y AnswerHub."
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.assetid: 818b91d7-01df-4b36-9706-f167c710a73c
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/16/2017
ms.author: jeedes
ms.openlocfilehash: 3a1c9cc5d7a2ebe28e9fb7e0e6ed8e3d393873ae
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-answerhub"></a>Tutorial: Integración de Azure Active Directory con AnswerHub

En este tutorial, obtendrá información sobre cómo integrar AnswerHub con Azure Active Directory (Azure AD).

La integración de AnswerHub con Azure AD proporciona las siguientes ventajas:

- En Azure AD puede controlar quién tiene acceso a AnswerHub
- Puede permitir que los usuarios inicien sesión automáticamente en AnswerHub (inicio de sesión único) con sus cuentas de Azure AD
- Puede administrar sus cuentas en una ubicación central: el nuevo Azure Portal.

Si desea saber más sobre la integración de aplicaciones SaaS con Azure AD, consulte [¿Qué es el acceso a aplicaciones y el inicio de sesión único con Azure Active Directory?](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Requisitos previos

Para configurar la integración de Azure AD con AnswerHub, se necesitan los siguientes elementos:

- Una suscripción de Azure AD
- Una suscripción habilitada para el inicio de sesión único en AnswerHub

> [!NOTE]
> Para probar los pasos de este tutorial, no se recomienda el uso de un entorno de producción.

Para probar los pasos de este tutorial, debe seguir estas recomendaciones:

- No use el entorno de producción, salvo que sea necesario.
- Si no dispone de un entorno de prueba de Azure AD, puede obtener una versión de prueba de un mes [aquí](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descripción del escenario
En este tutorial, puede probar el inicio de sesión único de Azure AD en un entorno de prueba. La situación descrita en este tutorial consta de dos bloques de creación principales:

1. Incorporación de AnswerHub desde la galería
2. Configuración y comprobación del inicio de sesión único de Azure AD

## <a name="adding-answerhub-from-the-gallery"></a>Incorporación de AnswerHub desde la galería
Para configurar la integración de AnswerHub en Azure AD, será preciso que agregue AnswerHub desde la galería a la lista de aplicaciones SaaS administradas.

**Para agregar AnswerHub desde la galería, realice los pasos siguientes:**

1. En el panel de navegación izquierdo de **[Azure Portal](https://portal.azure.com)**, haga clic en el icono de **Azure Active Directory**. 

    ![Active Directory][1]

2. Vaya a **Aplicaciones empresariales**. A continuación, vaya a **Todas las aplicaciones**.

    ![Aplicaciones][2]
    
3. Para agregar una nueva aplicación, haga clic en el botón **Nueva aplicación** de la parte superior del cuadro de diálogo.

    ![Aplicaciones][3]

4. En el cuadro de búsqueda, escriba **AnswerHub**.

    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-answerhub-tutorial/tutorial_answerhub_search.png)

5. En el panel de resultados, seleccione **AnswerHub** y, luego, haga clic en el botón **Agregar** para agregar la aplicación.

    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-answerhub-tutorial/tutorial_answerhub_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configuración y comprobación del inicio de sesión único de Azure AD
En esta sección, podrá configurar y probar el inicio de sesión único de Azure AD con AnswerHub con un usuario de prueba llamado "Britta Simon".

Para que el inicio de sesión único funcione, Azure AD debe saber cuál es el usuario homólogo de AnswerHub para un usuario de Azure AD. Es decir, es necesario establecer una relación de vínculo entre un usuario de Azure AD y el usuario relacionado de AnswerHub.

Para establecer la relación de vínculo, en AnswerHub, asigne el valor de **nombre de usuario** de Azure AD como valor de **Nombre de usuario**.

Para configurar y probar el inicio de sesión único de Azure AD con AnswerHub, es preciso completar los siguientes bloques de creación:

1. **[Configuración del inicio de sesión único de Azure AD](#configuring-azure-ad-single-sign-on)** : para permitir a los usuarios usar esta característica.
2. **[Creación de un usuario de prueba de Azure AD](#creating-an-azure-ad-test-user)** : para probar el inicio de sesión único de Azure AD con Britta Simon.
3. **[Creación de un usuario de prueba de AnswerHub](#creating-an-answerhub-test-user)**: para tener un homólogo de Britta Simon en AnswerHub que esté vinculado a la representación del usuario en Azure AD.
4. **[Asignación del usuario de prueba de Azure AD](#assigning-the-azure-ad-test-user)** : para permitir que Britta Simon use el inicio de sesión único de Azure AD.
5. **[Testing Single Sign-On](#testing-single-sign-on)** : para comprobar si funciona la configuración.

### <a name="configuring-azure-ad-single-sign-on"></a>Configuración del inicio de sesión único de Azure AD

En esta sección, habilitará el inicio de sesión único de Azure AD en Azure Portal y configurará el inicio de sesión único en la aplicación AnswerHub.

**Para configurar el inicio de sesión único de Azure AD con AnswerHub, realice los pasos siguientes:**

1. En Azure Portal, en la página de integración de la aplicación **AnswerHub**, haga clic en **Inicio de sesión único**.

    ![Configurar inicio de sesión único][4]

2. En el cuadro de diálogo **Inicio de sesión único**, en **Modo** seleccione **Inicio de sesión basado en SAML** para habilitar el inicio de sesión único.
 
    ![Configurar inicio de sesión único](./media/active-directory-saas-answerhub-tutorial/tutorial_answerhub_samlbase.png)

3. En la sección de **dominio y direcciones URL de AnswerHub**, lleve a cabo los pasos siguientes:

    ![Configurar inicio de sesión único](./media/active-directory-saas-answerhub-tutorial/tutorial_answerhub_url.png)

    a. En el cuadro de texto **URL de inicio de sesión**, escriba una dirección URL con el siguiente patrón: `https://<company>.answerhub.com`.

    b. En el cuadro de texto **Identificador**, escriba una dirección URL con el siguiente patrón: `https://<company>.answerhub.com`

    > [!NOTE] 
    > Estos valores no son reales. Debe actualizarlos con la dirección URL y el identificador reales de inicio de sesión. Póngase en contacto con el [equipo de soporte de cliente de AnswerHub](mailto:success@answerhub.com) para obtener estos valores. 
 
4. En la sección **Certificado de firma de SAML**, haga clic en **Certificado (Base64)** y, luego, guarde el archivo de certificado en el equipo.

    ![Configurar inicio de sesión único](./media/active-directory-saas-answerhub-tutorial/tutorial_answerhub_certificate.png) 

5. Haga clic en el botón **Guardar** .

    ![Configurar inicio de sesión único](./media/active-directory-saas-answerhub-tutorial/tutorial_general_400.png)

6. En la sección **Configuración de AnswerHub**, haga clic en **Configurar AnswerHub** para abrir la ventana **Configurar inicio de sesión**. Copie la **dirección URL de cierre de sesión y la dirección URL del servicio de inicio de sesión único de SAML** de la **sección de referencia rápida**.

    ![Configurar inicio de sesión único](./media/active-directory-saas-answerhub-tutorial/tutorial_answerhub_configure.png) 

7. En otra ventana del explorador web, inicie sesión en el sitio de la compañía de AnswerHub como administrador.
   
    >[!NOTE]
    >Si necesita ayuda para configurar AnswerHub, póngase en contacto con el [equipo de soporte técnico de AnswerHub](mailto:success@answerhub.com.).
   
8. Vaya a **Administración**.

9. Haga clic en la pestaña **Usuario y grupo** .

10. En el panel de navegación izquierdo, en la sección **Social Settings** (Configuración social), haga clic en **SAML Setup** (Configuración de SAML).

11. Haga clic en la pestaña **Configuración de IDP** .

12. En la pestaña **Configuración de IDP** , lleve a cabo estos pasos:

     ![Configuración de SAML](./media/active-directory-saas-answerhub-tutorial/ic785172.png "Configuración de SAML")  
  
     a. En el cuadro de texto **IDP Login URL** (Dirección URL de inicio de sesión de IDP), pegue la **dirección URL del servicio de inicio de sesión único de SAML** que copió desde Azure Portal.
  
     b. En el cuadro de texto **IDP Logout URL** (Dirección URL de cierre de sesión de IDP), pegue la **dirección URL de cierre de sesión** que copió desde Azure Portal.
     
     c. En el cuadro de texto **IDP Name Identifier Format** (Formato del identificador de nombre de IDP), escriba el valor de identificador de usuario igual que el que se seleccionó en Azure Portal en la sección **Atributos de usuario**.
  
     d. Haga clic en **Claves y certificados**.

13. En la pestaña Claves y certificados, realice los pasos siguientes:
    
     ![Claves y certificados](./media/active-directory-saas-answerhub-tutorial/ic785173.png "Claves y certificados")  
 
     a. Abra el certificado codificado en Base 64 que descargó de Azure Portal en el Bloc de notas, copie el contenido en el Portapapeles y, luego, péguelo en el cuadro de texto **IDP Public Key (x509 Format)** (Clave pública de IDP [formato x509]).
  
     b. Haga clic en **Guardar**.

14. En la pestaña **IDP Config** (Configuración de IDP), haga clic en **Save** (Guardar).

> [!TIP]
> Ahora puede leer una versión resumida de estas instrucciones dentro de [Azure Portal](https://portal.azure.com) mientras configura la aplicación.  Después de agregar esta aplicación desde la sección **Active Directory > Aplicaciones empresariales**, simplemente haga clic en la pestaña **Inicio de sesión único** y acceda a la documentación insertada a través de la sección **Configuración** de la parte inferior. Puede leer más sobre la característica de documentación insertada aquí: [Vista previa: Administración de inicio de sesión único para aplicaciones empresariales en el nuevo Azure Portal]( https://go.microsoft.com/fwlink/?linkid=845985)

### <a name="creating-an-azure-ad-test-user"></a>Creación de un usuario de prueba de Azure AD
El objetivo de esta sección es crear un usuario de prueba en Azure Portal llamado "Britta Simon".

![Creación de un usuario de Azure AD][100]

**Siga estos pasos para crear un usuario de prueba en Azure AD:**

1. En el panel de navegación izquierdo de **Azure Portal**, haga clic en el icono de **Azure Active Directory**.

    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-answerhub-tutorial/create_aaduser_01.png) 

2. Para mostrar la lista de usuarios, vaya a **Usuarios y grupos** y haga clic en **Todos los usuarios**.
    
    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-answerhub-tutorial/create_aaduser_02.png) 

3. Para abrir el cuadro de diálogo **Usuario**, haga clic en **Agregar** en la parte superior del cuadro de diálogo.
 
    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-answerhub-tutorial/create_aaduser_03.png) 

4. En la página de diálogo **Usuario**, realice los siguientes pasos:
 
    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-answerhub-tutorial/create_aaduser_04.png) 

    a. En el cuadro de texto **Nombre**, escriba **BrittaSimon**.

    b. En el cuadro de texto **Nombre de usuario**, escriba la **dirección de correo electrónico** de Britta Simon.

    c. Seleccione **Mostrar contraseña** y anote el valor del cuadro **Contraseña**.

    d. Haga clic en **Crear**.
 
### <a name="creating-an-answerhub-test-user"></a>Creación de un usuario de prueba de AnswerHub

Para permitir que los usuarios de Azure AD inicien sesión en AnswerHub, tienen que aprovisionarse en AnswerHub.  
En el caso de AnswerHub, el aprovisionamiento es una tarea manual.

**Para aprovisionar una cuenta de usuario, realice estos pasos:**

1. Inicie sesión en el sitio de la compañía de **AnswerHub** como administrador.

2. Vaya a **Administración**.

3. Haga clic en la pestaña **Users & Groups** (Usuarios y grupos).

4. En el panel de navegación izquierdo, en la sección **Manage Users** (Administrar usuarios), haga clic en **Create or import users** (Crear o importar usuarios).
   
   ![Usuarios y grupos](./media/active-directory-saas-answerhub-tutorial/ic785175.png "Usuarios y grupos")

5. Escriba la **dirección de correo electrónico**, el **nombre de usuario** y la **contraseña** de una cuenta de Azure Active Directory válida que desee aprovisionar en los cuadros de texto relacionados y haga clic en **Save** (Guardar).

>[!NOTE]
>Puede usar cualquier otra API o herramienta de creación de cuentas de usuario de AnswerHub ofrecida por AnswerHub para aprovisionar cuentas de usuario de AAD.

### <a name="assigning-the-azure-ad-test-user"></a>Asignación del usuario de prueba de Azure AD

En esta sección, habilitará a Britta Simon para que use el inicio de sesión único de Azure concediéndole acceso a AnswerHub.

![Asignar usuario][200] 

**Para asignar Britta Simon a AnswerHub, realice los pasos siguientes:**

1. En Azure Portal, abra la vista de aplicaciones, vaya a la vista de directorio y vaya a **Aplicaciones empresariales**. Luego, haga clic en **Todas las aplicaciones**.

    ![Asignar usuario][201] 

2. En la lista de aplicaciones, seleccione **AnswerHub**.

    ![Configurar inicio de sesión único](./media/active-directory-saas-answerhub-tutorial/tutorial_answerhub_app.png) 

3. En el menú de la izquierda, haga clic en **Usuarios y grupos**.

    ![Asignar usuario][202] 

4. Haga clic en el botón **Agregar**. Después, seleccione **Usuarios y grupos** en el cuadro de diálogo **Agregar asignación**.

    ![Asignar usuario][203]

5. En el cuadro de diálogo **Usuarios y grupos**, seleccione **Britta Simon** en la lista de usuarios.

6. Haga clic en el botón **Seleccionar** del cuadro de diálogo **Usuarios y grupos**.

7. Haga clic en el botón **Asignar** del cuadro de diálogo **Agregar asignación**.
    
### <a name="testing-single-sign-on"></a>Prueba del inicio de sesión único 

En esta sección, probará la configuración de inicio de sesión único de Azure AD mediante el Panel de acceso.

Al hacer clic en el icono de AnswerHub en el Panel de acceso, debería iniciar sesión automáticamente en su aplicación AnswerHub.
Para más información sobre el Panel de acceso, consulte [Introducción al Panel de acceso](active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Recursos adicionales

* [Lista de tutoriales sobre cómo integrar aplicaciones SaaS con Azure Active Directory](active-directory-saas-tutorial-list.md)
* [¿Qué es el acceso a aplicaciones y el inicio de sesión único con Azure Active Directory?](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-answerhub-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-answerhub-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-answerhub-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-answerhub-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-answerhub-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-answerhub-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-answerhub-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-answerhub-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-answerhub-tutorial/tutorial_general_203.png

