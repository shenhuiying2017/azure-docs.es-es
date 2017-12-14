---
title: "Tutorial: integración de Azure Active Directory con CloudPassage | Microsoft Docs"
description: "Aprenda a configurar el inicio de sesión único entre Azure Active Directory y CloudPassage."
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: bfe1f14e-74e4-4680-ac9e-f7355e1c94cc
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/22/2017
ms.author: jeedes
ms.openlocfilehash: b9d3329547b10d67c175c349fc643289823a3d81
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-cloudpassage"></a>Tutorial: integración de Azure Active Directory con CloudPassage

En este tutorial, aprenderá a integrar CloudPassage con Azure Active Directory (Azure AD).

La integración de CloudPassage con Azure AD proporciona las siguientes ventajas:

- Puede controlar en Azure AD quién tiene acceso a CloudPassage.
- Puede permitir que los usuarios inicien sesión automáticamente en CloudPassage (inicio de sesión único) con sus cuentas de Azure AD.
- Puede administrar las cuentas en una sola ubicación central: Azure Portal.

Si desea saber más sobre la integración de aplicaciones SaaS con Azure AD, consulte [¿Qué es el acceso a aplicaciones y el inicio de sesión único con Azure Active Directory?](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Requisitos previos

Para configurar la integración de Azure AD con CloudPassage, necesita los siguientes elementos:

- Una suscripción de Azure AD
- Una suscripción habilitada para el inicio de sesión único en CloudPassage

> [!NOTE]
> Para probar los pasos de este tutorial, no se recomienda el uso de un entorno de producción.

Para probar los pasos de este tutorial, debe seguir estas recomendaciones:

- No use el entorno de producción, salvo que sea necesario.
- Si no dispone de un entorno de prueba de Azure AD, puede obtener una versión de prueba de un mes [aquí](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descripción del escenario
En este tutorial, puede probar el inicio de sesión único de Azure AD en un entorno de prueba. La situación descrita en este tutorial consta de dos bloques de creación principales:

1. Adición de CloudPassage desde la galería
2. Configuración y comprobación del inicio de sesión único de Azure AD

## <a name="adding-cloudpassage-from-the-gallery"></a>Adición de CloudPassage desde la galería
Para configurar la integración de CloudPassage en Azure AD, deberá agregar CloudPassage desde la galería a la lista de aplicaciones SaaS administradas.

**Para agregar CloudPassage desde la galería, realice los pasos siguientes:**

1. En el panel de navegación izquierdo de **[Azure Portal](https://portal.azure.com)**, haga clic en el icono de **Azure Active Directory**. 

    ![Active Directory][1]

2. Vaya a **Aplicaciones empresariales**. A continuación, vaya a **Todas las aplicaciones**.

    ![Aplicaciones][2]
    
3. Para agregar una nueva aplicación, haga clic en el botón **Nueva aplicación** de la parte superior del cuadro de diálogo.

    ![Aplicaciones][3]

4. En el cuadro de búsqueda, escriba **CloudPassage**.

    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-cloudpassage-tutorial/tutorial_cloudpassage_search.png)

5. En el panel de resultados, seleccione **CloudPassage** y luego haga clic en el botón **Agregar** para agregar la aplicación.

    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-cloudpassage-tutorial/tutorial_cloudpassage_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configuración y comprobación del inicio de sesión único de Azure AD
En esta sección, va a configurar y probar el inicio de sesión único de Azure AD con CloudPassage con un usuario de prueba llamado "Britta Simon".

Para que el inicio de sesión único funcione, Azure AD debe saber cuál es el usuario homólogo de CloudPassage para un usuario de Azure AD. Es decir, es necesario establecer una relación de vínculo entre un usuario de Azure AD y el usuario relacionado de CloudPassage.

Para establecer la relación de vínculo, en CloudPassage, asigne el valor de **nombre de usuario** de Azure AD como valor de **nombre de usuario**.

Para configurar y probar el inicio de sesión único de Azure AD con CloudPassage, es preciso completar los siguientes bloques de creación:

1. **[Configuración del inicio de sesión único de Azure AD](#configuring-azure-ad-single-sign-on)** : para permitir a los usuarios usar esta característica.
2. **[Creación de un usuario de prueba de Azure AD](#creating-an-azure-ad-test-user)** : para probar el inicio de sesión único de Azure AD con Britta Simon.
3. **[Creación de un usuario de prueba de CloudPassage](#creating-a-cloudpassage-test-user)**: para tener un homólogo de Britta Simon en CloudPassage que esté vinculado a la representación del usuario en Azure AD.
4. **[Asignación del usuario de prueba de Azure AD](#assigning-the-azure-ad-test-user)** : para permitir que Britta Simon use el inicio de sesión único de Azure AD.
5. **[Testing Single Sign-On](#testing-single-sign-on)** : para comprobar si funciona la configuración.

### <a name="configuring-azure-ad-single-sign-on"></a>Configuración del inicio de sesión único de Azure AD

En esta sección, habilitará el inicio de sesión único de Azure AD en Azure Portal y configurará el inicio de sesión único en la aplicación CloudPassage.

**Para configurar el inicio de sesión único de Azure AD con CloudPassage, realice los pasos siguientes:**

1. En Azure Portal, en la página de integración de la aplicación **CloudPassage**, haga clic en **Inicio de sesión único**.

    ![Configurar inicio de sesión único][4]

2. En el cuadro de diálogo **Inicio de sesión único**, en **Modo** seleccione **Inicio de sesión basado en SAML** para habilitar el inicio de sesión único.
 
    ![Configurar inicio de sesión único](./media/active-directory-saas-cloudpassage-tutorial/tutorial_cloudpassage_samlbase.png)

3. En la sección **Dominio y direcciones URL de CloudPassage**, lleve a cabo los pasos siguientes:

    ![Configurar inicio de sesión único](./media/active-directory-saas-cloudpassage-tutorial/tutorial_cloudpassage_url.png)

    a. En el cuadro de texto **URL de inicio de sesión**, escriba una dirección URL con el siguiente patrón: `https://portal.cloudpassage.com/saml/init/accountid`.

    b. En el cuadro de texto **URL de respuesta**, escriba una dirección URL con el siguiente patrón: `https://portal.cloudpassage.com/saml/consume/accountid`. Para obtener el valor de este atributo, haga clic en **SSO Setup documentation** (Documentación de instalación de inicio de sesión única) en la sección **Single Sign-on Settings** (Configuración de inicio de sesión único) del portal de CloudPassage.

    ![Configurar inicio de sesión único](./media/active-directory-saas-cloudpassage-tutorial/tutorial_cloudpassage_05.png)
     
    > [!NOTE] 
    > Estos valores no son reales. Actualice estos valores con los valores reales de URL de respuesta y URL de inicio de sesión. Póngase en contacto con el [equipo de soporte de cliente de CloudPassage](https://www.cloudpassage.com/company/contact/) para obtener estos valores. 

4. En la sección **Certificado de firma de SAML**, haga clic en **Certificado (Base64)** y, luego, guarde el archivo de certificado en el equipo.

    ![Configurar inicio de sesión único](./media/active-directory-saas-cloudpassage-tutorial/tutorial_cloudpassage_certificate.png) 

5. La aplicación CloudPassage espera las aserciones de SAML en un formato específico, que requiere que se agreguen asignaciones de atributos personalizados a la configuración de los atributos del token de SAML. La siguiente captura de pantalla le muestra un ejemplo de esto.
   
   ![Configurar inicio de sesión único](./media/active-directory-saas-cloudpassage-tutorial/tutorial_cloudpassage_25.png) 

6. En la sección **Atributos de usuario** del cuadro de diálogo **Inicio de sesión único**, configure el atributo Token SAML como muestra la imagen anterior y realice los siguientes pasos:

    | Nombre del atributo | Valor de atributo |
    | --- | --- |
    | firstname |user.givenname |
    | lastname |user.surname |
    | email |user.mail |
    
    a. Haga clic en **Agregar atributo** para abrir el cuadro de diálogo **Agregar atributo**.

    ![Configurar inicio de sesión único](./media/active-directory-saas-cloudpassage-tutorial/tutorial_attribute_04.png)
    
    ![Configurar inicio de sesión único](./media/active-directory-saas-cloudpassage-tutorial/tutorial_attribute_05.png)
    
    b. En el cuadro de texto **Nombre**, escriba el nombre que se muestra para la fila.

    c. En la lista **Valor**, seleccione el atributo que se muestra para esa fila.
    
    d. Haga clic en **Aceptar**.

7. Haga clic en el botón **Guardar** .

    ![Configurar inicio de sesión único](./media/active-directory-saas-cloudpassage-tutorial/tutorial_general_400.png)
    
8. En la sección **Configuración de CloudPassage**, haga clic en **Configurar CloudPassage** para abrir la ventana **Configurar inicio de sesión**. Copie la **URL del servicio de inicio de sesión único de SAML, el identificador de entidad de SAML y la dirección URL de cierre de sesión** de la sección **Referencia rápida**.

    ![Configurar inicio de sesión único](./media/active-directory-saas-cloudpassage-tutorial/tutorial_cloudpassage_configure.png) 

9. En otra ventana del explorador, inicie sesión en su sitio de la compañía de CloudPassage como administrador.

10. En el menú de la parte superior, haga clic en **Settings** (Configuración) y en **Site Administration** (Administración del sitio). 
   
    ![Configurar inicio de sesión único][12]

11. Haga clic en la pestaña **Authentication Settings** (Configuración de autenticación). 
   
    ![Configurar inicio de sesión único][13]

12. En la sección **Single Sign-on Settings** (Configuración del inicio de sesión único), siga estos pasos: 
   
    ![Configurar inicio de sesión único][14]

    a. Seleccione la casilla **Enable Single sign-on(SSO)(SSO Setup Documentation)** (Habilitar el inicio de sesión único (SSO) (Documentación de configuración de SSO)).
    
    b. Pegue el **identificador de entidad de SAML** en el cuadro de texto **URL del emisor de SAML**.
  
    c. Pegue **Dirección URL del servicio de inicio de sesión único de SAML** en el cuadro de texto **Dirección URL del punto de conexión de SAML**.
  
    d. Pegue **Dirección URL de cierre de sesión** en el cuadro de texto **Logout landing page** (Página de aterrizaje de cierre de sesión).
  
    e. Abra el certificado descargado en el Bloc de notas, copie el contenido del mismo en el Portapapeles y luego péguelo en el cuadro de texto **Certificado x 509**.
  
    f. Haga clic en **Guardar**.

> [!TIP]
> Ahora puede leer una versión resumida de estas instrucciones dentro de [Azure Portal](https://portal.azure.com) mientras configura la aplicación.  Después de agregar esta aplicación desde la sección **Active Directory > Aplicaciones empresariales**, simplemente haga clic en la pestaña **Inicio de sesión único** y acceda a la documentación insertada a través de la sección **Configuración** de la parte inferior. Puede leer más sobre la característica de documentación insertada aquí: [Vista previa: Administración de inicio de sesión único para aplicaciones empresariales en el nuevo Azure Portal]( https://go.microsoft.com/fwlink/?linkid=845985)

### <a name="creating-an-azure-ad-test-user"></a>Creación de un usuario de prueba de Azure AD
El objetivo de esta sección es crear un usuario de prueba en Azure Portal llamado "Britta Simon".

![Creación de un usuario de Azure AD][100]

**Siga estos pasos para crear un usuario de prueba en Azure AD:**

1. En el panel de navegación izquierdo de **Azure Portal**, haga clic en el icono de **Azure Active Directory**.

    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-cloudpassage-tutorial/create_aaduser_01.png) 

2. Para mostrar la lista de usuarios, vaya a **Usuarios y grupos** y haga clic en **Todos los usuarios**.
    
    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-cloudpassage-tutorial/create_aaduser_02.png) 

3. Para abrir el cuadro de diálogo **Usuario**, haga clic en **Agregar** en la parte superior del cuadro de diálogo.
 
    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-cloudpassage-tutorial/create_aaduser_03.png) 

4. En la página de diálogo **Usuario**, realice los siguientes pasos:
 
    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-cloudpassage-tutorial/create_aaduser_04.png) 

    a. En el cuadro de texto **Nombre**, escriba **BrittaSimon**.

    b. En el cuadro de texto **Nombre de usuario**, escriba la **dirección de correo electrónico** de Britta Simon.

    c. Seleccione **Mostrar contraseña** y anote el valor del cuadro **Contraseña**.

    d. Haga clic en **Crear**.
 
### <a name="creating-a-cloudpassage-test-user"></a>Creación de un usuario de prueba de CloudPassage

El objetivo de esta sección es crear un usuario de prueba llamado Britta Simon en CloudPassage.

**Para crear un usuario llamado Britta Simon en CloudPassage, realice los pasos siguientes:**

1. Inicie sesión en su sitio de la compañía de **CloudPassage** como administrador. 

2. En la barra de herramientas de la parte superior, haga clic en **Settings** (Configuración) y en **Site Administration** (Administración del sitio). 
   
   ![Creación de un usuario de prueba de CloudPassage][22] 

3. Haga clic en la pestaña **Users** (Usuarios) y, a continuación, en **Add New User** (Agregar nuevo usuario). 
   
   ![Creación de un usuario de prueba de CloudPassage][23]

4. En la sección **Agregar nuevo usuario** , lleve a cabo estos pasos: 
   
   ![Creación de un usuario de prueba de CloudPassage][24]
    
    a. En el cuadro de texto **First Name** (Nombre), escriba Britta. 
  
    b. En el cuadro de texto **Last Name** (Apellidos), escriba Simon.
  
    c. En el cuadro de texto **Username** (Nombre de usuario), el cuadro de texto **Email** (Correo electrónico) y el cuadro de texto **Retype Email** (Volver a escribir correo electrónico), escriba el nombre de usuario de Britta en Azure AD.
  
    d. En **Access Type** (Tipo de acceso), seleccione **Enable Halo Portal Access** (Habilitar el acceso de Portal de Halo).
  
    e. Haga clic en **Agregar**.

### <a name="assigning-the-azure-ad-test-user"></a>Asignación del usuario de prueba de Azure AD

En esta sección, habilitará a Britta Simon para que use el inicio de sesión único de Azure concediéndole acceso a CloudPassage.

![Asignar usuario][200] 

**Para asignar un usuario llamado Britta Simon a CloudPassage, realice los pasos siguientes:**

1. En Azure Portal, abra la vista de aplicaciones, navegue a la vista de directorio y vaya a **Aplicaciones empresariales**. Luego haga clic en **Todas las aplicaciones**.

    ![Asignar usuario][201] 

2. En la lista de aplicaciones, seleccione **CloudPassage**.

    ![Configurar inicio de sesión único](./media/active-directory-saas-cloudpassage-tutorial/tutorial_cloudpassage_app.png) 

3. En el menú de la izquierda, haga clic en **Usuarios y grupos**.

    ![Asignar usuario][202] 

4. Haga clic en el botón **Agregar**. Después, seleccione **Usuarios y grupos** en el cuadro de diálogo **Agregar asignación**.

    ![Asignar usuario][203]

5. En el cuadro de diálogo **Usuarios y grupos**, seleccione **Britta Simon** en la lista de usuarios.

6. Haga clic en el botón **Seleccionar** del cuadro de diálogo **Usuarios y grupos**.

7. Haga clic en el botón **Asignar** del cuadro de diálogo **Agregar asignación**.
    
### <a name="testing-single-sign-on"></a>Prueba del inicio de sesión único 

El objetivo de esta sección es probar la configuración del inicio de sesión único de Azure AD mediante el panel de acceso.

Al hacer clic en el icono de CloudPassage en el Panel de acceso, debería iniciar sesión automáticamente en su aplicación de CloudPassage.

## <a name="additional-resources"></a>Recursos adicionales

* [Lista de tutoriales sobre cómo integrar aplicaciones SaaS con Azure Active Directory](active-directory-saas-tutorial-list.md)
* [¿Qué es el acceso a aplicaciones y el inicio de sesión único con Azure Active Directory?](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-cloudpassage-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-cloudpassage-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-cloudpassage-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-cloudpassage-tutorial/tutorial_general_04.png
[12]: ./media/active-directory-saas-cloudpassage-tutorial/tutorial_cloudpassage_07.png
[13]: ./media/active-directory-saas-cloudpassage-tutorial/tutorial_cloudpassage_08.png
[14]: ./media/active-directory-saas-cloudpassage-tutorial/tutorial_cloudpassage_09.png
[15]: ./media/active-directory-saas-cloudpassage-tutorial/tutorial_cloudpassage_10.png
[22]: ./media/active-directory-saas-cloudpassage-tutorial/tutorial_cloudpassage_15.png
[23]: ./media/active-directory-saas-cloudpassage-tutorial/tutorial_cloudpassage_16.png
[24]: ./media/active-directory-saas-cloudpassage-tutorial/tutorial_cloudpassage_17.png

[100]: ./media/active-directory-saas-cloudpassage-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-cloudpassage-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-cloudpassage-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-cloudpassage-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-cloudpassage-tutorial/tutorial_general_203.png

