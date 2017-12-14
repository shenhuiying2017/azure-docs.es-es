---
title: "Tutorial: Integración de Azure Active Directory con Gigya | Microsoft Doc"
description: "Aprenda a configurar el inicio de sesión único entre Azure Active Directory y Gigya."
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: 2c7d200b-9242-44a5-ac8a-ab3214a78e41
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/18/2017
ms.author: jeedes
ms.openlocfilehash: 74bc67fb90f0505d2e1f213a4a0cdc26ade22872
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-gigya"></a>Tutorial: integración de Azure Active Directory con Gigya

En este tutorial, aprenderá a integrar Gigya con Azure Active Directory (Azure AD).

La integración de Gigya con Azure AD proporciona las siguientes ventajas:

- Puede controlar en Azure AD quién tiene acceso a Gigya
- Puede permitir que los usuarios inicien sesión automáticamente en Gigya (inicio de sesión único) con sus cuentas de Azure AD
- Puede administrar las cuentas en una sola ubicación central: Azure Portal.

Si desea saber más sobre la integración de aplicaciones SaaS con Azure AD, consulte [¿Qué es el acceso a aplicaciones y el inicio de sesión único con Azure Active Directory?](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Requisitos previos

Para configurar la integración de Azure AD con Gigya, se necesitan los siguientes elementos:

- Una suscripción de Azure AD
- Una suscripción habilitada para el inicio de sesión único en Gigya

> [!NOTE]
> Para probar los pasos de este tutorial, no se recomienda el uso de un entorno de producción.

Para probar los pasos de este tutorial, debe seguir estas recomendaciones:

- No use el entorno de producción, salvo que sea necesario.
- Si no dispone de un entorno de prueba de Azure AD, puede obtener una versión de prueba de un mes [aquí](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descripción del escenario
En este tutorial, puede probar el inicio de sesión único de Azure AD en un entorno de prueba. La situación descrita en este tutorial consta de dos bloques de creación principales:

1. Adición de Gigya desde la galería
2. Configuración y comprobación del inicio de sesión único de Azure AD

## <a name="adding-gigya-from-the-gallery"></a>Adición de Gigya desde la galería
Para configurar la integración de Gigya en Azure AD, es preciso agregar Gigya desde la galería a la lista de aplicaciones SaaS administradas.

**Para agregar Gigya desde la galería, siga estos pasos:**

1. En el panel de navegación izquierdo de **[Azure Portal](https://portal.azure.com)**, haga clic en el icono de **Azure Active Directory**. 

    ![Active Directory][1]

2. Vaya a **Aplicaciones empresariales**. A continuación, vaya a **Todas las aplicaciones**.

    ![Aplicaciones][2]
    
3. Para agregar una nueva aplicación, haga clic en el botón **Nueva aplicación** de la parte superior del cuadro de diálogo.

    ![Aplicaciones][3]

4. En el cuadro de búsqueda, escriba **Gigya**.

    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-gigya-tutorial/tutorial_gigya_search.png)

5. En el panel de resultados, seleccione **Gigya** y haga clic en el botón **Agregar** para agregar la aplicación.

    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-gigya-tutorial/tutorial_gigya_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configuración y comprobación del inicio de sesión único de Azure AD
En esta sección, podrá configurar y probar el inicio de sesión único de Azure AD con Gigya con un usuario de prueba llamado "Britta Simon".

Para que el inicio de sesión único funcione, Azure AD debe saber cuál es el usuario homólogo de Gigya para un usuario de Azure AD. Es decir, es necesario establecer una relación de vínculo entre un usuario de Azure AD y el usuario relacionado de Gigya.

Para establecer la relación de vínculo en Gigya, asigne el valor de **nombre de usuario** de Azure AD como valor de **nombre de usuario**.

Para configurar y probar el inicio de sesión único de Azure AD con Gigya, es preciso completar los siguientes bloques de creación:

1. **[Configuración del inicio de sesión único de Azure AD](#configuring-azure-ad-single-sign-on)** : para permitir a los usuarios usar esta característica.
2. **[Creación de un usuario de prueba de Azure AD](#creating-an-azure-ad-test-user)** : para probar el inicio de sesión único de Azure AD con Britta Simon.
3. **[Creación de un usuario de prueba de Gigya](#creating-a-gigya-test-user)**: para tener un homólogo de Britta Simon en Gigya que esté vinculado a la representación de Azure AD de usuario.
4. **[Asignación del usuario de prueba de Azure AD](#assigning-the-azure-ad-test-user)** : para permitir que Britta Simon use el inicio de sesión único de Azure AD.
5. **[Testing Single Sign-On](#testing-single-sign-on)** : para comprobar si funciona la configuración.

### <a name="configuring-azure-ad-single-sign-on"></a>Configuración del inicio de sesión único de Azure AD

En esta sección, habilitará el inicio de sesión único de Azure AD en Azure Portal y configurará el inicio de sesión único en la aplicación Gigya.

**Para configurar el inicio de sesión único de Azure AD con Gigya, siga estos pasos:**

1. En Azure Portal, en la página de integración de la aplicación **Gigya**, haga clic en **Inicio de sesión único**.

    ![Configurar inicio de sesión único][4]

2. En el cuadro de diálogo **Inicio de sesión único**, en **Modo** seleccione **Inicio de sesión basado en SAML** para habilitar el inicio de sesión único.
 
    ![Configurar inicio de sesión único](./media/active-directory-saas-gigya-tutorial/tutorial_gigya_samlbase.png)

3. En la sección **Dominio y direcciones URL de Gigya**, siga estos pasos:

    ![Configurar inicio de sesión único](./media/active-directory-saas-gigya-tutorial/tutorial_gigya_url.png)

    a. En el cuadro de texto **URL de inicio de sesión**, escriba una dirección URL con el siguiente patrón: `http://<companyname>.gigya.com`.

    b. En el cuadro de texto **Identificador**, escriba una dirección URL con el siguiente patrón: `https://fidm.gigya.com/saml/v2.0/<companyname>`

    > [!NOTE] 
    > Estos valores no son reales. Debe actualizarlos con la dirección URL y el identificador reales de inicio de sesión. Para obtener estos valores, póngase en contacto con el [equipo de soporte técnico de clientes de Gigya](https://www.gigya.com/support-policy/). 
 
4. En la sección **Certificado de firma de SAML**, haga clic en **Certificado (Base64)** y, luego, guarde el archivo de certificado en el equipo.

    ![Configurar inicio de sesión único](./media/active-directory-saas-gigya-tutorial/tutorial_gigya_certificate.png) 

5. Haga clic en el botón **Guardar** .

    ![Configurar inicio de sesión único](./media/active-directory-saas-gigya-tutorial/tutorial_general_400.png)

6. En la sección **Configuración de Gigya**, haga clic en **Configurar Gigya** para abrir la ventana **Configurar inicio de sesión**. Copie los valores de **SAML Entity ID y SAML Single Sign-On Service URL** (Identificador de entidad de SAML y URL del servicio de inicio de sesión único de SAML) de la sección de **referencia rápida**.

    ![Configurar inicio de sesión único](./media/active-directory-saas-gigya-tutorial/tutorial_gigya_configure.png) 

7. En otra ventana del explorador web, inicie sesión en el sitio de la compañía de Gigya como administrador.

8. Vaya **Settings \> SAML Login** (Configuración > Inicio de sesión de SAML), y luego haga clic en el botón **Add** (Agregar).
   
    ![Inicio de sesión SAML](./media/active-directory-saas-gigya-tutorial/ic789532.png "Inicio de sesión SAML")

9. En la sección **Inicio de sesión de SAML** , realice los pasos siguientes:
   
    ![Configuración de SAML](./media/active-directory-saas-gigya-tutorial/ic789533.png "Configuración de SAML")
   
    a. En el cuadro de texto **Name** (Nombre), escriba el nombre de la configuración.
   
    b. En el cuadro de texto **Emisor**, pegue el valor de **SAML Entity ID** (Identificador de entidad de SAML) que ha copiado de Azure Portal. 
   
    c. En el cuadro de texto **Single Sign-On Service URL** (Dirección URL del servicio de inicio de sesión único), pegue el valor de **Dirección URL del servicio de inicio de sesión único** que ha copiado de Azure Portal.
   
    d. En el cuadro de texto **Name ID Format** (Formato de id. de nombre), pegue el valor de **Formato de identificador de nombre** que haya copiado de Azure Portal.
   
    e. Abra el certificado codificado en base 64 descargado de Azure Portal en el Bloc de notas, copie su contenido en el Portapapeles y luego péguelo en el cuadro de texto **Certificado X.509**.
   
    f. Haga clic en **Guardar configuración**.

> [!TIP]
> Ahora puede leer una versión resumida de estas instrucciones dentro de [Azure Portal](https://portal.azure.com) mientras configura la aplicación.  Después de agregar esta aplicación desde la sección **Active Directory > Aplicaciones empresariales**, simplemente haga clic en la pestaña **Inicio de sesión único** y acceda a la documentación insertada a través de la sección **Configuración** de la parte inferior. Puede leer más sobre la característica de documentación insertada aquí: [Vista previa: Administración de inicio de sesión único para aplicaciones empresariales en el nuevo Azure Portal]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>Creación de un usuario de prueba de Azure AD

El objetivo de esta sección es crear un usuario de prueba en Azure Portal llamado "Britta Simon".

![Creación de un usuario de Azure AD][100]

**Siga estos pasos para crear un usuario de prueba en Azure AD:**

1. En el panel de navegación izquierdo de **Azure Portal**, haga clic en el icono de **Azure Active Directory**.

    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-gigya-tutorial/create_aaduser_01.png) 

2. Para mostrar la lista de usuarios, vaya a **Usuarios y grupos** y haga clic en **Todos los usuarios**.
    
    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-gigya-tutorial/create_aaduser_02.png) 

3. Para abrir el cuadro de diálogo **Usuario**, haga clic en **Agregar** en la parte superior del cuadro de diálogo.
 
    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-gigya-tutorial/create_aaduser_03.png) 

4. En la página de diálogo **Usuario**, realice los siguientes pasos:
 
    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-gigya-tutorial/create_aaduser_04.png) 

    a. En el cuadro de texto **Nombre**, escriba **BrittaSimon**.

    b. En el cuadro de texto **Nombre de usuario**, escriba la **dirección de correo electrónico** de Britta Simon.

    c. Seleccione **Mostrar contraseña** y anote el valor del cuadro **Contraseña**.

    d. Haga clic en **Crear**.
 
### <a name="creating-a-gigya-test-user"></a>Creación de usuario de prueba de Gigya

Para permitir que los usuarios de Azure AD inicien sesión en Gigya, deben aprovisionarse en Gigya.  
En el caso de Gigya, el aprovisionamiento es una tarea manual.

### <a name="to-provision-a-user-accounts-perform-the-following-steps"></a>Para aprovisionar cuentas de usuario, realice estos pasos:

1. Inicie sesión en el sitio de la compañía de **Gigya** como administrador.

2. Vaya a **Admin \> Manage Users** (Administrador > Administrar usuarios) y, a continuación, haga clic en **Invite Users** (Invitar a usuarios).
   
    ![Administración de usuarios](./media/active-directory-saas-gigya-tutorial/ic789535.png "Administración de usuarios")

3. En el cuadro de diálogo Invite Users (Invitar a usuarios), realice los pasos siguientes:
   
    ![Invitar a usuarios](./media/active-directory-saas-gigya-tutorial/ic789536.png "Invitar a usuarios")
   
    a. En el cuadro de texto **Correo electrónico** , escriba el alias de correo electrónico de la cuenta válida de Azure Active Directory que quiera aprovisionar.
    
    b. Haga clic en **Invitar a usuario**.
      
    > [!NOTE]
    > El titular de la cuenta de Azure Active Directory recibirá un mensaje de correo electrónico con un vínculo para confirmar la cuenta antes de que se active.
    > 
    

### <a name="assigning-the-azure-ad-test-user"></a>Asignación del usuario de prueba de Azure AD

En esta sección, habilitará a Britta Simon para que use el inicio de sesión único de Azure concediéndole acceso a Gigya.

![Asignar usuario][200] 

**Para asignar el usuario Britta Simon a Gigya, siga estos pasos:**

1. En Azure Portal, abra la vista de aplicaciones, navegue a la vista de directorio y vaya a **Aplicaciones empresariales**. Luego haga clic en **Todas las aplicaciones**.

    ![Asignar usuario][201] 

2. En la lista de aplicaciones, seleccione **Gigya**.

    ![Configurar inicio de sesión único](./media/active-directory-saas-gigya-tutorial/tutorial_gigya_app.png) 

3. En el menú de la izquierda, haga clic en **Usuarios y grupos**.

    ![Asignar usuario][202] 

4. Haga clic en el botón **Agregar**. Después, seleccione **Usuarios y grupos** en el cuadro de diálogo **Agregar asignación**.

    ![Asignar usuario][203]

5. En el cuadro de diálogo **Usuarios y grupos**, seleccione **Britta Simon** en la lista de usuarios.

6. Haga clic en el botón **Seleccionar** del cuadro de diálogo **Usuarios y grupos**.

7. Haga clic en el botón **Asignar** del cuadro de diálogo **Agregar asignación**.
    
### <a name="testing-single-sign-on"></a>Prueba del inicio de sesión único 

El objetivo de esta sección es probar la configuración del inicio de sesión único de Azure AD mediante el panel de acceso.

Al hacer clic en el icono de Gigya en el panel de acceso, debería iniciar sesión automáticamente en su aplicación Gigya.

## <a name="additional-resources"></a>Recursos adicionales

* [Lista de tutoriales sobre cómo integrar aplicaciones SaaS con Azure Active Directory](active-directory-saas-tutorial-list.md)
* [¿Qué es el acceso a aplicaciones y el inicio de sesión único con Azure Active Directory?](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-gigya-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-gigya-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-gigya-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-gigya-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-gigya-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-gigya-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-gigya-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-gigya-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-gigya-tutorial/tutorial_general_203.png

