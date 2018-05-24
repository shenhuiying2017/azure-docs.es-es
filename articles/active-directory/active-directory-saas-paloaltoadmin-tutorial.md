---
title: 'Tutorial: Integración de Azure Active Directory con Palo Alto Networks - Admin UI | Microsoft Docs'
description: 'Obtenga información sobre cómo configurar el inicio de sesión único entre Azure Active Directory y Palo Alto Networks: interfaz de usuario de administración.'
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: a826eaec-15af-4c85-8855-8a3374d1efb9
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/01/2017
ms.author: jeedes
ms.openlocfilehash: aa3366810a40b004fe510cb2909f8da0f3513ddb
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/20/2018
---
# <a name="integrate-azure-active-directory-with-palo-alto-networks---admin-ui"></a>Integración de Azure Active Directory con Palo Alto Networks - Admin UI

En este tutorial, aprenderá a integrar Azure Active Directory (Azure AD) con Palo Alto Networks - Admin UI.

Integración de Azure AD con Palo Alto Networks - Admin UI:

- Puede controlar en Azure AD quién tiene acceso a Palo Alto Networks: interfaz de usuario de administración.
- Puede permitir que los usuarios inicien sesión automáticamente en Palo Alto Networks - Admin UI (inicio de sesión único o SSO) con sus cuentas de Azure AD.
- Puede administrar sus cuentas en una ubicación central: Azure Portal.

Para información sobre la integración de aplicaciones SaaS con Azure AD, consulte [¿Qué es el acceso a aplicaciones y el inicio de sesión único con Azure Active Directory?](manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>requisitos previos

Para configurar la integración de Azure AD con Palo Alto Networks: interfaz de usuario de administración, se necesitan los siguientes elementos:

- Una suscripción de Azure AD
- Un panorama o firewall de nueva generación de Palo Alto Networks (sistema de administración centralizado para los firewalls).

> [!NOTE]
> *No* se recomienda usar un entorno de producción para probar los pasos de este tutorial.

Para probar los pasos de este tutorial, siga estas recomendaciones:

- No use el entorno de producción, salvo que sea necesario.
- Si no dispone de un entorno de prueba de Azure AD, puede [obtener una versión de prueba durante un mes](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descripción del escenario
En este tutorial, puede probar el inicio de sesión único de Azure AD en un entorno de prueba. 

La situación descrita en este tutorial consta de dos bloques de creación principales:

* Agregación de Palo Alto Networks: interfaz de usuario de administración
* Configuración y comprobación del inicio de sesión único de Azure AD

## <a name="add-palo-alto-networks---admin-ui-from-the-gallery"></a>Adición de Palo Alto Networks - Admin UI desde la galería
Para configurar la integración de Azure AD con Palo Alto Networks - Admin UI, es preciso agregar esta aplicación desde la galería hasta la lista de aplicaciones SaaS administradas.

1. En el panel izquierdo de [Azure Portal](https://portal.azure.com), seleccione **Azure Active Directory**. 

    ![Botón Azure Active Directory][1]

2. Seleccione **Aplicaciones empresariales** > **Todas las aplicaciones**.

    ![Ventana Aplicaciones empresariales][2]
    
3. Para agregar una nueva aplicación, seleccione el botón **Nueva aplicación** en la parte superior de la ventana.

    ![Botón “Nueva aplicación”][3]

4. En el cuadro de búsqueda, escriba **Palo Alto Networks - Admin UI**, seleccione **Palo Alto Networks - Admin UI** en la lista de resultados y, a continuación, seleccione **Agregar**.

    ![Palo Alto Networks: interfaz de usuario de administración en la lista de resultados](./media/active-directory-saas-paloaltoadmin-tutorial/tutorial_step4-add-from-the-gallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configuración y prueba del inicio de sesión único en Azure AD

En esta sección, configurará y probará el inicio de sesión único de Azure AD con Palo Alto Networks - Admin UI con un usuario de prueba llamado "Britta Simon".

Para que el inicio de sesión único funcione, Azure AD necesita identificar al usuario de Palo Alto Networks - Admin UI y a su homólogo en Azure AD. Es decir, es preciso establecer una relación de vínculo entre un usuario de Azure AD y el mismo usuario de Palo Alto Networks - Admin UI.

Para establecer la relación de vínculo, en Palo Alto Networks - Admin UI, asigne el valor de *nombre de usuario* como valor de *nombre de usuario* en Azure AD.

Para configurar y probar el inicio de sesión único de Azure AD con Palo Alto Networks - Admin UI, es preciso completar los bloques de creación que se detallan en las siguientes cinco secciones:

### <a name="configure-azure-ad-single-sign-on"></a>Configuración del inicio de sesión único de Azure AD

Habilite el inicio de sesión único de Azure AD en Azure Portal y configure el inicio de sesión único en la aplicación Palo Alto Networks - Admin UI mediante estos pasos:

1. En la página de integración de la aplicación **Palo Alto Networks - Admin UI** de Azure Portal, haga clic en **Inicio de sesión único**.

    ![Vínculo Inicio de sesión único][4]

2. En la ventana **Inicio de sesión único**, en el cuadro **Modo de inicio de sesión único**, seleccione **Inicio de sesión basado en SAML**.
 
    ![Ventana Inicio de sesión único](./media/active-directory-saas-paloaltoadmin-tutorial/tutorial_paloaltoadmin_samlbase.png)

3. En **Palo Alto Networks - Admin UI Domain and URLs** (Dominio y direcciones URL de Palo Alto Networks - Admin UI), haga lo siguiente:

    ![Información de dominio y direcciones URL de inicio de sesión único de Palo Alto Networks - Admin UI](./media/active-directory-saas-paloaltoadmin-tutorial/tutorial_general_show_advanced_url.png)
    
    a. En el cuadro **Inicio de sesión único**, escriba una dirección URL en el siguiente formato: *https://\<FQDN del firewall cliente>/php/login.php*.

    b. En el cuadro **Identificador**, escriba una dirección URL con el siguiente formato: *https://\<FQDN del firewall cliente>:443/SAML20/SP*.
    
    c. En el cuadro **Dirección URL de respuesta**, escriba la dirección URL del servicio de consumidor de aserciones (ACS) en el siguiente formato: *https://\<FQDN del firewall cliente>:443/SAML20/SP/ACS*.
    
    > [!NOTE] 
    > Los valores anteriores no son reales. Debe actualizarlos con la dirección URL y el identificador reales de inicio de sesión. Para obtener los valores, póngase en contacto con [el equipo de atención al cliente de Palo Alto Networks - Admin UI](https://support.paloaltonetworks.com/support). 
 
4. Como la aplicación Palo Alto Networks - Admin UI espera las aserciones de SAML en un formato específico, configure las notificaciones como se muestra en la imagen siguiente. Administre los valores de atributo en la sección **Atributos de usuario** de la página de **integración de aplicaciones** mediante los pasos siguientes:
    
    ![Lista de atributos de token de SAML](./media/active-directory-saas-paloaltoadmin-tutorial/tutorial_paloaltoadmin_attribute.png)
    
   > [!NOTE]
   > Como los valores de atributo son solo ejemplos, asigne los valores adecuados para *username* y *adminrole*. Hay otro atributo opcional, *accessdomain*, que se usa para restringir el acceso de administrador a sistemas virtuales específicos en el firewall.
   >
        
    | Nombre del atributo | Valor de atributo |
    | --- | --- |    
    | nombre de usuario | user.userprincipalname |
    | adminrole | customadmin |

    a. Seleccione **Agregar atributo**.  
    
    ![Botón "Agregar atributo"](./media/active-directory-saas-paloaltoadmin-tutorial/tutorial_attribute_04.png)

    Se abre la ventana **Agregar atributo**.

    ![Ventana "Agregar atributo"](./media/active-directory-saas-paloaltoadmin-tutorial/tutorial_attribute_05.png)
    
    b. En el cuadro **Nombre**, escriba el nombre del atributo que se muestra para esa fila.
    
    c. En el cuadro **Valor**, escriba el valor del atributo que se muestra para esa fila.
    
    d. Seleccione **Aceptar**.

    > [!NOTE]
    > Para más información sobre los atributos, consulte los siguientes artículos:
    > * [Perfil de rol administrativo de Admin UI (adminrole)](https://www.paloaltonetworks.com/documentation/80/pan-os/pan-os/firewall-administration/manage-firewall-administrators/configure-an-admin-role-profile)
    > * [Dominio de acceso de dispositivo de Admin UI (accessdomain)](https://www.paloaltonetworks.com/documentation/80/pan-os/web-interface-help/device/device-access-domain)
    >

5. En **Certificado de firma de SAML**, seleccione **XML de metadatos** y, luego, seleccione **Guardar**.

    ![Vínculo de descarga de XML de metadatos](./media/active-directory-saas-paloaltoadmin-tutorial/tutorial_paloaltoadmin_certificate.png) 

    ![Botón Guardar](./media/active-directory-saas-paloaltoadmin-tutorial/tutorial_general_400.png)

6. Abra la interfaz de usuario de administración del firewall de Palo Alto Networks como administrador en una nueva ventana.

7. Seleccione la pestaña **Device** (Dispositivo).

    ![Pestaña Device (Dispositivo)](./media/active-directory-saas-paloaltoadmin-tutorial/tutorial_paloaltoadmin_admin1.png)

8. En el panel izquierdo, seleccione **SAML Identity Provider** (Proveedor de identidades de SAML) y, luego, seleccione **Import** (Importar) para importar el archivo de metadatos.

    ![Botón para importar archivo de metadatos](./media/active-directory-saas-paloaltoadmin-tutorial/tutorial_paloaltoadmin_admin2.png)

9. En la ventana **SAML Identify Provider Server Profile Import** (Importación del perfil de servidor del proveedor de identidades de SAML), haga lo siguiente:

    ![Ventana de importación del perfil de servidor del proveedor de identidades de SAML](./media/active-directory-saas-paloaltoadmin-tutorial/tutorial_paloaltoadmin_idp.png)

    a. En el cuadro **Profile Name** (Nombre del perfil), proporcione un nombre (por ejemplo, **AzureAD Admin UI**).
    
    b. En **Identity Provider Metadata** (Metadatos del proveedor de identidades), seleccione **Browse** (Examinar) y elija el archivo metadata.xml que descargó anteriormente de Azure Portal.
    
    c. Desactive la casilla **Validate Identity Provider Certificate** (Validar certificado de proveedor de identidades).
    
    d. Seleccione **Aceptar**.
    
    e. Para confirmar las configuraciones en el firewall, seleccione **Commit** (Confirmar).

10. En el panel izquierdo, seleccione **SAML Identity Provider** (Proveedor de identidades de SAML) y elija el perfil del proveedor de identidades de SAML (por ejemplo, **AzureAD Admin UI**) que creó en el paso anterior. 

    ![Perfil del proveedor de identidades de SAML](./media/active-directory-saas-paloaltoadmin-tutorial/tutorial_paloaltoadmin_idp_select.png)

11. En la ventana **SAML Identity Provider Server Profile** (Perfil de servidor del proveedor de identidades de SAML), realice lo siguiente:

    ![Ventana de perfil de servidor del proveedor de identidades de SAML](./media/active-directory-saas-paloaltoadmin-tutorial/tutorial_paloaltoadmin_slo.png)
  
    a. En el cuadro **Identity Provider SLO URL** (Dirección URL de SLO del proveedor de identidades), sustituya la dirección URL de SLO importada anteriormente y agregue la siguiente dirección URL: **https://login.microsoftonline.com/common/wsfederation?wa=wsignout1.0**.
  
    b. Seleccione **Aceptar**.

12. En la interfaz de usuario del firewall de Palo Alto Networks, seleccione **Device** (Dispositivo) y elija **Admin Roles** (Roles de administrador).

13. Seleccione el botón **Agregar**. 

14. En la ventana **Admin Role Profile** (Perfil de rol de administrador), en el cuadro **Name** (Name), proporcione un nombre para el rol de administrador (por ejemplo, **fwadmin**).  
    El nombre del rol de administrador debe coincidir con el nombre de atributo del rol de administrador de SAML enviado por el proveedor de identidades. El nombre del rol de administrador y el valor se crearon en el paso 4.

    ![Configuración del rol de administrador de Palo Alto Networks](./media/active-directory-saas-paloaltoadmin-tutorial/tutorial_paloaltoadmin_adminrole.png)
  
15. En la interfaz de usuario de administrador del firewall, seleccione **Device** (Dispositivo) y elija **Authentication Profile** (Perfil de autenticación).

16. Seleccione el botón **Agregar**. 

17. En la ventana **Authentication Profile** (Perfil de autenticación), haga lo siguiente: 

    ![Ventana de perfil de autenticación](./media/active-directory-saas-paloaltoadmin-tutorial/tutorial_paloaltoadmin_authentication_profile.png)

    a. En el cuadro **Name** (Nombre), proporcione un nombre (por ejemplo, **AzureSAML_Admin_AuthProfile**).
    
    b. En la lista desplegable **Type** (Tipo), seleccione **SAML**. 
   
    c. En la lista desplegable **IdP Server Profile** (Perfil del servidor de IdP), seleccione el perfil adecuado de servidor del proveedor de identidades de SAML (por ejemplo, **AzureAD Admin UI**).
   
    c. Active la casilla **Enable Single Logout** (Habilitar el cierre de sesión único).
    
    d. En el cuadro **Admin Role Attribute** (Atributo de rol de administrador), escriba el nombre del atributo (por ejemplo, **adminrole**). 
    
    e. Seleccione la pestaña **Advanced** (Opciones avanzadas) y, en **Allow List** (Lista de permitidos), seleccione **Add** (Agregar). 
    
    ![Botón para agregar de la pestaña de opciones avanzadas](./media/active-directory-saas-paloaltoadmin-tutorial/tutorial_paloaltoadmin_allowlist.png)
    
    f. Active la casilla **All** (Todos) o seleccione los usuarios y grupos que se pueden autenticar con este perfil.  
    Cuando un usuario se autentica, el firewall compara el nombre de usuario o grupo asociado con las entradas de esta lista. Si no agrega entradas, ningún usuario puede autenticarse.

    g. Seleccione **Aceptar**.

18. Para permitir que los administradores usen SSO de SAML mediante Azure, seleccione **Device** (Dispositivo)  > **Setup** (Configuración). En el panel **Setup** (Configuración), seleccione la pestaña **Management** (Administración) y, en **Authentication Settings** (Configuración de autenticación), seleccione el botón de engranaje **Settings** (Configuración). 

 ![Botón de configuración](./media/active-directory-saas-paloaltoadmin-tutorial/tutorial_paloaltoadmin_authsetup.png)

19. Seleccione el perfil de autenticación de SAML que creó en el paso 17 (por ejemplo, **AzureSAML_Admin_AuthProfile**).

 ![Campo de perfil de autenticación](./media/active-directory-saas-paloaltoadmin-tutorial/tutorial_paloaltoadmin_authsettings.png)

20. Seleccione **Aceptar**.

21. Para confirmar la configuración, seleccione **Commit** (Confirmar).


> [!TIP]
> Mientras configure la aplicación, puede leer una versión concisa de las instrucciones anteriores en [Azure Portal](https://portal.azure.com). Después de agregar la aplicación desde la sección **Active Directory** > **Aplicaciones empresariales**, seleccione la pestaña **Inicio de sesión único** y, después, acceda a la documentación insertada a través de la sección **Configuración** en la parte inferior. Para más información sobre la característica de documentación insertada, consulte la [documentación insertada de Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985).
> 

### <a name="create-an-azure-ad-test-user"></a>Creación de un usuario de prueba de Azure AD

En esta sección, creará el usuario de prueba Britta Simon en Azure Portal siguiendo estos pasos:

![Creación de un usuario de prueba de Azure AD][100]

1. En el panel izquierdo de Azure Portal, seleccione **Azure Active Directory**.

    ![Vínculo de Azure Active Directory](./media/active-directory-saas-paloaltoadmin-tutorial/create_aaduser_01.png)

2. Para mostrar una lista de usuarios actuales, seleccione **Usuarios y grupos** > **Todos los usuarios**.

    ![Vínculos "Usuarios y grupos" y "Todos los usuarios"](./media/active-directory-saas-paloaltoadmin-tutorial/create_aaduser_02.png)

3. En la parte superior de la ventana **Todos los usuarios**, seleccione **Agregar**.

    ![Botón Agregar](./media/active-directory-saas-paloaltoadmin-tutorial/create_aaduser_03.png)
    
    Se abre la ventana **Usuario**.

4. En la ventana **Usuario**, haga lo siguiente:

    ![Ventana Usuario](./media/active-directory-saas-paloaltoadmin-tutorial/create_aaduser_04.png)

    a. En el cuadro **Nombre**, escriba **BrittaSimon**.

    b. En el cuadro de texto **Nombre de usuario**, escriba la dirección de correo electrónico del usuario Britta Simon.

    c. Active la casilla **Mostrar contraseña** y, después, anote el valor que se muestra en el cuadro **Contraseña**.

    d. Seleccione **Crear**.
 
### <a name="create-a-palo-alto-networks---admin-ui-test-user"></a>Creación de un usuario de prueba de Palo Alto Networks: usuario de prueba de la interfaz de usuario de administrador

Palo Alto Networks - Admin UI admite el aprovisionamiento de usuarios Just-In-Time. Si un usuario aún no existe, se crea automáticamente en el sistema después de una autenticación correcta. No es necesaria ninguna acción por su parte para crear el usuario.

### <a name="assign-the-azure-ad-test-user"></a>Asignación del usuario de prueba de Azure AD

En esta sección, permitirá que Britta Simon use el inicio de sesión único de Azure concediéndole acceso a Palo Alto Networks - Admin UI. Para ello, haga lo siguiente:

![Asignación de rol de usuario][200] 

1. En Azure Portal, abra la vista **Aplicaciones**, vaya a la vista **Directorio** y seleccione **Aplicaciones empresariales** > **Todas las aplicaciones**.

    ![Vínculos "Aplicaciones empresariales" y "Todas las aplicaciones"][201] 

2. En la lista **Aplicaciones**, seleccione **Palo Alto Networks - Admin UI**.

    ![Vínculo a Palo Alto Networks - Admin UI](./media/active-directory-saas-paloaltoadmin-tutorial/tutorial_paloaltoadmin_app.png)  

3. En el panel izquierdo, seleccione **Usuarios y grupos**.

    ![Vínculo "Usuarios y grupos"][202]

4. Seleccione **Agregar** y, después, en el panel **Agregar asignación**, seleccione **Usuarios y grupos**.

    ![Panel Agregar asignación][203]

5. En la ventana **Usuarios y grupos**, en la lista **Usuarios**, seleccione **Britta Simon**.

6. Seleccione el botón **Seleccionar**.

7. En la ventana **Agregar asignación**, seleccione **Asignar**.
    
### <a name="test-single-sign-on"></a>Prueba de inicio de sesión único

En esta sección, probará la configuración de inicio de sesión único de Azure AD mediante el Panel de acceso.

Al seleccionar el icono Palo Alto Networks - Admin UI en el Panel de acceso, debería iniciar sesión automáticamente en su aplicación Palo Alto Networks - Admin UI.

Para más información sobre el Panel de acceso, consulte [Introducción al Panel de acceso](active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Recursos adicionales

* [Lista de tutoriales sobre cómo integrar aplicaciones SaaS con Azure Active Directory](active-directory-saas-tutorial-list.md)
* [¿Qué es el acceso a aplicaciones y el inicio de sesión único con Azure Active Directory?](manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/active-directory-saas-paloaltoadmin-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-paloaltoadmin-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-paloaltoadmin-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-paloaltoadmin-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-paloaltoadmin-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-paloaltoadmin-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-paloaltoadmin-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-paloaltoadmin-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-paloaltoadmin-tutorial/tutorial_general_203.png

