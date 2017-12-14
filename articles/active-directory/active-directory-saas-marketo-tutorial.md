---
title: "Tutorial: Integración de Azure Active Directory con Marketo | Microsoft Docs"
description: "Aprenda a configurar el inicio de sesión único entre Azure Active Directory y Marketo."
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: b88c45f5-d288-4717-835c-ca965add8735
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/09/2017
ms.author: jeedes
ms.openlocfilehash: 10ce59668127ff918a85caf537e8ab495c542278
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-marketo"></a>Tutorial: integración de Azure Active Directory con Marketo

En este tutorial, aprenderá a integrar Marketo con Azure Active Directory (Azure AD).

Integrar Marketo con Azure AD proporciona las siguientes ventajas:

- Puede controlar en Azure AD quién tiene acceso a Marketo.
- Puede permitir que los usuarios inicien sesión automáticamente en Marketo (inicio de sesión único) con sus cuentas de Azure AD.
- Puede administrar las cuentas en una sola ubicación central: Azure Portal.

Si desea saber más sobre la integración de aplicaciones SaaS con Azure AD, consulte [¿Qué es el acceso a aplicaciones y el inicio de sesión único con Azure Active Directory?](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Requisitos previos

Para configurar la integración de Azure AD con Marketo, necesita los siguientes elementos:

- Una suscripción de Azure AD
- Una suscripción habilitada para el inicio de sesión único en Marketo

> [!NOTE]
> Para probar los pasos de este tutorial, no se recomienda el uso de un entorno de producción.

Para probar los pasos de este tutorial, debe seguir estas recomendaciones:

- No use el entorno de producción, salvo que sea necesario.
- Si no dispone de un entorno de prueba de Azure AD, puede obtener una versión de prueba de un mes [aquí](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descripción del escenario
En este tutorial, puede probar el inicio de sesión único de Azure AD en un entorno de prueba. La situación descrita en este tutorial consta de dos bloques de creación principales:

1. Incorporación de Marketo desde la galería
2. Configuración y comprobación del inicio de sesión único de Azure AD

## <a name="adding-marketo-from-the-gallery"></a>Incorporación de Marketo desde la galería
Para configurar la integración de Marketo en Azure AD, deberá agregar Marketo desde la galería a la lista de aplicaciones SaaS administradas.

**Para agregar Marketo desde la galería, realice los pasos siguientes:**

1. En el panel de navegación izquierdo de **[Azure Portal](https://portal.azure.com)**, haga clic en el icono de **Azure Active Directory**. 

    ![Active Directory][1]

2. Vaya a **Aplicaciones empresariales**. A continuación, vaya a **Todas las aplicaciones**.

    ![Aplicaciones][2]
    
3. Para agregar una nueva aplicación, haga clic en el botón **Nueva aplicación** de la parte superior del cuadro de diálogo.

    ![Aplicaciones][3]

4. En el cuadro de búsqueda, escriba **Marketo**.

    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-marketo-tutorial/tutorial_marketo_search.png)

5. En el panel de resultados, seleccione **Marketo** y luego haga clic en el botón **Agregar** para agregar la aplicación.

    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-marketo-tutorial/tutorial_marketo_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configuración y comprobación del inicio de sesión único de Azure AD
En esta sección, configurará y probará el inicio de sesión único de Azure AD con Marketo con un usuario de prueba llamado "Britta Simon".

Para que el inicio de sesión único funcione, Azure AD debe saber cuál es el usuario homólogo de Marketo para un usuario de Azure AD. Es decir, es necesario establecer una relación de vínculo entre un usuario de Azure AD y el usuario correspondiente de Marketo.

Para establecer la relación de vínculo, en Marketo, asigne el valor de **nombre de usuario** de Azure AD como valor de **nombre de usuario**.

Para configurar y probar el inicio de sesión único de Azure AD con Marketo, es preciso completar los siguientes bloques de creación:

1. **[Configuring Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)** : para permitir a los usuarios usar esta característica.
2. **[Creación de un usuario de prueba de Azure AD](#creating-an-azure-ad-test-user)** : para probar el inicio de sesión único de Azure AD con Britta Simon.
3. **[Creación de un usuario de prueba de Marketo](#creating-a-marketo-test-user)**: el objetivo es tener un homólogo de Britta Simon en Marketo que esté vinculado a la representación del usuario en Azure AD.
4. **[Asignación del usuario de prueba de Azure AD](#assigning-the-azure-ad-test-user)** : para permitir que Britta Simon use el inicio de sesión único de Azure AD.
5. **[Testing Single Sign-On](#testing-single-sign-on)** : para comprobar si funciona la configuración.

### <a name="configuring-azure-ad-single-sign-on"></a>Configuración del inicio de sesión único de Azure AD

En esta sección, habilitará el inicio de sesión único de Azure AD en Azure Portal y lo configurará en la aplicación Marketo.

**Para configurar el inicio de sesión único de Azure AD con Marketo, realice los pasos siguientes:**

1. En la página de integración de la aplicación **Marketo** de Azure Portal, haga clic en **Inicio de sesión único**.

    ![Configurar inicio de sesión único][4]

2. En el cuadro de diálogo **Inicio de sesión único**, en **Modo** seleccione **Inicio de sesión basado en SAML** para habilitar el inicio de sesión único.
 
    ![Configurar inicio de sesión único](./media/active-directory-saas-marketo-tutorial/tutorial_marketo_samlbase.png)

3. En la sección **Dominio y direcciones URL de Marketo**, lleve a cabo los pasos siguientes:

    ![Configurar inicio de sesión único](./media/active-directory-saas-marketo-tutorial/tutorial_marketo_url.png)

    a. En el cuadro de texto **Identificador**, escriba una dirección URL con el siguiente patrón: `https://saml.marketo.com/sp`

    b. En el cuadro de texto **URL de respuesta**, escriba una dirección URL con el siguiente patrón: `https://login.marketo.com/saml/assertion/\<munchkinid\>`.

    > [!NOTE] 
    > Estos valores no son reales. Actualice estos valores con el identificador y la URL de respuesta reales. Póngase en contacto con el [equipo de soporte técnico de Marketo](http://investors.marketo.com/contactus.cfm) para obtener estos valores.
 
4. En la sección **Certificado de firma de SAML**, haga clic en **Certificado (Base64)** y, luego, guarde el archivo de certificado en el equipo.

    ![Configurar inicio de sesión único](./media/active-directory-saas-marketo-tutorial/tutorial_marketo_certificate.png) 

5. Haga clic en el botón **Guardar** .

    ![Configurar inicio de sesión único](./media/active-directory-saas-marketo-tutorial/tutorial_general_400.png)

6. En la sección **Configuración de Marketo**, haga clic en **Configurar Marketo** para abrir la ventana **Configurar inicio de sesión**. Copie la **URL del servicio de inicio de sesión único de SAML, el identificador de entidad de SAML y la dirección URL de cierre de sesión** de la sección **Referencia rápida**.

    ![Configurar inicio de sesión único](./media/active-directory-saas-marketo-tutorial/tutorial_marketo_configure.png) 

7. Para obtener el identificador de Munchkin de la aplicación, inicie sesión con credenciales de administrador de Marketo y realizar las siguientes acciones:
   
    a. Inicie sesión en la aplicación Marketo con credenciales de administrador.
   
    b. Haga clic en el botón **Administrador** en el panel de navegación superior.
   
    ![Configurar inicio de sesión único](./media/active-directory-saas-marketo-tutorial/tutorial_marketo_06.png) 
   
    c. Vaya al menú Integración y haga clic en el **vínculo de Munchkin**.
   
    ![Configurar inicio de sesión único](./media/active-directory-saas-marketo-tutorial/tutorial_marketo_11.png)
   
    d. Copie el identificador de Munchkin mostrado en la pantalla y complete la dirección URL de respuesta en el asistente para configuración de Azure AD.
   
    ![Configurar inicio de sesión único](./media/active-directory-saas-marketo-tutorial/tutorial_marketo_12.png) 

8. Para configurar el inicio de sesión único en la aplicación, siga estos pasos:
   
    a. Inicie sesión en la aplicación Marketo con credenciales de administrador.
   
    b. Haga clic en el botón **Administrador** en el panel de navegación superior.
   
    ![Configurar inicio de sesión único](./media/active-directory-saas-marketo-tutorial/tutorial_marketo_06.png) 
   
    c. Vaya al menú Integración y haga clic en **Inicio de sesión único**.
   
    ![Configurar inicio de sesión único](./media/active-directory-saas-marketo-tutorial/tutorial_marketo_07.png) 
   
    d. Para habilitar la configuración de SAML, haga clic en el botón **Editar**.
   
    ![Configurar inicio de sesión único](./media/active-directory-saas-marketo-tutorial/tutorial_marketo_08.png) 
   
    e. Ya está la configuración de inicio de sesión único **habilitada**.
   
    f. Pegue el **id. de entidad de SAML** en el cuadro de texto **Issuer ID** (Id. del emisor).
   
    g. En el cuadro de texto **Entity ID** (Id. de entidad), escriba la dirección URL como `http://saml.marketo.com/sp`.
   
    h. Seleccione la ubicación del identificador de usuario como **elemento Identificador de nombre**.
   
    ![Configurar inicio de sesión único](./media/active-directory-saas-marketo-tutorial/tutorial_marketo_09.png)
   
    > [!NOTE]
    > Si su identificador de usuario no es el valor UPN, cambie el valor en la pestaña Atributo.
   
    i. Cargue el certificado que ha descargado del Asistente para la configuración de Azure AD. **Guarde** la configuración.
   
    j. Edite la configuración delas páginas de redireccionamiento.
   
    k. Pegue la **dirección URL del servicio de inicio de sesión único de SAML** en el cuadro de texto **Login URL** (URL de inicio de sesión).
   
    l. Pegue la **dirección URL de cierre de sesión** en el cuadro de texto **Logout URL** (URL de cierre de sesión).
   
    m. En **URL del error**, copie la **dirección URL de la instancia de Marketo** y haga clic en el botón **Guardar** para guardar la configuración.
   
    ![Configurar inicio de sesión único](./media/active-directory-saas-marketo-tutorial/tutorial_marketo_10.png)

9. Para habilitar el SSO para los usuarios, complete las siguientes acciones:
   
    a. Inicie sesión en la aplicación Marketo con credenciales de administrador.
   
    b. Haga clic en el botón **Administrador** en el panel de navegación superior.
   
    ![Configurar inicio de sesión único](./media/active-directory-saas-marketo-tutorial/tutorial_marketo_06.png) 
   
    c. Desplácese hasta el menú **Seguridad** y haga clic en **Login Settings** (Configuración de inicio de sesión).
   
    ![Configurar inicio de sesión único](./media/active-directory-saas-marketo-tutorial/tutorial_marketo_13.png)
   
    d. Active la opción **Require SSO** (Requerir SSO) y **guarde** la configuración.
   
    ![Configurar inicio de sesión único](./media/active-directory-saas-marketo-tutorial/tutorial_marketo_14.png)

> [!TIP]
> Ahora puede leer una versión concisa de estas instrucciones en [Azure Portal](https://portal.azure.com) mientras configura la aplicación.  Después de agregar esta aplicación desde la sección **Active Directory > Aplicaciones empresariales**, simplemente haga clic en la pestaña **Inicio de sesión único** y acceda a la documentación insertada a través de la sección **Configuración** de la parte inferior. Puede leer más sobre la característica de documentación insertada aquí: [Vista previa: Administración de inicio de sesión único para aplicaciones empresariales en el nuevo Azure Portal]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>Creación de un usuario de prueba de Azure AD
El objetivo de esta sección es crear un usuario de prueba en Azure Portal llamado "Britta Simon".

![Creación de un usuario de Azure AD][100]

**Siga estos pasos para crear un usuario de prueba en Azure AD:**

1. En el panel de navegación izquierdo de **Azure Portal**, haga clic en el icono de **Azure Active Directory**.

    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-marketo-tutorial/create_aaduser_01.png) 

2. Para mostrar la lista de usuarios, vaya a **Usuarios y grupos** y haga clic en **Todos los usuarios**.
    
    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-marketo-tutorial/create_aaduser_02.png) 

3. Para abrir el cuadro de diálogo **Usuario**, haga clic en **Agregar** en la parte superior del cuadro de diálogo.
 
    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-marketo-tutorial/create_aaduser_03.png) 

4. En la página de diálogo **Usuario**, realice los siguientes pasos:
 
    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-marketo-tutorial/create_aaduser_04.png) 

    a. En el cuadro de texto **Nombre**, escriba **BrittaSimon**.

    b. En el cuadro de texto **Nombre de usuario**, escriba la **dirección de correo electrónico** de Britta Simon.

    c. Seleccione **Mostrar contraseña** y anote el valor del cuadro **Contraseña**.

    d. Haga clic en **Crear**.
 
### <a name="creating-a-marketo-test-user"></a>Creación de un usuario de prueba de Marketo

En esta sección, creará un usuario llamado Britta Simon en Marketo. Siga estos pasos para crear un usuario en la plataforma Marketo.

1. Inicie sesión en la aplicación Marketo con credenciales de administrador.

2. Haga clic en el botón **Administrador** en el panel de navegación superior.
   
    ![Configurar inicio de sesión único](./media/active-directory-saas-marketo-tutorial/tutorial_marketo_06.png) 

3. Desplácese hasta el menú **Security** (Seguridad) y haga clic en **Users & Roles** (Usuarios y roles).
   
    ![Configurar inicio de sesión único](./media/active-directory-saas-marketo-tutorial/tutorial_marketo_19.png)  

4. Haga clic en el vínculo **Invite New User** (Invitar a un usuario nuevo) en la pestaña Users (Usuarios).
   
    ![Configurar inicio de sesión único](./media/active-directory-saas-marketo-tutorial/tutorial_marketo_15.png) 

5. En el asistente Invitar a un usuario nuevo, rellene la siguiente información.
   
    a. Especifique la dirección de **Correo electrónico** del usuario en el cuadro de texto.
   
    ![Configurar inicio de sesión único](./media/active-directory-saas-marketo-tutorial/tutorial_marketo_16.png)
   
    b. Escriba el **Nombre** en el cuadro de texto.
   
    c. Escriba los **Apellidos** en el cuadro de texto.
   
    d. Haga clic en **Siguiente**

6. En la pestaña **Permissions** (Permisos) seleccione los **roles de usuario** y haga clic en **Next** (Siguiente).
   
    ![Configurar inicio de sesión único](./media/active-directory-saas-marketo-tutorial/tutorial_marketo_17.png)
7. Haga clic en el botón **Send** (Enviar) para enviar la invitación del usuario.
   
    ![Configurar inicio de sesión único](./media/active-directory-saas-marketo-tutorial/tutorial_marketo_18.png)

8. El usuario recibe la notificación de correo electrónico y tiene que hacer clic en el vínculo y cambiar la contraseña para activar la cuenta. 

### <a name="assigning-the-azure-ad-test-user"></a>Asignación del usuario de prueba de Azure AD

En esta sección, habilitará a Britta Simon para que use el inicio de sesión único de Azure concediéndole acceso a Marketo.

![Asignar usuario][200] 

**Para asignar Britta Simon a Marketo, realice los pasos siguientes:**

1. En Azure Portal, abra la vista de aplicaciones, navegue a la vista de directorio y vaya a **Aplicaciones empresariales**. Luego haga clic en **Todas las aplicaciones**.

    ![Asignar usuario][201] 

2. En la lista de aplicaciones, seleccione **Marketo**.

    ![Configurar inicio de sesión único](./media/active-directory-saas-marketo-tutorial/tutorial_marketo_app.png) 

3. En el menú de la izquierda, haga clic en **Usuarios y grupos**.

    ![Asignar usuario][202] 

4. Haga clic en el botón **Agregar**. Después, seleccione **Usuarios y grupos** en el cuadro de diálogo **Agregar asignación**.

    ![Asignar usuario][203]

5. En el cuadro de diálogo **Usuarios y grupos**, seleccione **Britta Simon** en la lista de usuarios.

6. Haga clic en el botón **Seleccionar** del cuadro de diálogo **Usuarios y grupos**.

7. Haga clic en el botón **Asignar** del cuadro de diálogo **Agregar asignación**.
    
### <a name="testing-single-sign-on"></a>Prueba del inicio de sesión único 

En esta sección, probará la configuración de inicio de sesión único de Azure AD mediante el Panel de acceso.

Al hacer clic en el icono de Marketo en el Panel de acceso, debería iniciar sesión automáticamente en su aplicación Marketo.

## <a name="additional-resources"></a>Recursos adicionales

* [Lista de tutoriales sobre cómo integrar aplicaciones SaaS con Azure Active Directory](active-directory-saas-tutorial-list.md)
* [¿Qué es el acceso a aplicaciones y el inicio de sesión único con Azure Active Directory?](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-marketo-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-marketo-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-marketo-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-marketo-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-marketo-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-marketo-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-marketo-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-marketo-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-marketo-tutorial/tutorial_general_203.png

