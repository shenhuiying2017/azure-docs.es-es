---
title: "Tutorial: Integración de Azure Active Directory con etouches | Microsoft Docs"
description: "Aprenda a configurar el inicio de sesión único entre Azure Active Directory y etouches."
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: 76cccaa8-859c-4c16-9d1d-8a6496fc7520
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/19/2017
ms.author: jeedes
ms.openlocfilehash: bc111d6805af9e4f86ab91b6673a0d52dca78223
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-etouches"></a>Tutorial: Integración de Azure Active Directory con etouches

En este tutorial, obtendrá información sobre cómo integrar etouches con Azure Active Directory (Azure AD).

La integración de etouches con Azure AD proporciona las siguientes ventajas:

- Puede controlar en Azure AD quién tiene acceso a etouches.
- Puede permitir que los usuarios inicien sesión automáticamente en etouches (inicio de sesión único) con sus cuentas de Azure AD.
- Puede administrar sus cuentas en una ubicación central: el nuevo Azure Portal.

Si desea saber más sobre la integración de aplicaciones SaaS con Azure AD, consulte [¿Qué es el acceso a aplicaciones y el inicio de sesión único con Azure Active Directory?](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Requisitos previos

Para configurar la integración de Azure AD con etouches, necesita los siguientes elementos:

- Una suscripción de Azure AD
- Una suscripción habilitada para el inicio de sesión único en etouches

> [!NOTE]
> Para probar los pasos de este tutorial, no se recomienda el uso de un entorno de producción.

Para probar los pasos de este tutorial, debe seguir estas recomendaciones:

- No use el entorno de producción, salvo que sea necesario.
- Si no dispone de un entorno de prueba de Azure AD, puede [obtener una versión de prueba durante un mes](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descripción del escenario
En este tutorial, puede probar el inicio de sesión único de Azure AD en un entorno de prueba. La situación descrita en este tutorial consta de dos bloques de creación principales:

1. Incorporación de etouches desde la galería
2. Configuración y comprobación del inicio de sesión único de Azure AD

## <a name="adding-etouches-from-the-gallery"></a>Incorporación de etouches desde la galería
Para configurar la integración de etouches en Azure AD, deberá agregar etouches desde la galería a la lista de aplicaciones SaaS administradas.

**Para agregar etouches desde la galería, realice los pasos siguientes:**

1. En el panel de navegación izquierdo de **[Azure Portal](https://portal.azure.com)**, haga clic en el icono de **Azure Active Directory**. 

    ![Botón Azure Active Directory][1]

2. Vaya a **Aplicaciones empresariales**. A continuación, vaya a **Todas las aplicaciones**.

    ![Hoja Aplicaciones empresariales][2]
    
3. Para agregar una nueva aplicación, haga clic en el botón **Nueva aplicación** de la parte superior del cuadro de diálogo.

    ![Botón Nueva aplicación][3]

4. En el cuadro de búsqueda, escriba **etouches**, seleccione **etouches** en el panel de resultados y, luego, haga clic en el botón **Agregar** para agregar la aplicación.

    ![etouches en la lista de resultados](./media/active-directory-saas-etouches-tutorial/tutorial_etouches_addfromgallery.png)

##  <a name="configure-and-test-azure-ad-single-sign-on"></a>Configuración y prueba del inicio de sesión único en Azure AD
En esta sección, se configura y se prueba el inicio de sesión único de Azure AD con etouches con un usuario de prueba llamado "Britta Simon".

Para que el inicio de sesión único funcione, Azure AD debe saber cuál es el usuario homólogo de etouches para un usuario de Azure AD. Es decir, es necesario establecer una relación de vínculo entre un usuario de Azure AD y el usuario relacionado de etouches.

Para establecer la relación de vínculo, en etouches, asigne el valor del **nombre de usuario** de Azure AD como el valor del **nombre de usuario**.

Para configurar y probar el inicio de sesión único de Azure AD con etouches, es preciso completar los siguientes bloques de creación:

1. **[Configuración del inicio de sesión único de Azure AD](#configure-azure-ad-single-sign-on)**: para permitir que los usuarios utilicen esta característica.
2. **[Creación de un usuario de prueba de Azure AD](#create-an-azure-ad-test-user)**: para probar el inicio de sesión único de Azure AD con Britta Simon.
3. **[Creación de un usuario de prueba de etouches](#create-an-etouches-test-user)**: para tener un homólogo de Britta Simon en etouches que esté vinculado a la representación de ella en Azure AD.
4. **[Asignación del usuario de prueba de Azure AD](#assign-the-azure-ad-test-user)**: para permitir que Britta Simon use el inicio de sesión único de Azure AD.
5. **[Prueba del inicio de sesión único](#test-single-sign-on)**: para comprobar si funciona la configuración.

### <a name="configure-azure-ad-single-sign-on"></a>Configuración del inicio de sesión único de Azure AD

En esta sección, habilitará el inicio de sesión único de Azure AD en Azure Portal y configurará el inicio de sesión único en la aplicación etouches.

**Para configurar el inicio de sesión único de Azure AD con etouches, realice los pasos siguientes:**

1. En Azure Portal, en la página de integración de la aplicación **etouches**, haga clic en **Inicio de sesión único**.

    ![Configurar inicio de sesión único][4]

2. En el cuadro de diálogo **Inicio de sesión único**, en **Modo** seleccione **Inicio de sesión basado en SAML** para habilitar el inicio de sesión único.
 
    ![Cuadro de diálogo Inicio de sesión único](./media/active-directory-saas-etouches-tutorial/tutorial_etouches_samlbase.png)

3. En la sección **Dominio y direcciones URL de etouches**, lleve a cabo los pasos siguientes:

    ![Información de dominio y direcciones URL de inicio de sesión único de etouches](./media/active-directory-saas-etouches-tutorial/tutorial_etouches_url.png)

    a. En el cuadro de texto **URL de inicio de sesión**, escriba una dirección URL con el siguiente patrón: `https://www.eiseverywhere.com/saml/accounts/?sso&accountid=<ACCOUNTID>`.

    b. En el cuadro de texto **Identificador**, escriba una dirección URL con el siguiente patrón: `https://www.eiseverywhere.com/<instance name>`

    > [!NOTE] 
    > Estos valores no son reales. El valor se actualizará con la dirección URL de inicio de sesión y el identificador reales, que se explican más adelante en el tutorial.
    > 

4. La aplicación etouches espera las aserciones de SAML en un formato específico. Configure las siguientes notificaciones para esta aplicación. Puede administrar el valor de estos atributos desde la pestaña **Atributos de usuario** de la aplicación. La siguiente captura de pantalla le muestra un ejemplo de esto. 

    ![Atributo de usuario](./media/active-directory-saas-etouches-tutorial/tutorial_etouches_attribute.png) 

5. En la sección **Atributos de usuario** del cuadro de diálogo **Inicio de sesión único**, configure el atributo token de SAML como muestra la imagen y siga estos pasos:
    
    | Nombre del atributo | Valor de atributo |
    | ------------------- | -------------------- |
    | Email | user.mail |    
    
    a. Haga clic en **Agregar atributo** para abrir el cuadro de diálogo **Agregar atributo**.

    ![Agregar atributo](./media/active-directory-saas-etouches-tutorial/tutorial_attribute_04.png)

    ![Cuadro de diálogo Agregar atributo](./media/active-directory-saas-etouches-tutorial/tutorial_attribute_05.png)

    b. En el cuadro de texto **Nombre**, escriba el nombre que se muestra para la fila.

    c. En la lista **Valor**, seleccione el atributo que se muestra para esa fila.
    
    d. Haga clic en **Aceptar**. 

6. En la sección **Certificado de firma de SAML**, haga clic en **XML de metadatos** y luego guarde el archivo de metadatos en el equipo.

    ![Vínculo de descarga del certificado](./media/active-directory-saas-etouches-tutorial/tutorial_etouches_certificate.png) 

7. Haga clic en el botón **Guardar** .

    ![Botón Guardar de Configuración de inicio de sesión único](./media/active-directory-saas-etouches-tutorial/tutorial_general_400.png)

8. Para que SSO se configure para su aplicación, siga estos pasos en la aplicación etouches: 

    ![Configuración de etouches](./media/active-directory-saas-etouches-tutorial/tutorial_etouches_06.png) 

    a. Inicie sesión en la aplicación **etouches** con derechos de administrador.
   
    b. Vaya a la configuración de **SAML**.

    c. En la sección **Configuración general**, abra el certificado descargado de Azure Portal en el Bloc de notas, copie el contenido y, luego, péguelo en el cuadro de texto de metadatos de IDP. 

    d. Haga clic en el botón **Save & Stay** (Guardar y permanecer).
  
    e. Haga clic en el botón **Update Metadata** (Actualizar metadatos) en la sección SAML Metadata (Metadatos de SAML). 

    f. Esta acción abre la página y se realiza el inicio de sesión único. Cuando el inicio de sesión único esté funcionando, puede configurar el nombre de usuario.

    g. En el campo Username (Nombre de usuario) seleccione **emailaddress**, como se muestra en la siguiente imagen. 

    h. Copie el valor de **SP entity ID** (Identificador de entidad del proveedor de servicios) y péguelo en el cuadro de texto **Identifier** (Identificador), que se encuentra en la sección **Dominio y direcciones URL de etouches** de Azure Portal.

    i. Copie el valor de **SSO URL / ACS** (URL de SSO/ACS) y péguelo en el cuadro de texto **Sign on URL** (Dirección URL de inicio de sesión), que se encuentra en la sección **Dominio y direcciones URL de etouches** de Azure Portal.
   
> [!TIP]
> Ahora puede leer una versión resumida de estas instrucciones dentro de [Azure Portal](https://portal.azure.com) mientras configura la aplicación.  Después de agregar esta aplicación desde la sección **Active Directory > Aplicaciones empresariales**, simplemente haga clic en la pestaña **Inicio de sesión único** y acceda a la documentación insertada a través de la sección **Configuración** de la parte inferior. Puede leer más sobre la característica de documentación insertada aquí: [Vista previa: Administración de inicio de sesión único para aplicaciones empresariales en el nuevo Azure Portal]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="create-an-azure-ad-test-user"></a>Creación de un usuario de prueba de Azure AD
El objetivo de esta sección es crear un usuario de prueba en Azure Portal llamado "Britta Simon".

![Creación de un usuario de prueba de Azure AD][100]

**Siga estos pasos para crear un usuario de prueba en Azure AD:**

1. En el panel de navegación izquierdo de **Azure Portal**, haga clic en el icono de **Azure Active Directory**.

    ![Botón Azure Active Directory](./media/active-directory-saas-etouches-tutorial/create_aaduser_01.png) 

2. Para mostrar la lista de usuarios, vaya a **Usuarios y grupos** y haga clic en **Todos los usuarios**.
    
    ![Vínculos "Usuarios y grupos" y "Todos los usuarios"](./media/active-directory-saas-etouches-tutorial/create_aaduser_02.png) 

3. Para abrir el cuadro de diálogo **Usuario**, haga clic en **Agregar** en la parte superior del cuadro de diálogo.
 
    ![Botón Agregar](./media/active-directory-saas-etouches-tutorial/create_aaduser_03.png) 

4. En la página de diálogo **Usuario**, realice los siguientes pasos:
 
    ![Cuadro de diálogo Usuario](./media/active-directory-saas-etouches-tutorial/create_aaduser_04.png) 

    a. En el cuadro de texto **Nombre**, escriba **BrittaSimon**.

    b. En el cuadro de texto **Nombre de usuario**, escriba la **dirección de correo electrónico** de Britta Simon.

    c. Seleccione **Mostrar contraseña** y anote el valor del cuadro **Contraseña**.

    d. Haga clic en **Crear**.
 
### <a name="create-an-etouches-test-user"></a>Creación de un usuario de prueba de etouches

En esta sección, se crea un usuario denominado Britta Simon en etouches. Trabaje con el [equipo de soporte técnico de etouches](https://www.etouches.com/event-software/support/customer-support/) para agregar los usuarios a la plataforma de etouches.

### <a name="assign-the-azure-ad-test-user"></a>Asignación del usuario de prueba de Azure AD

En esta sección, habilitará a Britta Simon para que use el inicio de sesión único de Azure concediéndole acceso a etouches.

![Asignación del rol de usuario][200] 

**Para asignar Britta Simon a etouches, realice los pasos siguientes:**

1. En Azure Portal, abra la vista de aplicaciones, vaya a la vista de directorio y vaya a **Aplicaciones empresariales**. Luego, haga clic en **Todas las aplicaciones**.

    ![Asignar usuario][201] 

2. En la lista de aplicaciones, seleccione **etouches**.

    ![Vínculo a etouches en la lista de aplicaciones](./media/active-directory-saas-etouches-tutorial/tutorial_etouches_app.png) 

3. En el menú de la izquierda, haga clic en **Usuarios y grupos**.

    ![Vínculo "Usuarios y grupos"][202] 

4. Haga clic en el botón **Agregar**. Después, seleccione **Usuarios y grupos** en el cuadro de diálogo **Agregar asignación**.

    ![Panel Agregar asignación][203]

5. En el cuadro de diálogo **Usuarios y grupos**, seleccione **Britta Simon** en la lista de usuarios.

6. Haga clic en el botón **Seleccionar** del cuadro de diálogo **Usuarios y grupos**.

7. Haga clic en el botón **Asignar** del cuadro de diálogo **Agregar asignación**.
    
### <a name="test-single-sign-on"></a>Prueba de inicio de sesión único


El objetivo de esta sección es probar la configuración del inicio de sesión único de Azure AD mediante el panel de acceso.

Al hacer clic en el icono de etouches en el panel de acceso, debe iniciar sesión automáticamente en su aplicación etouches.

## <a name="additional-resources"></a>Recursos adicionales

* [Lista de tutoriales sobre cómo integrar aplicaciones SaaS con Azure Active Directory](active-directory-saas-tutorial-list.md)
* [¿Qué es el acceso a aplicaciones y el inicio de sesión único con Azure Active Directory?](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-etouches-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-etouches-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-etouches-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-etouches-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-etouches-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-etouches-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-etouches-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-etouches-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-etouches-tutorial/tutorial_general_203.png

