---
title: "Tutorial: Integración de Azure Active Directory con Deputy | Microsoft Docs"
description: "Aprenda a configurar el inicio de sesión único entre Azure Active Directory y Deputy."
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: 5665c3ac-5689-4201-80fe-fcc677d4430d
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/22/2017
ms.author: jeedes
ms.openlocfilehash: 612a0d298eb1153bc8a64f0d7fbcb4dbd8752672
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-deputy"></a>Tutorial: integración de Azure Active Directory con Deputy

En este tutorial, aprenderá a integrar Deputy con Azure Active Directory (Azure AD).

Integrar Deputy con Azure AD proporciona las siguientes ventajas:

- Puede controlar en Azure AD quién tiene acceso a Deputy.
- Puede permitir que los usuarios inicien sesión automáticamente en Deputy (inicio de sesión único) con sus cuentas de Azure AD.
- Puede administrar las cuentas en una sola ubicación central: Azure Portal.

Si desea saber más sobre la integración de aplicaciones SaaS con Azure AD, consulte [¿Qué es el acceso a aplicaciones y el inicio de sesión único con Azure Active Directory?](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Requisitos previos

Para configurar la integración de Azure AD con Deputy, necesita los siguientes elementos:

- Una suscripción de Azure AD
- Una suscripción habilitada para el inicio de sesión único en Deputy

> [!NOTE]
> Para probar los pasos de este tutorial, no se recomienda el uso de un entorno de producción.

Para probar los pasos de este tutorial, debe seguir estas recomendaciones:

- No use el entorno de producción, salvo que sea necesario.
- Si no dispone de un entorno de prueba de Azure AD, puede obtener una versión de prueba de un mes [aquí](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descripción del escenario
En este tutorial, puede probar el inicio de sesión único de Azure AD en un entorno de prueba. La situación descrita en este tutorial consta de dos bloques de creación principales:

1. Adición de Deputy desde la galería
2. Configuración y comprobación del inicio de sesión único de Azure AD

## <a name="adding-deputy-from-the-gallery"></a>Adición de Deputy desde la galería
Para configurar la integración de Deputy en Azure AD, es preciso agregar Deputy desde la galería a la lista de aplicaciones SaaS administradas.

**Para agregar Deputy desde la galería, realice los pasos siguientes:**

1. En el panel de navegación izquierdo de **[Azure Portal](https://portal.azure.com)**, haga clic en el icono de **Azure Active Directory**. 

    ![Active Directory][1]

2. Vaya a **Aplicaciones empresariales**. A continuación, vaya a **Todas las aplicaciones**.

    ![Aplicaciones][2]
    
3. Para agregar una nueva aplicación, haga clic en el botón **Nueva aplicación** de la parte superior del cuadro de diálogo.

    ![Aplicaciones][3]

4. En el cuadro de búsqueda, escriba **Deputy**.

    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-deputy-tutorial/tutorial_deputy_search.png)

5. En el panel de resultados, seleccione **Deputy** y luego haga clic en el botón **Agregar** para agregar la aplicación.

    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-deputy-tutorial/tutorial_deputy_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configuración y comprobación del inicio de sesión único de Azure AD
En esta sección, va a configurar y probar el inicio de sesión único de Azure AD con Deputy con un usuario de prueba llamado "Britta Simon".

Para que el inicio de sesión único funcione, Azure AD debe saber cuál es el usuario homólogo de Deputy para un usuario de Azure AD. Es decir, es necesario establecer una relación de vínculo entre un usuario de Azure AD y el usuario correspondiente de Deputy.

Para establecer la relación de vínculo, en Deputy, asigne el valor de **nombre de usuario** de Azure AD como valor de **nombre de usuario**.

Para configurar y probar el inicio de sesión único de Azure AD con Deputy, es preciso completar los siguientes bloques de creación:

1. **[Configuring Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)** : para permitir a los usuarios usar esta característica.
2. **[Creación de un usuario de prueba de Azure AD](#creating-an-azure-ad-test-user)** : para probar el inicio de sesión único de Azure AD con Britta Simon.
3. **[Creación de usuario de prueba de Deputy](#creating-a-deputy-test-user)**: para tener un homólogo de Britta Simon en Deputy que esté vinculado a la representación del usuario en Azure AD.
4. **[Asignación del usuario de prueba de Azure AD](#assigning-the-azure-ad-test-user)** : para permitir que Britta Simon use el inicio de sesión único de Azure AD.
5. **[Testing Single Sign-On](#testing-single-sign-on)** : para comprobar si funciona la configuración.

### <a name="configuring-azure-ad-single-sign-on"></a>Configuración del inicio de sesión único de Azure AD

En esta sección, habilitará el inicio de sesión único de Azure AD en Azure Portal y configurará el inicio de sesión único en la aplicación Deputy.

**Para configurar el inicio de sesión único de Azure AD con Deputy, realice los pasos siguientes:**

1. En Azure Portal, en la página de integración de la aplicación **Deputy**, haga clic en **Inicio de sesión único**.

    ![Configurar inicio de sesión único][4]

2. En el cuadro de diálogo **Inicio de sesión único**, en **Modo** seleccione **Inicio de sesión basado en SAML** para habilitar el inicio de sesión único.
 
    ![Configurar inicio de sesión único](./media/active-directory-saas-deputy-tutorial/tutorial_deputy_samlbase.png)

3. En la sección **Dominio y direcciones URL de Deputy**, si quiere configurar la aplicación en modo iniciado por **IDP**:

    ![Configurar inicio de sesión único](./media/active-directory-saas-deputy-tutorial/tutorial_deputy_url1.png)

    a. En el cuadro de texto **Identificador**, escriba una dirección URL con el siguiente patrón:
    |  |
    | ----|
    | `https://<subdomain>.<region>.au.deputy.com` |
    | `https://<subdomain>.<region>.ent-au.deputy.com` |
    | `https://<subdomain>.<region>.na.deputy.com`|
    | `https://<subdomain>.<region>.ent-na.deputy.com`|
    | `https://<subdomain>.<region>.eu.deputy.com` |
    | `https://<subdomain>.<region>.ent-eu.deputy.com` |
    | `https://<subdomain>.<region>.as.deputy.com` |
    | `https://<subdomain>.<region>.ent-as.deputy.com` |
    | `https://<subdomain>.<region>.la.deputy.com` |
    | `https://<subdomain>.<region>.ent-la.deputy.com` |
    | `https://<subdomain>.<region>.af.deputy.com` |
    | `https://<subdomain>.<region>.ent-af.deputy.com` |
    | `https://<subdomain>.<region>.an.deputy.com` |
    | `https://<subdomain>.<region>.ent-an.deputy.com` |
    | `https://<subdomain>.<region>.deputy.com` |

    b. En el cuadro de texto **URL de respuesta** , escriba una dirección URL con el siguiente patrón:
    | |
    |----|
    | `https://<subdomain>.<region>.au.deputy.com/exec/devapp/samlacs.` |
    | `https://<subdomain>.<region>.ent-au.deputy.com/exec/devapp/samlacs.` |
    | `https://<subdomain>.<region>.na.deputy.com/exec/devapp/samlacs.` |
    | `https://<subdomain>.<region>.ent-na.deputy.com/exec/devapp/samlacs.` |
    | `https://<subdomain>.<region>.eu.deputy.com/exec/devapp/samlacs.` |
    | `https://<subdomain>.<region>.ent-eu.deputy.com/exec/devapp/samlacs.` |
    | `https://<subdomain>.<region>.as.deputy.com/exec/devapp/samlacs.` |
    | `https://<subdomain>.<region>.ent-as.deputy.com/exec/devapp/samlacs.` |
    | `https://<subdomain>.<region>.la.deputy.com/exec/devapp/samlacs.` |
    | `https://<subdomain>.<region>.ent-la.deputy.com/exec/devapp/samlacs.` |
    | `https://<subdomain>.<region>.af.deputy.com/exec/devapp/samlacs.` |
    | `https://<subdomain>.<region>.ent-af.deputy.com/exec/devapp/samlacs.` |
    | `https://<subdomain>.<region>.an.deputy.com/exec/devapp/samlacs.` |
    | `https://<subdomain>.<region>.ent-an.deputy.com/exec/devapp/samlacs.` |
    | `https://<subdomain>.<region>.deputy.com/exec/devapp/samlacs.` |

4. Active **Mostrar configuración avanzada de URL**. Si quiere volver a configurar la aplicación en modo iniciado por **SP**:

    ![Configurar inicio de sesión único](./media/active-directory-saas-deputy-tutorial/tutorial_deputy_url2.png)

    En el cuadro de texto **URL de inicio de sesión**, escriba una dirección URL con el siguiente patrón: `https://<your-subdomain>.<region>.deputy.com`.
    
    >[!NOTE]
    > El sufijo de la región de Deputy es opcional o debe usar uno de los siguientes: au | na | eu |as |la |af |an |ent-au |ent-na |ent-eu |ent-as | ent-la | ent-af | ent-an

    > [!NOTE] 
    > Estos valores no son reales. Actualice estos valores con los valores reales de Identificador, URL de respuesta y URL de inicio de sesión. Póngase en contacto con el [equipo de soporte técnico de Deputy](https://www.deputy.com/call-centers-customer-support-scheduling-software) para obtener estos valores. 

5. En la sección **Certificado de firma de SAML**, haga clic en **Certificado (Base64)** y, luego, guarde el archivo de certificado en el equipo.

    ![Configurar inicio de sesión único](./media/active-directory-saas-deputy-tutorial/tutorial_deputy_certificate.png) 

6. Haga clic en el botón **Guardar** .

    ![Configurar inicio de sesión único](./media/active-directory-saas-deputy-tutorial/tutorial_general_400.png)
    
7. En la sección **Configuración de Deputy**, haga clic en **Configurar Deputy** para abrir la ventana **Configurar inicio de sesión**. Copie la **dirección URL de servicio de inicio de sesión único de SAML** de la sección **Referencia rápida**.

    ![Configurar inicio de sesión único](./media/active-directory-saas-deputy-tutorial/tutorial_deputy_configure.png) 

8. Vaya a la siguiente dirección URL: [https://(su subdominio).deputy.com/exec/config/system_config]( https://(your-subdomain).deputy.com/exec/config/system_config). Vaya a **Configuración de seguridad** y haga clic en **Editar**.
   
    ![Configurar inicio de sesión único](./media/active-directory-saas-deputy-tutorial/tutorial_deputy_004.png)

9. En esta página de **configuración de seguridad** , siga estos pasos.

    ![Configurar inicio de sesión único](./media/active-directory-saas-deputy-tutorial/tutorial_deputy_005.png)
    
    a. Habilite el **inicio de sesión social**.
   
    b. Abra el certificado codificado en base 64 descargado de Azure Portal en el Bloc de notas, copie su contenido en el portapapeles y luego péguelo en el cuadro de texto **Certificado OpenSSL**.
   
    c. En el cuadro de texto Dirección URL de inicio de sesión único de SAML, escriba `https://<your subdomain>.deputy.com/exec/devapp/samlacs?dpLoginTo=<saml sso url>`.
    
    d. En el cuadro de texto Dirección URL de inicio de sesión único de SAML, reemplace `<your subdomain>` por el subdominio.
   
    e. En el cuadro de texto Dirección URL de inicio de sesión único de SAML, reemplace `<saml sso url>` por la **dirección URL del servicio de inicio de sesión único de SAML que haya copiado desde Azure Portal**.
   
    f. Haga clic en **Guardar configuración**.

> [!TIP]
> Ahora puede leer una versión resumida de estas instrucciones dentro de [Azure Portal](https://portal.azure.com) mientras configura la aplicación.  Después de agregar esta aplicación desde la sección **Active Directory > Aplicaciones empresariales**, simplemente haga clic en la pestaña **Inicio de sesión único** y acceda a la documentación insertada a través de la sección **Configuración** de la parte inferior. Puede leer más sobre la característica de documentación insertada aquí: [Vista previa: Administración de inicio de sesión único para aplicaciones empresariales en el nuevo Azure Portal]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>Creación de un usuario de prueba de Azure AD
El objetivo de esta sección es crear un usuario de prueba en Azure Portal llamado "Britta Simon".

![Creación de un usuario de Azure AD][100]

**Siga estos pasos para crear un usuario de prueba en Azure AD:**

1. En el panel de navegación izquierdo de **Azure Portal**, haga clic en el icono de **Azure Active Directory**.

    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-deputy-tutorial/create_aaduser_01.png) 

2. Para mostrar la lista de usuarios, vaya a **Usuarios y grupos** y haga clic en **Todos los usuarios**.
    
    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-deputy-tutorial/create_aaduser_02.png) 

3. Para abrir el cuadro de diálogo **Usuario**, haga clic en **Agregar** en la parte superior del cuadro de diálogo.
 
    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-deputy-tutorial/create_aaduser_03.png) 

4. En la página de diálogo **Usuario**, realice los siguientes pasos:
 
    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-deputy-tutorial/create_aaduser_04.png) 

    a. En el cuadro de texto **Nombre**, escriba **BrittaSimon**.

    b. En el cuadro de texto **Nombre de usuario**, escriba la **dirección de correo electrónico** de Britta Simon.

    c. Seleccione **Mostrar contraseña** y anote el valor del cuadro **Contraseña**.

    d. Haga clic en **Crear**.
 
### <a name="creating-a-deputy-test-user"></a>Creación de usuario de prueba de Deputy

Para permitir que los usuarios de Azure AD inicien sesión en Deputy, deben aprovisionarse en Deputy. En el caso de Deputy, el aprovisionamiento es una tarea manual.

#### <a name="to-provision-a-user-account-perform-the-following-steps"></a>Para aprovisionar una cuenta de usuario, realice estos pasos:
1. Inicie sesión en su sitio de la compañía de Deputy como administrador.

2. En la parte superior del panel de navegación, haga clic en **Contactos**.
   
   ![Personas](./media/active-directory-saas-deputy-tutorial/tutorial_deputy_001.png "Personas")

3. Haga clic en el botón **Agregar personas** y en **Agregar una sola persona**.
   
   ![Agregar personas](./media/active-directory-saas-deputy-tutorial/tutorial_deputy_002.png "Agregar personas")

4. Realice los pasos siguientes y haga clic en **Guardar e invitar**.
   
   ![Nuevo usuario](./media/active-directory-saas-deputy-tutorial/tutorial_deputy_003.png "Nuevo usuario")

   a. En el cuadro de texto **Nombre**, escriba el nombre de un usuario, por ejemplo, **Britta Simon**.
   
   b. En el cuadro de texto **Correo electrónico** , escriba la dirección de correo electrónico de la cuenta de Azure AD que quiera aprovisionar.
   
   c. En el cuadro de texto **Trabaja en**, escriba el nombre de la empresa.
   
   d. Haga clic en el botón **Guardar e invitar**.

5. El titular de la cuenta de AAD recibe un mensaje de correo y sigue un vínculo para confirmar su cuenta antes de que se active. Puede usar cualquier otra API o herramienta de creación de cuentas de usuario de Deputy que proporcione Deputy para aprovisionar cuentas de usuario de AAD.

### <a name="assigning-the-azure-ad-test-user"></a>Asignación del usuario de prueba de Azure AD

En esta sección, habilitará a Britta Simon para que use el inicio de sesión único de Azure concediéndole acceso a Deputy.

![Asignar usuario][200] 

**Para asignar a Britta Simon a Deputy, realice los pasos siguientes:**

1. En Azure Portal, abra la vista de aplicaciones, navegue a la vista de directorio y vaya a **Aplicaciones empresariales**. Luego haga clic en **Todas las aplicaciones**.

    ![Asignar usuario][201] 

2. En la lista de aplicaciones, seleccione **Deputy**.

    ![Configurar inicio de sesión único](./media/active-directory-saas-deputy-tutorial/tutorial_deputy_app.png) 

3. En el menú de la izquierda, haga clic en **Usuarios y grupos**.

    ![Asignar usuario][202] 

4. Haga clic en el botón **Agregar**. Después, seleccione **Usuarios y grupos** en el cuadro de diálogo **Agregar asignación**.

    ![Asignar usuario][203]

5. En el cuadro de diálogo **Usuarios y grupos**, seleccione **Britta Simon** en la lista de usuarios.

6. Haga clic en el botón **Seleccionar** del cuadro de diálogo **Usuarios y grupos**.

7. Haga clic en el botón **Asignar** del cuadro de diálogo **Agregar asignación**.
    
### <a name="testing-single-sign-on"></a>Prueba del inicio de sesión único 

El objetivo de esta sección es probar la configuración del inicio de sesión único de Azure AD mediante el panel de acceso.

Al hacer clic en el icono de Deputy en el panel de acceso, debería iniciar sesión automáticamente en su aplicación Deputy.

## <a name="additional-resources"></a>Recursos adicionales

* [Lista de tutoriales sobre cómo integrar aplicaciones SaaS con Azure Active Directory](active-directory-saas-tutorial-list.md)
* [¿Qué es el acceso a aplicaciones y el inicio de sesión único con Azure Active Directory?](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-deputy-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-deputy-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-deputy-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-deputy-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-deputy-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-deputy-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-deputy-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-deputy-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-deputy-tutorial/tutorial_general_203.png

