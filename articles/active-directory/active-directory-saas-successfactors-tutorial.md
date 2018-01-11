---
title: "Tutorial: integración de Azure Active Directory con SuccessFactors | Microsoft Docs"
description: "Aprenda a configurar el inicio de sesión único entre Azure Active Directory y SuccessFactors."
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 32bd8898-c2d2-4aa7-8c46-f1f5c2aa05f1
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/08/2017
ms.author: jeedes
ms.openlocfilehash: b9545599b4ac02927c38931777a3cee623a4e940
ms.sourcegitcommit: 922687d91838b77c038c68b415ab87d94729555e
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/13/2017
---
# <a name="tutorial-azure-active-directory-integration-with-successfactors"></a>Tutorial: integración de Azure Active Directory con SuccessFactors

En este tutorial, aprenderá a integrar SuccessFactors con Azure Active Directory (Azure AD).

La integración de SuccessFactors con Azure AD proporciona las siguientes ventajas:

- Puede controlar en Azure AD quién tiene acceso a SuccessFactors.
- Puede permitir que los usuarios inicien sesión automáticamente en SuccessFactors (inicio de sesión único) con sus cuentas de Azure AD.
- Puede administrar sus cuentas en una ubicación central: Azure Portal.

Si desea saber más sobre la integración de aplicaciones SaaS con Azure AD, consulte [¿Qué es el acceso a aplicaciones y el inicio de sesión único con Azure Active Directory?](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Requisitos previos

Para configurar la integración de Azure AD con SuccessFactors, necesita los siguientes elementos:

- Una suscripción de Azure AD
- Una suscripción habilitada para inicio de sesión único en SuccessFactors

> [!NOTE]
> Para probar los pasos de este tutorial, no se recomienda el uso de un entorno de producción.

Para probar los pasos de este tutorial, debe seguir estas recomendaciones:

- No use el entorno de producción, salvo que sea necesario.
- Si no dispone de un entorno de prueba de Azure AD, puede [obtener una versión de prueba durante un mes](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descripción del escenario
En este tutorial, puede probar el inicio de sesión único de Azure AD en un entorno de prueba. La situación descrita en este tutorial consta de dos bloques de creación principales:

1. Incorporación de SuccessFactors desde la galería
2. Configuración y comprobación del inicio de sesión único de Azure AD

## <a name="adding-successfactors-from-the-gallery"></a>Incorporación de SuccessFactors desde la galería
Para configurar la integración de SuccessFactors en Azure AD, deberá agregar SuccessFactors desde la galería a la lista de aplicaciones SaaS administradas.

**Para agregar SuccessFactors desde la galería, siga estos pasos:**

1. En el panel de navegación izquierdo de **[Azure Portal](https://portal.azure.com)**, haga clic en el icono de **Azure Active Directory**. 

    ![Botón Azure Active Directory][1]

2. Vaya a **Aplicaciones empresariales**. A continuación, vaya a **Todas las aplicaciones**.

    ![Hoja Aplicaciones empresariales][2]
    
3. Para agregar una nueva aplicación, haga clic en el botón **Nueva aplicación** de la parte superior del cuadro de diálogo.

    ![Botón Nueva aplicación][3]

4. En el cuadro de búsqueda, escriba **SuccessFactors**, seleccione **SuccessFactors** en el panel de resultados y, luego, haga clic en el botón **Agregar** para agregar la aplicación.

    ![SuccessFactors en la lista de resultados](./media/active-directory-saas-successfactors-tutorial/tutorial_successfactors_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configuración y prueba del inicio de sesión único en Azure AD

En esta sección, podrá configurar y probar el inicio de sesión único de Azure AD con SuccessFactors utilizando un usuario de prueba llamado "Britta Simon".

Para que el inicio de sesión único funcione, Azure AD debe saber cuál es el usuario homólogo de SuccessFactors para un usuario de Azure AD. Es decir, es necesario establecer una relación de vínculo entre un usuario de Azure AD y el usuario relacionado de SuccessFactors.

Para establecer la relación de vínculo, en SuccessFactors, asigne el valor de **nombre de usuario** de Azure AD como valor de **nombre de usuario**.

Para configurar y probar el inicio de sesión único de Azure AD con SuccessFactors, es preciso completar los siguientes bloques de creación:

1. **[Configuración del inicio de sesión único de Azure AD](#configure-azure-ad-single-sign-on)**: para permitir que los usuarios utilicen esta característica.
2. **[Creación de un usuario de prueba de Azure AD](#create-an-azure-ad-test-user)**: para probar el inicio de sesión único de Azure AD con Britta Simon.
3. **[Creación de un usuario de prueba de SuccessFactors](#create-a-successfactors-test-user)** : para tener un homólogo de Britta Simon en SuccessFactors que esté vinculado a su representación en Azure AD.
4. **[Asignación del usuario de prueba de Azure AD](#assign-the-azure-ad-test-user)**: para permitir que Britta Simon use el inicio de sesión único de Azure AD.
5. **[Prueba del inicio de sesión único](#test-single-sign-on)**: para comprobar si la configuración funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configuración del inicio de sesión único de Azure AD

En esta sección, habilitará el inicio de sesión único de Azure AD en Azure Portal y configurará el inicio de sesión único en la aplicación SuccessFactors.

**Para configurar el inicio de sesión único de Azure AD con SuccessFactors, realice los pasos siguientes:**

1. En la página de integración de la aplicación **SuccessFactors** de Azure Portal, haga clic en **Inicio de sesión único**.

    ![Vínculo Configurar inicio de sesión único][4]

2. En el cuadro de diálogo **Inicio de sesión único**, en **Modo** seleccione **Inicio de sesión basado en SAML** para habilitar el inicio de sesión único.
 
    ![Cuadro de diálogo Inicio de sesión único](./media/active-directory-saas-successfactors-tutorial/tutorial_successfactors_samlbase.png)

3. En la sección **SuccessFactors Domain and URLs** (Dominios y direcciones URL de SuccessFactors), lleve a cabo los pasos siguientes:

    ![Información de dominio y direcciones URL de inicio de sesión único de SuccessFactors](./media/active-directory-saas-successfactors-tutorial/tutorial_successfactors_url.png)

    a. En el cuadro de texto **URL de inicio de sesión**, escriba una dirección URL con el siguiente patrón:
    | |
    |--|
    | `https://<companyname>.successfactors.com/<companyname>`|
    | `https://<companyname>.sapsf.com/<companyname>`|
    | `https://<companyname>.successfactors.eu/<companyname>`|
    | `https://<companyname>.sapsf.eu`|

    b. En el cuadro de texto **Identificador**, escriba una dirección URL con el siguiente modelo:
    | |
    |--|
    | `https://www.successfactors.com/<companyname>`|
    | `https://www.successfactors.com`|
    | `https://<companyname>.successfactors.eu`|
    | `https://www.successfactors.eu/<companyname>`|
    | `https://<companyname>.sapsf.com`|
    | `https://hcm4preview.sapsf.com/<companyname>`|
    | `https://<companyname>.sapsf.eu`|
    | `https://www.successfactors.cn`|
    | `https://www.successfactors.cn/<companyname>`|

    c. En el cuadro de texto **URL de respuesta** , escriba una dirección URL con el siguiente patrón:
    | |
    |--|
    | `https://<companyname>.successfactors.com/<companyname>`|
    | `https://<companyname>.successfactors.com`|
    | `https://<companyname>.sapsf.com/<companyname>`|
    | `https://<companyname>.sapsf.com`|
    | `https://<companyname>.successfactors.eu/<companyname>`|
    | `https://<companyname>.successfactors.eu`|
    | `https://<companyname>.sapsf.eu`|
    | `https://<companyname>.sapsf.eu/<companyname>`|
    | `https://<companyname>.sapsf.cn`|
    | `https://<companyname>.sapsf.cn/<companyname>`|
         
    > [!NOTE] 
    > Estos valores no son reales. Actualice estos valores con los valores reales de Identificador, URL de respuesta y URL de inicio de sesión. Para obtener estos valores, póngase en contacto con el [equipo de soporte técnico de SuccessFactors](https://www.successfactors.com/en_us/support.html). 

4. En la sección **Certificado de firma de SAML**, haga clic en **Certificado (Base64)** y, luego, guarde el archivo de certificado en el equipo.

    ![Vínculo de descarga del certificado](./media/active-directory-saas-successfactors-tutorial/tutorial_successfactors_certificate.png) 

5. Haga clic en el botón **Guardar** .

    ![Botón Guardar de Configuración de inicio de sesión único](./media/active-directory-saas-successfactors-tutorial/tutorial_general_400.png)
    
6. En la sección **Configuración de SuccessFactors**, haga clic en **Configurar SuccessFactors** para abrir la ventana **Configurar inicio de sesión**. Copie la **URL del servicio de inicio de sesión único de SAML, el identificador de entidad de SAML y la dirección URL de cierre de sesión** de la sección **Referencia rápida**.

    ![Configurar inicio de sesión único](./media/active-directory-saas-successfactors-tutorial/tutorial_successfactors_configure.png) 

7. En otra ventana del explorador web, inicie sesión en el **Portal de administración de SuccessFactors** como administrador.
    
8. Visite **Application Security** (Seguridad de aplicaciones) y establezca nativo en **Single Sign On Features** (Características de Inicio de sesión único). 

9. Coloque cualquier valor en **Reset Token** (Restablecer Token) y haga clic en **Save Token** (Guardar Token) para habilitar SSO de SAML.
   
    ![Configuración del inicio de sesión único en la aplicación][11]

    > [!NOTE] 
    > Este valor se utiliza como el conmutador de activado y desactivado. Si se guarda algún valor, el SSO de SAML está activado. Si se guarda un valor en blanco, el SSO de SAML está desactivado.

10. Vaya a la siguiente captura de pantalla y realice las acciones siguientes:
   
    ![Configuración del inicio de sesión único en la aplicación][12]
   
    a. Seleccione el botón de selección **SAML v2 SSO** (SSO de SAML v2).
   
    b. Establezca el **nombre de entidad asertivo de SAML** (por ejemplo, emisor de SAML + nombre de la empresa).
   
    c. En el cuadro de texto **Issuer URL** (Dirección URL del emisor), pegue el valor de **SAML Entity ID** (Identificador de entidad de SAML) que copió de Azure Portal.
   
    d. Seleccione **Response(Customer Generated/IdP/AP)** [Respuesta (cliente generado/IdP/AP)] como **Require Mandatory Signature** (Requerir firma obligatoria).
   
    e. Seleccione **Enabled** (Habilitado) como **Enable SAML Flag** (Habilitar marca SAML).
   
    f. Seleccione **No** como **Login Request Signature(SF Generated/SP/RP)** [Firma de solicitud de inicio de sesión (SF generado/SP/RP)].
   
    g. Seleccione **Browser/Post Profile** (Perfil de explorador/envío) como **SAML Profile** (Perfil SAML).
   
    h. Seleccione **No** como **Enforce Certificate Valid Period** (Aplicar período válido de certificado).
   
    i. Copie el contenido del archivo de certificado descargado de Azure Portal y péguelo en el cuadro de texto **SAML Verifying Certificate** (Certificado de verificación de firma).

    > [!NOTE] 
    > El contenido del certificado debe tener etiquetas de inicio y fin del certificado.

11. Vaya a SAML V2 y realice los pasos siguientes:
   
    ![Configuración del inicio de sesión único en la aplicación][13]
   
    a. Seleccione **Yes** (Sí) como **Support SP-initiated Global Logout** (Permitir cierre de sesión global iniciado por SP).
   
    b. En el cuadro de texto **Global Logout Service URL (LogoutRequest destination)** (URL del servicio de cierre de sesión global (destino LogoutRequest)), pegue el valor **Sign-Out URL** (Dirección URL de cierre de sesión) que copió de Azure Portal.
   
    c. Seleccione **No** en **Require sp must encrypt all NameID element** (Requerir que sp cifre todos los elementos NameID).
   
    d. Seleccione **Unspecified** (Sin especificar) como **NameID Format** (Formato de NameID).
   
    e. Seleccione **Yes** (Sí) como **Enable sp initiated login (AuthnRequest)** [Permitir inicio de sesión iniciado por sp (AuthnRequest)].
   
    f. En el cuadro de texto **Send request as Company-Wide issuer** (Enviar solicitud como emisor para toda la compañía), pegue el valor de **SAML Single Sign-On Service URL** (URL de servicio de inicio de sesión único de SAML) que copió de Azure Portal.

12. Siga estos pasos si desea que los nombres de usuario de inicio de sesión no distingan mayúsculas de minúsculas.
   
    ![Configurar inicio de sesión único][29]
    
    a. Visite **Company Settings**(Configuración de la empresa) en la parte inferior.
   
    b. Seleccione la casilla junto a **Enable Non-Case-Sensitive Username**(Habilitar nombre de usuario sin distinción de mayúsculas y minúsculas).
   
    Haga clic en **Save**(Guardar).
   
    > [!NOTE] 
    > Si intenta habilitar esta opción, el sistema comprueba si creará un nombre de inicio de sesión de SAML duplicado. Por ejemplo, si el cliente tiene nombres de usuario User1 y user1. Al no distinguir mayúsculas de minúsculas, estos nombres pasan a ser duplicados. El sistema mostrará un mensaje de error y no se habilitará la característica. El cliente deberá cambiar uno de los nombres de usuario, para que esté escrito diferente.

> [!TIP]
> Ahora puede leer una versión resumida de estas instrucciones dentro de [Azure Portal](https://portal.azure.com) mientras configura la aplicación.  Después de agregar esta aplicación desde la sección **Active Directory > Aplicaciones empresariales**, simplemente haga clic en la pestaña **Inicio de sesión único** y acceda a la documentación insertada a través de la sección **Configuración** de la parte inferior. Puede leer más sobre la característica de documentación insertada aquí: [Vista previa: Administración de inicio de sesión único para aplicaciones empresariales en el nuevo Azure Portal]( https://go.microsoft.com/fwlink/?linkid=845985)

### <a name="create-an-azure-ad-test-user"></a>Creación de un usuario de prueba de Azure AD

El objetivo de esta sección es crear un usuario de prueba en Azure Portal llamado "Britta Simon".

   ![Creación de un usuario de prueba de Azure AD][100]

**Siga estos pasos para crear un usuario de prueba en Azure AD:**

1. En el panel izquierdo de Azure Portal, haga clic en el botón **Azure Active Directory**.

    ![Botón Azure Active Directory](./media/active-directory-saas-successfactors-tutorial/create_aaduser_01.png)

2. Para mostrar la lista de usuarios, vaya a **Usuarios y grupos** y, luego, haga clic en **Todos los usuarios**.

    ![Vínculos "Usuarios y grupos" y "Todos los usuarios"](./media/active-directory-saas-successfactors-tutorial/create_aaduser_02.png)

3. En la parte superior del cuadro de diálogo **Todos los usuarios**, haga clic en **Agregar** para abrir el cuadro de diálogo **Agregar**.

    ![Botón Agregar](./media/active-directory-saas-successfactors-tutorial/create_aaduser_03.png)

4. En el cuadro de diálogo **Usuario** , realice los pasos siguientes:

    ![Cuadro de diálogo Usuario](./media/active-directory-saas-successfactors-tutorial/create_aaduser_04.png)

    a. En el cuadro **Nombre**, escriba **BrittaSimon**.

    b. En el cuadro de texto **Nombre de usuario**, escriba la dirección de correo electrónico del usuario Britta Simon.

    c. Active la casilla **Mostrar contraseña** y, después, anote el valor que se muestra en el cuadro **Contraseña**.

    d. Haga clic en **Crear**.
 
### <a name="create-a-successfactors-test-user"></a>Creación de un usuario de prueba de SuccessFactors

Para permitir que los usuarios de Azure AD inicien sesión en SuccessFactors, deben aprovisionarse en SuccessFactors.  
En el caso de SuccessFactors, el aprovisionamiento es una tarea manual.

Para que se creen los usuarios en SuccessFactors, deberá ponerse en contacto con el [equipo de soporte técnico de SuccessFactors](https://www.successfactors.com/en_us/support.html).

### <a name="assign-the-azure-ad-test-user"></a>Asignación del usuario de prueba de Azure AD

En esta sección, habilitará a Britta Simon para que use el inicio de sesión único de Azure concediéndole acceso a SuccessFactors.

![Asignación del rol de usuario][200] 

**Para asignar Britta Simon a SuccessFactors, siga estos pasos:**

1. En Azure Portal, abra la vista de aplicaciones, vaya a la vista de directorio y vaya a **Aplicaciones empresariales**. Luego, haga clic en **Todas las aplicaciones**.

    ![Asignar usuario][201] 

2. En la lista de aplicaciones, seleccione **SuccessFactors**.

    ![Vínculo de SuccessFactors en la lista de aplicaciones.](./media/active-directory-saas-successfactors-tutorial/tutorial_successfactors_app.png)  

3. En el menú de la izquierda, haga clic en **Usuarios y grupos**.

    ![Vínculo "Usuarios y grupos"][202]

4. Haga clic en el botón **Agregar**. Después, seleccione **Usuarios y grupos** en el cuadro de diálogo **Agregar asignación**.

    ![Panel Agregar asignación][203]

5. En el cuadro de diálogo **Usuarios y grupos**, seleccione **Britta Simon** en la lista de usuarios.

6. Haga clic en el botón **Seleccionar** del cuadro de diálogo **Usuarios y grupos**.

7. Haga clic en el botón **Asignar** del cuadro de diálogo **Agregar asignación**.
    
### <a name="test-single-sign-on"></a>Prueba de inicio de sesión único

En esta sección, probará la configuración de inicio de sesión único de Azure AD mediante el Panel de acceso.

Al hacer clic en el icono de SuccessFactors en el panel de acceso, debería iniciar sesión automáticamente en la aplicación SuccessFactors.
Para más información sobre el Panel de acceso, consulte [Introducción al Panel de acceso](active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Recursos adicionales

* [Lista de tutoriales sobre cómo integrar aplicaciones SaaS con Azure Active Directory](active-directory-saas-tutorial-list.md)
* [¿Qué es el acceso a aplicaciones y el inicio de sesión único con Azure Active Directory?](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-successfactors-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-successfactors-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-successfactors-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-successfactors-tutorial/tutorial_general_04.png

[11]: ./media/active-directory-saas-successfactors-tutorial/tutorial_successfactors_07.png
[12]: ./media/active-directory-saas-successfactors-tutorial/tutorial_successfactors_08.png
[13]: ./media/active-directory-saas-successfactors-tutorial/tutorial_successfactors_09.png
[14]: ./media/active-directory-saas-successfactors-tutorial/tutorial_general_05.png
[15]: ./media/active-directory-saas-successfactors-tutorial/tutorial_general_06.png
[29]: ./media/active-directory-saas-successfactors-tutorial/tutorial_successfactors_10.png

[100]: ./media/active-directory-saas-successfactors-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-successfactors-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-successfactors-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-successfactors-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-successfactors-tutorial/tutorial_general_203.png

