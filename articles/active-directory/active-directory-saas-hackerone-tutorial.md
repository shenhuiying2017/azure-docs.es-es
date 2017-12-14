---
title: "Tutorial: Integración de Azure Active Directory con HackerOne | Microsoft Docs"
description: "Aprenda a configurar el inicio de sesión único entre Azure Active Directory y HackerOne."
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: 229d1efb-b6a5-4df8-9839-5d551487db4e
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/28/2017
ms.author: jeedes
ms.openlocfilehash: 3f8a4b6b3b089ce075b3398ac36fe5bd1979c366
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-hackerone"></a>Tutorial: Integración de Azure Active Directory con HackerOne

En este tutorial se aprende a integrar HackerOne con Azure Active Directory (Azure AD).

La integración de HackerOne con Azure AD proporciona las siguientes ventajas:

- Puede controlar en Azure AD quién tiene acceso a HackerOne.
- Puede permitir que los usuarios inicien sesión automáticamente en HackerOne (inicio de sesión único) con sus cuentas de Azure AD.
- Puede administrar sus cuentas en una ubicación central: el nuevo Azure Portal.

Si desea saber más sobre la integración de aplicaciones SaaS con Azure AD, consulte [¿Qué es el acceso a aplicaciones y el inicio de sesión único con Azure Active Directory?](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Requisitos previos

Para configurar la integración de Azure AD con HackerOne, necesita los siguientes elementos:

- Una suscripción de Azure AD
- Una suscripción habilitada para el inicio de sesión único en HackerOne

> [!NOTE]
> Para probar los pasos de este tutorial, no se recomienda el uso de un entorno de producción.

Para probar los pasos de este tutorial, debe seguir estas recomendaciones:

- No use el entorno de producción, salvo que sea necesario.
- Si no dispone de un entorno de prueba de Azure AD, puede obtener una versión de prueba de un mes [aquí](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descripción del escenario
En este tutorial, puede probar el inicio de sesión único de Azure AD en un entorno de prueba. La situación descrita en este tutorial consta de dos bloques de creación principales:

1. Incorporación de HackerOne desde la galería
2. Configuración y comprobación del inicio de sesión único de Azure AD

## <a name="adding-hackerone-from-the-gallery"></a>Incorporación de HackerOne desde la galería
Para configurar la integración de HackerOne en Azure AD, será preciso que agregue HackerOne desde la galería a la lista de aplicaciones SaaS administradas.

**Para agregar HackerOne desde la galería, realice los pasos siguientes:**

1. En el panel de navegación izquierdo de **[Azure Portal](https://portal.azure.com)**, haga clic en el icono de **Azure Active Directory**. 

    ![Active Directory][1]

2. Vaya a **Aplicaciones empresariales**. A continuación, vaya a **Todas las aplicaciones**.

    ![Aplicaciones][2]
    
3. Para agregar una nueva aplicación, haga clic en el botón **Nueva aplicación** de la parte superior del cuadro de diálogo.

    ![Aplicaciones][3]

4. En el cuadro de búsqueda, escriba **HackerOne**.

    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-hackerone-tutorial/tutorial_hackerone_search.png)

5. En el panel de resultados, seleccione **HackerOne** y haga clic en el botón **Agregar** para agregar la aplicación.

    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-hackerone-tutorial/tutorial_hackerone_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configuración y comprobación del inicio de sesión único de Azure AD

En esta sección se configura y prueba el inicio de sesión único de Azure AD con HackerOne con un usuario de prueba llamado "Britta Simon".

Para que el inicio de sesión único funcione, Azure AD debe saber cuál es el usuario homólogo de HackerOne para un usuario de Azure AD. Es decir, es necesario establecer una relación de vínculo entre un usuario de Azure AD y el usuario relacionado de HackerOne.

Para establecer la relación de vínculo, en HackerOne, asigne el valor de **nombre de usuario** de Azure AD como valor de **Nombre de usuario**.

Para configurar y probar el inicio de sesión único de Azure AD con HackerOne, es preciso completar los siguientes bloques de creación:

1. **[Configuración del inicio de sesión único de Azure AD](#configuring-azure-ad-single-sign-on)** : para permitir a los usuarios usar esta característica.
2. **[Creación de un usuario de prueba de Azure AD](#creating-an-azure-ad-test-user)** : para probar el inicio de sesión único de Azure AD con Britta Simon.
3. **[Creación de un usuario de prueba de HackerOne](#creating-a-hackerone-test-user)**: para tener un homólogo de Britta Simon en HackerOne vinculado a la representación de usuario en Azure AD.
4. **[Asignación del usuario de prueba de Azure AD](#assigning-the-azure-ad-test-user)** : para permitir que Britta Simon use el inicio de sesión único de Azure AD.
5. **[Testing Single Sign-On](#testing-single-sign-on)** : para comprobar si funciona la configuración.

### <a name="configuring-azure-ad-single-sign-on"></a>Configuración del inicio de sesión único de Azure AD

En esta sección se habilita el inicio de sesión único de Azure AD en Azure Portal y se configura el inicio de sesión único en la aplicación HackerOne.

**Para configurar el inicio de sesión único de Azure AD con HackerOne, realice los pasos siguientes:**

1. En Azure Portal, en la página de integración de la aplicación **HackerOne**, haga clic en **Inicio de sesión único**.

    ![Configurar inicio de sesión único][4]

2. En el cuadro de diálogo **Inicio de sesión único**, en **Modo** seleccione **Inicio de sesión basado en SAML** para habilitar el inicio de sesión único.
 
    ![Configurar inicio de sesión único](./media/active-directory-saas-hackerone-tutorial/tutorial_hackerone_samlbase.png)

3. En la sección de **dirección URL de inicio de sesión único e identificador de HackerOne**, realice los pasos siguientes:

    ![Configurar inicio de sesión único](./media/active-directory-saas-hackerone-tutorial/tutorial_hackerone_url.png)

    a. En el cuadro de texto **URL de inicio de sesión**, escriba una dirección URL con el siguiente patrón: `https://hackerone.com/<company name>/authentication`.

    b. En el cuadro de texto **Identificador**, escriba una dirección URL como: `https://hackerone.com/users/saml/metadata`
    
    > [!NOTE] 
    > Este valor no es real. Actualícelo con la dirección URL de inicio de sesión real. Contacte con el [equipo de soporte técnico de HackerOne](mailto:support@hackerone.com) para obtener este valor. 
 
4. En la sección **Certificado de firma de SAML**, haga clic en **Certificado (Base64)** y, luego, guarde el archivo de certificado en el equipo.

    ![Configurar inicio de sesión único](./media/active-directory-saas-hackerone-tutorial/tutorial_hackerone_certificate.png) 

5. Haga clic en el botón **Guardar** .

    ![Configurar inicio de sesión único](./media/active-directory-saas-hackerone-tutorial/tutorial_general_400.png)

6. En la sección **Configuración de HackerOne**, haga clic en **Configurar HackerOne** para abrir la ventana **Configurar inicio de sesión**. Copie la **dirección URL de servicio de inicio de sesión único de SAML** de la sección **Referencia rápida**.

    ![Configurar inicio de sesión único](./media/active-directory-saas-hackerone-tutorial/tutorial_hackerone_configure.png) 

7. Inicie sesión en el inquilino de HackerOne como administrador.

8. En el menú de la parte superior, haga clic en "**Settings**" (Configuración).
   
    ![Configurar inicio de sesión único](./media/active-directory-saas-hackerone-tutorial/tutorial_hackerone_001.png) 

9. Vaya a "**Authentication**" (Autenticación) y haga clic en el botón "**Add SAML settings**" (Agregar configuración de SAML).
   
    ![Configurar inicio de sesión único](./media/active-directory-saas-hackerone-tutorial/tutorial_hackerone_003.png) 

10. En el cuadro de diálogo **SAML Settings** (Configuración de SAML), siga estos pasos:
   
    ![Configurar inicio de sesión único](./media/active-directory-saas-hackerone-tutorial/tutorial_hackerone_004.png) 

    a. En el cuadro de texto **Dominio de correo electrónico** , escriba un dominio registrado.

    b. En el cuadro de texto **Single Sign On URL** (Dirección URL de inicio de sesión), pegue el valor de la **dirección URL del servicio de inicio de sesión único de SAML** que ha copiado de Azure Portal.

    c. Abra el **archivo de certificado** que descargó de Azure Portal en el Bloc de notas, copie su contenido en el Portapapeles y luego péguelo en el cuadro de texto **Certificado X509**.
    
    d. Haga clic en **Guardar**.

11. En el diálogo Configuración de autenticación, realice los pasos siguientes:
   
    ![Configurar inicio de sesión único](./media/active-directory-saas-hackerone-tutorial/tutorial_hackerone_005.png) 

    a. Haga clic en **Ejecutar prueba**.

    b. Si el valor del campo **Status** (Estado) es igual a **Last test status: created** (Último estado de la prueba: creado), contacte con el [equipo de soporte técnico de HackerOne](mailto:support@hackerone.com) para pedir una revisión de la configuración.

> [!TIP]
> Ahora puede leer una versión resumida de estas instrucciones dentro de [Azure Portal](https://portal.azure.com) mientras configura la aplicación.  Después de agregar esta aplicación desde la sección **Active Directory > Aplicaciones empresariales**, simplemente haga clic en la pestaña **Inicio de sesión único** y acceda a la documentación insertada a través de la sección **Configuración** de la parte inferior. Puede leer más sobre la característica de documentación insertada aquí: [Vista previa: Administración de inicio de sesión único para aplicaciones empresariales en el nuevo Azure Portal]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>Creación de un usuario de prueba de Azure AD
El objetivo de esta sección es crear un usuario de prueba en Azure Portal llamado "Britta Simon".

![Creación de un usuario de Azure AD][100]

**Siga estos pasos para crear un usuario de prueba en Azure AD:**

1. En el panel de navegación izquierdo de **Azure Portal**, haga clic en el icono de **Azure Active Directory**.

    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-hackerone-tutorial/create_aaduser_01.png) 

2. Para mostrar la lista de usuarios, vaya a **Usuarios y grupos** y haga clic en **Todos los usuarios**.
    
    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-hackerone-tutorial/create_aaduser_02.png) 

3. Para abrir el cuadro de diálogo **Usuario**, haga clic en **Agregar** en la parte superior del cuadro de diálogo.
 
    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-hackerone-tutorial/create_aaduser_03.png) 

4. En la página de diálogo **Usuario**, realice los siguientes pasos:
 
    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-hackerone-tutorial/create_aaduser_04.png) 

    a. En el cuadro de texto **Nombre**, escriba **BrittaSimon**.

    b. En el cuadro de texto **Nombre de usuario**, escriba la **dirección de correo electrónico** de Britta Simon.

    c. Seleccione **Mostrar contraseña** y anote el valor del cuadro **Contraseña**.

    d. Haga clic en **Crear**.
 
### <a name="creating-a-hackerone-test-user"></a>Creación de un usuario de prueba de HackerOne

A continuación, creará un usuario llamado Simon Britta en HackerOne. HackerOne admite el aprovisionamiento Just-In-Time, que está habilitado de forma predeterminada.

No hay ningún elemento de acción para usted en esta sección. Al acceder a HackerOne, se crea un usuario nuevo si todavía no existe uno.

>[!NOTE]
>Si necesita crear manualmente un usuario, debe ponerse en contacto con el equipo de soporte técnico de Certify. 
> 

### <a name="assigning-the-azure-ad-test-user"></a>Asignación del usuario de prueba de Azure AD

En esta sección se habilita a Britta Simon para que use el inicio de sesión único de Azure concediéndole acceso a HackerOne.

![Asignar usuario][200] 

**Para asignar a Britta Simon a HackerOne, realice los pasos siguientes:**

1. En Azure Portal, abra la vista de aplicaciones, vaya a la vista de directorio y vaya a **Aplicaciones empresariales**. Luego, haga clic en **Todas las aplicaciones**.

    ![Asignar usuario][201] 

2. En la lista de aplicaciones, seleccione **HackerOne**.

    ![Configurar inicio de sesión único](./media/active-directory-saas-hackerone-tutorial/tutorial_hackerone_app.png) 

3. En el menú de la izquierda, haga clic en **Usuarios y grupos**.

    ![Asignar usuario][202] 

4. Haga clic en el botón **Agregar**. Después, seleccione **Usuarios y grupos** en el cuadro de diálogo **Agregar asignación**.

    ![Asignar usuario][203]

5. En el cuadro de diálogo **Usuarios y grupos**, seleccione **Britta Simon** en la lista de usuarios.

6. Haga clic en el botón **Seleccionar** del cuadro de diálogo **Usuarios y grupos**.

7. Haga clic en el botón **Asignar** del cuadro de diálogo **Agregar asignación**.
    
### <a name="testing-single-sign-on"></a>Prueba del inicio de sesión único 

Por último, probará la configuración de inicio de sesión único de Azure AD mediante el panel de acceso.  

Al hacer clic en el icono de HackerOne en el Panel de acceso, debería iniciar sesión automáticamente en su aplicación HackerOne.

## <a name="additional-resources"></a>Recursos adicionales

* [Lista de tutoriales sobre cómo integrar aplicaciones SaaS con Azure Active Directory](active-directory-saas-tutorial-list.md)
* [¿Qué es el acceso a aplicaciones y el inicio de sesión único con Azure Active Directory?](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-hackerone-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-hackerone-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-hackerone-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-hackerone-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-hackerone-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-hackerone-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-hackerone-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-hackerone-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-hackerone-tutorial/tutorial_general_203.png

