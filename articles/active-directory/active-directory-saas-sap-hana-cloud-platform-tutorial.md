---
title: "Tutorial: Integración de Azure Active Directory con SAP HANA Cloud Platform | Microsoft Docs"
description: "Aprenda cómo usar SAP HANA Cloud Platform con Azure Active Directory para habilitar el inicio de sesión único, el aprovisionamiento automatizado, etc."
services: active-directory
author: jeevansd
documentationcenter: na
manager: femila
ms.assetid: bd398225-8bd8-4697-9a44-af6e6679113a
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/26/2016
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: dae115c597e3a86c954eafcdd322954774fd55a5


---
# <a name="tutorial-azure-active-directory-integration-with-sap-hana-cloud-platform"></a>Tutorial: Integración de Azure Active Directory con SAP HANA Cloud Platform
El objetivo de este tutorial es mostrar la integración de Azure y SAP HANA Cloud Platform.  
En la situación descrita en este tutorial se supone que ya cuenta con los elementos siguientes:

* Una suscripción de Azure válida
* Una cuenta de SAP HANA Cloud Platform

Después de completar este tutorial, los usuarios de Azure AD que ha asignado a SAP HANA Cloud Platform podrán realizar un inicio de sesión único en la aplicación con la [Introducción al Panel de acceso](active-directory-saas-access-panel-introduction.md).

> [!IMPORTANT]
> Deberá implementar su propia aplicación o suscribirse a una aplicación en su cuenta de SAP HANA Cloud Platform para probar el inicio de sesión único. En este tutorial, se implementa una aplicación en la cuenta.
> 
> 

La situación descrita en este tutorial consta de los siguientes bloques de creación:

1. Habilitación de la integración de aplicaciones para SAP HANA Cloud Platform
2. Configuración del inicio de sesión único
3. Asignación de un rol a un usuario
4. Asignación de usuarios

![Escenario](./media/active-directory-saas-sap-hana-cloud-platform-tutorial/IC790795.png "Scenario")

## <a name="enabling-the-application-integration-for-sap-hana-cloud-platform"></a>Habilitación de la integración de aplicaciones para SAP HANA Cloud Platform
El objetivo de esta sección es describir cómo habilitar la integración de las aplicaciones para SAP HANA Cloud Platform.

### <a name="to-enable-the-application-integration-for-sap-hana-cloud-platform-perform-the-following-steps"></a>Siga estos pasos para habilitar la integración de aplicaciones para SAP HANA Cloud Platform:
1. En el panel de navegación izquierdo del Portal de administración de Azure, haga clic en **Active Directory**.
   
   ![Active Directory](./media/active-directory-saas-sap-hana-cloud-platform-tutorial/IC700993.png "Active Directory")
2. En la lista **Directory** , seleccione el directorio cuya integración desee habilitar.
3. Para abrir la vista de aplicaciones, haga clic en **Applications** , en el menú superior de la vista de directorios.
   
   ![Applications](./media/active-directory-saas-sap-hana-cloud-platform-tutorial/IC700994.png "Applications")
4. Haga clic en **Agregar** en la parte inferior de la página.
   
   ![Agregar aplicación](./media/active-directory-saas-sap-hana-cloud-platform-tutorial/IC749321.png "Add application")
5. En el cuadro de diálogo **¿Qué desea hacer?**, haga clic en **Agregar una aplicación de la galería**.
   
   ![Agregar una aplicación de la galería](./media/active-directory-saas-sap-hana-cloud-platform-tutorial/IC749322.png "Add an application from gallerry")
6. En el **cuadro de búsqueda**, escriba **SAP HANA Cloud Platform**.
   
   ![Galería de aplicaciones](./media/active-directory-saas-sap-hana-cloud-platform-tutorial/IC790796.png "Application Gallery")
7. En el panel de resultados, seleccione **SAP HANA Cloud Platform** y haga clic en **Completar** para agregar la aplicación.
   
   ![SAP Hana](./media/active-directory-saas-sap-hana-cloud-platform-tutorial/IC793929.png "SAP Hana")
   
   ## <a name="configuring-single-sign-on"></a>Configuración del inicio de sesión único

El objetivo de esta sección es describir cómo se habilita la autenticación de los usuarios en SAP HANA Cloud Platform con su cuenta de Azure AD usando el protocolo SAML basado en la federación.  
Como parte de este procedimiento, es necesario cargar un certificado codificado en base 64 en su inquilino de SAP HANA Cloud Platform.  
Si no está familiarizado con este procedimiento, consulte [Conversión de un certificado binario en un archivo de texto](http://youtu.be/PlgrzUZ-Y1o)

### <a name="to-configure-single-sign-on-perform-the-following-steps"></a>Siga estos pasos para configurar el inicio de sesión único:
1. En el Portal de Azure clásico, en la página de integración de aplicaciones de **SAP HANA Cloud Platform**, haga clic en **Configurar inicio de sesión único** para abrir el cuadro de diálogo **Configurar inicio de sesión único**.
   
   ![Configurar inicio de sesión único](./media/active-directory-saas-sap-hana-cloud-platform-tutorial/IC778552.png "Configure single sign-on")
2. En la página **¿Cómo desea que los usuarios inicien sesión en SAP HANA Cloud Platform?**, seleccione **Inicio de sesión único de Microsoft Azure AD** y haga clic en **Siguiente**.
   
   ![Configurar inicio de sesión único](./media/active-directory-saas-sap-hana-cloud-platform-tutorial/IC790797.png "Configure Single Sign-On")
3. En otra ventana del explorador web, inicie sesión en SAP HANA Cloud Platform Cockpit en https://account.\<host horizontal\>.ondemand.com/cockpit (e.g.: *https://account.hanatrial.ondemand.com/cockpit*).
4. Haga clic en la pestaña **Trust** (Confianza).
   
   ![Trust](./media/active-directory-saas-sap-hana-cloud-platform-tutorial/IC790800.png "Trust")
5. En la sección de administración de confianza, lleve a cabo estos pasos:
   
   ![Get Metadata](./media/active-directory-saas-sap-hana-cloud-platform-tutorial/IC793930.png "Get Metadata")
   
   1. Haga clic en la pestaña **Local Service Provide** (Proveedor de servicios local).
   2. Para descargar el archivo de metadatos de SAP HANA Cloud Platform, haga clic en **Get Metadata**(Obtener metadatos).
6. En el Portal de Azure clásico, en la página **Configurar dirección URL** de la aplicación, realice los pasos siguientes y haga clic en **Siguiente**.
   
   ![Configurar dirección URL de la aplicación](./media/active-directory-saas-sap-hana-cloud-platform-tutorial/IC790798.png "Configure App URL")
   
   1. En el cuadro de texto **URL de inicio de sesión**, escriba la dirección URL utilizada por los usuarios para iniciar sesión en la aplicación **SAP HANA Cloud Platform**. Se trata de la URL específica de la cuenta de un recurso protegido en su aplicación SAP HANA Cloud Platform. La dirección URL se basa en el patrón siguiente: *https://\<nombreDeAplicación\>\<nombreDeCuenta\>.\<host horizontal\>.ondemand.com/\<ruta\_al\_recurso\_protegido\>* (p. ej.: *https://xleavep1941203872trial.hanatrial.ondemand.com/xleave*).
      
      > [!NOTE]
      > Se trata de la URL de la aplicación SAP HANA Cloud Platform que requiere que autentique el usuario.
      > 
      > 
   2. Abra el archivo de metadatos de SAP HANA Cloud Platform descargado y luego busque la etiqueta **ns3:AssertionConsumerService** .
   3. Copie el valor del atributo **Ubicación** y péguelo en el cuadro de texto **URL de respuesta de SAP HANA Cloud Platform**.
7. En la página **Configurar inicio de sesión único en SAP HANA Cloud Platform**, para descargar los metadatos, haga clic en **Descargar metadatos** y guarde el archivo en el equipo.
   
   ![Configurar inicio de sesión único](./media/active-directory-saas-sap-hana-cloud-platform-tutorial/IC790799.png "Configure Single Sign-On")
8. En SAP HANA Cloud Platform Cockpit, en la sección **Local Service Provider** (Proveedor de servicios local), realice los pasos siguientes:
   
   ![Administración de confianza](./media/active-directory-saas-sap-hana-cloud-platform-tutorial/IC793931.png "Trust Management")
   
   1. Haga clic en **Editar**.
   2. En **Configuration Type** (Tipo de configuración), seleccione **Custom** (Personalizado).
   3. Como **Local Provider Name**(Nombre de proveedor local), deje el valor predeterminado.
   4. Para generar una **Signing Key** (Clave de firma) y un par de claves **Signing Certificate** (Certificado de firma), haga clic en **Generate Key Pair** (Generar par de claves).
   5. En **Principal Propagation** (Propagación de entidad de seguridad), seleccione **Disabled** (Deshabilitada).
   6. En **Force Authentication** (Forzar autenticación), seleccione **Disabled** (Deshabilitado).
   7. Haga clic en **Guardar**.
9. Haga clic en la pestaña **Trusted Identity Provider** (Proveedor de identidades de confianza) y en **Add Trusted Identity Provider** (Agregar proveedor de identidad de confianza).
   
   ![Administración de confianza](./media/active-directory-saas-sap-hana-cloud-platform-tutorial/IC790802.png "Trust Management")
   
   > [!NOTE]
   > Para administrar la lista de proveedores de identidades de confianza, deberá haber elegido el tipo de configuración personalizada en la sección del proveedor de servicios local. Para el tipo de configuración predeterminado, tendrá una confianza implícita y no editable para el servicio de id. de SAP. Para Ninguno, no tiene ninguna configuración de confianza.
   > 
   > 
10. Haga clic en la pestaña **General** y en **Browse** (Examinar) para cargar el archivo de metadatos descargados.
    
    ![Administración de confianza](./media/active-directory-saas-sap-hana-cloud-platform-tutorial/IC793932.png "Trust Management")
    
    > [!NOTE]
    > Después de cargar el archivo de metadatos, los valores de **Dirección URL de inicio de sesión único**, **Dirección URL de cierre de sesión único** y **Certificado de firma** se rellenan automáticamente.
    > 
    > 
11. Haga clic en la pestaña **Attributes** (Atributos).
12. En la pestaña **Attributes** (Atributos), realice los pasos siguientes:
    
    ![Attributes](./media/active-directory-saas-sap-hana-cloud-platform-tutorial/IC790804.png "Attributes")
    
    1. Al hacer clic en **Add Assertion-Based Attribute**(Agregar atributo basado en la aserción), agregue los siguientes atributos basados en la aserción:
       
       | Atributo de aserción | Atributo de entidad de seguridad |
       | --- | --- |
       | http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname |firstname |
       | http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname |Lastname |
       | http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress |email |
    
    > [!NOTE]
    > La configuración de los atributos depende de cómo se desarrollan las aplicaciones en HCP, es decir, qué atributos esperan en la respuesta de SAML y con qué nombre (atributo de la entidad de seguridad) tienen acceso a este atributo en el código.
    > 
    > a.  El **atributo predeterminado** de la captura de pantalla solo es para fines ilustrativos. No es necesario para que el escenario funcione.  
    > 
    > b.  Los nombres y valores para el **Principal Attribute** (Atributo principal) que se muestran en la captura de pantalla dependen de cómo se desarrolle la aplicación. Es posible que la aplicación requiera diferentes asignaciones.
    > 
    > 
13. En el Portal de Azure clásico, en la página de diálogo **Configurar inicio de sesión único en SAP HANA Cloud Platform**, seleccione la confirmación de configuración de inicio de sesión único y haga clic en **Completar**.
    
    ![Configurar inicio de sesión único](./media/active-directory-saas-sap-hana-cloud-platform-tutorial/IC796933.png "Configure Single Sign-On")

Como paso opcional, puede configurar grupos basados en aserción para su proveedor de identidades de Azure Active Directory.

> [!NOTE]
> El uso de grupos en SAP HANA Cloud Platform le permite asignar dinámicamente uno o más usuarios a uno o más roles en sus aplicaciones de SAP HANA Cloud Platform, determinados por los valores de los atributos de la aserción de SAML 2.0. Por ejemplo, si la aserción contiene el atributo "*contrato=temporal*", puede que desee que se agreguen todos los usuarios afectados al grupo "*TEMPORAL*". El grupo "*TEMPORAL*" puede contener uno o varios roles de una o más de las aplicaciones implementadas en la cuenta de SAP HANA Cloud Platform.
> 
> Use los grupos basados en aserciones si quiere asignar de manera masiva muchos usuarios a uno o más roles de las aplicaciones en su cuenta de SAP HANA Cloud Platform. Si quiere asignar un número único o pequeño de usuarios a roles específicos, recomendamos asignarlos directamente en la pestaña**Autorizaciones**de la cabina de SAP HANA Cloud Platform.
> 
> 

## <a name="assigning-a-role-to-a-user"></a>Asignación de un rol a un usuario
Para permitir que los usuarios de Azure AD inicien sesión en SAP HANA Cloud Platform, debe asignarles roles en SAP HANA Cloud Platform.

### <a name="to-assign-a-role-to-a-user-perform-the-following-steps"></a>Para asignar un rol a un usuario, lleve a cabo los siguientes pasos:
1. Inicie sesión en su cabina de **SAP HANA Cloud Platform** .
2. Lleve a cabo los siguiente pasos:
   
   ![Autorizaciones](./media/active-directory-saas-sap-hana-cloud-platform-tutorial/IC790805.png "Authorizations")
   
   1. Haga clic en **Authorization**(Autorización).
   2. Haga clic en la pestaña **Usuarios** .
   3. En el cuadro de texto **User** (Usuario), escriba la dirección de correo electrónico del usuario.
   4. Haga clic en **Assign** (Asignar) para asignar el usuario a un rol.
   5. Haga clic en **Save**.

## <a name="assigning-users"></a>Asignación de usuarios
Para probar la configuración, debe conceder acceso a los usuarios de Azure AD a los que quiere permitir el uso de su aplicación.

### <a name="to-assign-users-to-sap-hana-cloud-platform-perform-the-following-steps"></a>Para asignar usuarios a SAP HANA Cloud Platform, lleve a cabo los siguientes pasos:
1. En el Portal de Azure clásico, cree una cuenta de prueba.
2. En la página de integración de aplicaciones de **SAP HANA Cloud Platform**, haga clic en **Asignar usuarios**.
   
   ![Asignar usuarios](./media/active-directory-saas-sap-hana-cloud-platform-tutorial/IC790806.png "Assign Users")
3. Seleccione su usuario de prueba, haga clic en **Asignar** y en **Sí** para confirmar la asignación.
   
   ![Sí](./media/active-directory-saas-sap-hana-cloud-platform-tutorial/IC767830.png "Yes")

Si desea probar la configuración de inicio de sesión único, abra el Panel de acceso. Para obtener más información sobre el Panel de acceso, vea [Introducción al Panel de acceso](active-directory-saas-access-panel-introduction.md).




<!--HONumber=Nov16_HO3-->


