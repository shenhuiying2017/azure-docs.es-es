---
title: 'Tutorial: Integración de Azure Active Directory con SignalFx | Microsoft Docs'
description: Aprenda a configurar el inicio de sesión único entre Azure Active Directory y SignalFx.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 6d5ab4b0-29bc-4b20-8536-d64db7530f32
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/16/2018
ms.author: jeedes
ms.openlocfilehash: 67e9bb69e1e8741e4a4ff7e7b78cb06856d2450f
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/20/2018
ms.locfileid: "34348740"
---
# <a name="tutorial-azure-active-directory-integration-with-signalfx"></a>Tutorial: Integración de Azure Active Directory con SignalFx

En este tutorial, obtendrá información sobre cómo integrar SignalFx con Azure Active Directory (Azure AD).

La integración de SignalFx con Azure AD le proporciona las siguientes ventajas:

- Puede controlar en Azure AD quién tiene acceso a SignalFx.
- Puede permitir que los usuarios inicien sesión automáticamente en SignalFx (inicio de sesión único) con sus cuentas de Azure AD.
- Puede administrar sus cuentas en una ubicación central: Azure Portal.

Si desea saber más sobre la integración de aplicaciones SaaS con Azure AD, consulte [¿Qué es el acceso a aplicaciones y el inicio de sesión único con Azure Active Directory?](manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>requisitos previos

Para configurar la integración de Azure AD con SignalFx, necesita los siguientes elementos:

- Una suscripción de Azure AD
- Suscripción habilitada para el inicio de sesión único en SignalFx

> [!NOTE]
> Para probar los pasos de este tutorial, no se recomienda el uso de un entorno de producción.

Para probar los pasos de este tutorial, debe seguir estas recomendaciones:

- No use el entorno de producción, salvo que sea necesario.
- Si no dispone de un entorno de prueba de Azure AD, puede [obtener una versión de prueba durante un mes](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descripción del escenario
En este tutorial, puede probar el inicio de sesión único de Azure AD en un entorno de prueba. El escenario descrito en este tutorial consta de dos bloques de creación principales:

1. Adición de SignalFx desde la galería
2. Configuración y comprobación del inicio de sesión único de Azure AD

## <a name="adding-signalfx-from-the-gallery"></a>Adición de SignalFx desde la galería
Para configurar la integración de SignalFx en Azure AD, deberá agregar SignalFx desde la galería a la lista de aplicaciones SaaS administradas.

**Para agregar SignalFx desde la galería, realice los pasos siguientes:**

1. En el panel de navegación izquierdo de **[Azure Portal](https://portal.azure.com)**, haga clic en el icono de **Azure Active Directory**. 

    ![Botón Azure Active Directory][1]

2. Vaya a **Aplicaciones empresariales**. A continuación, vaya a **Todas las aplicaciones**.

    ![Hoja Aplicaciones empresariales][2]
    
3. Para agregar una nueva aplicación, haga clic en el botón **Nueva aplicación** de la parte superior del cuadro de diálogo.

    ![Botón Nueva aplicación][3]

4. En el cuadro de búsqueda, escriba **SignalFx**, seleccione **SignalFx** en el panel de resultados y, luego, haga clic en el botón **Agregar** para agregar la aplicación.

    ![SignalFx en la lista de resultados](./media/active-directory-saas-signalfx-tutorial/tutorial_signalfx_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configuración y prueba del inicio de sesión único en Azure AD

En esta sección, podrá configurar y probar el inicio de sesión único de Azure AD con SignalFx con un usuario de prueba llamado "Britta Simon".

Para que el inicio de sesión único funcione, Azure AD debe saber cuál es el usuario homólogo de SignalFx para un usuario de Azure AD. Es decir, es necesario establecer una relación de vínculo entre un usuario de Azure AD y el usuario relacionado de SignalFx.

Para configurar y probar el inicio de sesión único de Azure AD con SignalFx, es preciso completar los siguientes bloques de creación:

1. **[Configuración del inicio de sesión único de Azure AD](#configure-azure-ad-single-sign-on)**: para que los usuarios puedan usar esta característica.
2. **[Creación de un usuario de prueba de Azure AD](#create-an-azure-ad-test-user)**, para probar el inicio de sesión único de Azure AD con Britta Simon.
3. **[Creación de un usuario de prueba de SignalFx](#create-a-signalfx-test-user)**: para tener un homólogo de Britta Simon en SignalFx vinculado a la representación del usuario en Azure AD.
4. **[Asignación del usuario de prueba de Azure AD](#assign-the-azure-ad-test-user)**, para permitir que Britta Simon use el inicio de sesión único de Azure AD.
5. **[Prueba del inicio de sesión único](#test-single-sign-on)**: para comprobar si la configuración funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configuración del inicio de sesión único de Azure AD

En esta sección, habilitará el inicio de sesión único de Azure AD en Azure Portal y configurará el inicio de sesión único en la aplicación SignalFx.

**Para configurar el inicio de sesión único de Azure AD con SignalFx, siga estos pasos:**

1. En Azure Portal, en la página de integración de aplicaciones **SignalFx**, haga clic en **Inicio de sesión único**.

    ![Vínculo Configurar inicio de sesión único][4]

2. En el cuadro de diálogo **Inicio de sesión único**, en **Modo** seleccione **Inicio de sesión basado en SAML** para habilitar el inicio de sesión único.
 
    ![Cuadro de diálogo Inicio de sesión único](./media/active-directory-saas-signalfx-tutorial/tutorial_signalfx_samlbase.png)

3. En la sección **SignalFx Domain and URLs** (Dominios y direcciones URL de SignalFx), lleve a cabo los pasos siguientes:

    ![Información de dominio y direcciones URL de inicio de sesión único de SignalFx](./media/active-directory-saas-signalfx-tutorial/tutorial_signalfx_url.png)

    a. En el cuadro de texto **Identificador**, escriba una dirección URL como: `https://api.signalfx.com/v1/saml/metadata`

    b. En el cuadro de texto **URL de respuesta**, escriba una dirección URL con el siguiente patrón: `https://api.signalfx.com/v1/saml/acs/<integration ID>`.

    > [!NOTE] 
    > El valor anterior no es real. El valor se actualizará con la dirección URL de respuesta real, que se explica más adelante en el tutorial.

4. La aplicación SignalFx espera las aserciones de SAML en un formato específico. Configure las siguientes notificaciones para esta aplicación. Puede administrar los valores de estos atributos en la sección **Atributos de usuario** de la página de integración de aplicaciones. La siguiente captura de pantalla le muestra un ejemplo de esto.   

    ![Configurar inicio de sesión único](./media/active-directory-saas-signalfx-tutorial/tutorial_signalfx_attribute.png)

5. En la sección **Atributos de usuario** del cuadro de diálogo **Inicio de sesión único**, configure el atributo token de SAML como muestra la imagen y siga estos pasos:
    
    | Nombre del atributo | Valor de atributo |
    | ------------------- | -------------------- |    
    | User.FirstName          | user.givenname |
    | User.email          | user.mail |
    | PersonImmutableID       | user.userprincipalname    |
    | User.LastName       | user.surname    |

    a. Haga clic en **Agregar atributo** para abrir el cuadro de diálogo **Agregar atributo**.

    ![Configurar la adición del inicio de sesión único](./media/active-directory-saas-signalfx-tutorial/tutorial_attribute_04.png)

    ![Configurar la adición del atributo del inicio de sesión único](./media/active-directory-saas-signalfx-tutorial/tutorial_attribute_05.png)

    b. En el cuadro de texto **Nombre**, escriba el nombre que se muestra para la fila.

    c. En la lista **Valor**, seleccione el atributo que se muestra para esa fila.

    d. Deje **Espacio de nombres** en blanco.
    
    e. Haga clic en **Aceptar**.
 
6. En la sección **Certificado de firma de SAML**, realice los pasos siguientes: 

    ![Vínculo de descarga del certificado](./media/active-directory-saas-signalfx-tutorial/tutorial_signalfx_certificate.png)

    a. Haga clic en el botón Copiar para copiar la **dirección URL de metadatos de federación de la aplicación** y péguela en el Bloc de notas.

    b. Haga clic en **Certificado (Base64)** y, luego, guarde el archivo de certificado en el equipo.

7. Haga clic en el botón **Guardar** .

    ![Botón Configurar inicio de sesión único](./media/active-directory-saas-signalfx-tutorial/tutorial_general_400.png)

8. En la sección **SignalFx Configuration** (Configuración de SignalFx), haga clic en **Configure SignalFx** (Configurar Sprinklr) para abrir la ventana **Configurar inicio de sesión**. Copie el valor de **Identificador de entidad de SAML** de la **sección Referencia rápida**

    ![Configuración de SignalFx](./media/active-directory-saas-signalfx-tutorial/tutorial_signalfx_configure.png) 

9. Inicie sesión en su sitio de la compañía de SignalFx como administrador.

10. En SignalFx, en la parte superior, haga clic en **Integrations** (Integraciones) para abrir la página correspondiente.

    ![Integración de SignalFx](./media/active-directory-saas-signalfx-tutorial/tutorial_signalfx_intg.png)

11. Haga clic en el icono **Azure Active Directory** en la sección **Login Services** (Servicios de inicio de sesión).
 
    ![SignalFx saml](./media/active-directory-saas-signalfx-tutorial/tutorial_signalfx_saml.png)

12. Haga clic en **NEW INTEGRATION** (Nueva integración) y en la pestaña **INSTALL** (Instalar) realice los pasos siguientes:
 
    ![SignalFx samlintgpage](./media/active-directory-saas-signalfx-tutorial/tutorial_signalfx_azure.png)

    a. En el cuadro de texto **Nombre**, escriba un nuevo nombre de integración, como **OurOrgName SAML SSO**.

    b. Copie el valor de **Id. de integración** y anexe la **URL de respuesta** como `https://api.signalfx.com/v1/saml/acs/<integration ID>` en el cuadro de texto **URL de respuesta** de **Dominio y direcciones URL de SignalFx** de Azure portal.

    c. Haga clic en **Carga de archivo** para cargar el **certificado codificado en Base 64** descargado desde Azure Portal en el cuadro de texto **Certificado**.

    d. En el cuadro de texto **URL del emisor**, pegue el valor de **SAML Entity ID** (Id. de entidad SAML) que ha copiado de Azure Portal.

    e. En el cuadro de texto **URL de metadatos**, pegue la **dirección URL de metadatos de federación de aplicación** que ha copiado de Azure Portal.

    f. Haga clic en **Save**(Guardar).

### <a name="create-an-azure-ad-test-user"></a>Creación de un usuario de prueba de Azure AD

El objetivo de esta sección es crear un usuario de prueba en Azure Portal llamado "Britta Simon".

   ![Creación de un usuario de prueba de Azure AD][100]

**Siga estos pasos para crear un usuario de prueba en Azure AD:**

1. En el panel izquierdo de Azure Portal, haga clic en el botón **Azure Active Directory**.

    ![Botón Azure Active Directory](./media/active-directory-saas-signalfx-tutorial/create_aaduser_01.png)

2. Para mostrar la lista de usuarios, vaya a **Usuarios y grupos** y, luego, haga clic en **Todos los usuarios**.

    ![Vínculos "Usuarios y grupos" y "Todos los usuarios"](./media/active-directory-saas-signalfx-tutorial/create_aaduser_02.png)

3. En la parte superior del cuadro de diálogo **Todos los usuarios**, haga clic en **Agregar** para abrir el cuadro de diálogo **Agregar**.

    ![Botón Agregar](./media/active-directory-saas-signalfx-tutorial/create_aaduser_03.png)

4. En el cuadro de diálogo **Usuario** , realice los pasos siguientes:

    ![Cuadro de diálogo Usuario](./media/active-directory-saas-signalfx-tutorial/create_aaduser_04.png)

    a. En el cuadro **Nombre**, escriba **BrittaSimon**.

    b. En el cuadro de texto **Nombre de usuario**, escriba la dirección de correo electrónico del usuario Britta Simon.

    c. Active la casilla **Mostrar contraseña** y, después, anote el valor que se muestra en el cuadro **Contraseña**.

    d. Haga clic en **Create**(Crear).
  
### <a name="create-a-signalfx-test-user"></a>Creación de un usuario de prueba de SignalFx

El objetivo de esta sección es crear un usuario llamado Britta Simon en SignalFx. SignalFx admite el aprovisionamiento Just-In-Time, que está habilitado de forma predeterminada. No hay ningún elemento de acción para usted en esta sección. Al intentar acceder a SignalFx, se crea un nuevo usuario, en caso de que no exista.

Cuando un usuario inicia sesión en SignalFx desde SSO de SAML por primera vez, [el equipo de soporte técnico de SignalFx](mailto:kmazzola@signalfx.com) envía un correo electrónico con un vínculo en el que debe hacer clic para autenticarse. Esto solo ocurrirá la primera vez que el usuario inicie sesión; los intentos de inicio de sesión posteriores no requerirán la validación del correo electrónico.

>[!Note]
>Si necesita crear manualmente un usuario, póngase en contacto con el [equipo de soporte técnico de SignalFx](mailto:kmazzola@signalfx.com).

### <a name="assign-the-azure-ad-test-user"></a>Asignación del usuario de prueba de Azure AD

En esta sección, habilitará a Britta Simon para que use el inicio de sesión único de Azure concediéndole acceso a SignalFx.

![Asignación de rol de usuario][200] 

**Para asignar a Britta Simon a SignalFx, realice los pasos siguientes:**

1. En Azure Portal, abra la vista de aplicaciones, navegue a la vista de directorio y vaya a **Aplicaciones empresariales**. Luego haga clic en **Todas las aplicaciones**.

    ![Asignar usuario][201] 

2. En la lista de aplicaciones, seleccione **SignalFx**.

    ![Vínculo a SignalFx en la lista de aplicaciones](./media/active-directory-saas-signalfx-tutorial/tutorial_signalfx_app.png)  

3. En el menú de la izquierda, haga clic en **Usuarios y grupos**.

    ![Vínculo "Usuarios y grupos"][202]

4. Haga clic en el botón **Agregar**. Después, seleccione **Usuarios y grupos** en el cuadro de diálogo **Agregar asignación**.

    ![Panel Agregar asignación][203]

5. En el cuadro de diálogo **Usuarios y grupos**, seleccione **Britta Simon** en la lista de usuarios.

6. Haga clic en el botón **Seleccionar** del cuadro de diálogo **Usuarios y grupos**.

7. Haga clic en el botón **Asignar** del cuadro de diálogo **Agregar asignación**.
    
### <a name="test-single-sign-on"></a>Prueba de inicio de sesión único

En esta sección, probará la configuración de inicio de sesión único de Azure AD mediante el Panel de acceso.

Al hacer clic en el icono de SignalFx en el panel de acceso, debería iniciar sesión automáticamente en la aplicación SignalFx.
Para más información sobre el Panel de acceso, consulte [Introducción al Panel de acceso](active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Recursos adicionales

* [Lista de tutoriales sobre cómo integrar aplicaciones SaaS con Azure Active Directory](active-directory-saas-tutorial-list.md)
* [¿Qué es el acceso a aplicaciones y el inicio de sesión único con Azure Active Directory?](manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/active-directory-saas-signalfx-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-signalfx-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-signalfx-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-signalfx-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-signalfx-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-signalfx-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-signalfx-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-signalfx-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-signalfx-tutorial/tutorial_general_203.png

