---
title: "Tutorial: Integración de Azure Active Directory con Coupa | Microsoft Docs"
description: "Aprenda a configurar el inicio de sesión único entre Azure Active Directory y Coupa."
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 47f27746-9057-4b9c-991e-3abf77710f73
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/08/2017
ms.author: jeedes
ms.openlocfilehash: 30149f181d8b0ebdc1ae6820da5d561f3a942fa3
ms.sourcegitcommit: aaba209b9cea87cb983e6f498e7a820616a77471
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/12/2017
---
# <a name="tutorial-azure-active-directory-integration-with-coupa"></a>Tutorial: Integración de Azure Active Directory con Coupa

En este tutorial, aprenderá a integrar Coupa con Azure Active Directory (Azure AD).

La integración de Coupa con Azure AD le proporciona las siguientes ventajas:

- Puede controlar en Azure AD quién tiene acceso a Coupa.
- Puede permitir que los usuarios inicien sesión automáticamente en Coupa (inicio de sesión único) con sus cuentas de Azure AD.
- Puede administrar sus cuentas en una ubicación central: Azure Portal.

Si desea saber más sobre la integración de aplicaciones SaaS con Azure AD, consulte [¿Qué es el acceso a aplicaciones y el inicio de sesión único con Azure Active Directory?](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Requisitos previos

Para configurar la integración de Azure AD con Coupa, necesita los siguientes elementos:

- Una suscripción de Azure AD
- Una suscripción habilitada para el inicio de sesión único en Coupa

> [!NOTE]
> Para probar los pasos de este tutorial, no se recomienda el uso de un entorno de producción.

Para probar los pasos de este tutorial, debe seguir estas recomendaciones:

- No use el entorno de producción, salvo que sea necesario.
- Si no dispone de un entorno de prueba de Azure AD, puede [obtener una versión de prueba durante un mes](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descripción del escenario
En este tutorial, puede probar el inicio de sesión único de Azure AD en un entorno de prueba. La situación descrita en este tutorial consta de dos bloques de creación principales:

1. Adición de Coupa desde la galería
2. Configuración y comprobación del inicio de sesión único de Azure AD

## <a name="adding-coupa-from-the-gallery"></a>Adición de Coupa desde la galería
Para configurar la integración de Coupa en Azure AD, deberá agregar Coupa desde la galería a la lista de aplicaciones SaaS administradas.

**Para agregar Coupa desde la galería, realice los pasos siguientes:**

1. En el panel de navegación izquierdo de **[Azure Portal](https://portal.azure.com)**, haga clic en el icono de **Azure Active Directory**. 

    ![Botón Azure Active Directory][1]

2. Vaya a **Aplicaciones empresariales**. A continuación, vaya a **Todas las aplicaciones**.

    ![Hoja Aplicaciones empresariales][2]
    
3. Para agregar una nueva aplicación, haga clic en el botón **Nueva aplicación** de la parte superior del cuadro de diálogo.

    ![Botón Nueva aplicación][3]

4. En el cuadro de búsqueda, escriba **Coupa**, seleccione **Coupa** en el panel de resultados y, luego, haga clic en el botón **Agregar** para añadir la aplicación.

    ![Coupa en la lista de resultados](./media/active-directory-saas-coupa-tutorial/tutorial_coupa_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configuración y prueba del inicio de sesión único en Azure AD

En esta sección, podrá configurar y probar el inicio de sesión único de Azure AD con Coupa con un usuario de prueba llamado "Britta Simon".

Para que el inicio de sesión único funcione, Azure AD debe saber cuál es el usuario homólogo de Coupa para un usuario de Azure AD. Es decir, es necesario establecer una relación de vínculo entre un usuario de Azure AD y el usuario relacionado de Coupa.

Para establecer la relación de vínculo, en Coupa, asigne el valor de **nombre de usuario** de Azure AD como valor de **nombre de usuario**.

Para configurar y probar el inicio de sesión único de Azure AD con Coupa, es preciso completar los siguientes bloques de creación:

1. **[Configuración del inicio de sesión único de Azure AD](#configure-azure-ad-single-sign-on)**: para permitir que los usuarios utilicen esta característica.
2. **[Creación de un usuario de prueba de Azure AD](#create-an-azure-ad-test-user)**: para probar el inicio de sesión único de Azure AD con Britta Simon.
3. **[Creación de un usuario de prueba de Coupa](#create-a-coupa-test-user)**: para tener un homólogo de Britta Simon en Coupa que esté vinculado a la representación del usuario en Azure AD.
4. **[Asignación del usuario de prueba de Azure AD](#assign-the-azure-ad-test-user)**: para permitir que Britta Simon use el inicio de sesión único de Azure AD.
5. **[Prueba del inicio de sesión único](#test-single-sign-on)**: para comprobar si la configuración funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configuración del inicio de sesión único de Azure AD

En esta sección, habilitará el inicio de sesión único de Azure AD en Azure Portal y lo configurará en la aplicación Coupa.

**Para configurar el inicio de sesión único de Azure AD con Coupa, realice los pasos siguientes:**

1. En Azure Portal, en la página de integración de la aplicación **Coupa**, haga clic en **Inicio de sesión único**.

    ![Vínculo Configurar inicio de sesión único][4]

2. En el cuadro de diálogo **Inicio de sesión único**, en **Modo** seleccione **Inicio de sesión basado en SAML** para habilitar el inicio de sesión único.
 
    ![Cuadro de diálogo Inicio de sesión único](./media/active-directory-saas-coupa-tutorial/tutorial_coupa_samlbase.png)

3. En la sección **Dominio y direcciones URL de Coupa**, lleve a cabo los pasos siguientes:

    ![Información de dominio y direcciones URL de inicio de sesión único de Coupa](./media/active-directory-saas-coupa-tutorial/tutorial_coupa_url.png)

    a. En el cuadro de texto **URL de inicio de sesión**, escriba una dirección URL con el siguiente patrón: `http://<companyname>.Coupa.com`.

    b. En el cuadro de texto **Identificador**, escriba una dirección URL con el siguiente patrón: `<companyname>.coupahost.com`

    c. En el cuadro de texto **URL de respuesta**, escriba una dirección URL con el siguiente patrón: `https://<companyname>.coupahost.com/sp/ACS.saml2`.

    > [!NOTE] 
    > Estos valores no son reales. Actualícelos con la dirección URL de inicio de sesión, el identificador y la dirección URL de respuesta reales. Póngase en contacto con el [equipo de soporte técnico de Coupa](https://success.coupa.com/Support/Contact_Us?) para obtener estos valores. Obtendrá el valor de la dirección URL de respuesta de los metadatos, que se explica más adelante en el tutorial.

4. En la sección **Certificado de firma de SAML**, haga clic en **XML de metadatos** y luego guarde el archivo de metadatos en el equipo.

    ![Vínculo de descarga del certificado](./media/active-directory-saas-coupa-tutorial/tutorial_coupa_certificate.png) 

5. Haga clic en el botón **Guardar** .

    ![Botón Guardar de Configuración de inicio de sesión único](./media/active-directory-saas-coupa-tutorial/tutorial_general_400.png)

6. Inicie sesión en su sitio de la compañía de Coupa como administrador.

7. Vaya a **Configuración \> Control de seguridad**.
   
   ![Controles de seguridad](./media/active-directory-saas-coupa-tutorial/ic791900.png "Controles de seguridad")

8. En la sección **Iniciar sesión con credenciales de Coupa** , realice los pasos siguientes:

    ![Metadatos SP de Coupa](./media/active-directory-saas-coupa-tutorial/ic791901.png "Metadatos SP de Coupa")
    
    a. Seleccione **Iniciar sesión con SAML**.
    
    b. Para descargar el archivo de metadatos de Coupa en el equipo, haga clic en **Descargar e importar metadatos de SP**. Abra los metadatos y copie el valor de **AssertionConsumerService index/URL**, péguelo en el cuadro de texto **Dirección URL de respuesta** de la sección **Dominio y direcciones de Coupa**. 
    
    c. Haga clic en **Browse** (Examinar) para cargar los metadatos de Azure Portal.
    
    d. Haga clic en **Guardar**.

> [!TIP]
> Ahora puede leer una versión resumida de estas instrucciones dentro de [Azure Portal](https://portal.azure.com) mientras configura la aplicación.  Después de agregar esta aplicación desde la sección **Active Directory > Aplicaciones empresariales**, simplemente haga clic en la pestaña **Inicio de sesión único** y acceda a la documentación insertada a través de la sección **Configuración** de la parte inferior. Puede leer más sobre la característica de documentación insertada aquí: [Vista previa: Administración de inicio de sesión único para aplicaciones empresariales en el nuevo Azure Portal]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="create-an-azure-ad-test-user"></a>Creación de un usuario de prueba de Azure AD

El objetivo de esta sección es crear un usuario de prueba en Azure Portal llamado "Britta Simon".

   ![Creación de un usuario de prueba de Azure AD][100]

**Siga estos pasos para crear un usuario de prueba en Azure AD:**

1. En el panel izquierdo de Azure Portal, haga clic en el botón **Azure Active Directory**.

    ![Botón Azure Active Directory](./media/active-directory-saas-coupa-tutorial/create_aaduser_01.png)

2. Para mostrar la lista de usuarios, vaya a **Usuarios y grupos** y, luego, haga clic en **Todos los usuarios**.

    ![Vínculos "Usuarios y grupos" y "Todos los usuarios"](./media/active-directory-saas-coupa-tutorial/create_aaduser_02.png)

3. En la parte superior del cuadro de diálogo **Todos los usuarios**, haga clic en **Agregar** para abrir el cuadro de diálogo **Agregar**.

    ![Botón Agregar](./media/active-directory-saas-coupa-tutorial/create_aaduser_03.png)

4. En el cuadro de diálogo **Usuario** , realice los pasos siguientes:

    ![Cuadro de diálogo Usuario](./media/active-directory-saas-coupa-tutorial/create_aaduser_04.png)

    a. En el cuadro **Nombre**, escriba **BrittaSimon**.

    b. En el cuadro de texto **Nombre de usuario**, escriba la dirección de correo electrónico del usuario Britta Simon.

    c. Active la casilla **Mostrar contraseña** y, después, anote el valor que se muestra en el cuadro **Contraseña**.

    d. Haga clic en **Crear**.
 
### <a name="create-a-coupa-test-user"></a>Creación de un usuario de prueba de Coupa

Para permitir que los usuarios de Azure AD inicien sesión en Coupa, tienen que aprovisionarse en Coupa.  

* En el caso de Coupa, el aprovisionamiento es una tarea manual.

**Siga estos pasos para configurar el aprovisionamiento de usuario:**

1. Inicie sesión como administrador en el sitio de la compañía de **Coupa** .

2. En el menú en la parte superior, haga clic en **Configurar** y, después, en **Usuarios**.
   
   ![Usuarios](./media/active-directory-saas-coupa-tutorial/ic791908.png "Usuarios")

3. Haga clic en **Create**(Crear).
   
   ![Creación de usuarios](./media/active-directory-saas-coupa-tutorial/ic791909.png "Creación de usuarios")

4. En la sección **Creación de usuario** , lleve a cabo estos pasos:
   
   ![Detalles del usuario](./media/active-directory-saas-coupa-tutorial/ic791910.png "Detalles del usuario")
   
   a. En los cuadros de texto relacionados, escriba los atributos **Nombre de usuario**, **Nombre**, **Apellidos**, **Id. de inicio de sesión único**, **Correo electrónico** de una cuenta válida de Azure Active Directory que quiera aprovisionar.

   b. Haga clic en **Crear**.   
   
   >[!NOTE]
   >El titular de la cuenta de Azure Active Directory recibirá un mensaje de correo electrónico con un vínculo para confirmar la cuenta antes de que se active. 
   > 

>[!NOTE]
>Puede usar cualquier otra API o herramienta de creación de cuentas de usuario de Canvas ofrecida por Coupa para aprovisionar cuentas de usuario de AAD. 

### <a name="assign-the-azure-ad-test-user"></a>Asignación del usuario de prueba de Azure AD

En esta sección, habilitará a Britta Simon para que use el inicio de sesión único de Azure concediéndole acceso a Coupa.

![Asignación del rol de usuario][200] 

**Para asignar a Britta Simon a Coupa, realice los pasos siguientes:**

1. En Azure Portal, abra la vista de aplicaciones, vaya a la vista de directorio y vaya a **Aplicaciones empresariales**. Luego, haga clic en **Todas las aplicaciones**.

    ![Asignar usuario][201] 

2. En la lista de aplicaciones, seleccione **Coupa**.

    ![Vínculo a Coupa en la lista de aplicaciones](./media/active-directory-saas-coupa-tutorial/tutorial_coupa_app.png)  

3. En el menú de la izquierda, haga clic en **Usuarios y grupos**.

    ![Vínculo "Usuarios y grupos"][202]

4. Haga clic en el botón **Agregar**. Después, seleccione **Usuarios y grupos** en el cuadro de diálogo **Agregar asignación**.

    ![Panel Agregar asignación][203]

5. En el cuadro de diálogo **Usuarios y grupos**, seleccione **Britta Simon** en la lista de usuarios.

6. Haga clic en el botón **Seleccionar** del cuadro de diálogo **Usuarios y grupos**.

7. Haga clic en el botón **Asignar** del cuadro de diálogo **Agregar asignación**.
    
### <a name="test-single-sign-on"></a>Prueba de inicio de sesión único

En esta sección, probará la configuración de inicio de sesión único de Azure AD mediante el Panel de acceso.

Al hacer clic en el icono de Coupa en el Panel de acceso, debería iniciar sesión automáticamente en su aplicación de Coupa.
Para más información sobre el Panel de acceso, consulte [Introducción al Panel de acceso](active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Recursos adicionales

* [Lista de tutoriales sobre cómo integrar aplicaciones SaaS con Azure Active Directory](active-directory-saas-tutorial-list.md)
* [¿Qué es el acceso a aplicaciones y el inicio de sesión único con Azure Active Directory?](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-coupa-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-coupa-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-coupa-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-coupa-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-coupa-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-coupa-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-coupa-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-coupa-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-coupa-tutorial/tutorial_general_203.png

