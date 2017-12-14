---
title: "Tutorial: Integración de Azure Active Directory con Onit | Microsoft Docs"
description: "Aprenda a configurar el inicio de sesión único entre Azure Active Directory y Onit."
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: bc479a28-8fcd-493f-ac53-681975a5149c
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/24/2017
ms.author: jeedes
ms.openlocfilehash: f001d2f9d5efd2b9252f0affc32506bd615437af
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-onit"></a>Tutorial: Integración de Azure Active Directory con Onit

En este tutorial, obtendrá información sobre cómo integrar Onit con Azure Active Directory (Azure AD).

La integración de Onit con Azure AD le proporciona las siguientes ventajas:

- Puede controlar en Azure AD quién tiene acceso a Onit.
- Puede permitir que los usuarios inicien sesión automáticamente en Onit (inicio de sesión único) con sus cuentas de Azure AD.
- Puede administrar sus cuentas en una ubicación central: Azure Portal.

Si desea saber más sobre la integración de aplicaciones SaaS con Azure AD, consulte [¿Qué es el acceso a aplicaciones y el inicio de sesión único con Azure Active Directory?](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Requisitos previos

Para configurar la integración de Azure AD con Onit se necesitan los siguientes elementos:

- Una suscripción de Azure AD
- Una suscripción habilitada para el inicio de sesión único en Onit

> [!NOTE]
> Para probar los pasos de este tutorial, no se recomienda el uso de un entorno de producción.

Para probar los pasos de este tutorial, debe seguir estas recomendaciones:

- No use el entorno de producción, salvo que sea necesario.
- Si no dispone de un entorno de prueba de Azure AD, puede [obtener una versión de prueba durante un mes](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descripción del escenario

En este tutorial, puede probar el inicio de sesión único de Azure AD en un entorno de prueba. La situación descrita en este tutorial consta de dos bloques de creación principales:

1. Agregación de Onit desde la galería
2. Configuración y comprobación del inicio de sesión único de Azure AD

## <a name="adding-onit-from-the-gallery"></a>Agregación de Onit desde la galería
Para configurar la integración de Onit en Azure AD, deberá agregar Onit desde la galería a la lista de aplicaciones SaaS administradas.

**Para agregar Onit desde la galería, siga estos pasos:**

1. En el panel de navegación izquierdo de **[Azure Portal](https://portal.azure.com)**, haga clic en el icono de **Azure Active Directory**. 

    ![Botón Azure Active Directory][1]

2. Vaya a **Aplicaciones empresariales**. A continuación, vaya a **Todas las aplicaciones**.

    ![Hoja Aplicaciones empresariales][2]
    
3. Para agregar una nueva aplicación, haga clic en el botón **Nueva aplicación** de la parte superior del cuadro de diálogo.

    ![Botón Nueva aplicación][3]

4. En el cuadro de búsqueda, escriba **Onit**, seleccione **Onit** en el panel de resultados y, luego, haga clic en el botón **Agregar** para agregar la aplicación.

    ![Onit en la lista de resultados](./media/active-directory-saas-onit-tutorial/tutorial_onit_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configuración y prueba del inicio de sesión único en Azure AD

En esta sección, podrá configurar y probar el inicio de sesión único de Azure AD con Onit con un usuario de prueba llamado "Britta Simon."

Para que el inicio de sesión único funcione, Azure AD debe saber cuál es el usuario homólogo de Onit para un usuario de Azure AD. Es decir, es necesario establecer una relación de vínculo entre un usuario de Azure AD y el usuario relacionado de Onit.

Para establecer la relación de vínculo, en Onit, asigne el valor de **nombre de usuario** de Azure AD como valor de **Nombre de usuario**.

Para configurar y probar el inicio de sesión único de Azure AD con Onit, es preciso completar los siguientes bloques de creación:

1. **[Configuración del inicio de sesión único de Azure AD](#configure-azure-ad-single-sign-on)**: para permitir que los usuarios utilicen esta característica.
2. **[Creación de un usuario de prueba de Azure AD](#create-an-azure-ad-test-user)**: para probar el inicio de sesión único de Azure AD con Britta Simon.
3. **[Creación de un usuario de prueba de Onit](#create-an-onit-test-user)** : para tener un homólogo de Britta Simon en Onit que esté vinculado a la representación del usuario en Azure AD.
4. **[Asignación del usuario de prueba de Azure AD](#assign-the-azure-ad-test-user)**: para permitir que Britta Simon use el inicio de sesión único de Azure AD.
5. **[Prueba del inicio de sesión único](#test-single-sign-on)**, para comprobar si funciona la configuración.

### <a name="configure-azure-ad-single-sign-on"></a>Configuración del inicio de sesión único de Azure AD

En esta sección, habilitará el inicio de sesión único de Azure AD en Azure Portal y lo configurará en la aplicación Onit.

**Para configurar el inicio de sesión único de Azure AD con Onit, realice los pasos siguientes:**

1. En Azure Portal, en la página de integración de la aplicación **Onit**, haga clic en **Inicio de sesión único**.

    ![Vínculo Configurar inicio de sesión único][4]

2. En el cuadro de diálogo **Inicio de sesión único**, en **Modo** seleccione **Inicio de sesión basado en SAML** para habilitar el inicio de sesión único.
 
    ![Cuadro de diálogo Inicio de sesión único](./media/active-directory-saas-onit-tutorial/tutorial_onit_samlbase.png)

3. En la sección **Dominio y direcciones URL de Onit**, lleve a cabo los pasos siguientes:

    ![Información de dominio y direcciones URL de inicio de sesión único de Onit](./media/active-directory-saas-onit-tutorial/tutorial_onit_url.png)

    a. En el cuadro de texto **URL de inicio de sesión**, escriba una dirección URL con el siguiente patrón: `https://<sub-domain>.onit.com`.

    b. En el cuadro de texto **Identificador**, escriba una dirección URL con el siguiente patrón: `https://<sub-domain>.onit.com`

    > [!NOTE] 
    > Estos valores no son reales. Debe actualizarlos con la dirección URL y el identificador reales de inicio de sesión. Póngase en contacto con el [equipo de soporte técnico de Onit](https://www.onit.com/support) para obtener estos valores. 
 
4. En la sección **Certificado de firma de SAML**, copie el valor de **HUELLA DIGITAL** del certificado.

    ![Vínculo de descarga del certificado](./media/active-directory-saas-onit-tutorial/tutorial_onit_certificate.png) 

5. La aplicación Onit espera las aserciones de SAML en un formato específico. Configure las siguientes notificaciones para esta aplicación. Puede administrar el valor de estos atributos desde la pestaña **"Atributo"** de la aplicación. La siguiente captura de pantalla le muestra un ejemplo de esto. 

    ![Configurar inicio de sesión único](./media/active-directory-saas-onit-tutorial/tutorial_onit_attribute.png) 

6. En la sección **Atributos de usuario** del cuadro de diálogo **Inicio de sesión único**, configure el atributo token de SAML como muestra la imagen y siga estos pasos:
    
    | Nombre del atributo | Valor de atributo |
    | ------------------- | -------------------- |
    | email | user.mail |
    
    a. Haga clic en **Agregar atributo** para abrir el cuadro de diálogo **Agregar atributo**.

    ![Configurar inicio de sesión único](./media/active-directory-saas-onit-tutorial/tutorial_attribute_04.png)

    ![Configurar inicio de sesión único](./media/active-directory-saas-onit-tutorial/tutorial_attribute_05.png)

    b. En el cuadro de texto **Nombre**, escriba el nombre que se muestra para la fila.

    c. En la lista **Valor**, seleccione el atributo que se muestra para esa fila.

    d. Deje **Espacio de nombres** en blanco.
    
    e. Haga clic en **Aceptar**.

7. Haga clic en el botón **Guardar** .

    ![Botón Guardar de Configuración de inicio de sesión único](./media/active-directory-saas-onit-tutorial/tutorial_general_400.png)

8. En la sección **Configuración de Onit**, haga clic en **Configurar Onit** para abrir la ventana **Configurar inicio de sesión**. Copie las **direcciones URL del servicio de inicio de sesión único de SAML y de cierre de sesión** de la sección **Referencia rápida.**

    ![Configuración de Onit](./media/active-directory-saas-onit-tutorial/tutorial_onit_configure.png)

9. En otra ventana del explorador web, inicie sesión en como administrador en el sitio de Onit de la compañía.

10. En el menú de la parte superior, haga clic en **Administration**(Administración).
   
   ![Administración](./media/active-directory-saas-onit-tutorial/IC791174.png "Administración")
11. Haga clic en **Edit Corporation**(Editar corporación).
   
   ![Editar corporación](./media/active-directory-saas-onit-tutorial/IC791175.png "Editar corporación")
   
12. Haga clic en la pestaña **Security** (Seguridad).
    
    ![Editar información de la compañía](./media/active-directory-saas-onit-tutorial/IC791176.png "Editar información de la compañía")

13. En la pestaña **Seguridad** , lleve a cabo estos pasos:

    ![Inicio de sesión único](./media/active-directory-saas-onit-tutorial/IC791177.png "Inicio de sesión único")

    a. Como **Estrategia de autenticación**, seleccione **Inicio de sesión único y contraseña**.
    
    b. En el cuadro de texto **Idp Target URL** (Dirección URL de destino del Idp), pegue el valor de la **dirección URL del servicio de inicio de sesión único de SAML** que ha copiado de Azure Portal.

    c. En el cuadro de texto **IDP Logout URL** (Dirección URL de cierre de sesión del IDP), pegue el valor de la **dirección URL de cierre de sesión** que copió de Azure Portal.

    d. En el cuadro de texto **Idp Cert Fingerprint (SHA1)** (Huella digital de certificado del Idp [SHA1]), pegue el valor de **Huella digital** del certificado que ha copiado de Azure Portal.

> [!TIP]
> Ahora puede leer una versión resumida de estas instrucciones dentro de [Azure Portal](https://portal.azure.com) mientras configura la aplicación.  Después de agregar esta aplicación desde la sección **Active Directory > Aplicaciones empresariales**, simplemente haga clic en la pestaña **Inicio de sesión único** y acceda a la documentación insertada a través de la sección **Configuración** de la parte inferior. Puede leer más sobre la característica de documentación insertada aquí: [Vista previa: Administración de inicio de sesión único para aplicaciones empresariales en el nuevo Azure Portal]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="create-an-azure-ad-test-user"></a>Creación de un usuario de prueba de Azure AD

El objetivo de esta sección es crear un usuario de prueba en Azure Portal llamado "Britta Simon".

   ![Creación de un usuario de prueba de Azure AD][100]

**Siga estos pasos para crear un usuario de prueba en Azure AD:**

1. En el panel izquierdo de Azure Portal, haga clic en el botón **Azure Active Directory**.

    ![Botón Azure Active Directory](./media/active-directory-saas-onit-tutorial/create_aaduser_01.png)

2. Para mostrar la lista de usuarios, vaya a **Usuarios y grupos** y, luego, haga clic en **Todos los usuarios**.

    ![Vínculos "Usuarios y grupos" y "Todos los usuarios"](./media/active-directory-saas-onit-tutorial/create_aaduser_02.png)

3. En la parte superior del cuadro de diálogo **Todos los usuarios**, haga clic en **Agregar** para abrir el cuadro de diálogo **Agregar**.

    ![Botón Agregar](./media/active-directory-saas-onit-tutorial/create_aaduser_03.png)

4. En el cuadro de diálogo **Usuario** , realice los pasos siguientes:

    ![Cuadro de diálogo Usuario](./media/active-directory-saas-onit-tutorial/create_aaduser_04.png)

    a. En el cuadro **Nombre**, escriba **BrittaSimon**.

    b. En el cuadro de texto **Nombre de usuario**, escriba la dirección de correo electrónico del usuario Britta Simon.

    c. Active la casilla **Mostrar contraseña** y, después, anote el valor que se muestra en el cuadro **Contraseña**.

    d. Haga clic en **Crear**.
 
### <a name="create-an-onit-test-user"></a>Creación de un usuario de prueba de Onit

Para permitir que los usuarios de Azure AD inicien sesión en Onit, tienen que aprovisionarse en Onit.  

En el caso de Onit, el aprovisionamiento es una tarea manual.

**Siga estos pasos para configurar el aprovisionamiento de usuario:**

1. Inicie sesión en su sitio de la compañía de **Onit** como administrador.
2. Haga clic en **Agregar usuario**.
   
   ![Administración](./media/active-directory-saas-onit-tutorial/IC791180.png "Administración")
3. En la página del cuadro de diálogo **Add User** (Agregar usuario), realice los siguientes pasos:
   
   ![Agregar usuario](./media/active-directory-saas-onit-tutorial/IC791181.png "Agregar usuario")
   
  1. Escriba el **Nombre** y la **Dirección de correo electrónico** de una cuenta de Azure AD válida que quiera aprovisionar en los cuadros de texto relacionados.
  2. Haga clic en **Crear**.    
   
 > [!NOTE]
 > El titular de la cuenta de Azure Active Directory recibirá un mensaje de correo y seguirá un vínculo para confirmar su cuenta antes de que se active.

### <a name="assign-the-azure-ad-test-user"></a>Asignación del usuario de prueba de Azure AD

En esta sección, habilitará a Britta Simon para que use el inicio de sesión único de Azure concediéndole acceso a Onit.

![Asignación del rol de usuario][200] 

**Para asignar al usuario Britta Simon a Onit, realice los pasos siguientes:**

1. En Azure Portal, abra la vista de aplicaciones, vaya a la vista de directorio y vaya a **Aplicaciones empresariales**. Luego, haga clic en **Todas las aplicaciones**.

    ![Asignar usuario][201] 

2. En la lista de aplicaciones, seleccione **Onit**.

    ![Vínculo a Onit en la lista de aplicaciones](./media/active-directory-saas-onit-tutorial/tutorial_onit_app.png)  

3. En el menú de la izquierda, haga clic en **Usuarios y grupos**.

    ![Vínculo "Usuarios y grupos"][202]

4. Haga clic en el botón **Agregar**. Después, seleccione **Usuarios y grupos** en el cuadro de diálogo **Agregar asignación**.

    ![Panel Agregar asignación][203]

5. En el cuadro de diálogo **Usuarios y grupos**, seleccione **Britta Simon** en la lista de usuarios.

6. Haga clic en el botón **Seleccionar** del cuadro de diálogo **Usuarios y grupos**.

7. Haga clic en el botón **Asignar** del cuadro de diálogo **Agregar asignación**.
    
### <a name="test-single-sign-on"></a>Prueba de inicio de sesión único

En esta sección, probará la configuración de inicio de sesión único de Azure AD mediante el Panel de acceso.

Al hacer clic en el icono de Onit en el panel de acceso, debería iniciar sesión automáticamente en la aplicación Onit.
Para más información sobre el Panel de acceso, consulte [Introducción al Panel de acceso](active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Recursos adicionales

* [Lista de tutoriales sobre cómo integrar aplicaciones SaaS con Azure Active Directory](active-directory-saas-tutorial-list.md)
* [¿Qué es el acceso a aplicaciones y el inicio de sesión único con Azure Active Directory?](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-onit-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-onit-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-onit-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-onit-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-onit-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-onit-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-onit-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-onit-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-onit-tutorial/tutorial_general_203.png

