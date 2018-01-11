---
title: "Tutorial: Integración de Azure Active Directory con Atlassian Cloud | Microsoft Docs"
description: "Aprenda a configurar el inicio de sesión único entre Azure Active Directory y Atlassian Cloud."
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: 729b8eb6-efc4-47fb-9f34-8998ca2c9545
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/05/2017
ms.author: jeedes
ms.openlocfilehash: db9e9c7ae8380612bac9d0aeaaaf6df78cba523f
ms.sourcegitcommit: d247d29b70bdb3044bff6a78443f275c4a943b11
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/13/2017
---
# <a name="tutorial-azure-active-directory-integration-with-atlassian-cloud"></a>Tutorial: Integración de Azure Active Directory con Atlassian Cloud

En este tutorial, aprenderá a integrar Atlassian Cloud con Azure Active Directory (Azure AD).

La integración de Atlassian Cloud con Azure AD ofrece las ventajas siguientes:

- En Azure AD, puede controlar quién tiene acceso a Atlassian Cloud.
- Puede permitir que los usuarios inicien sesión automáticamente (inicio de sesión único) en Atlassian Cloud con sus cuentas de Azure AD.
- Puede administrar sus cuentas en una ubicación central: Azure Portal.

Para más información sobre la integración de aplicaciones SaaS con Azure AD, consulte [¿Qué es el acceso a aplicaciones y el inicio de sesión único con Azure Active Directory?](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Requisitos previos

Para configurar la integración de Azure AD con Atlassian Cloud se necesitan los siguientes elementos:

- Una suscripción de Azure AD.
- Para habilitar el inicio de sesión único en Lenguaje de marcado de aserción de seguridad (SAML) para productos de Atlassian Cloud, debe configurar Identity Manager. Más información sobre [Identity Manager]( https://www.atlassian.com/enterprise/cloud/identity-manager).

> [!NOTE]
> No se recomienda usar un entorno de producción para probar los pasos de este tutorial.

Para probar los pasos de este tutorial, siga estas recomendaciones:

- No use el entorno de producción, salvo que sea necesario.
- Si no dispone de un entorno de prueba de Azure AD, puede [obtener una versión de prueba durante un mes](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descripción del escenario
En este tutorial, puede probar el inicio de sesión único de Azure AD en un entorno de prueba. El escenario descrito en el tutorial consta de dos bloques de creación principales:

* Incorporación de Atlassian Cloud desde la galería
* Configuración y comprobación del inicio de sesión único de Azure AD

## <a name="add-atlassian-cloud-from-the-gallery"></a>Agregar Atlassian Cloud desde la galería
Para configurar la integración de Atlassian Cloud con Azure AD, agregue Atlassian Cloud desde la galería a la lista de aplicaciones SaaS administradas siguiendo los pasos siguientes:

1. En el panel izquierdo de [Azure Portal](https://portal.azure.com), seleccione el botón **Azure Active Directory**. 

    ![Botón Azure Active Directory][1]

2. Seleccione **Aplicaciones empresariales** > **Todas las aplicaciones**.

    ![Panel Aplicaciones empresariales][2]
    
3. Para agregar una aplicación, seleccione **Nueva aplicación**.

    ![Botón “Nueva aplicación”][3]

4. En el cuadro de búsqueda, escriba **Atlassian Cloud**, en la lista de resultados, seleccione **Atlassian Cloud**y, después, seleccione **Agregar**.

    ![Atlassian Cloud en la lista de resultados](./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_atlassiancloud_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configuración y prueba del inicio de sesión único en Azure AD

En esta sección, configurará y probará el inicio de sesión único de Azure AD con Atlassian Cloud con un usuario de prueba llamado *Britta Simon*.

Para que el inicio de sesión único funcione, Azure AD necesita identificar al usuario de Atlassian Cloud y a su homólogo en Azure AD. Es decir, debe establecer una relación de vínculo entre un usuario de Azure AD y el usuario relacionado de Atlassian Cloud.

Para establecer la relación de vínculo, asigne como *Nombre de usuario* de Atlassian Cloud, el mismo valor que se asigna al *nombre de usuario* de Azure AD.

Para configurar y probar el inicio de sesión único de Azure AD con Atlassian Cloud, es necesario completar los bloques de creación en las secciones siguientes.

### <a name="configure-azure-ad-single-sign-on"></a>Configuración del inicio de sesión único de Azure AD

En esta sección, habilitará el inicio de sesión único de Azure AD en Azure portal y configurará el inicio de sesión único en la aplicación Atlassian Cloud.

Para configurar el inicio de sesión único de Azure AD con Atlassian Cloud, siga este procedimiento:

1. En Azure Portal, en el panel de integración de la aplicación **Atlassian Cloud**, seleccione **Inicio de sesión único**.

    ![Vínculo Configurar inicio de sesión único][4]

2. En la ventana **Inicio de sesión único**, en el cuadro **Modo de inicio de sesión único**, seleccione **Inicio de sesión basado en SAML**.
 
    ![Ventana de inicio de sesión único](./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_atlassiancloud_samlbase.png)

3. En la sección **Dominio y direcciones URL de Atlassian Cloud**, realice los siguientes pasos si quiere configurar la aplicación en el modo iniciado por IDP:

    ![Información de dominio y direcciones URL de inicio de sesión único de Atlassian Cloud](./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_atlassiancloud_url.png)
    
    a. En el cuadro **Identificador**, escriba: **`https://auth.atlassian.com/saml/<unique ID>`**.
    
    b. En el cuadro **URL de respuesta**, escriba: **`https://auth.atlassian.com/login/callback?connection=saml-<unique ID>`**.

    c. En el cuadro **Estado de la retransmisión**, escriba una dirección URL con la siguiente sintaxis: **`https://<instancename>.atlassian.net`**.

4. Para configurar la aplicación en modo iniciado por SP, seleccione **Mostrar configuración avanzada de URL** y, después, en el cuadro **Dirección URL de inicio de sesión**, escriba una dirección URL con la siguiente sintaxis: **`https://<instancename>.atlassian.net`**.

    ![Información de dominio y direcciones URL de inicio de sesión único de Atlassian Cloud](./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_atlassiancloud_url1.png)

    > [!NOTE] 
    > Los valores anteriores no son reales. Actualícelos con valores reales de identificador, URL de respuesta y URL de inicio de sesión. Puede obtener los valores reales en la pantalla de configuración de SAML de Atlassian Cloud. Los valores se explicarán más adelante en el tutorial.

5. En **Certificado de firma de SAML**, seleccione **Certificado (Base64)** y, luego, guarde el archivo del certificado en el equipo.

    ![Vínculo de descarga del certificado](./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_atlassiancloud_certificate.png) 

6. La aplicación Atlassian Cloud espera encontrar las aserciones de SAML en un formato específico, que requiere que se agreguen asignaciones de atributos personalizados a la configuración de los atributos del token de SAML. 

    De forma predeterminada, el valor **Identificador de usuario** se asigna a user.userprincipalname. Cambie este valor para asignarlo a user.mail. También puede elegir cualquier otro valor adecuada según la configuración de su organización, pero en la mayoría de los casos, el correo electrónico debería funcionar.

    ![Vínculo de descarga del certificado](./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_atlassiancloud_attribute.png) 

7. Seleccione **Guardar**.

    ![Botón Guardar de Configurar inicio de sesión único](./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_general_400.png)

8. En la sección **Configuración de Atlassian Cloud**, seleccione **Configurar Atlassian Cloud** para abrir la ventana **Configurar inicio de sesión**. 

9. En la sección **Referencia rápida**, copie los valores de **SAML Entity ID** y **SAML Single Sign-On Service URL** (Identificador de entidad de SAML y URL del servicio de inicio de sesión único de SAML). 

    ![Configuración de Atlassian Cloud](./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_atlassiancloud_configure.png) 

10. Para configurar el inicio de sesión único de la aplicación, inicie sesión en el portal de Atlassian con las credenciales de administrador.

11. Vaya a **Administración del sitio de Atlassian** > **Organizaciones y seguridad**. Si todavía no lo ha hecho, cree su organización y asígnele un nombre y, después, en el panel izquierdo, seleccione **Dominios**.

    ![Configurar inicio de sesión único](./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_atlassiancloud_06.png)

12. Seleccione el método de verificación del dominio: **DNS** o **HTTPS**.

    ![Configurar inicio de sesión único](./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_atlassiancloud_17.png)

13. Para la comprobación de DNS, en la ventana **Dominios**, seleccione la pestaña **DNS** y, después, haga lo siguiente:

    ![Configurar inicio de sesión único](./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_atlassiancloud_18.png)

    a. Para copiar el valor para el registro de texto (registro TXT), seleccione **Copiar**.

    b. Para agregar un registro, vaya a la página de configuración del DNS.

    c. Seleccione la opción para agregar un nuevo registro y pegue el valor copiado de la ventana **Dominios** en el campo **Valor**. El registro DNS también puede hacer referencia como **Respuesta** o **Descripción**.

    d. El registro DNS también puede incluir los campos siguientes:
    
    * En el cuadro **Tipo de registro**, escriba **TXT**.
    * En el cuadro **Nombre/Host/Alias**, deje el valor predeterminado (@ o en blanco).
    * En el cuadro **Período de vida (TTL)**, escriba **86400**.
    
    e.  Guarde el registro.

14. Vuelva a la ventana **Dominios** en la administración de la organización y, después, seleccione **Comprobar dominio**. En el cuadro **Dominio**, escriba el nombre de dominio y, después, seleccione **Comprobar dominio**.

    ![Configurar inicio de sesión único](./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_atlassiancloud_19.png)  

    > [!NOTE]
    > Debido a que los cambios en el registro TXT pueden tardar hasta 72 horas en surtir efecto, no sabrá al instante si la verificación del dominio es correcta. Para ver el estado de comprobación, compruebe la ventana **Dominios** poco después de finalizar este procedimiento. El estado actualizado se mostrará como *Comprobado*, tal como se muestra en la siguiente imagen:
    > 
    > ![Configurar inicio de sesión único](./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_atlassiancloud_20.png)
    > 
    > 

15. Para la comprobación de HTTPS, en la ventana **Dominios**, seleccione la pestaña **HTTPS** y, después, haga lo siguiente:

    ![Configurar inicio de sesión único](./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_atlassiancloud_21.png)

    a. Haga clic en **Descargar archivo** para descargar el archivo HTML.

    b. Cargue el archivo HTML en el directorio raíz de su dominio.

16. Vuelva a la página **Dominios** en la administración de la organización y seleccione **Comprobar dominio**. En la ventana **Comprobar dominio**, en el cuadro **Dominio**, escriba el **nombre de dominio** y, después, seleccione **Comprobar dominio**.

    ![Configurar inicio de sesión único](./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_atlassiancloud_22.png)

17. Si el proceso de comprobación puede ubicar el archivo cargado en el directorio raíz, el estado del dominio se actualiza a *Comprobado*, tal como se muestra aquí:

    ![Configurar inicio de sesión único](./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_atlassiancloud_23.png)

    > [!NOTE]
    > Para obtener más información, consulte [Atlassian domain verification](https://confluence.atlassian.com/cloud/domain-verification-873871234.html) (Comprobación del dominio Atlassian).

18. En el panel izquierdo, haga clic en **Inicio de sesión único de SAML**. Si no lo ha hecho todavía, suscríbase a Identity Manager de Atlassian.

    ![Configurar inicio de sesión único](./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_atlassiancloud_11.png)

19. En la ventana **Agregar configuración de SAML**, haga lo siguiente:

    ![Configurar inicio de sesión único](./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_atlassiancloud_12.png)

    a. En el cuadro **Identity provider Entity ID** (Id. de entidad del proveedor de identidades), pegue el Id. de entidad de SAML que copió de Azure Portal.

    b. En el cuadro **Identity provider SSO URL** (Dirección URL de inicio de sesión único del proveedor de identidades), pegue la dirección URL de inicio de sesión único de SAML que copió de Azure Portal.

    c. Abra el certificado descargado de Azure Portal en un archivo .txt, copie el valor (sin las líneas *Begin Certificate* (Inicio del certificado) ni *End Certificate* (Fin del certificado)) y, después, péguelo en el cuadro **Certificado Public X509**.
    
    d. Seleccione **Guardar configuración**.
     
20. Para asegurarse de que ha configurado las direcciones URL correctas, actualice la configuración de Azure AD mediante los pasos siguientes:
  
    ![Configurar inicio de sesión único](./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_atlassiancloud_13.png)

    a. En la ventana SAML, copie el valor de **Id. de identidad de SP** y, después, en Azure Portal, en la sección **Dominio y direcciones URL** de Atlassian Cloud, péguelo en el cuadro **Identificador**.
    
    b. En la ventana SAML, copie el valor de **SP Assertion Consumer Service URL** (URL del Servicio de consumidor de aserciones de SP) y, después, en Azure Portal, en la sección **Dominio y direcciones URL** de Atlassian Cloud, péguelo en el cuadro **URL de respuesta**.  
        La dirección URL de inicio de sesión es la dirección URL del inquilino de Atlassian Cloud. 

    > [!NOTE]
    > Si es un cliente existente, después de actualizar los valores de **Id. de identidad de SP** y de **SP Assertion Consumer Service URL** (URL del Servicio de consumidor de aserciones de SP) en Azure Portal, seleccione **Yes, update configuration** (Sí, actualizar configuración). Si es un cliente nuevo, puede omitir este paso. 
    
21. En Azure Portal, seleccione **Guardar**.

    ![Configurar inicio de sesión único](./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_general_400.png)

> [!TIP]
> Mientras configure la aplicación, puede leer una versión concisa de las instrucciones anteriores en [Azure Portal](https://portal.azure.com). Después de agregar esta aplicación desde la sección **Active Directory** > **Aplicaciones empresariales**, seleccione la pestaña **Inicio de sesión único** y, después, acceda a la documentación insertada a través de la sección **Configuración** en la parte inferior de la ventana. Para más información, consulte la [documentación insertada de Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985).

### <a name="create-an-azure-ad-test-user"></a>Creación de un usuario de prueba de Azure AD

En esta sección, creará el usuario de prueba Britta Simon en Azure Portal siguiendo estos pasos:

   ![Creación de un usuario de prueba de Azure AD][100]

1. En el panel izquierdo de Azure Portal, seleccione el botón **Azure Active Directory**.

    ![Botón Azure Active Directory](./media/active-directory-saas-atlassian-cloud-tutorial/create_aaduser_01.png)

2. Para mostrar una lista de usuarios, seleccione **Usuarios y grupos** > **Todos los usuarios**.

    ![Vínculos "Usuarios y grupos" y "Todos los usuarios"](./media/active-directory-saas-atlassian-cloud-tutorial/create_aaduser_02.png)

3. En la ventana **Todos los usuarios**, seleccione **Agregar**.

    ![Botón Agregar](./media/active-directory-saas-atlassian-cloud-tutorial/create_aaduser_03.png)

4. En la ventana **Usuario**, haga lo siguiente:

    ![Ventana Usuario](./media/active-directory-saas-atlassian-cloud-tutorial/create_aaduser_04.png)

    a. En el cuadro **Nombre**, escriba **BrittaSimon**.

    b. En el cuadro de texto **Nombre de usuario**, escriba la dirección de correo electrónico del usuario Britta Simon.

    c. Active la casilla **Mostrar contraseña** y, después, anote el valor que se muestra en el cuadro **Contraseña**.

    d. Seleccione **Crear**.
  
### <a name="create-an-atlassian-cloud-test-user"></a>Creación de un usuario de prueba de Atlassian Cloud

Para permitir que los usuarios de Azure AD inicien sesión en Atlassian Cloud, aprovisione las cuentas de usuario manualmente en Atlassian Cloud haciendo lo siguiente:

1. En el panel **Administración**, seleccione **Usuarios**.

    ![Vínculo de usuarios de Atlassian Cloud](./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_atlassiancloud_14.png) 

2. Para crear un usuario en Atlassian Cloud, seleccione **Invitar usuario**.

    ![Creación de un usuario de Atlassian Cloud](./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_atlassiancloud_15.png) 

3. En el cuadro **Dirección de correo electrónico**, escriba la dirección de correo electrónico del usuario y asigne el acceso a la aplicación. 

    ![Creación de un usuario de Atlassian Cloud](./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_atlassiancloud_16.png)
 
4. Para enviar una invitación por correo electrónico al usuario, seleccione **Invitar a usuarios**.  
    Se envía una invitación por correo electrónico al usuario y, después de aceptar la invitación, el usuario se activa en el sistema. 

>[!NOTE] 
>También puede crear usuarios de forma masiva mediante la selección del botón **Bulk Create** (Crear de forma masiva) en la sección **Usuarios**.

### <a name="assign-the-azure-ad-test-user"></a>Asignación del usuario de prueba de Azure AD

En esta sección, va a conceder acceso al usuario Britta Simon a Atlassian Cloud para que use el inicio de sesión único de Azure. Para ello, haga lo siguiente:

![Asignación del rol de usuario][200] 

1. En Azure Portal, abra la vista **Aplicaciones**, vaya a la vista del directorio y seleccione **Aplicaciones empresariales** > **Todas las aplicaciones**.

    ![Asignar usuario][201] 

2. En la lista **Aplicaciones**, seleccione **Atlassian Cloud**.

    ![Vínculo de Atlassian Cloud en la lista de aplicaciones](./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_atlassiancloud_app.png)  

3. En el panel izquierdo, seleccione **Usuarios y grupos**.

    ![Vínculo "Usuarios y grupos"][202]

4. Seleccione **Agregar** y, después, en el panel **Agregar asignación**, seleccione **Usuarios y grupos**.

    ![Panel Agregar asignación][203]

5. En la ventana **Usuarios y grupos**, en la lista **Usuarios**, seleccione **Britta Simon**.

6. En la ventana **Usuarios y grupos**, elija **Seleccionar**.

7. En la ventana **Agregar asignación**, seleccione **Asignar**.
    
### <a name="test-single-sign-on"></a>Prueba de inicio de sesión único

En esta sección, probará la configuración de inicio de sesión único de Azure AD mediante el Panel de acceso.

Al seleccionar el icono de **Atlassian Cloud** en el panel de acceso, debería iniciar sesión automáticamente en la aplicación Atlassian Cloud.
Para más información sobre el Panel de acceso, consulte [Introducción al Panel de acceso](active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Recursos adicionales

* [Lista de tutoriales sobre cómo integrar aplicaciones SaaS con Azure Active Directory](active-directory-saas-tutorial-list.md)
* [¿Qué es el acceso a aplicaciones y el inicio de sesión único con Azure Active Directory?](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_general_203.png

