---
title: "Tutorial: Integración de Azure Active Directory con Moxtra | Microsoft Docs"
description: "Aprenda a configurar el inicio de sesión único entre Azure Active Directory y Moxtra."
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.assetid: 2aed2d4b-1dcd-4839-8fed-9419d107c61c
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/29/2017
ms.author: jeedes
ms.openlocfilehash: db2f041a44b6771b0a4f734e58d899298ef0847b
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-moxtra"></a>Tutorial: Integración de Azure Active Directory con Moxtra

En este tutorial, obtendrá información sobre cómo integrar Moxtra con Azure Active Directory (Azure AD).

Integrar Moxtra con Azure AD le proporciona las siguientes ventajas:

- Puede controlar en Azure AD quién tiene acceso a Moxtra.
- Puede permitir que los usuarios inicien sesión automáticamente en Moxtra (inicio de sesión único) con sus cuentas de Azure AD.
- Puede administrar sus cuentas en una ubicación central: el nuevo Azure Portal.

Si desea saber más sobre la integración de aplicaciones SaaS con Azure AD, consulte [¿Qué es el acceso a aplicaciones y el inicio de sesión único con Azure Active Directory?](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Requisitos previos

Para configurar la integración de Azure AD con Moxtra, se necesitan los siguientes elementos:

- Una suscripción de Azure AD
- Una suscripción habilitada para el inicio de sesión único en Moxtra

> [!NOTE]
> Para probar los pasos de este tutorial, no se recomienda el uso de un entorno de producción.

Para probar los pasos de este tutorial, debe seguir estas recomendaciones:

- No use el entorno de producción, salvo que sea necesario.
- Si no dispone de un entorno de prueba de Azure AD, puede obtener una versión de prueba de un mes [aquí](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descripción del escenario
En este tutorial, puede probar el inicio de sesión único de Azure AD en un entorno de prueba. La situación descrita en este tutorial consta de dos bloques de creación principales:

1. Incorporación de Moxtra desde la galería
2. Configuración y comprobación del inicio de sesión único de Azure AD

## <a name="adding-moxtra-from-the-gallery"></a>Incorporación de Moxtra desde la galería
Para configurar la integración de Moxtra en Azure AD, es preciso agregar Moxtra desde la galería a la lista de aplicaciones SaaS administradas.

**Para agregar Moxtra desde la galería, realice los pasos siguientes:**

1. En el panel de navegación izquierdo de **[Azure Portal](https://portal.azure.com)**, haga clic en el icono de **Azure Active Directory**. 

    ![Active Directory][1]

2. Vaya a **Aplicaciones empresariales**. A continuación, vaya a **Todas las aplicaciones**.

    ![Aplicaciones][2]
    
3. Para agregar una nueva aplicación, haga clic en el botón **Nueva aplicación** de la parte superior del cuadro de diálogo.

    ![Aplicaciones][3]

4. En el cuadro de búsqueda, escriba **Moxtra**.

    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-moxtra-tutorial/tutorial_moxtra_search.png)

5. En el panel de resultados, seleccione **Moxtra** y luego haga clic en el botón **Agregar** para agregar la aplicación.

    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-moxtra-tutorial/tutorial_moxtra_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configuración y comprobación del inicio de sesión único de Azure AD
En esta sección, podrá configurar y probar el inicio de sesión único de Azure AD con Moxtra con un usuario de prueba llamado "Britta Simon".

Para que el inicio de sesión único funcione, Azure AD debe saber cuál es el usuario homólogo de Moxtra para un usuario de Azure AD. Es decir, hay que establecer una relación de vínculo entre un usuario de Azure AD y el usuario relacionado de Moxtra.

Para establecer la relación de vínculo, en Moxtra, asigne el valor de **nombre de usuario** de Azure AD como valor de **Nombre de usuario**.

Para configurar y probar el inicio de sesión único de Azure AD con Moxtra, es preciso completar los siguientes bloques de creación:

1. **[Configuración del inicio de sesión único de Azure AD](#configuring-azure-ad-single-sign-on)** : para permitir a los usuarios usar esta característica.
2. **[Creación de un usuario de prueba de Azure AD](#creating-an-azure-ad-test-user)** : para probar el inicio de sesión único de Azure AD con Britta Simon.
3. **[Creación de un usuario de prueba de Moxtra](#creating-a-moxtra-test-user)**: para tener un homólogo de Britta Simon en Moxtra que esté vinculado a la representación del usuario en Azure AD.
4. **[Asignación del usuario de prueba de Azure AD](#assigning-the-azure-ad-test-user)** : para permitir que Britta Simon use el inicio de sesión único de Azure AD.
5. **[Testing Single Sign-On](#testing-single-sign-on)** : para comprobar si funciona la configuración.

### <a name="configuring-azure-ad-single-sign-on"></a>Configuración del inicio de sesión único de Azure AD

En esta sección, habilitará el inicio de sesión único de Azure AD en Azure Portal y configurará el inicio de sesión único en la aplicación Moxtra.

**Para configurar el inicio de sesión único de Azure AD con Moxtra, realice los pasos siguientes:**

1. En Azure Portal, en la página de integración de la aplicación **Moxtra**, haga clic en **Inicio de sesión único**.

    ![Configurar inicio de sesión único][4]

2. En el cuadro de diálogo **Inicio de sesión único**, en **Modo** seleccione **Inicio de sesión basado en SAML** para habilitar el inicio de sesión único.
 
    ![Configurar inicio de sesión único](./media/active-directory-saas-moxtra-tutorial/tutorial_moxtra_samlbase.png)

3. En la sección **Dominio y direcciones URL de Moxtra**, lleve a cabo los pasos siguientes:

    ![Configurar inicio de sesión único](./media/active-directory-saas-moxtra-tutorial/tutorial_moxtra_url.png)

    En el cuadro de texto **URL de inicio de sesión**, escriba una URL como: `https://www.moxtra.com/service/#login`

4. La aplicación Moxtra espera las aserciones de SAML en un formato específico. Configure las siguientes notificaciones para esta aplicación. Puede administrar los valores de estos atributos en la sección "**Atributos de usuario**" de la página de integración de aplicaciones. En la siguiente captura de pantalla se muestra un ejemplo de esta configuración. 

    ![Configurar inicio de sesión único](./media/active-directory-saas-moxtra-tutorial/tutorial_moxtra_attributes.png)
    
5. En la sección **Atributos de usuario** del cuadro de diálogo **Inicio de sesión único**, configure el atributo token de SAML como muestra la imagen y siga estos pasos:
    
    | Nombre del atributo | Valor de atributo |
    | ------------------- | -------------------- |    
    | firstname | user.givenname |
    | lastname | user.surname |
    | idpid    | < SAML Entity ID > 

    > [!Note]
    > El valor del atributo **idpid** no es real. Puede obtener el valor real de la sección **Quick reference** (Referencia rápida) en **Moxtra Configuration** (Configuración de Moxtra).
    
    a. Haga clic en **Agregar atributo** para abrir el cuadro de diálogo **Agregar atributo**.

    ![Configurar inicio de sesión único](./media/active-directory-saas-moxtra-tutorial/tutorial_attribute_04.png)

    b. En el cuadro de texto **Nombre**, escriba el nombre que se muestra para la fila.

    ![Configurar inicio de sesión único](./media/active-directory-saas-moxtra-tutorial/tutorial_attribute_05.png)

    c. En la lista **Valor**, seleccione el atributo que se muestra para esa fila.

    d. Haga clic en **Aceptar**.
    
5. En la sección **Certificado de firma de SAML**, haga clic en **Certificado (Base64)** y, luego, guarde el archivo de certificado en el equipo.

    ![Configurar inicio de sesión único](./media/active-directory-saas-moxtra-tutorial/tutorial_moxtra_certificate.png) 

6. Haga clic en el botón **Guardar** .

    ![Configurar inicio de sesión único](./media/active-directory-saas-moxtra-tutorial/tutorial_general_400.png)

7. En la sección **Configuración de Moxtra**, haga clic en **Configurar Moxtra** para abrir la ventana **Configurar inicio de sesión**. Copie los valores de **SAML Entity ID y SAML Single Sign-On Service URL** (Identificador de entidad de SAML y URL del servicio de inicio de sesión único de SAML) de la sección de **referencia rápida.**

    ![Configurar inicio de sesión único](./media/active-directory-saas-moxtra-tutorial/tutorial_moxtra_configure.png) 

8. En otra ventana del explorador, inicie sesión en su sitio de la compañía de Moxtra como administrador.

9. En la barra de herramientas de la izquierda, haga clic en **Admin Console > SAML Single Sign-on** (Consola de administración > Inicio de sesión único de SAML) y, luego, en **New** (Nuevo).
   
    ![Configurar inicio de sesión único](./media/active-directory-saas-moxtra-tutorial/tutorial_moxtra_06.png) 

10. En la página **SAML** , realice los siguientes pasos:
   
    ![Configurar inicio de sesión único](./media/active-directory-saas-moxtra-tutorial/tutorial_moxtra_08.png)   
 
    a. En el cuadro de texto **Nombre** , escriba el nombre de la configuración (por ejemplo, *SAML*). 
  
    b. En el cuadro de texto **IdP Entity ID** (Id. de entidad IdP), pegue el valor de **SAML Entity ID** (Identificador de entidad de SAML) que copió de Azure Portal. 
 
    c. En el cuadro de texto **Dirección URL de inicio de sesión**, pegue el valor de la **dirección URL del servicio de inicio de sesión único de SAML** que ha copiado de Azure Portal. 
 
    d. En el cuadro de texto **AuthnContextClassRef**, escriba **urn: oasis: nombres: tc: SAML:2.0:ac:classes:Password**. 
 
    e. En el cuadro de texto **NameID format** (Formato de id. de nombre), escriba **urn:oasis:names:tc:SAML:1.1:nameid-format:emailAddress**. 
 
    f. Abra el certificado que ha descargado desde Azure Portal en el Bloc de notas, copie el contenido y luego péguelo en el cuadro de texto **Certificate** (Certificado).    
 
    g. En el cuadro de texto del dominio de correo electrónico SAML, escriba su dominio de correo electrónico SAML.    
  
    >[!NOTE]
    >Para ver los pasos para comprobar el dominio, haga clic en la "**i**" a continuación.

    h. Haga clic en **Update**(Actualizar).

> [!TIP]
> Ahora puede leer una versión resumida de estas instrucciones dentro de [Azure Portal](https://portal.azure.com) mientras configura la aplicación.  Después de agregar esta aplicación desde la sección **Active Directory > Aplicaciones empresariales**, simplemente haga clic en la pestaña **Inicio de sesión único** y acceda a la documentación insertada a través de la sección **Configuración** de la parte inferior. Puede leer más sobre la característica de documentación insertada aquí: [Vista previa: Administración de inicio de sesión único para aplicaciones empresariales en el nuevo Azure Portal]( https://go.microsoft.com/fwlink/?linkid=845985)

### <a name="creating-an-azure-ad-test-user"></a>Creación de un usuario de prueba de Azure AD
El objetivo de esta sección es crear un usuario de prueba en Azure Portal llamado "Britta Simon".

![Creación de un usuario de Azure AD][100]

**Siga estos pasos para crear un usuario de prueba en Azure AD:**

1. En el panel de navegación izquierdo de **Azure Portal**, haga clic en el icono de **Azure Active Directory**.

    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-moxtra-tutorial/create_aaduser_01.png) 

2. Para mostrar la lista de usuarios, vaya a **Usuarios y grupos** y haga clic en **Todos los usuarios**.
    
    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-moxtra-tutorial/create_aaduser_02.png) 

3. Para abrir el cuadro de diálogo **Usuario**, haga clic en **Agregar** en la parte superior del cuadro de diálogo.
 
    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-moxtra-tutorial/create_aaduser_03.png) 

4. En la página de diálogo **Usuario**, realice los siguientes pasos:
 
    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-moxtra-tutorial/create_aaduser_04.png) 

    a. En el cuadro de texto **Nombre**, escriba **BrittaSimon**.

    b. En el cuadro de texto **Nombre de usuario**, escriba la **dirección de correo electrónico** de Britta Simon.

    c. Seleccione **Mostrar contraseña** y anote el valor del cuadro **Contraseña**.

    d. Haga clic en **Crear**.
 
### <a name="creating-a-moxtra-test-user"></a>Creación de un usuario de prueba de Moxtra

El objetivo de esta sección es crear un usuario llamado Britta Simon en Moxtra.

**Para crear un usuario llamado Britta Simon en Moxtra, realice los pasos siguientes:**

1. Inicie sesión en su sitio de la compañía de Moxtra como administrador.

2. En la barra de herramientas de la izquierda, haga clic en **Consola de administración > Administración de usuarios** y, luego, en **Agregar usuario**.
   
    ![Configurar inicio de sesión único](./media/active-directory-saas-moxtra-tutorial/tutorial_moxtra_10.png) 

3. En el cuadro de diálogo **Agregar usuario** , realice los pasos siguientes:
  
    a. En el cuadro de texto **Nombre**, escriba **Britta**.
  
    b. En el cuadro de texto **Apellidos**, escriba **Simon**.
  
    c. En el cuadro de texto **Email** (Correo electrónico), escriba la dirección de correo electrónico de Britta en Azure Portal.
  
    d. En el cuadro de texto **Division** (División), escriba **Dev**.
  
    e. En el cuadro de texto **Department** (Departamento), escriba **IT**.
  
    f. Seleccione **Administrator** (Administrador).
  
    g. Haga clic en **Agregar**.

### <a name="assigning-the-azure-ad-test-user"></a>Asignación del usuario de prueba de Azure AD

En esta sección, concederá acceso a Britta Simon a Moxtra para que use el inicio de sesión único de Azure.

![Asignar usuario][200] 

**Para asignar Britta Simon a Moxtra, realice los pasos siguientes:**

1. En Azure Portal, abra la vista de aplicaciones, vaya a la vista de directorio y vaya a **Aplicaciones empresariales**. Luego, haga clic en **Todas las aplicaciones**.

    ![Asignar usuario][201] 

2. En la lista de aplicaciones, seleccione **Moxtra**.

    ![Configurar inicio de sesión único](./media/active-directory-saas-moxtra-tutorial/tutorial_moxtra_app.png) 

3. En el menú de la izquierda, haga clic en **Usuarios y grupos**.

    ![Asignar usuario][202] 

4. Haga clic en el botón **Agregar**. Después, seleccione **Usuarios y grupos** en el cuadro de diálogo **Agregar asignación**.

    ![Asignar usuario][203]

5. En el cuadro de diálogo **Usuarios y grupos**, seleccione **Britta Simon** en la lista de usuarios.

6. Haga clic en el botón **Seleccionar** del cuadro de diálogo **Usuarios y grupos**.

7. Haga clic en el botón **Asignar** del cuadro de diálogo **Agregar asignación**.
    
### <a name="testing-single-sign-on"></a>Prueba del inicio de sesión único 

En esta sección, probará la configuración de inicio de sesión único de Azure AD mediante el Panel de acceso.

Al hacer clic en el icono de Moxtra en el panel de acceso, debería iniciar sesión automáticamente en su aplicación Moxtra.
Para más información sobre el Panel de acceso, consulte [Introducción al Panel de acceso](active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Recursos adicionales

* [Lista de tutoriales sobre cómo integrar aplicaciones SaaS con Azure Active Directory](active-directory-saas-tutorial-list.md)
* [¿Qué es el acceso a aplicaciones y el inicio de sesión único con Azure Active Directory?](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-moxtra-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-moxtra-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-moxtra-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-moxtra-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-moxtra-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-moxtra-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-moxtra-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-moxtra-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-moxtra-tutorial/tutorial_general_203.png

