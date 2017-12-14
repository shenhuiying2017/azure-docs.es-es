---
title: "Tutorial: Integración de Azure Active Directory con LinkedIn Elevate | Microsoft Docs"
description: "Aprenda a configurar el inicio de sesión único entre Azure Active Directory y LinkedIn Elevate."
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: 2ad9941b-c574-42c3-bd0f-5d6ec68537ef
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/31/2017
ms.author: jeedes
ms.openlocfilehash: 5b46323dc487bbc714c2306ed006afffe8c1bb6a
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-linkedin-elevate"></a>Tutorial: Integración de Azure Active Directory con LinkedIn Elevate

En este tutorial, aprenderá a integrar LinkedIn Elevate con Azure Active Directory (Azure AD).

Integrar LinkedIn Elevate con Azure AD le proporciona las siguientes ventajas:

- Puede controlar en Azure AD quién tiene acceso a LinkedIn Elevate
- Puede permitir que los usuarios inicien sesión automáticamente en LinkedIn Elevate (inicio de sesión único) con sus cuentas de Azure AD.
- Puede administrar sus cuentas en una ubicación central: el Portal de administración de Azure.

Si desea obtener más información sobre la integración de aplicaciones SaaS con Azure AD, vea [Qué es el acceso a las aplicaciones y el inicio de sesión único en Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Requisitos previos

Para configurar la integración de Azure AD con LinkedIn Elevate, necesita lo siguiente:

- Una suscripción de Azure AD
- Una suscripción habilitada para el inicio de sesión único en LinkedIn Elevate

> [!NOTE]
> Para probar los pasos de este tutorial, no se recomienda el uso de un entorno de producción.

Para probar los pasos de este tutorial, debe seguir estas recomendaciones:

- No debe usar el entorno de producción, a menos que sea necesario.
- Si no dispone de un entorno de prueba de Azure AD, puede obtener una versión de prueba de un mes [aquí](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descripción del escenario
En este tutorial, puede probar el inicio de sesión único de Azure AD en un entorno de prueba. La situación descrita en este tutorial consta de dos bloques de creación principales:

1. Agregar LinkedIn Elevate desde la galería
2. Configuración y comprobación del inicio de sesión único de Azure AD

## <a name="adding-linkedin-elevate-from-the-gallery"></a>Agregar LinkedIn Elevate desde la galería
Para configurar la integración de LinkedIn Elevate en Azure AD, deberá agregar LinkedIn Elevate desde la galería a la lista de aplicaciones SaaS administradas.

**Para agregar LinkedIn Elevate desde la galería, realice los pasos siguientes:**

1. En el panel de navegación izquierdo del **[Portal de administración de Azure](https://portal.azure.com)**, haga clic en el icono de **Azure Active Directory**. 

    ![Active Directory][1]

2. Vaya a **Aplicaciones empresariales**. A continuación, vaya a **Todas las aplicaciones**.

    ![Aplicaciones][2]
    
3. Haga clic en el botón **Agregar** situado en la parte superior del cuadro de diálogo.

    ![Aplicaciones][3]

4. En el cuadro de búsqueda, escriba **LinkedIn Elevate**. En el panel de resultados, haga clic en **LinkedIn Elevate** para agregar la aplicación.

    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-linkedinElevate-tutorial/tutorial-linkedinElevate_000.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configuración y comprobación del inicio de sesión único de Azure AD
En esta sección, configurará y probará el inicio de sesión único de Azure AD con LinkedIn Elevate utilizando usuario de prueba llamado "Britta Simon".

Para que el inicio de sesión único funcione, Azure AD debe saber cuál es el usuario homólogo de LinkedIn Elevate para un usuario de Azure AD. Es decir, es necesario establecer una relación de vínculo entre un usuario de Azure AD y el usuario relacionado de LinkedIn Elevate.

Esta relación de vínculo se establece mediante la asignación del valor de **nombre de usuario** en Azure AD como valor de **Username** (Nombre de usuario) en LinkedIn Elevate.

Para configurar y probar el inicio de sesión único de Azure AD con LinkedIn Elevate, es preciso completar los siguientes bloques de creación:

1. **[Configuring Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)** : para permitir a los usuarios usar esta característica.
2. **[Creación de un usuario de prueba de Azure AD](#creating-an-azure-ad-test-user)** : para probar el inicio de sesión único de Azure AD con Britta Simon.
3. **[Creación de un usuario de prueba de LinkedIn Elevate](#creating-a-linkedin-elevate-test-user)**: para probar el inicio de sesión único de Azure AD con Britta Simon.
4. **[Asignación del usuario de prueba de Azure AD](#assigning-the-azure-ad-test-user)** : para permitir que Britta Simon use el inicio de sesión único de Azure AD.
5. **[Testing Single Sign-On](#testing-single-sign-on)** : para comprobar si funciona la configuración.

### <a name="configuring-azure-ad-single-sign-on"></a>Configuración del inicio de sesión único de Azure AD

En esta sección, habilitará el inicio de sesión único de Azure AD en el Portal de administración de Azure y configurará el inicio de sesión único en la aplicación LinkedIn Elevate.

**Para configurar el inicio de sesión único de Azure AD con LinkedIn Elevate, realice los pasos siguientes:**

1. En el Portal de administración de Azure, en la página de integración de la aplicación **LinkedIn Elevate**, haga clic en **Inicio de sesión único**.

    ![Configurar inicio de sesión único][4]

2. En el cuadro de diálogo **Inicio de sesión único**, en **Modo**, seleccione **Inicio de sesión basado en SAML** para habilitar el inicio de sesión único.
 
    ![Configurar inicio de sesión único](./media/active-directory-saas-linkedinElevate-tutorial/tutorial-linkedin_01.png)

3. En otra ventana del explorador web, inicie sesión como administrador en el inquilino LinkedIn Learning.

4. En **Account Center** (Centro de cuentas), haga clic en **Global Settings** (Configuración global) en **Settings** (Configuración). Además, seleccione **Elevate - Elevate AAD Test** en la lista desplegable.

    ![Configurar inicio de sesión único](./media/active-directory-saas-linkedinElevate-tutorial/tutorial_linkedin_admin_01.png)

5. Haga clic en **OR Click Here to load and copy individual fields from the form** (O haga clic aquí para cargar y copiar campos individuales del formulario) y copie el **Id.de entidad** y la **Assertion Consumer Access (ACS) Url** (Url de Acceso de consumidor de aserciones [ACS])

    ![Configurar inicio de sesión único](./media/active-directory-saas-linkedinElevate-tutorial/tutorial_linkedin_admin_03.png)

6. En Azure Portal, en **Dominio y direcciones URL de LinkedIn Elevate**, realice los pasos siguientes si quiere configurar SSO en modo **Iniciado por IDP**.

    ![Configurar inicio de sesión único](./media/active-directory-saas-linkedinElevate-tutorial/tutorial_linkedin_signon_01.png)

    a. En el cuadro de texto **Identificador**, escriba el **Id.de entidad** que copió de LinkedIn Portal 

    b. En el cuadro de texto **URL de respuesta**, escriba la **Assertion Consumer Access (ACS) Url** (Url de Acceso de consumidor de aserciones [ACS]) que copió de LinkedIn Portal

7. Si quiere configurar SSO en modo **SP Initiated**, haga clic en la opción de configuración Mostrar configuración avanzada de URL en la sección de configuración y configure la URL de inicio de sesión con el siguiente patrón:

    `https://www.linkedin.com/checkpoint/enterprise/login/<AccountId>?application=elevate&applicationInstanceId=<InstanceId>` 
    
    ![Configurar inicio de sesión único](./media/active-directory-saas-linkedinElevate-tutorial/tutorial_linkedin_signon_02.png) 
    
8. La aplicación LinkedIn Elevate espera las aserciones de SAML en un formato específico, lo que requiere que se agreguen asignaciones de atributos personalizados a la configuración de los atributos del token de SAML. La siguiente captura de pantalla le muestra un ejemplo de esto. El valor predeterminado de **Identificador de usuario** es **user.userprincipalname**, pero LinkedIn Elevate espera que este valor se asigne a la dirección de correo del usuario. Para ello, puede usar el atributo **user.mail** de la lista o usar el valor de atributo correspondiente en función de la configuración de su organización. 

    ![Configurar inicio de sesión único](./media/active-directory-saas-linkedinElevate-tutorial/updateusermail.png)

9. En la sección **Atributos de usuario**, haga clic en **Ver y editar todos los demás atributos de usuario** y establezca los atributos. Tiene que agregar otra notificación denominada **department** y hay que asignar el valor a **user.department**.

    | Nombre del atributo | Valor de atributo |
    | --- | --- |    
    | department| user.department |

      ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-linkedinElevate-tutorial/userattribute.png)

      a. Haga clic en Agregar atributo para abrir la página de detalles del atributo y agregue el atributo department tal como se muestra a continuación:

      ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-linkedinElevate-tutorial/adduserattribute.png)

      b. Haga clic en **Aceptar** para guardar el atributo.

      c. Cambie el nombre del atributo **emailaddress** a **email**.


10. En la sección **Certificado de firma de SAML**, haga clic en **XML de metadatos** y luego guarde el archivo XML en el equipo.

    ![Configurar inicio de sesión único](./media/active-directory-saas-linkedinElevate-tutorial/tutorial-linkedinElevate_certificate.png) 

11. Haga clic en **Guardar**.

    ![Configurar inicio de sesión único](./media/active-directory-saas-linkedinElevate-tutorial/tutorial_general_400.png)

12. Vaya a la sección **LinkedIn Admin Settings** (Configuración de administrador de LinkedIn). Cargue el archivo XML que acaba de descargar de Azure Portal haciendo clic en la opción Upload XML file (Cargar archivo XML).

    ![Configurar inicio de sesión único](./media/active-directory-saas-linkedinElevate-tutorial/tutorial_linkedin_metadata_03.png)

13. Haga clic en **On** (Activar) para habilitar SSO. El estado de SSO cambiará de **Not Connected** (No conectado) a **Connected** (Conectado)

    ![Configurar inicio de sesión único](./media/active-directory-saas-linkedinElevate-tutorial/tutorial_linkedin_admin_05.png)

### <a name="creating-an-azure-ad-test-user"></a>Creación de un usuario de prueba de Azure AD
El objetivo de esta sección es crear un usuario de prueba en el Portal de administración de Azure llamado Britta Simon.

![Creación de un usuario de Azure AD][100]

**Siga estos pasos para crear un usuario de prueba en Azure AD:**

1. En el panel de navegación izquierdo del **Portal de administración de Azure**, haga clic en el icono de **Azure Active Directory**.

    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-linkedinElevate-tutorial/create_aaduser_01.png) 

2. Vaya a **Usuarios y grupos** y haga clic en **Todos los usuarios** para mostrar la lista de usuarios.
    
    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-linkedinElevate-tutorial/create_aaduser_02.png) 

3. En la parte superior del diálogo, haga clic en **Agregar** para abrir el diálogo **Usuario**.
 
    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-linkedinElevate-tutorial/create_aaduser_03.png) 

4. En la página de diálogo **Usuario**, realice los siguientes pasos:
 
    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-linkedinElevate-tutorial/create_aaduser_04.png) 

    a. En el cuadro de texto **Nombre**, escriba **BrittaSimon**.

    b. En el cuadro de texto **Nombre de usuario**, escriba la **dirección de correo electrónico** de Britta Simon.

    c. Seleccione **Mostrar contraseña** y anote el valor del cuadro **Contraseña**.

    d. Haga clic en **Crear**. 

### <a name="creating-a-linkedin-elevate-test-user"></a>Creación de un usuario de prueba de LinkedIn Elevate

La aplicación Linked Elevate admite aprovisionamiento de usuarios Just-In-Time y, tras la autenticación, los usuarios se crearán automáticamente en la aplicación. En la página de configuración del administrador del portal de LinkedIn Elevate, invierta el conmutador **Automatically Assign licenses** (Asignar licencias automáticamente) a activo para habilitar el aprovisionamiento Just-In-Time y este también asignará una licencia al usuario.

   ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-linkedinElevate-tutorial/LinkedinUserprovswitch.png)

### <a name="assigning-the-azure-ad-test-user"></a>Asignación del usuario de prueba de Azure AD

En esta sección, habilitará a Britta Simon para que use el inicio de sesión único de Azure concediéndole acceso a LinkedIn Elevate.

![Asignar usuario][200] 

**Para asignar Britta Simon a LinkedIn Elevate, realice los pasos siguientes:**

1. En el Portal de administración de Azure, abra la vista de aplicaciones, vaya a la vista de directorio y vaya a **Aplicaciones empresariales**. A continuación, haga clic en **All applications** (Todas las aplicaciones).

    ![Asignar usuario][201] 

2. En la lista de aplicaciones, seleccione **LinkedIn Elevate**.

    ![Configurar inicio de sesión único](./media/active-directory-saas-linkedinElevate-tutorial/tutorial-linkedinElevate_0001.png) 

3. En el menú de la izquierda, haga clic en **Usuarios y grupos**.

    ![Asignar usuario][202] 

4. Haga clic en el botón **Agregar**. Después, seleccione **Usuarios y grupos** en el cuadro de diálogo **Agregar asignación**.

    ![Asignar usuario][203]

5. En el cuadro de diálogo **Usuarios y grupos**, seleccione **Britta Simon** en la lista de usuarios.

6. Haga clic en el botón **Seleccionar** del cuadro de diálogo **Usuarios y grupos**.

7. Haga clic en el botón **Asignar** del cuadro de diálogo **Agregar asignación**.
    
### <a name="testing-single-sign-on"></a>Prueba del inicio de sesión único 

En esta sección, probará la configuración de inicio de sesión único de Azure AD mediante el Panel de acceso.

Al hacer clic en el icono de LinkedIn Elevate en el Panel de acceso, debe abrirse la página de inicio de sesión único de Azure y, después de acceder, debe entrar a la aplicación LinkedIn Elevate.

## <a name="additional-resources"></a>Recursos adicionales

* [Tutorial: configuración de LinkedIn Elevate para el aprovisionamiento automático de usuarios con Azure Active Directory](active-directory-saas-linkedinelevate-provisioning-tutorial.md)
* [Lista de tutoriales sobre cómo integrar aplicaciones SaaS con Azure Active Directory](active-directory-saas-tutorial-list.md)
* [¿Qué es el acceso a aplicaciones y el inicio de sesión único con Azure Active Directory?](active-directory-appssoaccess-whatis.md)


<!--Image references-->

[1]: ./media/active-directory-saas-linkedinElevate-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-linkedinElevate-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-linkedinElevate-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-linkedinElevate-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-linkedinElevate-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-linkedinElevate-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-linkedinElevate-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-linkedinElevate-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-linkedinElevate-tutorial/tutorial_general_203.png
