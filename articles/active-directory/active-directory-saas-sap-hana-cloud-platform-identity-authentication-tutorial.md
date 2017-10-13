---
title: "Tutorial: Integración de Azure Active Directory con SAP Cloud Platform Identity Authentication | Microsoft Docs"
description: "Aprenda a configurar el inicio de sesión único entre Azure Active Directory y SAP Cloud Platform Identity Authentication."
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 1c1320d1-7ba4-4b5f-926f-4996b44d9b5e
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/20/2017
ms.author: jeedes
ms.openlocfilehash: e4e7c7273acc216ae82a52b3e6dcd530a6ad1bd7
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-sap-cloud-platform-identity-authentication"></a>Tutorial: Integración de Azure Active Directory con SAP Cloud Platform Identity Authentication

En este tutorial, aprenderá a integrar SAP Cloud Platform Identity Authentication con Azure Active Directory (Azure AD). SAP Cloud Platform Identity Authentication se utiliza como IdP de proxy para obtener acceso a las aplicaciones de SAP con Azure AD como IdP principal.

La integración de SAP Cloud Platform Identity Authentication con Azure AD proporciona las siguientes ventajas:

- En Azure AD puede controlar quién tiene acceso a la aplicación de SAP.
- Puede permitir que los usuarios inicien sesión automáticamente en aplicaciones de SAP (inicio de sesión único [SSO]) con sus cuentas de Azure AD.
- Puede administrar sus cuentas en una ubicación central: Azure Portal.

Si desea saber más sobre la integración de aplicaciones SaaS con Azure AD, consulte [¿Qué es el acceso a aplicaciones y el inicio de sesión único con Azure Active Directory?](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Requisitos previos

Para configurar la integración de Azure AD con SAP Cloud Platform Identity Authentication, necesita los siguientes elementos:

- Una suscripción de Azure AD
- Una suscripción de SAP Cloud Platform Identity Authentication con el inicio de sesión único habilitado

> [!NOTE]
> Para probar los pasos de este tutorial, no se recomienda el uso de un entorno de producción.

Para probar los pasos de este tutorial, debe seguir estas recomendaciones:

- No use el entorno de producción, salvo que sea necesario.
- Si no dispone de un entorno de prueba de Azure AD, puede [obtener una versión de prueba durante un mes](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descripción del escenario
En este tutorial, puede probar el inicio de sesión único de Azure AD en un entorno de prueba. La situación descrita en este tutorial consta de dos bloques de creación principales:

1. Adición de SAP Cloud Platform Identity Authentication desde la galería
2. Configuración y comprobación del inicio de sesión único de Azure AD

Antes de adentrarnos en los detalles técnicos, es fundamental entender los conceptos vamos a tratar. La federación de Azure Active Directory y SAP Cloud Platform Identity Authentication le permite implementar el inicio de sesión único en aplicaciones o servicios protegidos por AAD (como un IdP) con aplicaciones y servicios de SAP protegidos por SAP Cloud Platform Identity Authentication.

Actualmente, SAP Cloud Platform Identity Authentication actúa como un proveedor de identidades de proxy en las aplicaciones de SAP. A su vez, Azure Active Directory funciona como el proveedor de identidades principal en esta configuración. 

El siguiente diagrama lo muestra:

![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-sapcloudauth-tutorial/architecture-01.png)

Con esta configuración, su inquilino de SAP Cloud Platform Identity Authentication se configurará como una aplicación de confianza en Azure Active Directory. 

Todos los servicios y las aplicaciones de SAP que desea proteger de esta manera se configuran posteriormente en la consola de administración de SAP Cloud Platform Identity Authentication.

Es decir, para llevar a cabo este proceso de configuración, la autorización de concesión de acceso a servicios y aplicaciones de SAP debe realizarse en SAP Cloud Platform Identity Authentication (en lugar de en Azure Active Directory).

Si se configura SAP Cloud Platform Identity Authentication como una aplicación a través de Azure Active Directory Marketplace, no hay que ajustar las notificaciones ni las transformaciones y aserciones de SAML necesarias para producir un token de autenticación válido para las aplicaciones de SAP.

>[!NOTE] 
>Actualmente, el SSO web solo se ha probado en ambas soluciones. Aunque los flujos necesarios para establecer comunicación de aplicación a API o de API a API deberían funcionar, todavía no se han probado. Sin embargo, sí que se hará en actividades posteriores.
>

## <a name="adding-sap-cloud-platform-identity-authentication-from-the-gallery"></a>Adición de SAP Cloud Platform Identity Authentication desde la galería
Para configurar la integración de SAP Cloud Platform Identity Authentication en Azure AD, debe agregar SAP Cloud Platform Identity Authentication desde la galería a la lista de aplicaciones SaaS administradas.

**Para agregar SAP Cloud Platform Identity Authentication desde la galería, realice los siguientes pasos:**

1. En el panel de navegación izquierdo de **[Azure Portal](https://portal.azure.com)**, haga clic en el icono de **Azure Active Directory**. 

    ![Botón Azure Active Directory][1]

2. Vaya a **Aplicaciones empresariales**. A continuación, vaya a **Todas las aplicaciones**.

    ![Hoja Aplicaciones empresariales][2]
    
3. Para agregar una nueva aplicación, haga clic en el botón **Nueva aplicación** de la parte superior del cuadro de diálogo.

    ![Botón Nueva aplicación][3]

4. En el cuadro de búsqueda, escriba **SAP Cloud Platform Identity Authentication**, seleccione **SAP Cloud Platform Identity Authentication** en el panel de resultados y, luego, haga clic en el botón **Agregar** para agregar la aplicación.

    ![SAP Cloud Platform Identity Authentication en la lista de resultados](./media/active-directory-saas-sapcloudauth-tutorial/tutorial_sapcpia_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configuración y prueba del inicio de sesión único en Azure AD

En esta sección, se configura y prueba el inicio de sesión único de Azure AD con SAP Cloud Platform Identity Authentication con un usuario de prueba llamado "Britta Simon".

Para que el inicio de sesión único funcione, es preciso que Azure AD sepa cuál es el usuario homólogo de SAP Cloud Platform Identity Authentication para un usuario de Azure AD. Es decir, hay que establecer una relación de vínculo entre un usuario de Azure AD y el usuario relacionado de SAP Cloud Platform Identity Authentication.

Para establecer la relación de vínculo, en SAP Cloud Platform Identity Authentication, asigne el valor de **nombre de usuario** de Azure AD como valor de **Nombre de usuario**.

Para configurar y probar el inicio de sesión único de Azure AD con SAP Cloud Platform Identity Authentication, es preciso completar los siguientes bloques de creación:

1. **[Configuración del inicio de sesión único de Azure AD](#configure-azure-ad-single-sign-on)**: para permitir que los usuarios utilicen esta característica.
2. **[Creación de un usuario de prueba de Azure AD](#create-an-azure-ad-test-user)**: para probar el inicio de sesión único de Azure AD con Britta Simon.
3. **[Creación de un usuario de SAP Cloud Platform Identity Authentication](#create-an-sap-cloud-platform-identity-authentication-test-user)**: para tener un equivalente de Britta Simon en SAP Cloud Platform Identity Authentication que esté vinculado a la representación del usuario en Azure AD.
4. **[Asignación del usuario de prueba de Azure AD](#assign-the-azure-ad-test-user)**: para permitir que Britta Simon use el inicio de sesión único de Azure AD.
5. **[Prueba del inicio de sesión único](#test-single-sign-on)**: para comprobar si la configuración funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configuración del inicio de sesión único de Azure AD

En esta sección, habilitará el inicio de sesión único de Azure AD en Azure Portal y configurará el inicio de sesión único en la aplicación SAP Cloud Platform Identity Authentication.

**Para configurar el inicio de sesión único de Azure AD con SAP Cloud Platform Identity Authentication, realice los siguientes pasos:**

1. En Azure Portal, en la página de integración de la aplicación **SAP Cloud Platform Identity Authentication**, haga clic en **Inicio de sesión único**.

    ![Vínculo Configurar inicio de sesión único][4]

2. En el cuadro de diálogo **Inicio de sesión único**, en **Modo** seleccione **Inicio de sesión basado en SAML** para habilitar el inicio de sesión único.
 
    ![Cuadro de diálogo Inicio de sesión único](./media/active-directory-saas-sapcloudauth-tutorial/tutorial_sapcpia_samlbase.png)

3. En la sección **Dominio y direcciones URL de SAP Cloud Platform Identity Authentication**, si quiere configurar la aplicación en el modo iniciado por **IDP**:

    ![Información de dominio y direcciones URL de inicio de sesión único de SAP Cloud Platform Identity Authentication](./media/active-directory-saas-sapcloudauth-tutorial/tutorial_sapcpia_url.png)

    En el cuadro de texto **Identificador**, escriba una dirección URL con el siguiente patrón: `https://<entity-id>.accounts.ondemand.com`

    > [!NOTE] 
    > Este valor no es real. Actualícelo con el identificador real. Póngase en contacto con el [equipo de soporte técnico de SAP Cloud Platform Identity Authentication](https://cloudplatform.sap.com/capabilities/security/trustcenter.html) para obtener este valor. Si no conoce este valor, siga la documentación de SAP Cloud Platform Identity Authentication sobre la [Configuración de inquilino de SAML 2.0](https://help.hana.ondemand.com/cloud_identity/frameset.htm?e81a19b0067f4646982d7200a8dab3ca.html).

4. Active **Mostrar configuración avanzada de URL**. Si quiere volver a configurar la aplicación en modo iniciado por **SP**:

    ![Información de dominio y direcciones URL de inicio de sesión único de SAP Cloud Platform Identity Authentication](./media/active-directory-saas-sapcloudauth-tutorial/tutorial_sapcpia_url1.png)

    En el cuadro de texto **URL de inicio de sesión**, escriba una dirección URL con el siguiente patrón: `https://<entity-id>.accounts.ondemand.com/admin`.

    > [!NOTE] 
    > Este valor no es real. Actualícelo con la dirección URL de inicio de sesión real. Póngase en contacto con el [equipo de soporte técnico de SAP Cloud Platform Identity Authentication](https://cloudplatform.sap.com/capabilities/security/trustcenter.html) para obtener este valor.

5. En la sección **Certificado de firma de SAML**, haga clic en **XML de metadatos** y luego guarde el archivo de metadatos en el equipo.

    ![Vínculo de descarga del certificado](./media/active-directory-saas-sapcloudauth-tutorial/tutorial_sapcpia_certificate.png)

6. La aplicación SAP Cloud Platform Identity Authentication espera las aserciones de SAML con un formato concreto. Puede administrar los valores de estos atributos en la sección "**Atributos de usuario**" de la página de integración de aplicaciones. La siguiente captura de pantalla le muestra un ejemplo de esto. 

    ![Configurar inicio de sesión único](./media/active-directory-saas-sapcloudauth-tutorial/attribute.png)

7. En la sección **Atributos de usuario** del cuadro de diálogo **Inicio de sesión único**, si la aplicación de SAP espera un atributo, por ejemplo, "firstName". En el cuadro de diálogo Atributos de token de SAML, agregue el atributo "firstName".

    a. Haga clic en **Agregar atributo** para abrir el cuadro de diálogo **Agregar atributo**.
    
    ![Configurar inicio de sesión único](./media/active-directory-saas-sapcloudauth-tutorial/tutorial_attribute_04.png)
    
    ![Configurar inicio de sesión único](./media/active-directory-saas-sapcloudauth-tutorial/tutorial_attribute_05.png)
    
    b. En el cuadro de texto **Nombre**, escriba el nombre de atributo "firstName".
    
    c. En la lista **Valor**, seleccione el valor de atributo "user.givenname".
    
    d. Haga clic en **Aceptar**.

8. Haga clic en el botón **Guardar** .

    ![Botón Guardar de Configuración de inicio de sesión único](./media/active-directory-saas-sapcloudauth-tutorial/tutorial_general_400.png)

9. En la sección **Configuración de SAP Cloud Platform Identity Authentication**, haga clic en **Configurar SAP Cloud Platform Identity Authentication** para abrir la ventana **Configurar inicio de sesión**. Copie la **URL del servicio de inicio de sesión único de SAML, el identificador de entidad de SAML y la dirección URL de cierre de sesión** de la sección **Referencia rápida**.

    ![Configuración de SAP Cloud Platform Identity Authentication](./media/active-directory-saas-sapcloudauth-tutorial/tutorial_sapcpia_configure.png) 

10. Para configurar el SSO en la aplicación, vaya a la consola de administración de SAP Cloud Platform Identity Authentication. La dirección URL tiene el siguiente patrón: `https://<tenant-id>.accounts.ondemand.com/admin`. Después, siga la documentación de SAP Cloud Platform Identity Authentication para [Configurar Microsoft Azure AD como proveedor de identidades corporativas en SAP Cloud Platform Identity Authentication](https://help.hana.ondemand.com/cloud_identity/frameset.htm?626b17331b4d4014b8790d3aea70b240.html). 

11. En Azure Portal, haga clic en el botón **Guardar**.

12. Siga los pasos siguientes solo si desea agregar y habilitar SSO para otra aplicación de SAP. Repita los pasos de la sección "Agregación de SAP Cloud Platform Identity Authentication desde la galería" para agregar otra instancia de SAP Cloud Platform Identity Authentication.

13. En Azure Portal, en la página de integración de la aplicación **SAP Cloud Platform Identity Authentication**, haga clic en **Inicio de sesión vinculado**.

    ![Configuración del inicio de sesión vinculado](./media/active-directory-saas-sapcloudauth-tutorial/linked_sign_on.png)

14. Guarde la configuración.

>[!NOTE] 
>La nueva aplicación usará la configuración de SSO para la aplicación de SAP anterior. Asegúrese de usar los mismos proveedores de identidades corporativos en la consola de administración de SAP Cloud Platform Identity Authentication.

> [!TIP]
> Ahora puede leer una versión resumida de estas instrucciones dentro de [Azure Portal](https://portal.azure.com) mientras configura la aplicación.  Después de agregar esta aplicación desde la sección **Active Directory > Aplicaciones empresariales**, simplemente haga clic en la pestaña **Inicio de sesión único** y acceda a la documentación insertada a través de la sección **Configuración** de la parte inferior. Puede leer más sobre la característica de documentación insertada aquí: [Vista previa: Administración de inicio de sesión único para aplicaciones empresariales en el nuevo Azure Portal]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="create-an-azure-ad-test-user"></a>Creación de un usuario de prueba de Azure AD

El objetivo de esta sección es crear un usuario de prueba en Azure Portal llamado "Britta Simon".

   ![Creación de un usuario de prueba de Azure AD][100]

**Siga estos pasos para crear un usuario de prueba en Azure AD:**

1. En el panel izquierdo de Azure Portal, haga clic en el botón **Azure Active Directory**.

    ![Botón Azure Active Directory](./media/active-directory-saas-sapcloudauth-tutorial/create_aaduser_01.png)

2. Para mostrar la lista de usuarios, vaya a **Usuarios y grupos** y, luego, haga clic en **Todos los usuarios**.

    ![Vínculos "Usuarios y grupos" y "Todos los usuarios"](./media/active-directory-saas-sapcloudauth-tutorial/create_aaduser_02.png)

3. En la parte superior del cuadro de diálogo **Todos los usuarios**, haga clic en **Agregar** para abrir el cuadro de diálogo **Agregar**.

    ![Botón Agregar](./media/active-directory-saas-sapcloudauth-tutorial/create_aaduser_03.png)

4. En el cuadro de diálogo **Usuario** , realice los pasos siguientes:

    ![Cuadro de diálogo Usuario](./media/active-directory-saas-sapcloudauth-tutorial/create_aaduser_04.png)

    a. En el cuadro **Nombre**, escriba **BrittaSimon**.

    b. En el cuadro de texto **Nombre de usuario**, escriba la dirección de correo electrónico del usuario Britta Simon.

    c. Active la casilla **Mostrar contraseña** y, después, anote el valor que se muestra en el cuadro **Contraseña**.

    d. Haga clic en **Crear**.
 
### <a name="create-an-sap-cloud-platform-identity-authentication-test-user"></a>Creación de un usuario de prueba de SAP Cloud Platform Identity Authentication

No hace falta crear un usuario en SAP Cloud Platform Identity Authentication. Los usuarios que están en el almacén de usuarios de Azure AD pueden utilizar la funcionalidad de inicio de sesión único (SSO).

SAP Cloud Platform Identity Authentication es compatible con la opción de federación de identidades. Esta opción permite a la aplicación comprobar si los usuarios autenticados mediante el proveedor de identidades corporativas se encuentran en el almacén de usuarios de SAP Cloud Platform Identity Authentication. 

En la configuración predeterminada, la opción de federación de identidades está deshabilitada. Si se habilita, solo los usuarios que se importan de SAP Cloud Platform Identity Authentication pueden tener acceso a la aplicación. 

Para más información sobre cómo habilitar o deshabilitar la federación de identidades con SAP Cloud Platform Identity Authentication, consulte la sección Enable Identity Federation with SAP Cloud Platform Identity Authentication (Activación de la federación de identidades con SAP Cloud Platform Identity Authentication) del artículo [Configure Identity Federation with the User Store of SAP Cloud Platform Identity Authentication](https://help.hana.ondemand.com/cloud_identity/frameset.htm?c029bbbaefbf4350af15115396ba14e2.html) (Configuración de la federación de identidades con el almacén de usuarios de SAP Cloud Platform Identity Authentication).

### <a name="assign-the-azure-ad-test-user"></a>Asignación del usuario de prueba de Azure AD

En esta sección, permitirá que Britta Simon use el inicio de sesión único de Azure concediéndole acceso a SAP Cloud Platform Identity Authentication.

![Asignación del rol de usuario][200] 

**Para asignar a Britta Simon a SAP Cloud Platform Identity Authentication, realice los siguientes pasos:**

1. En Azure Portal, abra la vista de aplicaciones, vaya a la vista de directorio y vaya a **Aplicaciones empresariales**. Luego, haga clic en **Todas las aplicaciones**.

    ![Asignar usuario][201] 

2. En la lista de aplicaciones, seleccione **SAP Cloud Platform Identity Authentication**.

    ![Vínculo SAP Cloud Platform Identity Authentication en la lista de aplicaciones](./media/active-directory-saas-sapcloudauth-tutorial/tutorial_sapcpia_app.png)  

3. En el menú de la izquierda, haga clic en **Usuarios y grupos**.

    ![Vínculo "Usuarios y grupos"][202]

4. Haga clic en el botón **Agregar**. Después, seleccione **Usuarios y grupos** en el cuadro de diálogo **Agregar asignación**.

    ![Panel Agregar asignación][203]

5. En el cuadro de diálogo **Usuarios y grupos**, seleccione **Britta Simon** en la lista de usuarios.

6. Haga clic en el botón **Seleccionar** del cuadro de diálogo **Usuarios y grupos**.

7. Haga clic en el botón **Asignar** del cuadro de diálogo **Agregar asignación**.
    
### <a name="test-single-sign-on"></a>Prueba de inicio de sesión único

En esta sección, probará la configuración de inicio de sesión único de Azure AD mediante el Panel de acceso.

Al hacer clic en el icono de SAP Cloud Platform Identity Authentication del panel de acceso, debe iniciar sesión automáticamente en la aplicación SAP Cloud Platform Identity Authentication.
Para más información sobre el Panel de acceso, consulte [Introducción al Panel de acceso](active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Recursos adicionales

* [Lista de tutoriales sobre cómo integrar aplicaciones SaaS con Azure Active Directory](active-directory-saas-tutorial-list.md)
* [¿Qué es el acceso a aplicaciones y el inicio de sesión único con Azure Active Directory?](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-sapcloudauth-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-sapcloudauth-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-sapcloudauth-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-sapcloudauth-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-sapcloudauth-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-sapcloudauth-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-sapcloudauth-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-sapcloudauth-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-sapcloudauth-tutorial/tutorial_general_203.png

