---
title: "Tutorial: Integración de Azure Active Directory con OpsGenie | Microsoft Docs"
description: "Aprenda a configurar el inicio de sesión único entre Azure Active Directory y OpsGenie."
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: 41b59b22-a61d-4fe6-ab0d-6c3991d1375f
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/28/2017
ms.author: jeedes
ms.openlocfilehash: b0d8fa13c13ad8d4a85cb482bcd7e440006f0437
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-opsgenie"></a>Tutorial: Integración de Azure Active Directory con OpsGenie

En este tutorial, aprenderá a integrar Pluralsight con Azure Active Directory (Azure AD).

La integración de OpsGenie con Azure AD le proporciona las siguientes ventajas:

- Puede controlar en Azure AD quién tiene acceso a OpsGenie.
- Puede permitir que los usuarios inicien sesión automáticamente en OpsGenie (inicio de sesión único) con sus cuentas de Azure AD.
- Puede administrar sus cuentas en una ubicación central: el nuevo Azure Portal.

Si desea saber más sobre la integración de aplicaciones SaaS con Azure AD, consulte [¿Qué es el acceso a aplicaciones y el inicio de sesión único con Azure Active Directory?](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Requisitos previos

Para configurar la integración de Azure AD con OpsGenie, necesita los siguientes elementos:

- Una suscripción de Azure AD
- Una suscripción habilitada para inicio de sesión único en Pluralsight

> [!NOTE]
> Para probar los pasos de este tutorial, no se recomienda el uso de un entorno de producción.

Para probar los pasos de este tutorial, debe seguir estas recomendaciones:

- No use el entorno de producción, salvo que sea necesario.
- Si no dispone de un entorno de prueba de Azure AD, puede obtener una versión de prueba de un mes [aquí](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descripción del escenario
En este tutorial, puede probar el inicio de sesión único de Azure AD en un entorno de prueba. La situación descrita en este tutorial consta de dos bloques de creación principales:

1. Adición de OpsGenie desde la galería
2. Configuración y comprobación del inicio de sesión único de Azure AD

## <a name="adding-opsgenie-from-the-gallery"></a>Adición de OpsGenie desde la galería
Para configurar la integración de OpsGenie en Azure AD, deberá agregar OpsGenie desde la galería a la lista de aplicaciones SaaS administradas.

**Para agregar OpsGenie desde la galería, realice los pasos siguientes:**

1. En el panel de navegación izquierdo de **[Azure Portal](https://portal.azure.com)**, haga clic en el icono de **Azure Active Directory**. 

    ![Active Directory][1]

2. Vaya a **Aplicaciones empresariales**. A continuación, vaya a **Todas las aplicaciones**.

    ![Aplicaciones][2]
    
3. Para agregar una nueva aplicación, haga clic en el botón **Nueva aplicación** de la parte superior del cuadro de diálogo.

    ![Aplicaciones][3]

4. En el cuadro de búsqueda, escriba **OpsGenie**.

    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-opsgenie-tutorial/tutorial_opsgenie_search.png)

5. En el panel de resultados, seleccione **OpsGenie** y luego haga clic en el botón **Agregar** para agregar la aplicación.

    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-opsgenie-tutorial/tutorial_opsgenie_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configuración y comprobación del inicio de sesión único de Azure AD
En esta sección, configurará y probará el inicio de sesión único de Azure AD con OpsGenie con un usuario de prueba llamado "Britta Simon".

Para que el inicio de sesión único funcione, Azure AD debe saber cuál es el usuario homólogo de OpsGenie para un usuario de Azure AD. Es decir, es necesario establecer una relación de vínculo entre un usuario de Azure AD y el usuario relacionado de OpsGenie.

Para establecer la relación de vínculo, asigne el valor de **nombre de usuario** de Azure AD como valor de **nombre de usuario** de OpsGenie.

Para configurar y probar el inicio de sesión único de Azure AD con OpsGenie, es preciso completar los siguientes bloques de creación:

1. **[Configuring Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)** : para permitir a los usuarios usar esta característica.
2. **[Creación de un usuario de prueba de Azure AD](#creating-an-azure-ad-test-user)** : para probar el inicio de sesión único de Azure AD con Britta Simon.
3. **[Creación de un usuario de prueba de OpsGenie](#creating-a-opsgenie-test-user)**: el objetivo es tener un homólogo de Britta Simon en OpsGenie que esté vinculado a la representación del usuario en Azure AD.
4. **[Asignación del usuario de prueba de Azure AD](#assigning-the-azure-ad-test-user)** : para permitir que Britta Simon use el inicio de sesión único de Azure AD.
5. **[Testing Single Sign-On](#testing-single-sign-on)** : para comprobar si funciona la configuración.

### <a name="configuring-azure-ad-single-sign-on"></a>Configuración del inicio de sesión único de Azure AD

En esta sección, habilitará el inicio de sesión único de Azure AD en el portal de Azure y lo configurará en la aplicación OpsGenie.

**Para configurar el inicio de sesión único de Azure AD con OpsGenie, realice los pasos siguientes:**

1. En la página de integración de la aplicación **OpsGenie** de Azure Portal, haga clic en **Inicio de sesión único**.

    ![Configurar inicio de sesión único][4]

2. En el cuadro de diálogo **Inicio de sesión único**, en **Modo** seleccione **Inicio de sesión basado en SAML** para habilitar el inicio de sesión único.
 
    ![Configurar inicio de sesión único](./media/active-directory-saas-opsgenie-tutorial/tutorial_opsgenie_samlbase.png)

3. En la sección **Dominio y direcciones URL de OpsGenie**, lleve a cabo los pasos siguientes:

    ![Configurar inicio de sesión único](./media/active-directory-saas-opsgenie-tutorial/tutorial_opsgenie_url.png)

    En el cuadro de texto **URL de inicio de sesión**, escriba la dirección URL: `https://app.opsgenie.com/auth/login`

4. En la sección **Certificado de firma de SAML**, haga clic en **Certificado (Base64)** y, luego, guarde el archivo de certificado en el equipo.

    ![Configurar inicio de sesión único](./media/active-directory-saas-opsgenie-tutorial/tutorial_opsgenie_certificate.png) 

5. Haga clic en el botón **Guardar** .

    ![Configurar inicio de sesión único](./media/active-directory-saas-opsgenie-tutorial/tutorial_general_400.png)

6. En la sección **Configuración de OpsGenie**, haga clic en **Configurar OpsGenie** para abrir la ventana **Configurar inicio de sesión**. Copie la **URL del servicio de inicio de sesión único de SAML, el identificador de entidad de SAML y la dirección URL de cierre de sesión** de la sección **Referencia rápida**.

    ![Configurar inicio de sesión único](./media/active-directory-saas-opsgenie-tutorial/tutorial_opsgenie_configure.png) 

7. Abra otra instancia del explorador y después inicie sesión en OpsGenie como administrador.

8. Haga clic en **Configuración** y después en la pestaña **Inicio de sesión único**.
   
    ![Inicio de sesión único de OpsGenie](./media/active-directory-saas-opsgenie-tutorial/tutorial_opsgenie_06.png)

9. Para habilitar SSO, seleccione **Habilitado**.
   
    ![Configuración de OpsGenie](./media/active-directory-saas-opsgenie-tutorial/tutorial_opsgenie_07.png) 

10. En la sección **Proveedor**, haga clic en la pestaña **Azure Active Directory**.
   
    ![Configuración de OpsGenie](./media/active-directory-saas-opsgenie-tutorial/tutorial_opsgenie_08.png) 

11. En la página de diálogo de Azure Active Directory, realice los siguientes pasos:
   
    ![Configuración de OpsGenie](./media/active-directory-saas-opsgenie-tutorial/tutorial_opsgenie_09.png)
    
    a. Pegue la **dirección URL del servicio de inicio de sesión** que ha copiado de Azure Portal en el cuadro de texto **SAML 2.0 Endpoint** (Punto de conexión de SAML 2.0).
    
    b. Abra el certificado codificado en base 64 descargado en el Bloc de notas, copie su contenido en el Portapapeles y, a continuación, péguelo en el cuadro de texto **Certificado X.500**.
    
    c. Haga clic en **Guardar cambios**.

> [!TIP]
> Ahora puede leer una versión resumida de estas instrucciones dentro de [Azure Portal](https://portal.azure.com) mientras configura la aplicación.  Después de agregar esta aplicación desde la sección **Active Directory > Aplicaciones empresariales**, simplemente haga clic en la pestaña **Inicio de sesión único** y acceda a la documentación insertada a través de la sección **Configuración** de la parte inferior. Puede leer más sobre la característica de documentación insertada aquí: [Vista previa: Administración de inicio de sesión único para aplicaciones empresariales en el nuevo Azure Portal]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>Creación de un usuario de prueba de Azure AD
El objetivo de esta sección es crear un usuario de prueba en Azure Portal llamado "Britta Simon".

![Creación de un usuario de Azure AD][100]

**Siga estos pasos para crear un usuario de prueba en Azure AD:**

1. En el panel de navegación izquierdo de **Azure Portal**, haga clic en el icono de **Azure Active Directory**.

    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-opsgenie-tutorial/create_aaduser_01.png) 

2. Para mostrar la lista de usuarios, vaya a **Usuarios y grupos** y haga clic en **Todos los usuarios**.
    
    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-opsgenie-tutorial/create_aaduser_02.png) 

3. Para abrir el cuadro de diálogo **Usuario**, haga clic en **Agregar** en la parte superior del cuadro de diálogo.
 
    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-opsgenie-tutorial/create_aaduser_03.png) 

4. En la página de diálogo **Usuario**, realice los siguientes pasos:
 
    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-opsgenie-tutorial/create_aaduser_04.png) 

    a. En el cuadro de texto **Nombre**, escriba **BrittaSimon**.

    b. En el cuadro de texto **Nombre de usuario**, escriba la **dirección de correo electrónico** de Britta Simon.

    c. Seleccione **Mostrar contraseña** y anote el valor del cuadro **Contraseña**.

    d. Haga clic en **Crear**.
 
### <a name="creating-a-opsgenie-test-user"></a>Creación de un usuario de prueba de OpsGenie

El objetivo de esta sección es crear un usuario de prueba llamado Britta Simon en OpsGenie. 

1. En una ventana del explorador web, inicie sesión en el inquilino de OpsGenie como administrador.

2. Vaya a la lista Usuarios haciendo clic en **Usuario** en el panel izquierdo.
   
   ![Configuración de OpsGenie](./media/active-directory-saas-opsgenie-tutorial/tutorial_opsgenie_10.png) 

3. Haga clic en **Agregar usuario**.

4. En el cuadro de diálogo **Agregar usuario** , realice los pasos siguientes:
   
   ![Configuración de OpsGenie](./media/active-directory-saas-opsgenie-tutorial/tutorial_opsgenie_11.png)
   
   a. En el cuadro de texto **Email** (Correo electrónico), escriba la dirección de correo electrónico de BrittaSimon en Azure Active Directory.
   
   b. En el cuadro de texto **Nombre completo**, escriba **Britta Simon**.
   
   c. Haga clic en **Guardar**. 

>[!NOTE]
>Britta recibirá un correo electrónico con instrucciones sobre cómo configurar su perfil.

### <a name="assigning-the-azure-ad-test-user"></a>Asignación del usuario de prueba de Azure AD

En esta sección, habilitará a Britta Simon para que use el inicio de sesión único de Azure concediéndole acceso a OpsGenie.

![Asignar usuario][200] 

**Para asignar a Britta Simon a OpsGenie, realice los pasos siguientes:**

1. En Azure Portal, abra la vista de aplicaciones, vaya a la vista de directorio y vaya a **Aplicaciones empresariales**. Luego, haga clic en **Todas las aplicaciones**.

    ![Asignar usuario][201] 

2. En la lista de aplicaciones, seleccione **OpsGenie**.

    ![Configurar inicio de sesión único](./media/active-directory-saas-opsgenie-tutorial/tutorial_opsgenie_app.png) 

3. En el menú de la izquierda, haga clic en **Usuarios y grupos**.

    ![Asignar usuario][202] 

4. Haga clic en el botón **Agregar**. Después, seleccione **Usuarios y grupos** en el cuadro de diálogo **Agregar asignación**.

    ![Asignar usuario][203]

5. En el cuadro de diálogo **Usuarios y grupos**, seleccione **Britta Simon** en la lista de usuarios.

6. Haga clic en el botón **Seleccionar** del cuadro de diálogo **Usuarios y grupos**.

7. Haga clic en el botón **Asignar** del cuadro de diálogo **Agregar asignación**.
    
### <a name="testing-single-sign-on"></a>Prueba del inicio de sesión único 

El objetivo de esta sección es probar la configuración del inicio de sesión único de Azure AD mediante el panel de acceso.

Al hacer clic en el icono de OpsGenie en el panel de acceso, debería iniciar sesión automáticamente en su aplicación OpsGenie.

## <a name="additional-resources"></a>Recursos adicionales

* [Lista de tutoriales sobre cómo integrar aplicaciones SaaS con Azure Active Directory](active-directory-saas-tutorial-list.md)
* [¿Qué es el acceso a aplicaciones y el inicio de sesión único con Azure Active Directory?](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-opsgenie-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-opsgenie-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-opsgenie-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-opsgenie-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-opsgenie-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-opsgenie-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-opsgenie-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-opsgenie-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-opsgenie-tutorial/tutorial_general_203.png

