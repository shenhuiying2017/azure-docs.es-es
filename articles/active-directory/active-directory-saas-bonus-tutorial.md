---
title: "Tutorial: Integración de Azure Active Directory con Bonusly | Microsoft Docs"
description: "Obtenga información sobre cómo configurar el inicio de sesión único entre Azure Active Directory y Bonusly."
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: 29fea32a-fa20-47b2-9e24-26feb47b0ae6
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/19/2017
ms.author: jeedes
ms.openlocfilehash: b69cf1c42cbc6aedb064378271e2c631b4be39f3
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-bonusly"></a>Tutorial: Integración de Azure Active Directory con Bonusly

En este tutorial, obtendrá información sobre cómo integrar Bonusly con Azure Active Directory (Azure AD).

La integración de Bonusly con Azure AD le proporciona las siguientes ventajas:

- Puede controlar en Azure AD quién tiene acceso a Bonusly.
- Puede permitir que los usuarios inicien sesión automáticamente en Bonusly (inicio de sesión único) con sus cuentas de Azure AD.
- Puede administrar sus cuentas en una ubicación central: el nuevo Azure Portal.

Si desea saber más sobre la integración de aplicaciones SaaS con Azure AD, consulte [¿Qué es el acceso a aplicaciones y el inicio de sesión único con Azure Active Directory?](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Requisitos previos

Para configurar la integración de Azure AD con Bonusly, necesita los siguientes elementos:

- Una suscripción de Azure AD
- Una suscripción habilitada para el inicio de sesión único en Bonusly

> [!NOTE]
> Para probar los pasos de este tutorial, no se recomienda el uso de un entorno de producción.

Para probar los pasos de este tutorial, debe seguir estas recomendaciones:

- No use el entorno de producción, salvo que sea necesario.
- Si no dispone de un entorno de prueba de Azure AD, puede [obtener una versión de prueba durante un mes](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descripción del escenario
En este tutorial, puede probar el inicio de sesión único de Azure AD en un entorno de prueba. La situación descrita en este tutorial consta de dos bloques de creación principales:

1. Adición de Bonusly desde la galería
2. Configuración y comprobación del inicio de sesión único de Azure AD

## <a name="adding-bonusly-from-the-gallery"></a>Adición de Bonusly desde la galería
Para configurar la integración de Bonusly en Azure AD, debe agregar Bonusly desde la galería a la lista de aplicaciones SaaS administradas.

**Para agregar Bonusly desde la galería, realice los pasos siguientes:**

1. En el panel de navegación izquierdo de **[Azure Portal](https://portal.azure.com)**, haga clic en el icono de **Azure Active Directory**. 

    ![Botón Azure Active Directory][1]

2. Vaya a **Aplicaciones empresariales**. A continuación, vaya a **Todas las aplicaciones**.

    ![Hoja Aplicaciones empresariales][2]
    
3. Para agregar una nueva aplicación, haga clic en el botón **Nueva aplicación** de la parte superior del cuadro de diálogo.

    ![Botón Nueva aplicación][3]

4. En el cuadro de búsqueda, escriba **Bonusly**, seleccione **Bonusly** en el panel de resultados y, luego, haga clic en el botón **Agregar** para agregar la aplicación.

    ![Bonusly en la lista de resultados](./media/active-directory-saas-bonus-tutorial/tutorial_bonusly_addfromgallery.png)

##  <a name="configure-and-test-azure-ad-single-sign-on"></a>Configuración y prueba del inicio de sesión único en Azure AD
En esta sección, puede configurar y probar el inicio de sesión único de Azure AD con Bonusly con un usuario de prueba llamado "Britta Simon".

Para que el inicio de sesión único funcione, Azure AD debe saber cuál es el usuario homólogo de Bonusly para un usuario de Azure AD. Es decir, es necesario establecer una relación de vínculo entre un usuario de Azure AD y el usuario relacionado de Bonusly.

Para establecer la relación de vínculo, en Bonusly, asigne el valor de **nombre de usuario** de Azure AD como valor de **nombre de usuario**.

Para configurar y probar el inicio de sesión único de Azure AD con Bonusly, es preciso completar los siguientes bloques de creación:

1. **[Configuración del inicio de sesión único de Azure AD](#configure-azure-ad-single-sign-on)**: para permitir que los usuarios utilicen esta característica.
2. **[Creación de un usuario de prueba de Azure AD](#create-an-azure-ad-test-user)**: para probar el inicio de sesión único de Azure AD con Britta Simon.
3. **[Creación de un usuario de prueba de Bonusly](#create-a-bonusly-test-user)**: para tener un homólogo de Britta Simon en Bonusly que esté vinculado a su representación en Azure AD.
4. **[Asignación del usuario de prueba de Azure AD](#assign-the-azure-ad-test-user)**: para permitir que Britta Simon use el inicio de sesión único de Azure AD.
5. **[Prueba del inicio de sesión único](#test-single-sign-on)**: para comprobar si funciona la configuración.

### <a name="configure-azure-ad-single-sign-on"></a>Configuración del inicio de sesión único de Azure AD

En esta sección, habilitará el inicio de sesión único de Azure AD en Azure Portal y configurará el inicio de sesión único en la aplicación Bonusly.

**Para configurar el inicio de sesión único de Azure AD con Bonusly, realice los pasos siguientes:**

1. En Azure Portal, en la página de integración de la aplicación **Bonusly**, haga clic en **Inicio de sesión único**.

    ![Configurar inicio de sesión único][4]

2. En el cuadro de diálogo **Inicio de sesión único**, en **Modo** seleccione **Inicio de sesión basado en SAML** para habilitar el inicio de sesión único.
 
    ![Cuadro de diálogo Inicio de sesión único](./media/active-directory-saas-bonus-tutorial/tutorial_bonusly_samlbase.png)

3. En la sección **Dominio y direcciones URL de Bonusly**, lleve a cabo los pasos siguientes:

    ![Información de dominio y direcciones URL de inicio de sesión único de Bonusly](./media/active-directory-saas-bonus-tutorial/tutorial_bonusly_url.png)

    En el cuadro de texto **URL de respuesta**, escriba una dirección URL con el siguiente patrón: `https://Bonus.ly/saml/<tenant-name>`.

    > [!NOTE] 
    > Este valor no es real. Actualícelo con la dirección URL de respuesta real. Póngase en contacto con el [equipo de soporte técnico al cliente de Bonusly](https://Bonusly/contact) para obtener estos valores.
 
4. En la sección **Certificado de firma de SAML**, copie el valor de **HUELLA DIGITAL** del certificado.

    ![Vínculo de descarga del certificado](./media/active-directory-saas-bonus-tutorial/tutorial_bonusly_certificate.png) 

5. Haga clic en el botón **Guardar** .

    ![Botón Guardar de Configuración de inicio de sesión único](./media/active-directory-saas-bonus-tutorial/tutorial_general_400.png)

6. En la sección **Configuración de Bonusly**, haga clic en **Configurar Bonusly** para abrir la ventana **Configurar inicio de sesión**. Copie los valores de **SAML Entity ID y SAML Single Sign-On Service URL** (Identificador de entidad de SAML y URL del servicio de inicio de sesión único de SAML) de la sección de **referencia rápida**.

    ![Configuración de Bonusly](./media/active-directory-saas-bonus-tutorial/tutorial_bonusly_configure.png) 

7. En una ventana del explorador diferente, inicie sesión en su inquilino de **Bonusly**.

8. En la barra de herramientas de la parte superior, haga clic en **Configuración** y seleccione **Integraciones y aplicaciones**.
   
    ![Sección Bonusly Social](./media/active-directory-saas-bonus-tutorial/ic773686.png "Bonusly")
9. En **Inicio de sesión único**, seleccione **SAML**.

10. En la página de diálogo **SAML** , realice los pasos siguientes:
   
    ![Página de diálogo Saml Bonusly](./media/active-directory-saas-bonus-tutorial/ic773687.png "Bonusly")
   
    a. En el cuadro de texto **IdP SSO Target URL** (Dirección URL de destino de SSO de IdP), pegue el valor de la **SAML Single Sign-On Service URL** (Dirección URL del servicio de inicio de sesión único de SAML) que ha copiado de Azure Portal.
   
    b. En el cuadro de texto **IdP Issuer** (Emisor de IdP), pegue el valor de **SAML Entity ID** (Identificador de entidad de SAML) que ha copiado de Azure Portal. 

    c. En el cuadro de texto **IdP Login URL** (Dirección URL de inicio de sesión de IdP), pegue el valor de la **SAML Single Sign-On Service URL** (Dirección URL del servicio de inicio de sesión único de SAML) que ha copiado de Azure Portal.

    d. Pegue el valor de **Huella digital** de Azure Portal en el cuadro de texto **Cert Fingerprint** (Huella digital de certificado).
   
11. Haga clic en **Guardar**.

> [!TIP]
> Ahora puede leer una versión resumida de estas instrucciones dentro de [Azure Portal](https://portal.azure.com) mientras configura la aplicación.  Después de agregar esta aplicación desde la sección **Active Directory > Aplicaciones empresariales**, simplemente haga clic en la pestaña **Inicio de sesión único** y acceda a la documentación insertada a través de la sección **Configuración** de la parte inferior. Puede leer más sobre la característica de documentación insertada aquí: [Vista previa: Administración de inicio de sesión único para aplicaciones empresariales en el nuevo Azure Portal]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="create-an-azure-ad-test-user"></a>Creación de un usuario de prueba de Azure AD
El objetivo de esta sección es crear un usuario de prueba en Azure Portal llamado "Britta Simon".

![Creación de un usuario de prueba de Azure AD][100]

**Siga estos pasos para crear un usuario de prueba en Azure AD:**

1. En el panel de navegación izquierdo de **Azure Portal**, haga clic en el icono de **Azure Active Directory**.

    ![Botón Azure Active Directory](./media/active-directory-saas-bonus-tutorial/create_aaduser_01.png) 

2. Para mostrar la lista de usuarios, vaya a **Usuarios y grupos** y haga clic en **Todos los usuarios**.
    
    ![Vínculos "Usuarios y grupos" y "Todos los usuarios"](./media/active-directory-saas-bonus-tutorial/create_aaduser_02.png) 

3. Para abrir el cuadro de diálogo **Usuario**, haga clic en **Agregar** en la parte superior del cuadro de diálogo.
 
    ![Botón Agregar](./media/active-directory-saas-bonus-tutorial/create_aaduser_03.png) 

4. En la página de diálogo **Usuario**, realice los siguientes pasos:
 
    ![Cuadro de diálogo Usuario](./media/active-directory-saas-bonus-tutorial/create_aaduser_04.png) 

    a. En el cuadro de texto **Nombre**, escriba **BrittaSimon**.

    b. En el cuadro de texto **Nombre de usuario**, escriba la **dirección de correo electrónico** de Britta Simon.

    c. Seleccione **Mostrar contraseña** y anote el valor del cuadro **Contraseña**.

    d. Haga clic en **Crear**.
 
### <a name="create-a-bonusly-test-user"></a>Creación de un usuario de prueba de Bonusly

Para permitir que los usuarios de Azure AD inicien sesión en Bonusly, tienen que aprovisionarse en Bonusly. En el caso de Bonusly, el aprovisionamiento es una tarea manual.

>[!NOTE]
>Puede usar cualquier otra API o herramienta de creación de cuentas de usuario de Bonusly que proporcione Bonusly para aprovisionar cuentas de usuario de Azure Active Directory.
>  

**Siga estos pasos para configurar el aprovisionamiento de usuario:**

1. En una ventana de explorador web diferente, inicie sesión en su inquilino de Bonusly.

2. Haga clic en **Configuración**.
 
    ![Configuración](./media/active-directory-saas-bonus-tutorial/ic781041.png "Configuración")

3. Haga clic en la pestaña **Usuarios y bonificaciones** .
   
    ![Usuarios y bonificaciones](./media/active-directory-saas-bonus-tutorial/ic781042.png "Usuarios y bonificaciones")

4. Haga clic en **Administrar usuarios**.
   
    ![Administración de usuarios](./media/active-directory-saas-bonus-tutorial/ic781043.png "Administración de usuarios")

5. Haga clic en **Agregar usuario**.
   
    ![Agregar usuario](./media/active-directory-saas-bonus-tutorial/ic781044.png "Agregar usuario")

6. En el cuadro de diálogo **Agregar usuario** , realice los pasos siguientes:
   
    ![Agregar usuario](./media/active-directory-saas-bonus-tutorial/ic781045.png "Agregar usuario")  

    a. En el cuadro de texto **Nombre**, escriba el nombre de usuario, en este caso **Britta**.

    b. En el cuadro de texto **Apellidos**, escriba el apellido del usuario, en este caso **Simon**.
 
    c. En el cuadro de texto **E-mail** (Correo electrónico), escriba el correo electrónico del usuario con el siguiente formato **brittasimon@contoso.com**.

    d. Haga clic en **Guardar**.
   
     >[!NOTE]
     >El titular de la cuenta de Azure AD recibirá un mensaje de correo electrónico que incluye un vínculo para confirmar la cuenta antes de que se active.
     >  

### <a name="assign-the-azure-ad-test-user"></a>Asignación del usuario de prueba de Azure AD

En esta sección, habilitará a Britta Simon para que use el inicio de sesión único de Azure concediéndole acceso a Bonusly.

![Asignación del rol de usuario][200] 

**Para asignar Britta Simon a Bonusly, realice los pasos siguientes:**

1. En Azure Portal, abra la vista de aplicaciones, vaya a la vista de directorio y vaya a **Aplicaciones empresariales**. Luego, haga clic en **Todas las aplicaciones**.

    ![Asignar usuario][201] 

2. En la lista de aplicaciones, seleccione **Bonusly**.

    ![Vínculo a Bonusly en la lista de aplicaciones](./media/active-directory-saas-bonus-tutorial/tutorial_bonusly_app.png) 

3. En el menú de la izquierda, haga clic en **Usuarios y grupos**.

    ![Vínculo "Usuarios y grupos"][202] 

4. Haga clic en el botón **Agregar**. Después, seleccione **Usuarios y grupos** en el cuadro de diálogo **Agregar asignación**.

    ![Panel Agregar asignación][203]

5. En el cuadro de diálogo **Usuarios y grupos**, seleccione **Britta Simon** en la lista de usuarios.

6. Haga clic en el botón **Seleccionar** del cuadro de diálogo **Usuarios y grupos**.

7. Haga clic en el botón **Asignar** del cuadro de diálogo **Agregar asignación**.
    
### <a name="test-single-sign-on"></a>Prueba de inicio de sesión único

El objetivo de esta sección es probar la configuración del inicio de sesión único de Azure AD mediante el panel de acceso.

Al hacer clic en el icono de Bonusly en el panel de acceso, debería iniciar sesión automáticamente en su aplicación Bonusly.

## <a name="additional-resources"></a>Recursos adicionales

* [Lista de tutoriales sobre cómo integrar aplicaciones SaaS con Azure Active Directory](active-directory-saas-tutorial-list.md)
* [¿Qué es el acceso a aplicaciones y el inicio de sesión único con Azure Active Directory?](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-bonus-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-bonus-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-bonus-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-bonus-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-bonus-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-bonus-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-bonus-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-bonus-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-bonus-tutorial/tutorial_general_203.png

