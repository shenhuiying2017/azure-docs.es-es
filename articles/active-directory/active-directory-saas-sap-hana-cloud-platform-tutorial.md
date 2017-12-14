---
title: "Tutorial: Integración de Azure Active Directory con SAP Cloud Platform | Microsoft Docs"
description: "Aprenda a configurar el inicio de sesión único entre Azure Active Directory y SAP Cloud Platform."
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: bd398225-8bd8-4697-9a44-af6e6679113a
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/15/2017
ms.author: jeedes
ms.openlocfilehash: 309415a68308943f638195303ceb236569519472
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-sap-cloud-platform"></a>Tutorial: Integración de Azure Active Directory con SAP Cloud Platform

En este tutorial, aprenderá a integrar SAP Cloud Platform con Azure Active Directory (Azure AD).

La integración de SAP Cloud Platform con Azure AD proporciona las siguientes ventajas:

- En Azure AD se puede controlar quién tiene acceso a SAP Cloud Platform.
- Puede permitir que los usuarios inicien sesión automáticamente en SAP Cloud Platform (inicio de sesión único) con su cuenta de Azure AD.
- Puede administrar sus cuentas en una ubicación central: Azure Portal.

Si desea saber más sobre la integración de aplicaciones SaaS con Azure AD, consulte [¿Qué es el acceso a aplicaciones y el inicio de sesión único con Azure Active Directory?](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Requisitos previos

Para configurar la integración de Azure AD con SAP Cloud Platform, necesita los siguientes elementos:

- Una suscripción de Azure AD
- Una suscripción habilitada para inicio de sesión único en SAP Cloud Platform

Después de completar este tutorial, los usuarios de Azure AD que haya asignado a SAP Cloud Platform podrán realizar un inicio de sesión único en la aplicación con la información del artículo de [introducción al panel de acceso](active-directory-saas-access-panel-introduction.md).

>[!IMPORTANT]
>Deberá implementar su propia aplicación o suscribirse a una aplicación en su cuenta de SAP Cloud Platform para probar el inicio de sesión único. En este tutorial, se implementa una aplicación en la cuenta.
> 

Para probar los pasos de este tutorial, debe seguir estas recomendaciones:

- No use el entorno de producción, salvo que sea necesario.
- Si no dispone de un entorno de prueba de Azure AD, puede [obtener una versión de prueba durante un mes](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descripción del escenario
En este tutorial, puede probar el inicio de sesión único de Azure AD en un entorno de prueba. La situación descrita en este tutorial consta de dos bloques de creación principales:

1. Adición de SAP Cloud Platform desde la galería
2. Configuración y comprobación del inicio de sesión único de Azure AD

## <a name="adding-sap-cloud-platform-from-the-gallery"></a>Adición de SAP Cloud Platform desde la galería
Para configurar la integración de SAP Cloud Platform en Azure AD, deberá agregarlo desde la galería a la lista de aplicaciones SaaS administradas.

**Para agregar SAP Cloud Platform desde la galería, realice los pasos siguientes:**

1. En el panel de navegación izquierdo de **[Azure Portal](https://portal.azure.com)**, haga clic en el icono de **Azure Active Directory**. 

    ![Botón Azure Active Directory][1]

2. Vaya a **Aplicaciones empresariales**. A continuación, vaya a **Todas las aplicaciones**.

    ![Hoja Aplicaciones empresariales][2]
    
3. Para agregar una nueva aplicación, haga clic en el botón **Nueva aplicación** de la parte superior del cuadro de diálogo.

    ![Botón Nueva aplicación][3]

4. En el cuadro de búsqueda, escriba **SAP Cloud Platform**, seleccione **SAP Cloud Platform** en el panel de resultados y haga clic en el botón **Agregar** para agregar la aplicación.

    ![SAP Cloud Platform en la lista de resultados](./media/active-directory-saas-sap-hana-cloud-platform-tutorial/tutorial_sapcloudplatform_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configuración y prueba del inicio de sesión único en Azure AD

En esta sección, podrá configurar y probar el inicio de sesión único de Azure AD con SAP Cloud Platform con un usuario de prueba llamado Britta Simon.

Para que el inicio de sesión único funcione, Azure AD debe saber cuál es el usuario homólogo de SAP Cloud Platform para el usuario de Azure AD. Es decir, es necesario establecer una relación de vínculo entre el usuario de Azure AD y el relacionado de SAP Cloud Platform.

Para establecer la relación de vínculo, asigne el valor de **nombre de usuario** de Azure AD como valor de **Nombre de usuario** en SAP Cloud Platform.

Para configurar y probar el inicio de sesión único de Azure AD con SAP Cloud Platform, es preciso completar los siguientes bloques de creación:

1. **[Configuración del inicio de sesión único de Azure AD](#configure-azure-ad-single-sign-on)**: para permitir que los usuarios utilicen esta característica.
2. **[Creación de un usuario de prueba de Azure AD](#create-an-azure-ad-test-user)**: para probar el inicio de sesión único de Azure AD con Britta Simon.
3. **[Creación de un usuario de prueba de SAP Cloud Platform](#create-a-sap-cloud-platform-test-user)**: para tener un homólogo de Britta Simon en SAP Cloud Platform vinculado a la representación del usuario en Azure AD.
4. **[Asignación del usuario de prueba de Azure AD](#assign-the-azure-ad-test-user)**: para permitir que Britta Simon use el inicio de sesión único de Azure AD.
5. **[Prueba del inicio de sesión único](#test-single-sign-on)**: para comprobar si la configuración funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configuración del inicio de sesión único de Azure AD

En esta sección, habilitará el inicio de sesión único de Azure AD en Azure Portal y lo configurará en la aplicación SAP Cloud Platform.

**Para configurar el inicio de sesión único de Azure AD con SAP Cloud Platform, realice los pasos siguientes:**

1. En Azure Portal, en la página de integración de la aplicación **SAP Cloud Platform**, haga clic en **Inicio de sesión único**.

    ![Vínculo Configurar inicio de sesión único][4]

2. En el cuadro de diálogo **Inicio de sesión único**, en **Modo** seleccione **Inicio de sesión basado en SAML** para habilitar el inicio de sesión único.
 
    ![Cuadro de diálogo Inicio de sesión único](./media/active-directory-saas-sap-hana-cloud-platform-tutorial/tutorial_sapcloudplatform_samlbase.png)

3. En la sección **SAP Cloud Platform Domain and URLs** (Dominio y direcciones URL de SAP Cloud Platform), lleve a cabo los siguientes pasos:

    ![Información de dominio y direcciones URL de inicio de sesión único de SAP Cloud Platform](./media/active-directory-saas-sap-hana-cloud-platform-tutorial/tutorial_sapcloudplatform_url.png)

    a. En el cuadro de texto **URL de inicio de sesión**, escriba la dirección URL que utilizan los usuarios para iniciar sesión en la aplicación **SAP Cloud Platform**. Se trata de la URL específica de la cuenta de un recurso protegido en su aplicación SAP Cloud Platform. La dirección URL se parece al siguiente patrón: `https://<applicationName><accountName>.<landscape host>.ondemand.com/<path_to_protected_resource>`
      
     >[!NOTE]
     >Se trata de la URL de la aplicación SAP Cloud Platform que requiere la autenticación del usuario.
     > 

    | |
    |--|
    | `https://<subdomain>.hanatrial.ondemand.com/<instancename>` |
    | `https://<subdomain>.hana.ondemand.com/<instancename>` |

    b. En el cuadro de texto **Identificador** proporcionará el de su aplicación SAP Cloud Platforn, escriba una dirección URL conforme a uno de los siguientes patrones: 

    | |
    |--|
    | `https://hanatrial.ondemand.com/<instancename>` |
    | `https://hana.ondemand.com/<instancename>` |
    | `https://us1.hana.ondemand.com/<instancename>` |
    | `https://ap1.hana.ondemand.com/<instancename>` |

    c. En el cuadro de texto **URL de respuesta** , escriba una dirección URL con el siguiente patrón:

    | |
    |--|
    | `https://<subdomain>.hanatrial.ondemand.com/<instancename>` |
    | `https://<subdomain>.hana.ondemand.com/<instancename>` |
    | `https://<subdomain>.us1.hana.ondemand.com/<instancename>` |
    | `https://<subdomain>.dispatcher.us1.hana.ondemand.com/<instancename>` |
    | `https://<subdomain>.ap1.hana.ondemand.com/<instancename>` |
    | `https://<subdomain>.dispatcher.ap1.hana.ondemand.com/<instancename>` |
    | `https://<subdomain>.dispatcher.hana.ondemand.com/<instancename>` |

    > [!NOTE] 
    > Estos valores no son reales. Actualícelos con la dirección URL de inicio de sesión, el identificador y la dirección URL de respuesta reales. Póngase en contacto con el [equipo de atención al cliente de SAP Cloud Platform](https://help.sap.com/viewer/65de2977205c403bbc107264b8eccf4b/Cloud/5dd739823b824b539eee47b7860a00be.html) para obtener la dirección URL de inicio de sesión y el identificador. La dirección URL de respuesta se obtiene de la sección de administración de confianza que se explica más adelante en el tutorial.
    > 
     
4. En la sección **Certificado de firma de SAML**, haga clic en **XML de metadatos** y luego guarde el archivo de metadatos en el equipo.

    ![Vínculo de descarga del certificado](./media/active-directory-saas-sap-hana-cloud-platform-tutorial/tutorial_sapcloudplatform_certificate.png) 

5. Haga clic en el botón **Guardar** .

    ![Botón Guardar de Configuración de inicio de sesión único](./media/active-directory-saas-sap-hana-cloud-platform-tutorial/tutorial_general_400.png)

6. En otra ventana del explorador web, inicie sesión en SAP Cloud Platform Cockpit en `https://account.<landscape host>.ondemand.com/cockpit` (por ejemplo, https://account.hanatrial.ondemand.com/cockpit).

7. Haga clic en la pestaña **Trust** (Confianza).
   
    ![Confianza](./media/active-directory-saas-sap-hana-cloud-platform-tutorial/ic790800.png "Confianza")

8. En la sección Trust Management (Administración de confianza), en **Local Service Provider** (Proveedor de servicios local), realice los pasos siguientes:

    ![Administración de confianza](./media/active-directory-saas-sap-hana-cloud-platform-tutorial/ic793931.png "Administración de confianza")
   
    a. Haga clic en **Editar**.

    b. En **Configuration Type** (Tipo de configuración), seleccione **Custom** (Personalizado).

    c. Como **Local Provider Name**(Nombre de proveedor local), deje el valor predeterminado. Copie este valor y péguelo en el campo **Identificador** de la configuración de Azure AD para SAP Cloud Platform.

    d. Para generar una **Signing Key** (Clave de firma) y un par de claves **Signing Certificate** (Certificado de firma), haga clic en **Generate Key Pair** (Generar par de claves).

    e. En **Principal Propagation** (Propagación de entidad de seguridad), seleccione **Disabled** (Deshabilitada).

    f. En **Force Authentication** (Forzar autenticación), seleccione **Disabled** (Deshabilitado).

    g. Haga clic en **Guardar**.

9. Después de guardar la configuración de **Local Service Provider** (Proveedor de servicios local), realice lo siguiente para obtener la dirección URL de respuesta:
   
    ![Obtención de metadatos](./media/active-directory-saas-sap-hana-cloud-platform-tutorial/ic793930.png "Obtención de metadatos")

    a. Para descargar el archivo de metadatos de SAP Cloud Platform, haga clic en **Get Metadata** (Obtener metadatos).

    b. Abra el archivo de metadatos de SAP Cloud Platform descargado y busque la etiqueta **ns3:AssertionConsumerService**.
 
    c. Copie el valor del atributo **Location** y péguelo en el campo **URL de respuesta** de la configuración de Azure AD para SAP Cloud Platform.

10. Haga clic en la pestaña **Trusted Identity Provider** (Proveedor de identidades de confianza) y en **Add Trusted Identity Provider** (Agregar proveedor de identidad de confianza).
   
    ![Administración de confianza](./media/active-directory-saas-sap-hana-cloud-platform-tutorial/ic790802.png "Administración de confianza")
   
    >[!NOTE]
    >Para administrar la lista de proveedores de identidades de confianza, deberá haber elegido el tipo de configuración personalizada en la sección del proveedor de servicios local. Para el tipo de configuración predeterminado, tendrá una confianza implícita y no editable para el servicio de id. de SAP. Para Ninguno, no tiene ninguna configuración de confianza.
    > 
    > 

11. Haga clic en la pestaña **General** y en **Browse** (Examinar) para cargar el archivo de metadatos descargados.
    
    ![Administración de confianza](./media/active-directory-saas-sap-hana-cloud-platform-tutorial/ic793932.png "Administración de confianza")
    
    >[!NOTE]
    >Después de cargar el archivo de metadatos, los valores de **Dirección URL de inicio de sesión único**, **Dirección URL de cierre de sesión único** y **Certificado de firma** se rellenan automáticamente.
    > 
     
12. Haga clic en la pestaña **Attributes** (Atributos).

13. En la pestaña **Attributes** (Atributos), realice los pasos siguientes:
    
    ![Atributos](./media/active-directory-saas-sap-hana-cloud-platform-tutorial/ic790804.png "Atributos") 

    a. Haga clic en **Add Assertion-Based Attribute**(Agregar atributo basado en la aserción) y agregue los siguientes atributos basados en aserción:
       
    | Atributo de aserción | Atributo de entidad de seguridad |
    | --- | --- |
    | `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname` |firstname |
    | `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname` |lastname |
    | `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress` |email |
   
     >[!NOTE]
     >La configuración de los atributos depende de cómo se desarrollen las aplicaciones en SCP, es decir, qué atributos se esperan en la respuesta de SAML y con qué nombre (atributo de la entidad de seguridad) se accede a este atributo en el código.
     > 
    
    b. El **atributo predeterminado** de la captura de pantalla solo es para fines ilustrativos. No es necesario para que el escenario funcione.  
 
    c. Los nombres y valores para el **Principal Attribute** (Atributo principal) que se muestran en la captura de pantalla dependen de cómo se desarrolle la aplicación. Es posible que la aplicación requiera diferentes asignaciones.

###<a name="assertion-based-groups"></a>Grupos basados en aserción

Como paso opcional, puede configurar grupos basados en aserciones para su proveedor de identidades de Azure Active Directory.

El uso de grupos en SAP Cloud Platform permite asignar dinámicamente uno o más usuarios a uno o más roles en las aplicaciones SAP Cloud Platform, en función de los valores de los atributos de la aserción de SAML 2.0. 

Por ejemplo, si la aserción contiene el atributo "*contrato=temporal*", puede que desee que se agreguen todos los usuarios afectados al grupo "*TEMPORAL*". El grupo "*TEMPORAL*" puede contener uno o varios roles de una o más de las aplicaciones implementadas en la cuenta de SAP Cloud Platform.
 
Use grupos basados en aserciones si quiere asignar de manera simultánea muchos usuarios a uno o más roles de las aplicaciones en su cuenta de SAP Cloud Platform. Si quiere asignar un número único o pequeño de usuarios a roles específicos, se recomienda asignarlos directamente en la pestaña "**Autorizaciones**" de SAP Cloud Platform Cockpit.

> [!TIP]
> Ahora puede leer una versión resumida de estas instrucciones dentro de [Azure Portal](https://portal.azure.com) mientras configura la aplicación.  Después de agregar esta aplicación desde la sección **Active Directory > Aplicaciones empresariales**, simplemente haga clic en la pestaña **Inicio de sesión único** y acceda a la documentación insertada a través de la sección **Configuración** de la parte inferior. Puede leer más sobre la característica de documentación insertada aquí: [Vista previa: Administración de inicio de sesión único para aplicaciones empresariales en el nuevo Azure Portal]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="create-an-azure-ad-test-user"></a>Creación de un usuario de prueba de Azure AD

El objetivo de esta sección es crear un usuario de prueba en Azure Portal llamado "Britta Simon".

   ![Creación de un usuario de prueba de Azure AD][100]

**Siga estos pasos para crear un usuario de prueba en Azure AD:**

1. En el panel izquierdo de Azure Portal, haga clic en el botón **Azure Active Directory**.

    ![Botón Azure Active Directory](./media/active-directory-saas-sap-hana-cloud-platform-tutorial/create_aaduser_01.png)

2. Para mostrar la lista de usuarios, vaya a **Usuarios y grupos** y, luego, haga clic en **Todos los usuarios**.

    ![Vínculos "Usuarios y grupos" y "Todos los usuarios"](./media/active-directory-saas-sap-hana-cloud-platform-tutorial/create_aaduser_02.png)

3. En la parte superior del cuadro de diálogo **Todos los usuarios**, haga clic en **Agregar** para abrir el cuadro de diálogo **Agregar**.

    ![Botón Agregar](./media/active-directory-saas-sap-hana-cloud-platform-tutorial/create_aaduser_03.png)

4. En el cuadro de diálogo **Usuario** , realice los pasos siguientes:

    ![Cuadro de diálogo Usuario](./media/active-directory-saas-sap-hana-cloud-platform-tutorial/create_aaduser_04.png)

    a. En el cuadro **Nombre**, escriba **BrittaSimon**.

    b. En el cuadro de texto **Nombre de usuario**, escriba la dirección de correo electrónico del usuario Britta Simon.

    c. Active la casilla **Mostrar contraseña** y, después, anote el valor que se muestra en el cuadro **Contraseña**.

    d. Haga clic en **Crear**.
 
### <a name="create-a-sap-cloud-platform-test-user"></a>Creación de un usuario de prueba de SAP Cloud Platform

Para permitir que los usuarios de Azure AD inicien sesión en SAP Cloud Platform, debe asignarles roles en SAP Cloud Platform.

**Para asignar un rol a un usuario, lleve a cabo los siguientes pasos:**

1. Inicie sesión en de **SAP Cloud Platform Cockpit**.

2. Lleve a cabo los siguientes pasos:
   
    ![Autorizaciones](./media/active-directory-saas-sap-hana-cloud-platform-tutorial/ic790805.png "Autorizaciones")
   
    a. Haga clic en **Authorization**(Autorización).

    b. Haga clic en la pestaña **Usuarios** .

    c. En el cuadro de texto **User** (Usuario), escriba la dirección de correo electrónico del usuario.

    d. Haga clic en **Assign** (Asignar) para asignar el usuario a un rol.

    e. Haga clic en **Guardar**.

### <a name="assign-the-azure-ad-test-user"></a>Asignación del usuario de prueba de Azure AD

En esta sección, concederá acceso a Britta Simon a SAP Cloud Platform para que use el inicio de sesión único de Azure.

![Asignación del rol de usuario][200] 

**Para asignar a Britta Simon a SAP Cloud Platform, realice los pasos siguientes:**

1. En Azure Portal, abra la vista de aplicaciones, vaya a la vista de directorio y vaya a **Aplicaciones empresariales**. Luego, haga clic en **Todas las aplicaciones**.

    ![Asignar usuario][201] 

2. En la lista de aplicaciones, seleccione **SAP Cloud Platform**.

    ![Vínculo a SAP Cloud Platform en la lista de aplicaciones](./media/active-directory-saas-sap-hana-cloud-platform-tutorial/tutorial_sapcloudplatform_app.png)  

3. En el menú de la izquierda, haga clic en **Usuarios y grupos**.

    ![Vínculo "Usuarios y grupos"][202]

4. Haga clic en el botón **Agregar**. Después, seleccione **Usuarios y grupos** en el cuadro de diálogo **Agregar asignación**.

    ![Panel Agregar asignación][203]

5. En el cuadro de diálogo **Usuarios y grupos**, seleccione **Britta Simon** en la lista de usuarios.

6. Haga clic en el botón **Seleccionar** del cuadro de diálogo **Usuarios y grupos**.

7. Haga clic en el botón **Asignar** del cuadro de diálogo **Agregar asignación**.
    
### <a name="test-single-sign-on"></a>Prueba de inicio de sesión único

El objetivo de esta sección es probar la configuración del inicio de sesión único de Azure AD mediante el panel de acceso.

Al hacer clic en el icono de SAP Cloud Platform en el Panel de acceso, debería iniciar sesión automáticamente en su aplicación SAP Cloud Platform.


## <a name="additional-resources"></a>Recursos adicionales

* [Lista de tutoriales sobre cómo integrar aplicaciones SaaS con Azure Active Directory](active-directory-saas-tutorial-list.md)
* [¿Qué es el acceso a aplicaciones y el inicio de sesión único con Azure Active Directory?](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-sap-hana-cloud-platform-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-sap-hana-cloud-platform-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-sap-hana-cloud-platform-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-sap-hana-cloud-platform-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-sap-hana-cloud-platform-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-sap-hana-cloud-platform-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-sap-hana-cloud-platform-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-sap-hana-cloud-platform-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-sap-hana-cloud-platform-tutorial/tutorial_general_203.png

