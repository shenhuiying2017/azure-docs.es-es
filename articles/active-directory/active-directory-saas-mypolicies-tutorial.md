---
title: "Tutorial: Integración de Azure Active Directory con myPolicies | Microsoft Docs"
description: "Aprenda a configurar el inicio de sesión único entre Azure Active Directory y myPolicies."
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: bf79e858-1dfb-4ab3-a6df-74b2d5a878d2
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/04/2017
ms.author: jeedes
ms.openlocfilehash: 39c2acb24f1c15af9ab0c8698e9590fb0e032113
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-mypolicies"></a>Tutorial: Integración de Azure Active Directory con myPolicies

En este tutorial, obtendrá información sobre cómo integrar myPolicies con Azure Active Directory (Azure AD).

La integración de myPolicies con Azure AD proporciona las siguientes ventajas:

- Puede controlar en Azure AD quién tiene acceso a myPolicies
- Puede permitir que los usuarios inicien sesión automáticamente en myPolicies (inicio de sesión único) con sus cuentas de Azure AD
- Puede administrar sus cuentas en una ubicación central: el nuevo Azure Portal.

Si desea saber más sobre la integración de aplicaciones SaaS con Azure AD, consulte [¿Qué es el acceso a aplicaciones y el inicio de sesión único con Azure Active Directory?](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Requisitos previos

Para configurar la integración de Azure AD con myPolicies, necesita los siguientes elementos:

- Una suscripción de Azure AD
- Una suscripción habilitada para el inicio de sesión único en myPolicies

> [!NOTE]
> Para probar los pasos de este tutorial, no se recomienda el uso de un entorno de producción.

Para probar los pasos de este tutorial, debe seguir estas recomendaciones:

- No use el entorno de producción, salvo que sea necesario.
- Si no dispone de un entorno de prueba de Azure AD, puede obtener una versión de prueba de un mes [aquí](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descripción del escenario
En este tutorial, puede probar el inicio de sesión único de Azure AD en un entorno de prueba. La situación descrita en este tutorial consta de dos bloques de creación principales:

1. Agregación de myPolicies desde la galería
2. Configuración y comprobación del inicio de sesión único de Azure AD

## <a name="adding-mypolicies-from-the-gallery"></a>Agregación de myPolicies desde la galería
Para configurar la integración de myPolicies en Azure AD, será preciso que agregue myPolicies desde la galería a la lista de aplicaciones SaaS administradas.

**Para agregar myPolicies desde la galería, realice los pasos siguientes:**

1. En el panel de navegación izquierdo de **[Azure Portal](https://portal.azure.com)**, haga clic en el icono de **Azure Active Directory**. 

    ![Active Directory][1]

2. Vaya a **Aplicaciones empresariales**. A continuación, vaya a **Todas las aplicaciones**.

    ![Aplicaciones][2]
    
3. Para agregar una nueva aplicación, haga clic en el botón **Nueva aplicación** de la parte superior del cuadro de diálogo.

    ![Aplicaciones][3]

4. En el cuadro de búsqueda, escriba **myPolicies**.

    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-mypolicies-tutorial/tutorial_mypolicies_search.png)

5. En el panel de resultados, seleccione **myPolicies** y luego haga clic en el botón **Agregar** para agregar la aplicación.

    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-mypolicies-tutorial/tutorial_mypolicies_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configuración y comprobación del inicio de sesión único de Azure AD
En esta sección, se configura y se prueba el inicio de sesión único de Azure AD con myPolicies con un usuario de prueba llamado "Britta Simon".

Para que el inicio de sesión único funcione, Azure AD debe saber cuál es el usuario homólogo de myPolicies para un usuario de Azure AD. Es decir, es necesario establecer una relación de vínculo entre un usuario de Azure AD y el usuario relacionado de myPolicies.

Para establecer la relación de vínculo, en myPolicies, asigne el valor de **nombre de usuario** de Azure AD como valor de **Nombre de usuario**.

Para configurar y probar el inicio de sesión único de Azure AD con myPolicies, es preciso completar los siguientes bloques de creación:

1. **[Configuración del inicio de sesión único de Azure AD](#configuring-azure-ad-single-sign-on)** : para permitir a los usuarios usar esta característica.
2. **[Creación de un usuario de prueba de Azure AD](#creating-an-azure-ad-test-user)** : para probar el inicio de sesión único de Azure AD con Britta Simon.
3. **[Creación de un usuario de prueba de myPolicies](#creating-a-mypolicies-test-user)**: el objetivo es tener un homólogo de Britta Simon en myPolicies que esté vinculado a la representación del usuario en Azure AD.
4. **[Asignación del usuario de prueba de Azure AD](#assigning-the-azure-ad-test-user)** : para permitir que Britta Simon use el inicio de sesión único de Azure AD.
5. **[Testing Single Sign-On](#testing-single-sign-on)** : para comprobar si funciona la configuración.

### <a name="configuring-azure-ad-single-sign-on"></a>Configuración del inicio de sesión único de Azure AD

En esta sección, habilitará el inicio de sesión único de Azure AD en Azure Portal y lo configurará en la aplicación myPolicies.

**Para configurar el inicio de sesión único de Azure AD con myPolicies, realice los pasos siguientes:**

1. En Azure Portal, en la página de integración de la aplicación **myPolicies**, haga clic en **Inicio de sesión único**.

    ![Configurar inicio de sesión único][4]

2. En el cuadro de diálogo **Inicio de sesión único**, en **Modo** seleccione **Inicio de sesión basado en SAML** para habilitar el inicio de sesión único.
 
    ![Configurar inicio de sesión único](./media/active-directory-saas-mypolicies-tutorial/tutorial_mypolicies_samlbase.png)

3. En la sección **Dominio y direcciones URL de myPolicies**, lleve a cabo los pasos siguientes:

    ![Configurar inicio de sesión único](./media/active-directory-saas-mypolicies-tutorial/tutorial_mypolicies_url.png)

    a. En el cuadro de texto **Identificador**, escriba una dirección URL con el siguiente patrón: `https://<tenantname>.mypolicies.com/`

    b. En el cuadro de texto **URL de respuesta**, escriba una dirección URL con el siguiente patrón: `https://<tenantname>.mypolicies.com/users/auth/saml/callback`.

    > [!NOTE] 
    > Estos valores no son reales. Actualice estos valores con el identificador y la URL de respuesta reales. Póngase en contacto con el [equipo de soporte técnico de myPolicies](mailto:support@mypolicies.com) para obtener estos valores.

4. La aplicación myPolicies espera las aserciones de SAML en un formato específico, que requiere que se agreguen asignaciones de atributos personalizados a la configuración de los atributos del token SAML. Configure las siguientes notificaciones para esta aplicación. Puede administrar los valores de estos atributos en la sección "**Atributos de usuario**" de la página de integración de aplicaciones. La siguiente captura de pantalla le muestra un ejemplo de esto. 

    ![Configurar inicio de sesión único](./media/active-directory-saas-mypolicies-tutorial/tutorial_mypolicies_attribute.png)

5. En la sección **Atributos de usuario**, active la casilla **Ver y editar todos los demás atributos de usuario** para expandir los atributos. Realice los pasos siguientes en cada uno de los atributos mostrados:

    | Nombre del atributo | Valor de atributo |
    | ------------------- | ---------- |
    | givenname | user.givenname |
    | surname | user.surname |
    | emailaddress | user.mail |
    | name | user.userprincipalname |
    
    a. Haga clic en el atributo para abrir el cuadro de diálogo **Editar atributo**.
    
    ![Configurar inicio de sesión único](./media/active-directory-saas-mypolicies-tutorial/tutorial_attribute_05.png)
    
    b. Elimine el valor de la dirección URL en **Espacio de nombres**.
    
    c. Haga clic en **Aceptar** para guardar la configuración.
    
6. En la sección **Certificado de firma de SAML**, haga clic en **Certificado (Base64)** y, luego, guarde el archivo de certificado en el equipo.

    ![Configurar inicio de sesión único](./media/active-directory-saas-mypolicies-tutorial/tutorial_mypolicies_certificate.png) 

7. Haga clic en el botón **Guardar** .

    ![Configurar inicio de sesión único](./media/active-directory-saas-mypolicies-tutorial/tutorial_general_400.png)

8. En la sección **Configuración de myPolicies**, haga clic en **Configurar myPolicies** para abrir la ventana **Configurar inicio de sesión**. Copie la **dirección URL de servicio de inicio de sesión único de SAML** de la sección **Referencia rápida**.

    ![Configurar inicio de sesión único](./media/active-directory-saas-mypolicies-tutorial/tutorial_mypolicies_configure.png) 

9. Para configurar el inicio de sesión único en **myPolicies**, es preciso enviar el **certificado (Base64)** descargado y la **dirección URL del servicio de inicio de sesión único de SAML** al [equipo de soporte técnico de myPolicies](mailto:support@mypolicies.com). 

> [!TIP]
> Ahora puede leer una versión resumida de estas instrucciones dentro de [Azure Portal](https://portal.azure.com) mientras configura la aplicación.  Después de agregar esta aplicación desde la sección **Active Directory > Aplicaciones empresariales**, simplemente haga clic en la pestaña **Inicio de sesión único** y acceda a la documentación insertada a través de la sección **Configuración** de la parte inferior. Puede leer más sobre la característica de documentación insertada aquí: [Vista previa: Administración de inicio de sesión único para aplicaciones empresariales en el nuevo Azure Portal]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>Creación de un usuario de prueba de Azure AD
El objetivo de esta sección es crear un usuario de prueba en Azure Portal llamado "Britta Simon".

![Creación de un usuario de Azure AD][100]

**Siga estos pasos para crear un usuario de prueba en Azure AD:**

1. En el panel de navegación izquierdo de **Azure Portal**, haga clic en el icono de **Azure Active Directory**.

    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-mypolicies-tutorial/create_aaduser_01.png) 

2. Para mostrar la lista de usuarios, vaya a **Usuarios y grupos** y haga clic en **Todos los usuarios**.
    
    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-mypolicies-tutorial/create_aaduser_02.png) 

3. Para abrir el cuadro de diálogo **Usuario**, haga clic en **Agregar** en la parte superior del cuadro de diálogo.
 
    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-mypolicies-tutorial/create_aaduser_03.png) 

4. En la página de diálogo **Usuario**, realice los siguientes pasos:
 
    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-mypolicies-tutorial/create_aaduser_04.png) 

    a. En el cuadro de texto **Nombre**, escriba **BrittaSimon**.

    b. En el cuadro de texto **Nombre de usuario**, escriba la **dirección de correo electrónico** de Britta Simon.

    c. Seleccione **Mostrar contraseña** y anote el valor del cuadro **Contraseña**.

    d. Haga clic en **Crear**.
 
### <a name="creating-a-mypolicies-test-user"></a>Creación de un usuario de prueba de myPolicies

En esta sección, se crea un usuario denominado Britta Simon en myPolicies. Trabaje con el [equipo de soporte técnico de myPolicies](mailto:support@mypolicies.com) para agregar los usuarios a la plataforma de myPolicies. Los usuarios se tienen que crear y activar antes de usar el inicio de sesión único.

### <a name="assigning-the-azure-ad-test-user"></a>Asignación del usuario de prueba de Azure AD

En esta sección, habilitará a Britta Simon para que use el inicio de sesión único de Azure concediéndole acceso a myPolicies.

![Asignar usuario][200] 

**Para asignar a Britta Simon a myPolicies, siga estos pasos:**

1. En Azure Portal, abra la vista de aplicaciones, vaya a la vista de directorio y vaya a **Aplicaciones empresariales**. Luego, haga clic en **Todas las aplicaciones**.

    ![Asignar usuario][201] 

2. En la lista de aplicaciones, seleccione **myPolicies**.

    ![Configurar inicio de sesión único](./media/active-directory-saas-mypolicies-tutorial/tutorial_mypolicies_app.png) 

3. En el menú de la izquierda, haga clic en **Usuarios y grupos**.

    ![Asignar usuario][202] 

4. Haga clic en el botón **Agregar**. Después, seleccione **Usuarios y grupos** en el cuadro de diálogo **Agregar asignación**.

    ![Asignar usuario][203]

5. En el cuadro de diálogo **Usuarios y grupos**, seleccione **Britta Simon** en la lista de usuarios.

6. Haga clic en el botón **Seleccionar** del cuadro de diálogo **Usuarios y grupos**.

7. Haga clic en el botón **Asignar** del cuadro de diálogo **Agregar asignación**.
    
### <a name="testing-single-sign-on"></a>Prueba del inicio de sesión único 

En esta sección, probará la configuración de inicio de sesión único de Azure AD mediante el Panel de acceso.

Al hacer clic en el icono de myPolicies en el panel de acceso, debe iniciar sesión automáticamente en su aplicación de myPolicies.
Para más información sobre el Panel de acceso, consulte [Introducción al Panel de acceso](active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Recursos adicionales

* [Lista de tutoriales sobre cómo integrar aplicaciones SaaS con Azure Active Directory](active-directory-saas-tutorial-list.md)
* [¿Qué es el acceso a aplicaciones y el inicio de sesión único con Azure Active Directory?](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-mypolicies-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-mypolicies-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-mypolicies-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-mypolicies-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-mypolicies-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-mypolicies-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-mypolicies-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-mypolicies-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-mypolicies-tutorial/tutorial_general_203.png

