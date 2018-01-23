---
title: "Tutorial: Integración de Azure Active Directory con Central Desktop | Microsoft Docs"
description: "Aprenda a configurar el inicio de sesión único entre Azure Active Directory y Central Desktop."
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: b805d485-93db-49b4-807a-18d446c7090e
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/08/2017
ms.author: jeedes
ms.openlocfilehash: 94c67bef7a0c6ba60fc9c7a60c79a23bf7984fb1
ms.sourcegitcommit: b5c6197f997aa6858f420302d375896360dd7ceb
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/21/2017
---
# <a name="tutorial-azure-active-directory-integration-with-central-desktop"></a>Tutorial: integración de Azure Active Directory con Central Desktop

En este tutorial, obtendrá información sobre cómo integrar Central Desktop con Azure Active Directory (Azure AD).

Integrar Central Desktop con Azure AD proporciona las siguientes ventajas:

- Puede controlar en Azure AD quién tiene acceso a Central Desktop.
- Puede permitir que los usuarios inicien sesión automáticamente en Central Desktop con sus cuentas de Azure AD.
- Puede administrar sus cuentas en una ubicación central: Azure Portal.

Para obtener más detalles sobre la integración de aplicaciones SaaS con Azure AD, consulte [¿Qué es el acceso a aplicaciones y el inicio de sesión único con Azure Active Directory?](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>requisitos previos

Para configurar la integración de Azure AD con Central Desktop, se necesitan los siguientes elementos:

- Una suscripción de Azure AD
- Una suscripción a Central Desktop con el inicio de sesión único habilitado

> [!NOTE]
> Para probar los pasos de este tutorial, no se recomienda el uso de un entorno de producción.

Para probar los pasos de este tutorial, siga estas recomendaciones:

- No use el entorno de producción, salvo que sea necesario.
- Si todavía no dispone de un entorno de evaluación gratuita de Azure AD, [obtenga una evaluación gratuita de un mes](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descripción del escenario
En este tutorial, puede probar el inicio de sesión único de Azure AD en un entorno de prueba. La situación descrita en este tutorial consta de dos bloques de creación principales:

1. Adición de Central Desktop desde la galería
2. Configuración y comprobación del inicio de sesión único de Azure AD

## <a name="add-central-desktop-from-the-gallery"></a>Agregar Central Desktop desde la galería
Para configurar la integración de Central Desktop en Azure AD, deberá agregar Central Desktop desde la galería a la lista de aplicaciones SaaS administradas.

**Para agregar Central Desktop desde la galería, realice los pasos siguientes:**

1. En el panel izquierdo de [Azure Portal](https://portal.azure.com), seleccione el icono **Azure Active Directory**. 

    ![Botón Azure Active Directory][1]

2. Vaya a **Aplicaciones empresariales**. A continuación, vaya a **Todas las aplicaciones**.

    ![Hoja Aplicaciones empresariales][2]
    
3. Para agregar nuevas aplicaciones, seleccione el botón **Nueva aplicación** en la parte superior del cuadro de diálogo.

    ![Botón Nueva aplicación][3]

4. En el cuadro de búsqueda, escriba **Central Desktop**. En el panel de resultados, seleccione **Central Desktop** y, luego, haga clic en el botón **Agregar** para agregar la aplicación.

    ![Central Desktop en la lista de resultados](./media/active-directory-saas-central-desktop-tutorial/tutorial_centraldesktop_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configuración y prueba del inicio de sesión único en Azure AD

En esta sección, configurará y probará el inicio de sesión único de Azure AD con Central Desktop con un usuario de prueba llamado "Britta Simon".

Para que el inicio de sesión único funcione, Azure AD debe saber cuál es el usuario homólogo de Central Desktop para un usuario de Azure AD. Es decir, es necesario establecer un vínculo entre un usuario de Azure AD y el usuario relacionado de Central Desktop.

En Central Desktop, asigne a **Nombre de usuario** el mismo valor definido en **nombre de usuario** en Azure AD. Ahora ya ha establecido el vínculo entre los dos usuarios.

Para configurar y probar el inicio de sesión único de Azure AD con Central Desktop, es preciso completar los siguientes bloques de creación:

1. [Configuración del inicio de sesión único de Azure AD](#configure-azure-ad-single-sign-on), para permitir que los usuarios utilicen esta característica.
2. [Creación de un usuario de prueba de Azure AD](#create-an-azure-ad-test-user), para probar el inicio de sesión único de Azure AD con Britta Simon.
3. [Creación de un usuario de prueba de Central Desktop](#create-a-central-desktop-test-user): para tener un homólogo de Britta Simon en Central Desktop que esté vinculado a su representación en Azure AD.
4. [Asignación del usuario de prueba de Azure AD](#assign-the-azure-ad-test-user), para permitir que Britta Simon use el inicio de sesión único de Azure AD.
5. [Prueba del inicio de sesión único](#test-single-sign-on) para comprobar que la configuración funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configuración del inicio de sesión único de Azure AD

En esta sección, habilitará el inicio de sesión único de Azure AD en Azure Portal y configurará el inicio de sesión único en la aplicación Central Desktop.

**Para configurar el inicio de sesión único de Azure AD con Central Desktop, siga estos pasos:**

1. En la página de integración de la aplicación **Central Desktop** de Azure Portal, haga clic en **Inicio de sesión único**.

    ![Vínculo Configurar inicio de sesión único][4]

2. En el cuadro de diálogo **Inicio de sesión único**, en la lista desplegable **Modo**, seleccione **Inicio de sesión basado en SAML** para habilitar el inicio de sesión único.
 
    ![Cuadro de diálogo Inicio de sesión único](./media/active-directory-saas-central-desktop-tutorial/tutorial_centraldesktop_samlbase.png)

3. En la sección **Dominio y direcciones URL de Central Desktop**, lleve a cabo los pasos siguientes:

    ![Información acerca del inicio de sesión único de dominio y direcciones URL de Central Desktop](./media/active-directory-saas-central-desktop-tutorial/tutorial_centraldesktop_url.png)

    a. En el cuadro **URL de inicio de sesión**, escriba una dirección URL con el siguiente patrón: `https://<companyname>.centraldesktop.com`

    b. En el cuadro **Identificador**, escriba una dirección URL con el siguiente patrón:
    | |
    |--|
    | `https://<companyname>.centraldesktop.com/saml2-metadata.php`|
    | `https://<companyname>.imeetcentral.com/saml2-metadata.php`|

    c. En el cuadro **URL de respuesta**, escriba una dirección URL con el siguiente patrón: `https://<companyname>.centraldesktop.com/saml2-assertion.php`    
     
    > [!NOTE] 
    > Estos valores no son reales. Actualice estos valores con los valores reales de Identificador, URL de respuesta y URL de inicio de sesión. Póngase en contacto con el [equipo de asistencia al cliente de Central Desktop](https://imeetcentral.com/contact-us) para obtener estos valores. 

4. En la sección **Certificado de firma de SAML**, seleccione **Certificado**. A continuación, guarde el archivo de certificado en el equipo.

    ![Vínculo de descarga del certificado](./media/active-directory-saas-central-desktop-tutorial/tutorial_centraldesktop_certificate.png) 

5. Seleccione el botón **Guardar**.

    ![Botón Guardar de configuración de inicio de sesión único](./media/active-directory-saas-central-desktop-tutorial/tutorial_general_400.png)
    
6. En la sección **Configuración de Central Desktop**, seleccione **Configurar Central Desktop** para abrir la ventana **Configurar inicio de sesión**. Copie la **URL del servicio de inicio de sesión único de SAML, el identificador de entidad de SAML y la dirección URL de cierre de sesión** de la sección **Referencia rápida**.

    ![Configuración de Central Desktop](./media/active-directory-saas-central-desktop-tutorial/tutorial_centraldesktop_configure.png) 

7. Inicie sesión en su inquilino de **Central Desktop**.

8. Vaya a **Configuración**. Seleccione **Avanzado** y, a continuación, seleccione **Inicio de sesión único**.

    ![Programa de instalación: avanzado](./media/active-directory-saas-central-desktop-tutorial/ic769563.png "Programa de instalación: avanzado")

9. En la página **Configuración de inicio de sesión único**, siga estos pasos:

    ![Configuración de inicio de sesión único ](./media/active-directory-saas-central-desktop-tutorial/ic769564.png "Configuración de inicio de sesión único")
    
    a. Seleccione **Enable SAML v2 Single Sign On**(Habilitar inicio de sesión único de SAML).
    
    b. En el cuadro **SSO URL** (URL de SSO), pegue el **Identificador de entidad de SAML** que copió de Azure Portal.
    
    c. En el cuadro **SSO Login URL** (URL de inicio de sesión de SSO), pegue la dirección **URL de servicio de inicio de sesión único de SAML** que copió de Azure Portal.
    
    d. En el cuadro **SSO Logout URL** (URL de cierre de sesión de SSO), pegue la dirección **URL de cierre de sesión** que copió de Azure Portal.

10. En la sección **Message Signature Verification Method** (Método de autenticación de firma de mensaje), realice los pasos siguientes:

    ![Método de autenticación de firma de mensaje](./media/active-directory-saas-central-desktop-tutorial/ic769565.png "Método de autenticación de firma de mensaje") a. Seleccione **Certificado**.
    
    b. En la lista **SSO Certificate** (Certificado de SSO), seleccione **RSH SHA256**.
    
    c. Abra el certificado descargado en el Bloc de notas. A continuación, copie el contenido del certificado y péguelo en el campo **SSO Certificate** (Certificado de SSO).
        
    d. Seleccione **Mostrar un vínculo a la página de inicio de sesión SAMLv2**.
    
    e. Seleccione **Actualizar**.

> [!TIP]
> Ahora puede leer una versión resumida de estas instrucciones dentro de [Azure Portal](https://portal.azure.com) mientras configura la aplicación. Después de agregar esta aplicación desde la sección **Active Directory** > **Aplicaciones empresariales**, seleccione la pestaña **Inicio de sesión único** y, a continuación, acceda a la documentación insertada a través de la sección **Configuración** de la parte inferior. Puede leer más sobre la característica de documentación insertada en la [documentación insertada de Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985).

### <a name="create-an-azure-ad-test-user"></a>Creación de un usuario de prueba de Azure AD

El objetivo de esta sección es crear un usuario de prueba en Azure Portal llamado "Britta Simon".

   ![Creación de un usuario de prueba de Azure AD][100]

**Siga estos pasos para crear un usuario de prueba en Azure AD:**

1. En el panel izquierdo de Azure Portal, seleccione el botón **Azure Active Directory**.

    ![Botón Azure Active Directory](./media/active-directory-saas-central-desktop-tutorial/create_aaduser_01.png)

2. Para mostrar la lista de usuarios, vaya a **Usuarios y grupos**. Después, seleccione **Todos los usuarios**.

    ![Vínculos "Usuarios y grupos" y "Todos los usuarios"](./media/active-directory-saas-central-desktop-tutorial/create_aaduser_02.png)

3. En la parte superior del cuadro de diálogo **Todos los usuarios**, seleccione **Agregar** para abrir el cuadro de diálogo **Usuario**.

    ![Botón Agregar](./media/active-directory-saas-central-desktop-tutorial/create_aaduser_03.png)

4. En el cuadro de diálogo **Usuario**, realice los pasos siguientes:

    ![Cuadro de diálogo Usuario](./media/active-directory-saas-central-desktop-tutorial/create_aaduser_04.png)

    a. En el cuadro **Nombre**, escriba **BrittaSimon**.

    b. En el cuadro de texto **Nombre de usuario**, escriba la dirección de correo electrónico del usuario Britta Simon.

    c. Active la casilla **Mostrar contraseña** y, después, anote el valor que se muestra en el cuadro **Contraseña**.

    d. Seleccione **Crear**.
 
### <a name="create-a-central-desktop-test-user"></a>Crear un usuario de prueba de Central Desktop

Para que los usuarios de Azure AD puedan iniciar sesión, deben aprovisionarse en la aplicación Central Desktop. En esta sección se describe cómo crear cuentas de usuario de Azure AD en Central Desktop.

> [!NOTE]
> Puede usar cualquier otra API o herramienta de creación de cuentas de usuario de Central Desktop que ofrezca Central Desktop para aprovisionar cuentas de usuario de Azure AD.

**Para el aprovisionamiento de cuentas de usuario a Central Desktop:**

1. Inicie sesión en su inquilino de Central Desktop.

2. Vaya a **Contactos**  > **Internal Members** (Miembros internos).

3. Seleccione **Add Internal Members** (Agregar miembros internos).

    ![Personas](./media/active-directory-saas-central-desktop-tutorial/ic781051.png "Personas")
    
4. En el cuadro de texto **Email Address of New Members** (Dirección de correo electrónico de nuevos miembros), escriba una cuenta de Azure AD que quiera aprovisionar y luego haga clic en **Siguiente**.

    ![Dirección de correo electrónico de nuevos miembros](./media/active-directory-saas-central-desktop-tutorial/ic781052.png "Dirección de correo electrónico de nuevos miembros")

5. Seleccione **Add Internal Members** (Agregar miembros internos).

    ![Agregar miembros internos](./media/active-directory-saas-central-desktop-tutorial/ic781053.png "Agregar miembros internos")
   
   >[!NOTE]
   >Los usuarios que se agregan reciben un correo electrónico que incluye un vínculo de confirmación para activar sus cuentas.
   
### <a name="assign-the-azure-ad-test-user"></a>Asignación del usuario de prueba de Azure AD

En esta sección, va a conceder acceso al usuario Britta Simon a Central Desktop para que use el inicio de sesión único de Azure.

![Asignación de rol de usuario][200] 

**Para asignar a Britta Simon a Central Desktop, realice los pasos siguientes:**

1. En Azure Portal, abra la vista de aplicaciones. Vaya a la vista de directorio y, a continuación, a **Aplicaciones empresariales**.

2. Seleccione **Todas las aplicaciones**.

    ![Asignar usuario][201] 

2. En la lista de aplicaciones, seleccione **Central Desktop**.

    ![Vínculo a Central Desktop en la lista de aplicaciones](./media/active-directory-saas-central-desktop-tutorial/tutorial_centraldesktop_app.png)  

3. En el menú de la izquierda, seleccione **Usuarios y grupos**.

    ![Vínculo "Usuarios y grupos"][202]

4. Seleccione el botón **Agregar**. Después, seleccione **Usuarios y grupos**, en el cuadro de diálogo **Agregar asignación**.

    ![Panel Agregar asignación][203]

5. En el cuadro de diálogo **Usuarios y grupos**, seleccione **Britta Simon** en la lista **Usuarios**.

6. En el cuadro de diálogo **Usuarios y grupos**, haga clic en el botón **Seleccionar**.

7. En el cuadro de diálogo **Agregar asignación**, haga clic en el botón **Asignar**.
    
### <a name="test-single-sign-on"></a>Prueba de inicio de sesión único

En esta sección, probará la configuración de inicio de sesión único de Azure AD mediante el panel de acceso.

Al seleccionar el icono de Central Desktop en el panel de acceso, iniciará sesión automáticamente en la aplicación Central Desktop.
Para más información sobre el panel de acceso, consulte [Introducción al panel de acceso](active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Recursos adicionales

* [Lista de tutoriales sobre cómo integrar aplicaciones SaaS con Azure Active Directory](active-directory-saas-tutorial-list.md)
* [¿Qué es el acceso a aplicaciones y el inicio de sesión único con Azure Active Directory?](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-central-desktop-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-central-desktop-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-central-desktop-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-central-desktop-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-central-desktop-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-central-desktop-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-central-desktop-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-central-desktop-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-central-desktop-tutorial/tutorial_general_203.png

