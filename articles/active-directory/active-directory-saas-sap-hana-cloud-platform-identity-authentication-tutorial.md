---
title: "Tutorial: Integración de Azure Active Directory con SAP HANA Cloud Platform Identity Authentication | Microsoft Docs"
description: "Aprenda a configurar el inicio de sesión único entre Azure Active Directory y SAP HANA Cloud Platform Identity Authentication."
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.assetid: 1c1320d1-7ba4-4b5f-926f-4996b44d9b5e
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/07/2017
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: cfe4957191ad5716f1086a1a332faf6a52406770
ms.openlocfilehash: 87c3b66a8789254e962ccfd7fc1eb7842c0db638
ms.lasthandoff: 03/09/2017


---
# <a name="tutorial-azure-active-directory-integration-with-sap-hana-cloud-platform-identity-authentication"></a>Tutorial: Integración de Azure Active Directory con SAP HANA Cloud Platform Identity Authentication

En este tutorial, aprenderá a integrar SAP HANA Cloud Platform Identity Authentication con Azure Active Directory (Azure AD). SAP HANA Cloud Platform Identity Authentication se utiliza como IdP de proxy para obtener acceso a las aplicaciones de SAP con Azure AD como IdP principal.

La integración de SAP HANA Cloud Platform Identity Authentication con Azure AD proporciona las siguientes ventajas:

- En Azure AD puede controlar quién tiene acceso a la aplicación de SAP.
- Puede permitir que los usuarios inicien sesión automáticamente en las aplicaciones de SAP (inicio de sesión único) con sus cuentas de Azure AD.
- Puede administrar sus cuentas en una ubicación central: el Portal de Azure clásico.

Si desea obtener más información sobre la integración de aplicaciones SaaS con Azure AD, vea [Qué es el acceso a las aplicaciones y el inicio de sesión único en Azure Active Directory](active-directory-appssoaccess-whatis.md).


## <a name="prerequisites"></a>Requisitos previos

Para configurar la integración de Azure AD con SAP HANA Cloud Platform Identity Authentication, necesita los siguientes elementos:

- Una suscripción de Azure AD
- Una suscripción de **SAP HANA Cloud Platform Identity Authentication** con el inicio de sesión único habilitado


>[!NOTE] 
>Para probar los pasos de este tutorial, no se recomienda el uso de un entorno de producción.


Para probar los pasos de este tutorial, debe seguir estas recomendaciones:

- No debe usar el entorno de producción, a menos que sea necesario.
- Si no dispone de un entorno de prueba de Azure AD, puede obtener una versión de prueba de un mes [aquí](https://azure.microsoft.com/pricing/free-trial/).



## <a name="scenario-description"></a>Descripción del escenario
En este tutorial, puede probar el inicio de sesión único de Azure AD en un entorno de prueba.

La situación descrita en este tutorial consta de dos bloques de creación principales:

1. Incorporación de SAP HANA Cloud Platform Identity Authentication desde la galería
2. Configuración y comprobación del inicio de sesión único de Azure AD

Antes de adentrarnos en los detalles técnicos, es fundamental entender los conceptos vamos a estudiar. La federación de Azure Active Directory y SAP HANA Cloud Platform Identity Authentication le permite implementar el inicio de sesión único en aplicaciones o servicios protegidos por AAD (como un IdP) con aplicaciones y servicios de SAP protegidos por SAP HANA Cloud Platform Identity Authentication.

Actualmente, SAP HANA Cloud Platform Identity Authentication actúa como un proveedor de identidades de proxy en las aplicaciones de SAP. A su vez, Azure Active Directory funciona como el proveedor de identidades principal en esta configuración. El siguiente diagrama lo muestra:    

![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-sap-hana-cloud-tutorial/architecture-01.png)

Con esta configuración, su inquilino de SAP HANA Cloud Platform Identity Authentication se configurará como una aplicación de confianza en Azure Active Directory. 

Todos los servicios y las aplicaciones de SAP que desea proteger de esta manera se configuran posteriormente en la consola de administración de SAP HANA Cloud Platform Identity Authentication.

Es muy importante entender esto. Es decir, para llevar a cabo este proceso de configuración, la autorización de concesión de acceso a servicios y aplicaciones de SAP debe realizarse en SAP HANA Cloud Platform Identity Authentication (en lugar de en Azure Active Directory).

Si se configura SAP HANA Cloud Platform Identity Authentication como una aplicación a través de Azure Active Directory Marketplace, no hay que ajustar las notificaciones ni las transformaciones y aserciones de SAML necesarias para producir un token de autenticación válido para las aplicaciones de SAP.

>[!NOTE] 
>Actualmente, el SSO web solo se ha probado en ambas soluciones. Aunque los flujos necesarios para establecer comunicación de aplicación a API o de API a API deberían funcionar, todavía no se han probado. Sin embargo, sí que se hará en actividades posteriores. Cuando se valide correctamente el funcionamiento, la documentación se actualizará según corresponda.

## <a name="adding-sap-hana-cloud-platform-identity-authentication-from-the-gallery"></a>Incorporación de SAP HANA Cloud Platform Identity Authentication desde la galería
Para configurar la integración de SAP HANA Cloud Platform Identity Authentication en Azure AD, debe agregar SAP HANA Cloud Platform Identity Authentication desde la galería a la lista de aplicaciones administradas de SaaS.

**Para agregar SAP HANA Cloud Platform Identity Authentication desde la galería, realice los siguientes pasos:**

1. En el panel de navegación izquierdo del [**Portal de administración de Azure**](https://portal.azure.com), haga clic en el icono de **Azure Active Directory**. 

    ![Active Directory][1]

2. Vaya a **Aplicaciones empresariales**. A continuación, vaya a **Todas las aplicaciones**.

    ![Aplicaciones][2]
    
3. Haga clic en el botón **Agregar** situado en la parte superior del cuadro de diálogo.

    ![Aplicaciones][3]

4. En el cuadro de búsqueda, escriba **SAP HANA Cloud Platform Identity Authentication**.

    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-sap-hana-cloud-tutorial/tutorial_sap_cloud_identity_01.png)

5. En el panel de resultados, seleccione **SAP HANA Cloud Platform Identity Authentication**y, luego, haga clic en el botón **Agregar** para incorporar la aplicación.

    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-sap-hana-cloud-tutorial/tutorial_sap_cloud_identity_02.png)


##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configuración y comprobación del inicio de sesión único de Azure AD
En esta sección, se configura y prueba el inicio de sesión único de Azure AD con SAP HANA Cloud Platform Identity Authentication con un usuario de prueba llamado "Britta Simon".

Para que el inicio de sesión único funcione, es preciso que Azure AD sepa cuál es el usuario homólogo de SAP HANA Cloud Platform Identity Authentication para un usuario de Azure AD. Es decir, hay que establecer una relación de vínculo entre un usuario de Azure AD y el usuario relacionado de SAP HANA Cloud Platform Identity Authentication.

Esta relación de vínculo se establece asignando el valor del **nombre de usuario** de Azure AD como el del **nombre de usuario** de SAP HANA Cloud Platform Identity Authentication.

Para configurar y probar el inicio de sesión único de Azure AD con SAP HANA Cloud Platform Identity Authentication, es preciso completar los siguientes bloques de creación:

1. **[Configuring Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)** : para permitir a los usuarios usar esta característica.
2. **[Creación de un usuario de prueba de Azure AD](#creating-an-azure-ad-test-user)** : para probar el inicio de sesión único de Azure AD con Britta Simon.
3. **[Creación de un usuario de SAP HANA Cloud Platform Identity Authentication](#creating-a-sap-hana-cloud-platform-identity-authentication-test-user)**: para tener un equivalente de Britta Simon en SAP HANA Cloud Platform Identity Authentication que esté vinculado a su representación de Azure AD.
4. **[Asignación del usuario de prueba de Azure AD](#assigning-the-azure-ad-test-user)** : para permitir que Britta Simon use el inicio de sesión único de Azure AD.
5. **[Testing Single Sign-On](#testing-single-sign-on)** : para comprobar si funciona la configuración.

### <a name="configuring-azure-ad-single-sign-on"></a>Configuración del inicio de sesión único de Azure AD

En esta sección, habilitará el inicio de sesión único de Azure AD en el Portal de administración de Azure y configurará el inicio de sesión único en la aplicación SAP HANA Cloud Platform Identity Authentication.

La aplicación SAP HANA Cloud Platform Identity Authentication espera las aserciones de SAML con un formato concreto. Puede administrar los valores de estos atributos en la sección "**Atributos de usuario**" de la página de integración de aplicaciones. La siguiente captura de pantalla le muestra un ejemplo de esto.

![Configurar inicio de sesión único](./media/active-directory-saas-sap-hana-cloud-tutorial/tutorial_sap_cloud_identity_03.png)

**Para configurar el inicio de sesión único de Azure AD con SAP HANA Cloud Platform Identity Authentication, realice los siguientes pasos:**

1. En el Portal de administración de Azure, en la página de integración de la aplicación **SAP HANA Cloud Platform Identity Authentication**, haga clic en **Inicio de sesión único**.

    ![Configurar inicio de sesión único][4]

2. En el cuadro de diálogo **Inicio de sesión único**, en **Modo**, seleccione **Inicio de sesión basado en SAML** para habilitar el inicio de sesión único.
 
    ![Configurar inicio de sesión único][5]

3. En la sección **Atributos de usuario** del cuadro de diálogo **Inicio de sesión único**, si la aplicación de SAP espera un atributo, por ejemplo, "firstName". En el cuadro de diálogo Atributos de token de SAML, agregue el atributo "firstName".

    a. Haga clic en **Agregar atributo** para abrir el cuadro de diálogo **Agregar atributo**.

    ![Configurar inicio de sesión único][6]

    ![Configurar inicio de sesión único](./media/active-directory-saas-sap-hana-cloud-tutorial/tutorial_sap_cloud_identity_05.png)
    
    b. En el cuadro de texto **Nombre de atributo**, escriba el nombre de atributo "firstName".

    c. En la lista **Valor de atributo**, seleccione el valor de atributo "user.givenname".
    
    d. Haga clic en **Aceptar**.


4. En la sección **Dominio y direcciones URL de SAP HANA Cloud Platform Identity Authentication**, realice los siguientes pasos:

    ![Configurar inicio de sesión único](./media/active-directory-saas-sap-hana-cloud-tutorial/tutorial_sap_cloud_identity_06.png)

    a. En el cuadro de texto **URL de inicio de sesión**, escriba la URL de inicio de sesión de la aplicación de SAP.

    b. En el cuadro de texto **Identificador**, escriba el valor con el siguiente patrón: `<entity-id>.accounts.ondemand.com`. Si no conoce este valor, siga la documentación de SAP HANA Cloud Platform Identity Authentication; en concreto, el capítulo de [configuración de inquilino de SAML 2.0](https://help.hana.ondemand.com/cloud_identity/frameset.htm?e81a19b0067f4646982d7200a8dab3ca.html)


5. En la sección **Configuración de SAP HANA Cloud Platform Identity Authentication**, haga clic en **Configurar SAP HANA Cloud Platform Identity Authentication** para abrir el cuadro de diálogo **Configurar inicio de sesión**. Luego, haga clic en **SAML XML Metadata** (Metadatos XML de SAML) y guarde el archivo en el equipo.

    ![Configurar inicio de sesión único](./media/active-directory-saas-sap-hana-cloud-tutorial/tutorial_sap_cloud_identity_07.png) 

    ![Configurar inicio de sesión único](./media/active-directory-saas-sap-hana-cloud-tutorial/tutorial_sap_cloud_identity_08.png)


6. Para configurar el SSO en su aplicación, vaya a la consola de administración de SAP HANA Cloud Platform Identity Authentication. La dirección URL tiene el siguiente patrón: `https://<tenant-id>.accounts.ondemand.com/admin`. Después, siga la documentación de SAP HANA Cloud Platform Identity Authentication para [configurar Microsoft Azure AD como proveedor de identidades corporativas en SAP HANA Cloud Platform Identity Authentication](https://help.hana.ondemand.com/cloud_identity/frameset.htm?626b17331b4d4014b8790d3aea70b240.html). 

7. En el Portal de administración de Azure, haga clic en **Guardar**.
8. Siga los pasos siguientes solo si desea agregar y habilitar SSO para otra aplicación de SAP. Repita los pasos de la sección "Incorporación de SAP HANA Cloud Platform Identity Authentication desde la galería" para agregar otra instancia de SAP HANA Cloud Platform Identity Authentication.
9. En el Portal de administración de Azure, en la página de integración de la aplicación **SAP HANA Cloud Platform Identity Authentication**, haga clic en **Inicio de sesión vinculado**.

     ![Configuración del inicio de sesión vinculado](./media/active-directory-saas-sap-hana-cloud-tutorial/linked_sign_on.png)
10. Luego, guarde la configuración.

>[!NOTE] 
>La nueva aplicación usará la configuración de SSO para la aplicación de SAP anterior. Asegúrese de usar los mismos proveedores de identidades corporativas en la consola de administración de SAP HANA Cloud Platform Identity Authentication.

### <a name="creating-an-azure-ad-test-user"></a>Creación de un usuario de prueba de Azure AD
El objetivo de esta sección es crear un usuario de prueba en el nuevo portal llamado "Britta Simon".

![Creación de un usuario de Azure AD][100]

**Siga estos pasos para crear un usuario de prueba en Azure AD:**

1. En el panel de navegación izquierdo del **Portal de administración de Azure**, haga clic en el icono de **Azure Active Directory**.

    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-sap-hana-cloud-tutorial/create_aaduser_01.png) 

2. Vaya a **Usuarios y grupos** y haga clic en **Todos los usuarios** para mostrar la lista de usuarios.
    
    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-sap-hana-cloud-tutorial/create_aaduser_02.png) 

3. En la parte superior del diálogo, haga clic en **Agregar** para abrir el diálogo **Usuario**.
 
    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-sap-hana-cloud-tutorial/create_aaduser_03.png) 

4. En la página de diálogo **Usuario**, realice los siguientes pasos:
 
    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-sap-hana-cloud-tutorial/create_aaduser_04.png) 

    a. En el cuadro de texto **Nombre**, escriba **BrittaSimon**.

    b. En el cuadro de texto **Nombre de usuario**, escriba la **dirección de correo electrónico** de Britta Simon.

    c. Seleccione **Mostrar contraseña** y anote el valor del cuadro **Contraseña**.

    d. Haga clic en **Crear**. 



### <a name="creating-a-sap-hana-cloud-platform-identity-authentication-test-user"></a>Creación de un usuario de prueba de SAP HANA Cloud Platform Identity Authentication

No hace falta crear un usuario en SAP HANA Cloud Platform Identity Authentication. Los usuarios que están en el almacén de usuarios de Azure AD pueden utilizar la funcionalidad de inicio de sesión único (SSO).

SAP HANA Cloud Platform Identity Authentication es compatible con la opción de federación de identidades. Esta opción permite a la aplicación comprobar si los usuarios autenticados mediante el proveedor de identidades corporativas se encuentran en el almacén de usuarios de SAP HANA Cloud Platform Identity Authentication. En la configuración predeterminada, la opción de federación de identidades está deshabilitada. Si se habilita, solo los usuarios que se importan de SAP HANA Cloud Platform Identity Authentication pueden tener acceso a la aplicación. 

Para obtener más información sobre cómo habilitar o deshabilitar la federación de identidades con SAP HANA Cloud Platform Identity Authentication, consulte la sección Enable Identity Federation with SAP HANA Cloud Platform Identity Authentication (Activación de la federación de identidades con SAP HANA Cloud Platform Identity Authentication) del artículo [Configure Identity Federation with the User Store of SAP HANA Cloud Platform Identity Authentication](https://help.hana.ondemand.com/cloud_identity/frameset.htm?c029bbbaefbf4350af15115396ba14e2.html) (Configuración de la federación de identidades con el almacén de usuarios de SAP HANA Cloud Platform Identity Authentication).


### <a name="assigning-the-azure-ad-test-user"></a>Asignación del usuario de prueba de Azure AD

En esta sección, permitirá que Britta Simon use el inicio de sesión único de Azure concediéndole acceso a SAP HANA Cloud Platform Identity Authentication.

![Asignar usuario][200] 

**Para asignar Britta Simon a SAP HANA Cloud Platform Identity Authentication, realice los siguientes pasos:**

1. En el Portal de administración de Azure, abra la vista de aplicaciones, vaya a la vista de directorio y seleccione **Aplicaciones empresariales**. Después, haga clic en **Todas las aplicaciones**.

    ![Asignar usuario][201] 

2. En la lista de aplicaciones, seleccione **SAP HANA Cloud Platform Identity Authentication**.

    ![Configurar inicio de sesión único](./media/active-directory-saas-sap-hana-cloud-tutorial/tutorial_sap_cloud_identity_09.png)

3. En el menú de la izquierda, haga clic en **Usuarios y grupos**.

    ![Asignar usuario][202] 

4. Haga clic en el botón **Agregar**. Después, seleccione **Usuarios y grupos** en el cuadro de diálogo **Agregar asignación**.

    ![Asignar usuario][203]

5. En el cuadro de diálogo **Usuarios y grupos**, seleccione **Britta Simon** en la lista de usuarios.

6. Haga clic en el botón **Seleccionar** del cuadro de diálogo **Usuarios y grupos**.

7. Haga clic en el botón **Asignar** del cuadro de diálogo **Agregar asignación**.
    


### <a name="testing-single-sign-on"></a>Prueba del inicio de sesión único 

En esta sección, probará la configuración de inicio de sesión único de Azure AD mediante el Panel de acceso.

Al hacer clic en el icono de SAP HANA Cloud Platform Identity Authentication del panel de acceso, debe iniciar sesión automáticamente en su aplicación SAP HANA Cloud Platform Identity Authentication.


## <a name="additional-resources"></a>Recursos adicionales

* [Lista de tutoriales sobre cómo integrar aplicaciones SaaS con Azure Active Directory](active-directory-saas-tutorial-list.md)
* [¿Qué es el acceso a aplicaciones y el inicio de sesión único con Azure Active Directory?](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-sap-hana-cloud-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-sap-hana-cloud-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-sap-hana-cloud-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-sap-hana-cloud-tutorial/tutorial_general_04.png
[5]: ./media/active-directory-saas-sap-hana-cloud-tutorial/tutorial_general_05.png
[6]: ./media/active-directory-saas-sap-hana-cloud-tutorial/tutorial_general_06.png

[100]: ./media/active-directory-saas-sap-hana-cloud-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-sap-hana-cloud-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-sap-hana-cloud-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-sap-hana-cloud-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-sap-hana-cloud-tutorial/tutorial_general_203.png
