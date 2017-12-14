---
title: "Tutorial: integración de Azure Active Directory con Jitbit Helpdesk | Microsoft Docs"
description: "Aprenda a configurar el inicio de sesión único entre Azure Active Directory y Jitbit Helpdesk."
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: 15ce27d4-0621-4103-8a34-e72c98d72ec3
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/28/2017
ms.author: jeedes
ms.openlocfilehash: 31d3295fa8d75aa72c8e9967b7ca81f4e98e5608
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-jitbit-helpdesk"></a>Tutorial: Integración de Azure Active Directory con Jitbit Helpdesk

En este tutorial, obtendrá información sobre cómo integrar Jitbit Helpdesk con Azure Active Directory (Azure AD).

La integración de Jitbit Helpdesk con Azure AD proporciona las siguientes ventajas:

- Puede controlar en Azure AD quién tiene acceso a Jitbit Helpdesk
- Puede permitir que los usuarios inicien sesión automáticamente en Jitbit Helpdesk (inicio de sesión único) con sus cuentas de Azure AD
- Puede administrar sus cuentas en una ubicación central: el nuevo Azure Portal.

Si desea saber más sobre la integración de aplicaciones SaaS con Azure AD, consulte [¿Qué es el acceso a aplicaciones y el inicio de sesión único con Azure Active Directory?](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Requisitos previos

Para configurar la integración de Azure AD con Jitbit Helpdesk, se necesitan los siguientes elementos:

- Una suscripción de Azure AD
- Una suscripción habilitada para el inicio de sesión único en Jitbit Helpdesk

> [!NOTE]
> Para probar los pasos de este tutorial, no se recomienda el uso de un entorno de producción.

Para probar los pasos de este tutorial, debe seguir estas recomendaciones:

- No use el entorno de producción, salvo que sea necesario.
- Si no dispone de un entorno de prueba de Azure AD, puede obtener una versión de prueba de un mes [aquí](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descripción del escenario
En este tutorial, puede probar el inicio de sesión único de Azure AD en un entorno de prueba. La situación descrita en este tutorial consta de dos bloques de creación principales:

1. Incorporación de Jitbit Helpdesk desde la Galería
2. Configuración y comprobación del inicio de sesión único de Azure AD

## <a name="adding-jitbit-helpdesk-from-the-gallery"></a>Incorporación de Jitbit Helpdesk desde la Galería
Para configurar la integración de Jitbit Helpdesk en Azure AD, será preciso que agregue Jitbit Helpdesk desde la galería a la lista de aplicaciones SaaS administradas.

**Para agregar Jitbit Helpdesk desde la galería, realice los pasos siguientes:**

1. En el panel de navegación izquierdo de **[Azure Portal](https://portal.azure.com)**, haga clic en el icono de **Azure Active Directory**. 

    ![Active Directory][1]

2. Vaya a **Aplicaciones empresariales**. A continuación, vaya a **Todas las aplicaciones**.

    ![Aplicaciones][2]
    
3. Para agregar una nueva aplicación, haga clic en el botón **Nueva aplicación** de la parte superior del cuadro de diálogo.

    ![Aplicaciones][3]

4. En el cuadro de búsqueda, escriba **Jitbit Helpdesk**.

    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-jitbit-helpdesk-tutorial/tutorial_jitbit-helpdesk_search.png)

5. En el panel de resultados, seleccione **Jitbit Helpdesk** y luego haga clic en el botón **Agregar** para agregar la aplicación.

    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-jitbit-helpdesk-tutorial/tutorial_jitbit-helpdesk_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configuración y comprobación del inicio de sesión único de Azure AD
En esta sección, podrá configurar y probar el inicio de sesión único de Azure AD con Jitbit Helpdesk con un usuario de prueba llamado "Britta Simon".

Para que el inicio de sesión único funcione, Azure AD debe saber cuál es el usuario homólogo de Jitbit Helpdesk para un usuario de Azure AD. Es decir, es necesario establecer una relación de vínculo entre un usuario de Azure AD y el usuario relacionado de Jitbit Helpdesk.

Para establecer la relación de vínculo, en Jitbit Helpdesk, asigne el valor de **nombre de usuario** de Azure AD como valor de **Nombre de usuario**.

Para configurar y probar el inicio de sesión único de Azure AD con Jitbit Helpdesk, es preciso completar los siguientes bloques de creación:

1. **[Configuración del inicio de sesión único de Azure AD](#configuring-azure-ad-single-sign-on)** : para permitir a los usuarios usar esta característica.
2. **[Creación de un usuario de prueba de Azure AD](#creating-an-azure-ad-test-user)** : para probar el inicio de sesión único de Azure AD con Britta Simon.
3. **[Creación de un usuario de prueba de Jitbit Helpdesk](#creating-a-jitbit-helpdesk-test-user)**: el objetivo es tener un homólogo de Britta Simon en Jitbit Helpdesk que esté vinculado a la representación del usuario en Azure AD.
4. **[Asignación del usuario de prueba de Azure AD](#assigning-the-azure-ad-test-user)** : para permitir que Britta Simon use el inicio de sesión único de Azure AD.
5. **[Testing Single Sign-On](#testing-single-sign-on)** : para comprobar si funciona la configuración.

### <a name="configuring-azure-ad-single-sign-on"></a>Configuración del inicio de sesión único de Azure AD

En esta sección, habilitará el inicio de sesión único de Azure AD en Azure Portal y configurará el inicio de sesión único en la aplicación Jitbit Helpdesk.

**Para configurar el inicio de sesión único de Azure AD con Jitbit Helpdesk, realice los pasos siguientes:**

1. En Azure Portal, en la página de integración de la aplicación **Jitbit Helpdesk**, haga clic en **Inicio de sesión único**.

    ![Configurar inicio de sesión único][4]

2. En el cuadro de diálogo **Inicio de sesión único**, en **Modo** seleccione **Inicio de sesión basado en SAML** para habilitar el inicio de sesión único.
 
    ![Configurar inicio de sesión único](./media/active-directory-saas-jitbit-helpdesk-tutorial/tutorial_jitbit-helpdesk_samlbase.png)

3. En la sección **Dominio y direcciones URL de Jitbit Helpdesk**, lleve a cabo los pasos siguientes:

    ![Configurar inicio de sesión único](./media/active-directory-saas-jitbit-helpdesk-tutorial/tutorial_jitbit-helpdesk_url.png)

    a. En el cuadro de texto **URL de inicio de sesión**, escriba una dirección URL con el siguiente patrón: 
    | |     
    | ----------------------------------------|
    | `https://<hostname>/helpdesk/User/Login`|
    | `https://<tenant-name>.Jitbit.com`|
    | |
    
    > [!NOTE] 
    > Este valor no es real. Actualícelo con la dirección URL de inicio de sesión real. Póngase en contacto con el [equipo de soporte técnico de Jitbit Helpdesk](https://www.jitbit.com/support/) para obtener este valor. 
    
    b.  En el cuadro de texto **Identificador**, escriba una dirección URL como:`https://www.jitbit.com/web-helpdesk/`

    
 


4. En la sección **Certificado de firma de SAML**, haga clic en **Certificado (Base64)** y, luego, guarde el archivo de certificado en el equipo.

    ![Configurar inicio de sesión único](./media/active-directory-saas-jitbit-helpdesk-tutorial/tutorial_jitbit-helpdesk_certificate.png) 

5. Haga clic en el botón **Guardar** .

    ![Configurar inicio de sesión único](./media/active-directory-saas-jitbit-helpdesk-tutorial/tutorial_general_400.png)

6. En la sección **Configuración de Jitbit Helpdesk**, haga clic en **Configurar Jitbit Helpdesk** para abrir la ventana **Configurar inicio de sesión**. Copie la **dirección URL de servicio de inicio de sesión único de SAML** de la sección **Referencia rápida**.

    ![Configurar inicio de sesión único](./media/active-directory-saas-jitbit-helpdesk-tutorial/tutorial_jitbit-helpdesk_configure.png) 

7. En otra ventana del explorador web, inicie sesión como administrador en el sitio de la compañía de Jitbit Helpdesk.

8. En la barra de herramientas de la parte superior, haga clic en **Administración**.
   
    ![Administración](./media/active-directory-saas-jitbit-helpdesk-tutorial/ic777681.png "Administración")

9. Haga clic en **Configuración general**.
   
    ![Usuarios, compañías y permisos](./media/active-directory-saas-jitbit-helpdesk-tutorial/ic777680.png "Usuarios, compañías y permisos")

10. En la sección **Configuración de autenticación** , realice estos pasos:
   
    ![Configuración de la autenticación](./media/active-directory-saas-jitbit-helpdesk-tutorial/ic777683.png "Configuración de la autenticación")
    
    a. Seleccione **Enable SAML 2.0 single sign on** (Habilitar inicio de sesión único de SAML 2.0) para iniciar sesión mediante inicio de sesión único (SSO) con **OneLogin**.

    b. En el cuadro de texto **EndPoint URL** (Dirección URL del punto de conexión), pegue el valor de la **dirección URL del servicio de inicio de sesión único de SAML** que ha copiado de Azure Portal.

    c. Abra el certificado codificado en **base-64** en el Bloc de notas, copie el contenido del mismo en el Portapapeles y péguelo en el cuadro de texto **Certificado X.509**.

    d. Haga clic en **Guardar cambios**.

> [!TIP]
> Ahora puede leer una versión resumida de estas instrucciones dentro de [Azure Portal](https://portal.azure.com) mientras configura la aplicación.  Después de agregar esta aplicación desde la sección **Active Directory > Aplicaciones empresariales**, simplemente haga clic en la pestaña **Inicio de sesión único** y acceda a la documentación insertada a través de la sección **Configuración** de la parte inferior. Puede leer más sobre la característica de documentación insertada aquí: [Vista previa: Administración de inicio de sesión único para aplicaciones empresariales en el nuevo Azure Portal]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>Creación de un usuario de prueba de Azure AD
El objetivo de esta sección es crear un usuario de prueba en Azure Portal llamado "Britta Simon".

![Creación de un usuario de Azure AD][100]

**Siga estos pasos para crear un usuario de prueba en Azure AD:**

1. En el panel de navegación izquierdo de **Azure Portal**, haga clic en el icono de **Azure Active Directory**.

    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-jitbit-helpdesk-tutorial/create_aaduser_01.png) 

2. Para mostrar la lista de usuarios, vaya a **Usuarios y grupos** y haga clic en **Todos los usuarios**.
    
    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-jitbit-helpdesk-tutorial/create_aaduser_02.png) 

3. Para abrir el cuadro de diálogo **Usuario**, haga clic en **Agregar** en la parte superior del cuadro de diálogo.
 
    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-jitbit-helpdesk-tutorial/create_aaduser_03.png) 

4. En la página de diálogo **Usuario**, realice los siguientes pasos:
 
    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-jitbit-helpdesk-tutorial/create_aaduser_04.png) 

    a. En el cuadro de texto **Nombre**, escriba **BrittaSimon**.

    b. En el cuadro de texto **Nombre de usuario**, escriba la **dirección de correo electrónico** de Britta Simon.

    c. Seleccione **Mostrar contraseña** y anote el valor del cuadro **Contraseña**.

    d. Haga clic en **Crear**.
 
### <a name="creating-a-jitbit-helpdesk-test-user"></a>Creación de un usuario de prueba de Jitbit Helpdesk

Para permitir que los usuarios de Azure AD inicien sesión en Jitbit Helpdesk, deben aprovisionarse en Jitbit Helpdesk.  En el caso de Jitbit Helpdesk, el aprovisionamiento es una tarea manual.

**Para aprovisionar una cuenta de usuario, realice estos pasos:**

1. Inicie sesión en su inquilino de **Jitbit Helpdesk** .

2. En el menú de la parte superior, haga clic en **Administration**(Administración).
   
    ![Administración](./media/active-directory-saas-jitbit-helpdesk-tutorial/ic777681.png "Administración")

3. Haga clic en **Usuarios, compañías y permisos**.
   
    ![Usuarios, compañías y permisos](./media/active-directory-saas-jitbit-helpdesk-tutorial/ic777682.png "Usuarios, compañías y permisos")

4. Haga clic en **Agregar usuario**.
   
    ![Agregar usuario](./media/active-directory-saas-jitbit-helpdesk-tutorial/ic777685.png "Agregar usuario")
   
5. En la sección Crear, escriba los datos de la cuenta de Azure AD que desee aprovisionar como sigue:

    ![Crear](./media/active-directory-saas-jitbit-helpdesk-tutorial/ic777686.png "Crear")
   
   a. En el cuadro de texto **Nombre de usuario**, escriba el nombre de usuario **Britta Simon** como en Azure Portal.

   b. En el cuadro de texto **Correo electrónico**, escriba el correo electrónico del usuario, en el ejemplo  **BrittaSimon@contoso.com** .

   c. En el cuadro de texto **Nombre**, escriba el nombre del usuario, en este caso **Britta**.

   d. En el cuadro de texto **Apellidos**, escriba el apellido del usuario, en este caso **Simon**.
   
   e. Haga clic en **Crear**.

>[!NOTE]
>Puede usar cualquier herramienta de creación de cuentas de usuario de Jitbit Helpdesk u otras API proporcionadas por Jitbit Helpdesk para aprovisionar cuentas de usuario de Azure AD.
> 
        

### <a name="assigning-the-azure-ad-test-user"></a>Asignación del usuario de prueba de Azure AD

En esta sección, concederá acceso a Britta Simon a Jitbit Helpdesk para que use el inicio de sesión único de Azure.

![Asignar usuario][200] 

**Para asignar a Britta Simon a Jitbit Helpdesk, realice los pasos siguientes:**

1. En Azure Portal, abra la vista de aplicaciones, vaya a la vista de directorio y vaya a **Aplicaciones empresariales**. Luego, haga clic en **Todas las aplicaciones**.

    ![Asignar usuario][201] 

2. En la lista de aplicaciones, seleccione **Jitbit Helpdesk**.

    ![Configurar inicio de sesión único](./media/active-directory-saas-jitbit-helpdesk-tutorial/tutorial_jitbit-helpdesk_app.png) 

3. En el menú de la izquierda, haga clic en **Usuarios y grupos**.

    ![Asignar usuario][202] 

4. Haga clic en el botón **Agregar**. Después, seleccione **Usuarios y grupos** en el cuadro de diálogo **Agregar asignación**.

    ![Asignar usuario][203]

5. En el cuadro de diálogo **Usuarios y grupos**, seleccione **Britta Simon** en la lista de usuarios.

6. Haga clic en el botón **Seleccionar** del cuadro de diálogo **Usuarios y grupos**.

7. Haga clic en el botón **Asignar** del cuadro de diálogo **Agregar asignación**.
    
### <a name="testing-single-sign-on"></a>Prueba del inicio de sesión único 

En esta sección, probará la configuración de inicio de sesión único de Azure AD mediante el Panel de acceso.

Al hacer clic en el icono de Jitbit Helpdesk en el panel de acceso, debería entrar en la página de inicio de sesión de la aplicación Jitbit Helpdesk.
Para más información sobre el Panel de acceso, consulte [Introducción al Panel de acceso](active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Recursos adicionales

* [Lista de tutoriales sobre cómo integrar aplicaciones SaaS con Azure Active Directory](active-directory-saas-tutorial-list.md)
* [¿Qué es el acceso a aplicaciones y el inicio de sesión único con Azure Active Directory?](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-jitbit-helpdesk-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-jitbit-helpdesk-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-jitbit-helpdesk-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-jitbit-helpdesk-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-jitbit-helpdesk-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-jitbit-helpdesk-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-jitbit-helpdesk-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-jitbit-helpdesk-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-jitbit-helpdesk-tutorial/tutorial_general_203.png

