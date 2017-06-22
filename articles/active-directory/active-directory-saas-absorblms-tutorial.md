---
title: "Tutorial: Integración de Azure Active Directory con Absorb LMS | Microsoft Docs"
description: "Aprenda a configurar el inicio de sesión único entre Azure Active Directory y Absorb LMS."
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.assetid: ba9f1b3d-a4a0-4ff7-b0e7-428e0ed92142
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/20/2017
ms.author: jeedes
ms.translationtype: Human Translation
ms.sourcegitcommit: 2c33e75a7d2cb28f8dc6b314e663a530b7b7fdb4
ms.openlocfilehash: e42c541ce15b2e025c7019e91ae3c6c74483b6c2
ms.contentlocale: es-es
ms.lasthandoff: 04/21/2017


---
# <a name="tutorial-azure-active-directory-integration-with-absorb-lms"></a>Tutorial: Integración de Azure Active Directory con Absorb LMS

En este tutorial, aprenderá a integrar Absorb LMS con Azure Active Directory (Azure AD).

La integración de Absorb LMS con Azure AD ofrece las siguientes ventajas:

- Puede controlar en Azure AD quién tiene acceso a Absorb LMS.
- Puede permitir que los usuarios inicien sesión automáticamente en Absorb LMS (inicio de sesión único) con sus cuentas de Azure AD.
- Puede administrar las cuentas en una sola ubicación central: Azure Portal.

Si quiere conocer más detalles sobre la integración de aplicaciones SaaS con Azure AD, vea: [¿Qué es el acceso a aplicaciones y el inicio de sesión único con Azure Active Directory?](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Requisitos previos

Para configurar la integración de Azure AD con Absorb LMS, necesita lo siguiente:

- Una suscripción de Azure AD
- Una suscripción habilitada para inicio de sesión único en Absorb LMS

> [!NOTE]
> Para probar los pasos de este tutorial, no se recomienda el uso de un entorno de producción.

Para probar los pasos de este tutorial, debe seguir estas recomendaciones:

- No use el entorno de producción, salvo que sea necesario.
- Si no dispone de un entorno de prueba de Azure AD, puede obtener una versión de prueba de un mes [aquí](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descripción del escenario
En este tutorial, puede probar el inicio de sesión único de Azure AD en un entorno de prueba. La situación descrita en este tutorial consta de dos bloques de creación principales:

1. Adición de Absorb LMS desde la galería
2. Configuración y comprobación del inicio de sesión único de Azure AD

## <a name="adding-absorb-lms-from-the-gallery"></a>Adición de Absorb LMS desde la galería
Para configurar la integración de Absorb LMS en Azure AD, será preciso que agregue Absorb LMS desde la galería a la lista de aplicaciones SaaS administradas.

**Para agregar Absorb LMS desde la galería, siga estos pasos:**

1. En el panel de navegación izquierdo de **[Azure Portal](https://portal.azure.com)**, haga clic en el icono de **Azure Active Directory**. 

    ![Active Directory][1]

2. Vaya a **Aplicaciones empresariales**. A continuación, vaya a **Todas las aplicaciones**.

    ![Aplicaciones][2]
    
3. Para agregar una nueva aplicación, haga clic en el botón **Nueva aplicación** de la parte superior del cuadro de diálogo.

    ![Aplicaciones][3]

4. En el cuadro de búsqueda, escriba **Absorb LMS**.

    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-absorblms-tutorial/tutorial_absorblms_search.png)

5. En el panel de resultados, seleccione **Absorb LMS** y luego haga clic en el botón **Agregar** para agregar la aplicación.

    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-absorblms-tutorial/tutorial_absorblms_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configuración y comprobación del inicio de sesión único de Azure AD
En esta sección, podrá configurar y probar el inicio de sesión único de Azure AD con Absorb LMS con un usuario de prueba llamado "Britta Simon".

Para que el inicio de sesión único funcione, Azure AD debe saber cuál es el usuario homólogo de Absorb LMS para un usuario de Azure AD. Es decir, es necesario establecer una relación de vínculo entre un usuario de Azure AD y el usuario relacionado de Absorb LMS.

Esta relación de vínculo se establece mediante la asignación del valor del **nombre de usuario** en Azure AD como valor del **Username** (Nombre de usuario) en Absorb LMS.

Para configurar y probar el inicio de sesión único de Azure AD con Absorb LMS, es preciso completar los siguientes bloques de creación:

1. **[Configuring Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)** : para permitir a los usuarios usar esta característica.
2. **[Creación de un usuario de prueba de Azure AD](#creating-an-azure-ad-test-user)** : para probar el inicio de sesión único de Azure AD con Britta Simon.
3. **[Creación de un usuario de prueba de Absorb LMS](#creating-an-absorb-lms-test-user)**: para tener un homólogo de Britta Simon en Absorb LMS que esté vinculado a la representación del usuario en Azure AD.
4. **[Asignación del usuario de prueba de Azure AD](#assigning-the-azure-ad-test-user)** : para permitir que Britta Simon use el inicio de sesión único de Azure AD.
5. **[Testing Single Sign-On](#testing-single-sign-on)** : para comprobar si funciona la configuración.

### <a name="configuring-azure-ad-single-sign-on"></a>Configuración del inicio de sesión único de Azure AD

En esta sección, habilitará el inicio de sesión único de Azure AD en Azure Portal y configurará el inicio de sesión único en la aplicación Absorb LMS.

**Para configurar el inicio de sesión único de Azure AD con Absorb LMS, siga estos pasos:**

1. En la página de integración de la aplicación **Absorb LMS** de Azure Portal, haga clic en **Inicio de sesión único**.

    ![Configurar inicio de sesión único][4]

2. En el cuadro de diálogo **Inicio de sesión único**, en **Modo** seleccione **Inicio de sesión basado en SAML** para habilitar el inicio de sesión único.
 
    ![Configurar inicio de sesión único](./media/active-directory-saas-absorblms-tutorial/tutorial_absorblms_samlbase.png)

3. En la sección **Dominio y direcciones URL de Absorb LMS**, si quiere configurar la aplicación en modo iniciado por **IDP**:

    ![Configurar inicio de sesión único](./media/active-directory-saas-absorblms-tutorial/tutorial_absorblms_url.png)

    a. En el cuadro de texto **Identificador**, escriba una dirección URL con el siguiente patrón: `https://<subdomain>.myabsorb.com/Account/SAML`

    b. En el cuadro de texto **URL de respuesta**, escriba una dirección URL con el siguiente patrón: `https://<subdomain>.myabsorb.com/Account/SAML`.

4. Active **Mostrar configuración avanzada de URL**. Si quiere configurar la aplicación en modo iniciado por **SP**:

    ![Configurar inicio de sesión único](./media/active-directory-saas-absorblms-tutorial/tutorial_absorblms_url2.png)

    En el cuadro de texto **URL de inicio de sesión**, escriba una dirección URL con el siguiente patrón: `https://<subdomain>.myabsorb.com/`.
     
    > [!NOTE] 
    > Estos valores no son reales. Actualice estos valores con los valores reales de Identificador, URL de respuesta y URL de inicio de sesión. Se recomienda usar el valor de cadena único de Identificador, URL de respuesta y URL de inicio de sesión. Póngase en contacto con el [equipo de soporte técnico de Absorb LMS](https://www.absorblms.com/support) para obtener estos valores. 

5. En la sección **Certificado de firma de SAML**, haga clic en **XML de metadatos** y luego guarde el archivo de metadatos en el equipo.

    ![Configurar inicio de sesión único](./media/active-directory-saas-absorblms-tutorial/tutorial_absorblms_certificate.png) 

6. Haga clic en el botón **Guardar** .

    ![Configurar inicio de sesión único](./media/active-directory-saas-absorblms-tutorial/tutorial_general_400.png)
    
7. En la sección **Configuración de Absorb LMS**, haga clic en **Configurar Absorb LMS** para abrir la ventana **Configurar inicio de sesión**. Copie las **direcciones URL del servicio de inicio de sesión único de SAML y de cierre de sesión** de la sección **Referencia rápida**.

    ![Configurar inicio de sesión único](./media/active-directory-saas-absorblms-tutorial/tutorial_absorblms_configure.png) 

8. En otra ventana del explorador web, inicie sesión como administrador en el sitio de la compañía de Absorb LMS.

9. Haga clic en el **icono de cuenta** de la interfaz de administración. 

    ![Configurar inicio de sesión único](./media/active-directory-saas-absorblms-tutorial/1.png)

10. Haga clic en **Portal Settings** (Configuración del portal).

    ![Configurar inicio de sesión único](./media/active-directory-saas-absorblms-tutorial/2.png)
    
11.    Haga clic en la pestaña **Usuarios** .

    ![Configurar inicio de sesión único](./media/active-directory-saas-absorblms-tutorial/3.png)

12. Siga estos pasos para acceder a los campos de configuración de inicio de sesión único:

    ![Configurar inicio de sesión único](./media/active-directory-saas-absorblms-tutorial/4.png)

    a. Seleccione el **Mode** (Modo) adecuado.

    > [!NOTE]
    > Se admite tanto el modo iniciado por IDP como por SP.

    b. Abra el certificado que ha descargado de Azure Portal en el Bloc de notas, quite las etiquetas **---BEGIN CERTIFICATE---** y **---END CERTIFICATE---** y pegue el resto del contenido en el cuadro de texto **Key** (Clave).
    
    c. En el cuadro de texto **Id Property** (Propiedad Id), seleccione el atributo adecuado que ha configurado como identificador de usuario en Azure AD; por ejemplo, si se selecciona userprinciplename en Azure AD, aquí se seleccionaría Username (Nombre de usuario).

    d. En el cuadro de texto **Login URL** (URL de inicio de sesión), pegue el valor de **"URL del servicio de inicio de sesión único de SAML"** que copió de la ventana **Configurar inicio de sesión** de Azure Portal.

    e. En el cuadro de texto **Logout URL** (URL de cierre de sesión), pegue el valor de **"Dirección URL de cierre de sesión"** que copió de la ventana **Configurar inicio de sesión** de Azure Portal.

13. Habilite **"Only Allow SSO Login"** (Solo permitir inicio de sesión SSO).

    ![Configurar inicio de sesión único](./media/active-directory-saas-absorblms-tutorial/5.png)

14. Haga clic en **"Save"** (Guardar).

> [!TIP]
> Ahora puede leer una versión concisa de estas instrucciones en [Azure Portal](https://portal.azure.com) mientras configura la aplicación.  Después de agregar esta aplicación desde la sección **Active Directory > Aplicaciones empresariales**, simplemente haga clic en la pestaña **Inicio de sesión único** y acceda a la documentación insertada a través de la sección **Configuración** de la parte inferior. Puede leer más sobre la característica de documentación insertada aquí: [Vista previa: Administración de inicio de sesión único para aplicaciones empresariales en el nuevo Azure Portal]( https://go.microsoft.com/fwlink/?linkid=845985)

### <a name="creating-an-azure-ad-test-user"></a>Creación de un usuario de prueba de Azure AD
El objetivo de esta sección es crear un usuario de prueba en Azure Portal llamado Britta Simon.

![Creación de un usuario de Azure AD][100]

**Siga estos pasos para crear un usuario de prueba en Azure AD:**

1. En el panel de navegación izquierdo de **Azure Portal**, haga clic en el icono de **Azure Active Directory**.

    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-absorblms-tutorial/create_aaduser_01.png) 

2. Para mostrar la lista de usuarios, vaya a **Usuarios y grupos** y haga clic en **Todos los usuarios**.
    
    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-absorblms-tutorial/create_aaduser_02.png) 

3. En la parte superior del diálogo, haga clic en **Agregar** para abrir el diálogo **Usuario**.
 
    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-absorblms-tutorial/create_aaduser_03.png) 

4. En la página de diálogo **Usuario**, realice los siguientes pasos:
 
    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-absorblms-tutorial/create_aaduser_04.png) 

    a. En el cuadro de texto **Nombre**, escriba **BrittaSimon**.

    b. En el cuadro de texto **Nombre de usuario**, escriba la **dirección de correo electrónico** de Britta Simon.

    c. Seleccione **Mostrar contraseña** y anote el valor del cuadro **Contraseña**.

    d. Haga clic en **Crear**.

### <a name="creating-an-absorb-lms-test-user"></a>Creación de un usuario de prueba de Absorb LMS

Para permitir que los usuarios de Azure AD inicien sesión en Absorb LMS, tienen que aprovisionarse en Absorb LMS.  
En el caso de Absorb LMS, el aprovisionamiento es una tarea manual.

**Para aprovisionar una cuenta de usuario, realice estos pasos:**

1. Inicie sesión como administrador en el sitio de la compañía de Absorb LMS.

2. Haga clic en la pestaña **Users** (Usuarios).

    ![Invitar a contactos](./media/active-directory-saas-absorblms-tutorial/absorblms_users.png)

3. En la pestaña **Users** (Usuarios), haga clic en **Users** (Usuarios).

    ![Invitar a contactos](./media/active-directory-saas-absorblms-tutorial/absorblms_userssub.png)

4.  Seleccione **User** (Usuario) de la lista desplegable **Add New** (Agregar nuevo).

    ![Invitar a contactos](./media/active-directory-saas-absorblms-tutorial/absorblms_createuser.png)

5. En la página **Add User** (Agregar usuario), siga estos pasos:

    ![Invitar a contactos](./media/active-directory-saas-absorblms-tutorial/user.png)

    a. En el cuadro de texto **First Name** (Nombre), escriba el nombre, por ejemplo, Britta.

    b. En el cuadro de texto **Last Name** (Apellido), escriba el apellido, por ejemplo, Simon.
    
    c. En el cuadro de texto **Username** (Nombre de usuario), escriba el nombre de usuario, por ejemplo, Britta Simon.

    d. En el cuadro de texto **Password** (Contraseña), escriba la contraseña de Britta Simon.

    e. En el cuadro de texto **Confirm Password** (Confirmar contraseña), seleccione la confirmación de contraseña de Britta Simon.
    
    f. Establézcalo en **ACTIVE** (ACTIVO).    

6. Haga clic en **"Save"** (Guardar).
 
### <a name="assigning-the-azure-ad-test-user"></a>Asignación del usuario de prueba de Azure AD

En esta sección, habilitará a Britta Simon para que use el inicio de sesión único de Azure concediéndole acceso a Absorb LMS.

![Asignar usuario][200] 

**Para asignar a Britta Simon a Absorb LMS, siga estos pasos:**

1. En Azure Portal, abra la vista de aplicaciones, navegue a la vista de directorio y vaya a **Aplicaciones empresariales**. Luego haga clic en **Todas las aplicaciones**.

    ![Asignar usuario][201] 

2. En la lista de aplicaciones, seleccione **Absorb LMS**.

    ![Configurar inicio de sesión único](./media/active-directory-saas-absorblms-tutorial/tutorial_absorblms_app.png) 

3. En el menú de la izquierda, haga clic en **Usuarios y grupos**.

    ![Asignar usuario][202] 

4. Haga clic en el botón **Agregar**. Después, seleccione **Usuarios y grupos** en el cuadro de diálogo **Agregar asignación**.

    ![Asignar usuario][203]

5. En el cuadro de diálogo **Usuarios y grupos**, seleccione **Britta Simon** en la lista de usuarios.

6. Haga clic en el botón **Seleccionar** del cuadro de diálogo **Usuarios y grupos**.

7. Haga clic en el botón **Asignar** del cuadro de diálogo **Agregar asignación**.
    
### <a name="testing-single-sign-on"></a>Prueba del inicio de sesión único 

En esta sección, probará la configuración de inicio de sesión único de Azure AD mediante el Panel de acceso.

Al hacer clic en el icono de Absorb LMS del panel de acceso, debería iniciar sesión automáticamente en su aplicación Absorb LMS. Para más información sobre el Panel de acceso, consulte [Introducción al Panel de acceso](https://msdn.microsoft.com/library/dn308586).

## <a name="additional-resources"></a>Recursos adicionales

* [Lista de tutoriales sobre cómo integrar aplicaciones SaaS con Azure Active Directory](active-directory-saas-tutorial-list.md)
* [¿Qué es el acceso a aplicaciones y el inicio de sesión único con Azure Active Directory?](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-absorblms-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-absorblms-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-absorblms-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-absorblms-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-absorblms-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-absorblms-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-absorblms-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-absorblms-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-absorblms-tutorial/tutorial_general_203.png


