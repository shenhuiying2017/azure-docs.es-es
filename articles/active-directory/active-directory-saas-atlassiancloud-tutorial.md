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
ms.date: 11/06/2017
ms.author: jeedes
ms.openlocfilehash: fff906913b637c47d394f9127983a08f96d568e4
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-atlassian-cloud"></a>Tutorial: Integración de Azure Active Directory con Atlassian Cloud

En este tutorial, aprenderá a integrar Atlassian Cloud con Azure Active Directory (Azure AD).

La integración de Atlassian Cloud con Azure AD ofrece las ventajas siguientes:

- En Azure AD, puede controlar quién tiene acceso a Atlassian Cloud.
- Puede permitir que los usuarios inicien sesión automáticamente en Atlassian Cloud (inicio de sesión único) con sus cuentas de Azure AD.
- Puede administrar sus cuentas en una ubicación central: Azure Portal.

Si desea saber más sobre la integración de aplicaciones SaaS con Azure AD, consulte [¿Qué es el acceso a aplicaciones y el inicio de sesión único con Azure Active Directory?](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Requisitos previos

Para configurar la integración de Azure AD con Atlassian Cloud se necesitan los siguientes elementos:

- Una suscripción de Azure AD
- Para habilitar el inicio de sesión único en SAML para productos de Atlassian Cloud, debe configurar Identity Manager. Más información sobre [Identity Manager]( https://www.atlassian.com/enterprise/cloud/identity-manager)

> [!NOTE]
> Para probar los pasos de este tutorial, no se recomienda el uso de un entorno de producción.

Para probar los pasos de este tutorial, debe seguir estas recomendaciones:

- No use el entorno de producción, salvo que sea necesario.
- Si no dispone de un entorno de prueba de Azure AD, puede [obtener una versión de prueba durante un mes](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descripción del escenario
En este tutorial, puede probar el inicio de sesión único de Azure AD en un entorno de prueba. La situación descrita en este tutorial consta de dos bloques de creación principales:

1. Incorporación de Atlassian Cloud desde la galería
2. Configuración y comprobación del inicio de sesión único de Azure AD

## <a name="adding-atlassian-cloud-from-the-gallery"></a>Incorporación de Atlassian Cloud desde la galería
Para configurar la integración de Atlassian Cloud en Azure AD, necesita agregar Atlassian Cloud desde la galería a la lista de aplicaciones SaaS administradas.

**Para agregar Atlassian Cloud desde la galería, siga este procedimiento:**

1. En el panel de navegación izquierdo de **[Azure Portal](https://portal.azure.com)**, haga clic en el icono de **Azure Active Directory**. 

    ![Botón Azure Active Directory][1]

2. Vaya a **Aplicaciones empresariales**. A continuación, vaya a **Todas las aplicaciones**.

    ![Hoja Aplicaciones empresariales][2]
    
3. Para agregar una nueva aplicación, haga clic en el botón **Nueva aplicación** de la parte superior del cuadro de diálogo.

    ![Botón Nueva aplicación][3]

4. En el cuadro de búsqueda, escriba **Atlassian Cloud**, seleccione **Atlassian Cloud** en el panel de resultados y, luego, haga clic en el botón **Agregar** para agregar la aplicación.

    ![Atlassian Cloud en la lista de resultados](./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_atlassiancloud_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configuración y prueba del inicio de sesión único en Azure AD

En esta sección, configurará y probará el inicio de sesión único de Azure AD con Atlassian Cloud con un usuario de prueba llamado "Britta Simon".

Para que el inicio de sesión único funcione, Azure AD necesita saber cuál es el usuario homólogo de Atlassian Cloud para un usuario de Azure AD. Es decir, es necesario establecer una relación de vínculo entre un usuario de Azure AD y el usuario relacionado de Atlassian Cloud.

Para establecer la relación de vínculo, en Atlassian Cloud, asigne el valor de **nombre de usuario** de Azure AD como valor de **Nombre de usuario**.

Para configurar y probar el inicio de sesión único de Azure AD con Atlassian Cloud, es necesario completar los siguientes bloques de creación:

1. **[Configuración del inicio de sesión único de Azure AD](#configure-azure-ad-single-sign-on)**: para permitir que los usuarios utilicen esta característica.
2. **[Creación de un usuario de prueba de Azure AD](#create-an-azure-ad-test-user)**: para probar el inicio de sesión único de Azure AD con Britta Simon.
3. **[Creación de un usuario de prueba en Atlassian Cloud](#create-an-atlassian-cloud-test-user)**: para tener un homólogo de Britta Simon en Atlassian Cloud que esté vinculado a su representación en Azure AD.
4. **[Asignación del usuario de prueba de Azure AD](#assign-the-azure-ad-test-user)**: para permitir que Britta Simon use el inicio de sesión único de Azure AD.
5. **[Prueba del inicio de sesión único](#test-single-sign-on)**: para comprobar si la configuración funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configuración del inicio de sesión único de Azure AD

En esta sección, habilitará el inicio de sesión único de Azure AD en Azure portal y configurará el inicio de sesión único en la aplicación Atlassian Cloud.

**Para configurar el inicio de sesión único de Azure AD con Atlassian Cloud, siga este procedimiento:**

1. En la página de integración de la aplicación **Atlassian Cloud** de Azure Portal, haga clic en **Inicio de sesión único**.

    ![Vínculo Configurar inicio de sesión único][4]

2. En el cuadro de diálogo **Inicio de sesión único**, en **Modo** seleccione **Inicio de sesión basado en SAML** para habilitar el inicio de sesión único.
 
    ![Cuadro de diálogo Inicio de sesión único](./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_atlassiancloud_samlbase.png)

3. En la sección **Dominio y direcciones URL de Atlassian Cloud**, realice los siguientes pasos si quiere configurar la aplicación en el modo iniciado por **IDP**:

    ![Información de dominio y direcciones URL de inicio de sesión único de Atlassian Cloud](./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_atlassiancloud_url_new.png)
    
    a. En el cuadro de texto **Identificador**, escriba la dirección URL: `https://id.atlassian.com/login`
    
    b. En el cuadro de texto **URL de respuesta**, escriba la siguiente dirección URL: `https://id.atlassian.com/login/saml/acs`

    c. En el cuadro de texto **Estado de la retransmisión**, escriba una dirección URL que siga este patrón: `https://<instancename>.atlassian.net`

4. Active **Mostrar configuración avanzada de URL** y siga estos pasos si desea configurar la aplicación en el modo iniciado por **SP**:

    ![Información de dominio y direcciones URL de inicio de sesión único de Atlassian Cloud](./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_atlassiancloud_url1.png)

    En el **cuadro de texto URL de inicio de sesión**, escriba una dirección URL con el siguiente patrón: `https://<instancename>.atlassian.net`

    > [!NOTE] 
    > Estos valores no son reales. Puede obtener estos valores en la pantalla de configuración de SAML de Atlassian Cloud, que se explica más adelante en el tutorial.

5. En la sección **Certificado de firma de SAML**, haga clic en **Certificado (Base64)** y, luego, guarde el archivo de certificado en el equipo.

    ![Vínculo de descarga del certificado](./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_atlassiancloud_certificate.png) 

6. Haga clic en el botón **Guardar** .

    ![Botón Guardar de Configuración de inicio de sesión único](./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_general_400.png)

7. En la sección **Configuración de Atlassian Cloud**, haga clic en **Configurar Atlassian Cloud** para abrir la ventana **Configurar inicio de sesión**. Copie **SAML Entity ID and SAML Single Sign-On Service URL** (URL del servicio de inicio de sesión único de SAML e Identificador de entidad de SAML) de la sección **Referencia rápida**.

    ![Configuración de Atlassian Cloud](./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_atlassiancloud_configure.png) 

8. Para configurar el inicio de sesión único de la aplicación, inicie sesión en el portal de Atlassian mediante los derechos de administrador.

9. Vaya a **Administración del sitio de Atlassian** > **Organizaciones y seguridad**. Si no lo ha hecho todavía, cree su organización y asígnele un nombre. A continuación, en el panel de navegación izquierdo, haga clic en **Dominios**.

    ![Configurar inicio de sesión único](./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_atlassiancloud_06.png)

10. Seleccione el método de verificación del dominio: **DNS** o **HTTPS**.

    ![Configurar inicio de sesión único](./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_atlassiancloud_17.png)

11. Para la verificación de DNS, seleccione la pestaña **DNS** en la página **Dominios** y siga estos pasos:

    ![Configurar inicio de sesión único](./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_atlassiancloud_18.png)

    a. Haga clic en **Copiar** para copiar el valor para el registro TXT.

    b. Desde su DNS, busque la página de configuración para agregar un nuevo registro.

    c. Seleccione la opción para agregar un nuevo registro y pegue el valor copiado de la página **Dominios** en el campo **Valor**. Su DNS también puede hacer referencia como **Respuesta** o **Descripción**.

    d. El registro DNS también puede incluir los campos siguientes:
    
    * **Tipo de registro**: escriba **TXT**.
    * **Nombre/Host/Alias**: deje el valor predeterminado (@ o en blanco).
    * **Período de vida (TTL)**: escriba **86400**.
    
    e.  Guarde el registro.

12. Vuelva a la **página Dominios** en la administración de la organización y haga clic en el botón **Verificar dominio**. Escriba el nombre de dominio en la ventana emergente y haga clic en el botón **Verificar dominio**.

    ![Configurar inicio de sesión único](./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_atlassiancloud_19.png)
    
    > [!NOTE]
    > Los cambios en el registro TXT pueden tardar hasta 72 horas en surtir efecto, de modo que no sabrá al instante si la verificación del dominio es correcta. Verifique el estado de verificación en la página **Dominios** poco después de realizar estos pasos. En la pantalla siguiente, verá el estado actualizado como **VERIFICADO**.

    ![Configurar inicio de sesión único](./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_atlassiancloud_20.png)

13. Para la verificación de HTTPS, seleccione la pestaña **HTTPS** en la página **Dominios** y siga estos pasos:

    ![Configurar inicio de sesión único](./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_atlassiancloud_21.png)

    a.  Haga clic en **Descargar archivo** para descargar el archivo HTML.

    b.  Cargue el archivo HTML en el directorio raíz de su dominio.

14. Vuelva a la página **Dominios** en la administración de la organización y haga clic en el botón **Verificar dominio**. Escriba el **nombre de dominio** en la ventana emergente y haga clic en el botón **Verificar dominio**.

    ![Configurar inicio de sesión único](./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_atlassiancloud_22.png)

15. Si el proceso de verificación puede ubicar el archivo cargado en el directorio raíz, el estado del dominio se actualiza a **Verificado**.

    ![Configurar inicio de sesión único](./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_atlassiancloud_23.png)

    > [!NOTE]
    > Para obtener más información, consulte la [documentación de verificación de dominio de Atlassian](https://confluence.atlassian.com/cloud/domain-verification-873871234.html)

16. En la barra de navegación izquierda, haga clic en **Inicio de sesión único de SAML**. Si no lo ha hecho todavía, suscríbase a Identity Manager de Atlassian.

    ![Configurar inicio de sesión único](./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_atlassiancloud_11.png)

17. En el cuadro de diálogo **Agregar configuración de SAML**, agregue la configuración del proveedor de identidades de la manera siguiente:

    ![Configurar inicio de sesión único](./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_atlassiancloud_12.png)

    a. En el cuadro de texto **Identity Provider Entity ID)** (Identificador de entidad del proveedor de identidades), pegue el valor del **identificador de entidad de SAML** que ha copiado de Azure Portal.

    b. En el cuadro de texto **Identity provider SSO URL** (Dirección URL de inicio de sesión único del proveedor de identidades), pegue el valor de la **dirección URL del servicio de inicio de sesión único de SAML**, que ha copiado de Azure Portal.

    c. Abra el certificado descargado en el bloc de notas, copie los valores sin las líneas de inicio y fin y péguelos en el cuadro de **certificado Public X509**.
    
    d. Para guardar la configuración, haga clic en **Guardar configuración**.
     
18. Actualice la configuración de Azure AD para asegurarse de haber configurado las direcciones URL correctas.
  
    ![Configurar inicio de sesión único](./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_atlassiancloud_13.png)

    a. Copie el **id. de identidad de SP** de la pantalla de SAML y pegue el valor en el cuadro **Identificador** en Azure Portal, en la sección **Dominio y direcciones URL** de Atlassian Cloud.
    
    b. Copie la **dirección URL del Servicio de consumidor de aserciones de SP** de la pantalla de SAML y pegue el valor en el cuadro **Dirección URL de respuesta** en Azure Portal, en la sección **Dominio y direcciones URL** de Atlassian Cloud.
    
    c. La dirección URL de inicio de sesión es la dirección URL del inquilino de Atlassian Cloud. 
    
19. En Azure Portal, haga clic en el botón **Guardar**.

    ![Configurar inicio de sesión único](./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_general_400.png)

> [!TIP]
> Ahora puede leer una versión concisa de estas instrucciones en [Azure Portal](https://portal.azure.com) mientras configura la aplicación.  Después de agregar esta aplicación desde la sección **Active Directory > Aplicaciones empresariales**, simplemente haga clic en la pestaña **Inicio de sesión único** y acceda a la documentación insertada a través de la sección **Configuración** de la parte inferior. Puede leer más sobre la característica de documentación insertada aquí: [Vista previa: Administración de inicio de sesión único para aplicaciones empresariales en el nuevo Azure Portal]( https://go.microsoft.com/fwlink/?linkid=845985)

### <a name="create-an-azure-ad-test-user"></a>Creación de un usuario de prueba de Azure AD

El objetivo de esta sección es crear un usuario de prueba en Azure Portal llamado "Britta Simon".

   ![Creación de un usuario de prueba de Azure AD][100]

**Siga estos pasos para crear un usuario de prueba en Azure AD:**

1. En el panel izquierdo de Azure Portal, haga clic en el botón **Azure Active Directory**.

    ![Botón Azure Active Directory](./media/active-directory-saas-atlassian-cloud-tutorial/create_aaduser_01.png)

2. Para mostrar la lista de usuarios, vaya a **Usuarios y grupos** y, luego, haga clic en **Todos los usuarios**.

    ![Vínculos "Usuarios y grupos" y "Todos los usuarios"](./media/active-directory-saas-atlassian-cloud-tutorial/create_aaduser_02.png)

3. En la parte superior del cuadro de diálogo **Todos los usuarios**, haga clic en **Agregar** para abrir el cuadro de diálogo **Agregar**.

    ![Botón Agregar](./media/active-directory-saas-atlassian-cloud-tutorial/create_aaduser_03.png)

4. En el cuadro de diálogo **Usuario** , realice los pasos siguientes:

    ![Cuadro de diálogo Usuario](./media/active-directory-saas-atlassian-cloud-tutorial/create_aaduser_04.png)

    a. En el cuadro **Nombre**, escriba **BrittaSimon**.

    b. En el cuadro de texto **Nombre de usuario**, escriba la dirección de correo electrónico del usuario Britta Simon.

    c. Active la casilla **Mostrar contraseña** y, después, anote el valor que se muestra en el cuadro **Contraseña**.

    d. Haga clic en **Crear**.
  
### <a name="create-an-atlassian-cloud-test-user"></a>Creación de un usuario de prueba de Atlassian Cloud

Para permitir que los usuarios de Azure AD inicien sesión en Atlassian Cloud, tienen que aprovisionarse en Atlassian Cloud. En el caso de Atlassian Cloud, el aprovisionamiento es una tarea manual.

**Para aprovisionar una cuenta de usuario, realice estos pasos:**

1. En la sección de administración del sitio de Atlassian Portal, haga clic en el botón **Usuarios**.

    ![Creación de un usuario de Atlassian Cloud](./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_atlassiancloud_14.png) 

2. Haga clic en el botón **Invitar a usuario** para crear un usuario en Atlassian Cloud.

    ![Creación de un usuario de Atlassian Cloud](./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_atlassiancloud_15.png) 

3. Escriba la **dirección de correo electrónico** del usuario y asigne el acceso a la aplicación. 

    ![Creación de un usuario de Atlassian Cloud](./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_atlassiancloud_16.png)
 
4. Haga clic en el botón **Invitar a usuarios**. Se enviará la invitación por correo electrónico al usuario y, cuando acepte la invitación, el usuario estará activo en el sistema. 

>[!NOTE] 
>También puede crear usuarios de forma masiva haciendo clic en el botón **Bulk Create** (Crear de forma masiva) en la sección de usuarios.

### <a name="assign-the-azure-ad-test-user"></a>Asignación del usuario de prueba de Azure AD

En esta sección, va a conceder acceso a Britta Simon a Atlassian Cloud para que use el inicio de sesión único de Azure.

![Asignación del rol de usuario][200] 

**Para asignar Britta Simon a Atlassian Cloud, siga este procedimiento:**

1. En Azure Portal, abra la vista de aplicaciones, vaya a la vista de directorio y vaya a **Aplicaciones empresariales**. Luego, haga clic en **Todas las aplicaciones**.

    ![Asignar usuario][201] 

2. En la lista de aplicaciones, seleccione **Atlassian Cloud**.

    ![Vínculo de Atlassian Cloud en la lista de aplicaciones](./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_atlassiancloud_app.png)  

3. En el menú de la izquierda, haga clic en **Usuarios y grupos**.

    ![Vínculo "Usuarios y grupos"][202]

4. Haga clic en el botón **Agregar**. Después, seleccione **Usuarios y grupos** en el cuadro de diálogo **Agregar asignación**.

    ![Panel Agregar asignación][203]

5. En el cuadro de diálogo **Usuarios y grupos**, seleccione **Britta Simon** en la lista de usuarios.

6. Haga clic en el botón **Seleccionar** del cuadro de diálogo **Usuarios y grupos**.

7. Haga clic en el botón **Asignar** del cuadro de diálogo **Agregar asignación**.
    
### <a name="test-single-sign-on"></a>Prueba de inicio de sesión único

En esta sección, probará la configuración de inicio de sesión único de Azure AD mediante el Panel de acceso.

Al hacer clic en el icono de Atlassian Cloud en el panel de acceso, debería iniciar sesión automáticamente en la aplicación Atlassian Cloud.
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

