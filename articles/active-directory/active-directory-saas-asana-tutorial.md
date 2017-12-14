---
title: "Tutorial: Integración de Azure Active Directory con Asana | Microsoft Docs"
description: "Aprenda a configurar el inicio de sesión único entre Azure Active Directory y Asana."
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: 837e38fe-8f55-475c-87f4-6394dc1fee2b
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/01/2017
ms.author: jeedes
ms.openlocfilehash: 8058dcd397e5f81f4a8c8cd1845353fd789f604b
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-asana"></a>Tutorial: Integración de Azure Active Directory con Asana

En este tutorial, obtendrá información sobre cómo integrar Asana con Azure Active Directory (Azure AD).

Integrar Asana con Azure AD le proporciona las siguientes ventajas:

- Puede controlar en Azure AD quién tiene acceso a Asana.
- Puede permitir que los usuarios inicien sesión automáticamente en Asana (inicio de sesión único) con sus cuentas de Azure AD.
- Puede administrar las cuentas en una sola ubicación central: Azure Portal.

Si desea saber más sobre la integración de aplicaciones SaaS con Azure AD, consulte [¿Qué es el acceso a aplicaciones y el inicio de sesión único con Azure Active Directory?](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Requisitos previos

Para configurar la integración de Azure AD con Asana, se necesitan los siguientes elementos:

- Una suscripción de Azure AD
- Una suscripción habilitada para el inicio de sesión único en Asana

> [!NOTE]
> Para probar los pasos de este tutorial, no se recomienda el uso de un entorno de producción.

Para probar los pasos de este tutorial, debe seguir estas recomendaciones:

- No use el entorno de producción, salvo que sea necesario.
- Si no dispone de un entorno de prueba de Azure AD, puede [obtener una versión de prueba durante un mes](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descripción del escenario
En este tutorial, puede probar el inicio de sesión único de Azure AD en un entorno de prueba. La situación descrita en este tutorial consta de dos bloques de creación principales:

1. Adición de Asana desde la galería
2. Configuración y comprobación del inicio de sesión único de Azure AD

## <a name="adding-asana-from-the-gallery"></a>Adición de Asana desde la galería
Para configurar la integración de Asana en Azure AD, es preciso agregar Asana desde la galería a la lista de aplicaciones SaaS administradas.

**Para agregar Asana desde la galería, realice los pasos siguientes:**

1. En el panel de navegación izquierdo de **[Azure Portal](https://portal.azure.com)**, haga clic en el icono de **Azure Active Directory**. 

    ![Botón Azure Active Directory][1]

2. Vaya a **Aplicaciones empresariales**. A continuación, vaya a **Todas las aplicaciones**.

    ![Hoja Aplicaciones empresariales][2]
    
3. Para agregar una nueva aplicación, haga clic en el botón **Nueva aplicación** de la parte superior del cuadro de diálogo.

    ![Botón Nueva aplicación][3]

4. En el cuadro de búsqueda, escriba **Asana**, seleccione **Asana** en el panel de resultados y haga clic en el botón **Agregar** para agregar la aplicación.

    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-asana-tutorial/tutorial_asana_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configuración y prueba del inicio de sesión único en Azure AD

En esta sección, podrá configurar y probar el inicio de sesión único de Azure AD con Asana con un usuario de prueba denominado "Britta Simon".

Para que el inicio de sesión único funcione, Azure AD debe saber cuál es el usuario homólogo de Asana para un usuario de Azure AD. Es decir, es necesario establecer una relación de vínculo entre un usuario de Azure AD y el usuario relacionado de Asana.

Para establecer la relación de vínculo, en Asana, asigne el valor de **nombre de usuario** de Azure AD como valor de **Nombre de usuario**.

Para configurar y probar el inicio de sesión único de Azure AD con Asana, es preciso completar los siguientes bloques de creación:

1. **[Configuración del inicio de sesión único de Azure AD](#configure-azure-ad-single-sign-on)**: para permitir que los usuarios utilicen esta característica.
2. **[Creación de un usuario de prueba de Azure AD](#create-an-azure-ad-test-user)**: para probar el inicio de sesión único de Azure AD con Britta Simon.
3. **[Creación de un usuario de prueba de Asana](#create-an-asana-test-user)**: para tener un homólogo de Britta Simon en Asana que esté vinculado a la representación de ella en Azure AD.
4. **[Asignación del usuario de prueba de Azure AD](#assign-the-azure-ad-test-user)**: para permitir que Britta Simon use el inicio de sesión único de Azure AD.
5. **[Prueba del inicio de sesión único](#test-single-sign-on)**: para comprobar si la configuración funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configuración del inicio de sesión único de Azure AD

En esta sección, habilitará el inicio de sesión único de Azure AD en Azure Portal y lo configurará en la aplicación Asana.

**Para configurar el inicio de sesión único de Azure AD con Asana, realice los pasos siguientes:**

1. En Azure Portal, en la página de integración de la aplicación **Asana**, haga clic en **Inicio de sesión único**.

    ![Configurar inicio de sesión único][4]

2. En el cuadro de diálogo **Inicio de sesión único**, en **Modo** seleccione **Inicio de sesión basado en SAML** para habilitar el inicio de sesión único.
 
    ![Cuadro de diálogo Inicio de sesión único](./media/active-directory-saas-asana-tutorial/tutorial_asana_samlbase.png)

3. En la sección **Dominio y direcciones URL de Asana**, lleve a cabo los pasos siguientes:

    ![Información de dominio y direcciones URL de inicio de sesión único de Asana](./media/active-directory-saas-asana-tutorial/tutorial_asana_url.png)

    a. En el cuadro de texto **URL de inicio de sesión**, escriba la dirección URL: `https://app.asana.com/`.

    b. En el cuadro de texto **Identificador**, escriba el valor: `https://app.asana.com/`
 
4. En la sección **Certificado de firma de SAML**, haga clic en **Certificado (Base64)** y, luego, guarde el archivo de certificado en el equipo.

    ![Vínculo de descarga del certificado](./media/active-directory-saas-asana-tutorial/tutorial_asana_certificate.png)
    
5. Haga clic en el botón **Guardar** .

    ![Botón Guardar de Configuración de inicio de sesión único](./media/active-directory-saas-asana-tutorial/tutorial_general_400.png)

6. En la sección **Configuración de Asana**, haga clic en **Configurar Asana** para abrir la ventana **Configurar inicio de sesión**. Copie la **dirección URL de servicio de inicio de sesión único de SAML** de la sección **Referencia rápida**.

    ![Configuración de Asana](./media/active-directory-saas-asana-tutorial/tutorial_asana_configure.png) 

7. En una ventana de explorador diferente, inicie sesión en su aplicación de Asana como administrador. Para configurar SSO en Asana, acceda a la configuración del área de trabajo haciendo clic en el nombre del área de trabajo en la esquina superior derecha de la pantalla. Después, haga clic en la **configuración del \<nombre del área de trabajo\>**. 
   
    ![Configuración de SSO de Asana](./media/active-directory-saas-asana-tutorial/tutorial_asana_09.png)

8. En la ventana **Organization settings** (Configuración de la organización), haga clic en **Administration** (Administración). Después, haga clic en **Members must log in via SAML** (Los miembros deben iniciar sesión mediante SAML) para habilitar la configuración de SSO. Lleve a cabo los siguiente pasos:
   
    ![Definición de la configuración de la organización de inicio de sesión único](./media/active-directory-saas-asana-tutorial/tutorial_asana_10.png)  

     a. En el cuadro de texto **Sign-in page URL** (Dirección URL de la página de inicio de sesión), pegue la **SAML Single Sign-On Service URL** (Dirección URL del servicio de inicio de sesión único de SAML).

     b. Haga clic con el botón derecho en el certificado descargado de Azure Portal y después abra el archivo de certificado con el Bloc de notas o el editor de texto que prefiera. Copie el contenido entre el título inicial y final del certificado y péguelo en el cuadro de texto **Certificado X.509**.

9. Haga clic en **Guardar**. Vaya a la [guía de Asana para configurar el SSO](https://asana.com/guide/help/premium/authentication#gl-saml) si necesita más ayuda.

> [!TIP]
> Ahora puede leer una versión concisa de estas instrucciones en [Azure Portal](https://portal.azure.com) mientras configura la aplicación.  Después de agregar esta aplicación desde la sección **Active Directory > Aplicaciones empresariales**, simplemente haga clic en la pestaña **Inicio de sesión único** y acceda a la documentación insertada a través de la sección **Configuración** de la parte inferior. Puede leer más sobre la característica de documentación insertada aquí: [Vista previa: Administración de inicio de sesión único para aplicaciones empresariales en el nuevo Azure Portal]( https://go.microsoft.com/fwlink/?linkid=845985)

### <a name="create-an-azure-ad-test-user"></a>Creación de un usuario de prueba de Azure AD

El objetivo de esta sección es crear un usuario de prueba en Azure Portal llamado "Britta Simon".

![Creación de un usuario de prueba de Azure AD][100]

**Siga estos pasos para crear un usuario de prueba en Azure AD:**

1. En el panel de navegación izquierdo de **Azure Portal**, haga clic en el icono de **Azure Active Directory**.

    ![Botón Azure Active Directory](./media/active-directory-saas-asana-tutorial/create_aaduser_01.png) 

2. Para mostrar la lista de usuarios, vaya a **Usuarios y grupos** y haga clic en **Todos los usuarios**.
    
    ![Vínculos "Usuarios y grupos" y "Todos los usuarios"](./media/active-directory-saas-asana-tutorial/create_aaduser_02.png) 

3. Para abrir el cuadro de diálogo **Usuario**, haga clic en **Agregar** en la parte superior del cuadro de diálogo.
 
    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-asana-tutorial/create_aaduser_03.png) 

4. En la página de diálogo **Usuario**, realice los siguientes pasos:
 
    ![Botón Agregar](./media/active-directory-saas-asana-tutorial/create_aaduser_04.png) 

    a. En el cuadro de texto **Nombre**, escriba **BrittaSimon**.

    b. En el cuadro de texto **Nombre de usuario**, escriba la **dirección de correo electrónico** de Britta Simon.

    c. Seleccione **Mostrar contraseña** y anote el valor del cuadro **Contraseña**.

    d. Haga clic en **Crear**.
 
### <a name="create-an-asana-test-user"></a>Creación de un usuario de prueba de Asana

En esta sección, creará un usuario llamado Britta Simon en Asana.

1. En **Asana**, vaya a la sección **Teams** (Equipos) en el panel izquierdo. Haga clic en el botón de signo más. 
   
    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-asana-tutorial/tutorial_asana_12.png) 

2. Escriba el correo electrónico britta.simon@contoso.com en el cuadro de texto y, después, seleccione **Invitar**.

3. Haga clic en **Enviar invitación**. El nuevo usuario recibirá un correo electrónico en su cuenta de correo electrónico. Tendrá que crear y validar la cuenta.

### <a name="assign-the-azure-ad-test-user"></a>Asignación del usuario de prueba de Azure AD

En esta sección, habilitará a Britta Simon para que use el inicio de sesión único de Azure concediéndole acceso a Asana.

![Asignación del rol de usuario][200]

**Para asignar Britta Simon a Asana, realice los pasos siguientes:**

1. En Azure Portal, abra la vista de aplicaciones, navegue a la vista de directorio y vaya a **Aplicaciones empresariales**. Luego haga clic en **Todas las aplicaciones**.

    ![Asignar usuario][201] 

2. En la lista de aplicaciones, seleccione **Asana**.

    ![Vínculo a Asana en la lista de aplicaciones](./media/active-directory-saas-asana-tutorial/tutorial_asana_app.png) 

3. En el menú de la izquierda, haga clic en **Usuarios y grupos**.

    ![Vínculo "Usuarios y grupos"][202]

4. Haga clic en el botón **Agregar**. Después, seleccione **Usuarios y grupos** en el cuadro de diálogo **Agregar asignación**.

    ![Panel Agregar asignación][203]

5. En el cuadro de diálogo **Usuarios y grupos**, seleccione **Britta Simon** en la lista de usuarios.

6. Haga clic en el botón **Seleccionar** del cuadro de diálogo **Usuarios y grupos**.

7. Haga clic en el botón **Asignar** del cuadro de diálogo **Agregar asignación**.
    
### <a name="test-single-sign-on"></a>Prueba de inicio de sesión único

El objetivo de esta sección es probar la configuración del inicio de sesión único de Azure AD.

Vaya a la página de inicio de sesión de Asana. En el cuadro de texto Email address (Dirección de correo electrónico), escriba la dirección de correo electrónico britta.simon@contoso.com. Deje el cuadro de texto de contraseña en blanco y, después, haga clic en **Iniciar sesión**. Se le redirigirá a la página de inicio de sesión de Azure AD. Complete sus credenciales de Azure AD. Ya ha iniciado sesión en Asana.

## <a name="additional-resources"></a>Recursos adicionales

* [Lista de tutoriales sobre cómo integrar aplicaciones SaaS con Azure Active Directory](active-directory-saas-tutorial-list.md)
* [¿Qué es el acceso a aplicaciones y el inicio de sesión único con Azure Active Directory?](active-directory-appssoaccess-whatis.md)


<!--Image references-->

[1]: ./media/active-directory-saas-asana-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-asana-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-asana-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-asana-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-asana-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-asana-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-asana-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-asana-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-asana-tutorial/tutorial_general_203.png
[10]: ./media/active-directory-saas-asana-tutorial/tutorial_general_060.png
[11]: ./media/active-directory-saas-asana-tutorial/tutorial_general_070.png
