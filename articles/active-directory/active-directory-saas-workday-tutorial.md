---
title: "Tutorial: Integración de Azure Active Directory con Workday | Microsoft Docs"
description: "Aprenda cómo usar Workday con Azure Active Directory para habilitar el inicio de sesión único, el aprovisionamiento automatizado, etc."
services: active-directory
author: jeevansd
documentationcenter: na
manager: femila
ms.assetid: e9da692e-4a65-4231-8ab3-bc9a87b10bca
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 02/22/2017
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 1c22e4fc17226578aaaf272fdf79178da65c63c2
ms.openlocfilehash: e6b1c8cbf45fea4f5af16231003ccc07a2a53233
ms.lasthandoff: 02/23/2017


---
# <a name="tutorial-azure-active-directory-integration-with-workday"></a>Tutorial: Integración de Azure Active Directory con Workday
El objetivo de este tutorial es mostrar la integración de Azure y Workday. En la situación descrita en este tutorial se supone que ya cuenta con los elementos siguientes:

* Una suscripción de Azure válida
* Un inquilino en Workday

La situación descrita en este tutorial consta de los siguientes bloques de creación:

1. Habilitación de la integración de aplicaciones en Workday
2. Configuración del inicio de sesión único
3. Configuración del aprovisionamiento de usuario
4. Configuración del aprovisionamiento de usuario

![Escenario](./media/active-directory-saas-workday-tutorial/IC782919.png "Escenario")

## <a name="enabling-the-application-integration-for-workday"></a>Habilitación de la integración de aplicaciones en Workday
El objetivo de esta sección es describir cómo habilitar la integración de aplicaciones para Salesforce.

### <a name="to-enable-the-application-integration-for-workday-perform-the-following-steps"></a>Siga estos pasos para habilitar la integración de aplicaciones para Workday:
1. En el panel de navegación izquierdo del Portal de Azure clásico, haga clic en **Active Directory**.
   
    ![Active Directory](./media/active-directory-saas-workday-tutorial/IC700993.png "Active Directory")

2. En la lista **Directory** , seleccione el directorio cuya integración desee habilitar.

3. Para abrir la vista de aplicaciones, haga clic en **Applications** , en el menú superior de la vista de directorios.
   
    ![Aplicaciones](./media/active-directory-saas-workday-tutorial/IC700994.png "Aplicaciones")

4. Para abrir la **Galería de aplicaciones**, haga clic en **Agregar una aplicación** y en **Agregar una aplicación que mi organización use**.
   
    ![¿Qué quiere hacer? ] (./media/active-directory-saas-workday-tutorial/IC700995.png "¿Qué quiere hacer?")

5. En el **cuadro de búsqueda**, escriba **Workday**.
   
    ![Workday](./media/active-directory-saas-workday-tutorial/IC701021.png "Workday")

6. En el panel de resultados, seleccione **Workday** y haga clic en **Completar** para agregar la aplicación.
   
    ![Workday](./media/active-directory-saas-workday-tutorial/IC701022.png "Workday")

## <a name="configuring-single-sign-on"></a>Configuración del inicio de sesión único
El objetivo de esta sección es describir cómo se habilita la autenticación de los usuarios en Workday con su cuenta de Azure AD usando el protocolo SAML basado en la federación.  
Como parte de este procedimiento, es necesario crear un certificado codificado en base&64;.  
Si no está familiarizado con este procedimiento, consulte [Conversión de un certificado binario en un archivo de texto](http://youtu.be/PlgrzUZ-Y1o).

### <a name="to-configure-single-sign-on-perform-the-following-steps"></a>Siga estos pasos para configurar el inicio de sesión único:
1. En la página de integración de aplicaciones de **Workday**, haga clic en **Configurar inicio de sesión único** para abrir el diálogo **Configurar inicio de sesión único**.
   
    ![Configurar inicio de sesión único](./media/active-directory-saas-workday-tutorial/IC782920.png "Configurar inicio de sesión único")

2. En la página **¿Cómo desea que los usuarios inicien sesión en Workday?**, seleccione **Inicio de sesión único de Microsoft Azure AD** y, después, haga clic en **Siguiente**.
   
    ![Configurar inicio de sesión único](./media/active-directory-saas-workday-tutorial/IC782921.png "Configurar inicio de sesión único")

3. En la página **Configurar dirección URL de la aplicación**, realice los pasos siguientes y luego haga clic en **Siguiente**.
   
    ![Configurar dirección URL de la aplicación](./media/active-directory-saas-workday-tutorial/IC782957.png "Configurar dirección URL de la aplicación")
   
    a. En el cuadro de texto **URL de inicio de sesión**, escriba la dirección URL que usan los usuarios para iniciar sesión en Workday con el patrón siguiente: `https://impl.workday.com/<tenant>/login-saml2.htmld`
   
    b.  En el cuadro de texto **Dirección URL de respuesta de Workday**, escriba la dirección URL de respuesta de Workday con el patrón siguiente: `https://impl.workday.com/<tenant>/login-saml.htmld`
   
    > [!NOTE]
    > Su URL de respuesta debe tener un subdominio (por ejemplo, www, wd2, wd3, wd3-impl, wd5, wd5-impl). Usar algo como "*http://www.myworkday.com*" funciona, pero "*http://myworkday.com*" no funciona. 
    > 
    > 

4. En la página **Configurar inicio de sesión único en Workday**, para descargar el certificado, haga clic en **Descargar certificado** y, después, guarde el archivo de certificado en el equipo.
 
    ![Configurar inicio de sesión único](./media/active-directory-saas-workday-tutorial/IC782922.png "Configurar inicio de sesión único")

5. En otra ventana del explorador web, inicie sesión en su sitio de la compañía de Workday como administrador.

6. Vaya a **Menú \> Workbench**.
   
    ![Workbench](./media/active-directory-saas-workday-tutorial/IC782923.png "Workbench")

7. Vaya a **Administración de cuentas**.
   
    ![Administración de cuentas](./media/active-directory-saas-workday-tutorial/IC782924.png "Administración de cuentas")

8. Vaya a **Editar configuración de inquilino: Seguridad**.
   
    ![Edición de seguridad del inquilino](./media/active-directory-saas-workday-tutorial/IC782925.png "Edición de seguridad del inquilino")

9. En la sección **URL de redireccionamiento** , siga estos pasos:
   
    ![Direcciones URL de redirección](./media/active-directory-saas-workday-tutorial/IC7829581.png "Direcciones URL de redirección")
   
    a. Haga clic en **Add Row**(Agregar fila).
   
    b. En los cuadros de texto **Dirección URL de redireccionamiento de inicio de sesión** y **URL de redireccionamiento móvil**, escriba la **dirección URL del inquilino de Workday** que ha especificado en la página **Configurar dirección URL** de la aplicación del Portal de Azure clásico.
   
    c. En el Portal de Azure clásico, en la página de diálogo **Configurar inicio de sesión único en Workday**, copie la **Dirección URL del servicio de cierre de sesión único** y péguela en el cuadro de texto **URL de redireccionamiento de cierre de sesión**.
   
    d.  En el cuadro de texto **Entorno** , escriba el nombre del entorno.  

    >[!NOTE]
    > El valor del atributo Entorno está vinculado con el valor de la URL del inquilino:  
    >-   Si el nombre de dominio de la URL de inquilino de Workday empieza por impl (por ejemplo, *https://impl.workday.com/\<inquilino\>/login-saml2.htmld*), el atributo **Environment** tiene que establecerse en Implementación.  
    >-   Si el nombre de dominio empieza por algo más, deberá ponerse en contacto con Workday para obtener el valor de **Entorno** coincidente.

1. En la sección **Configuración de SAML** , realice los pasos siguientes:
   
    ![Configuración de SAML](./media/active-directory-saas-workday-tutorial/IC782926.png "Configuración de SAML")
   
    a.  Seleccione **Enable SAML Authentication**(Habilitar autenticación SAML).
   
    b.  Haga clic en **Add Row**(Agregar fila).

2. En la sección Proveedores de identidades SAML, realice los pasos siguientes:
   
    ![Proveedores de identidades SAML](./media/active-directory-saas-workday-tutorial/IC7829271.png "Proveedores de identidades SAML")
   
    a. En el cuadro de texto Identity Provider Name (Nombre del proveedor de identidades), escriba un nombre de proveedor (por ejemplo, *SPInitiatedSSO*).
   
    b. En el Portal de Azure clásico, en la página de diálogo **Configurar inicio de sesión único en Workday**, copie el valor de **Id. de proveedor de identidad** y péguelo en el cuadro de texto **Emisor**.
   
    c. Seleccione **Habilitar el cierre de sesión iniciado de Workday**.
   
    d. En el Portal de Azure clásico, en la página de diálogo **Configurar inicio de sesión único en Workday**, copie el valor de **Dirección URL del servicio de cierre de sesión único** y péguelo en el cuadro de texto **URL de solicitud de cierre de sesión**.

    e. Haga clic en **Identity Provider Public Key Certificate** (Certificado de clave pública de proveedor de identidades) y, después, en **Crear**. 

    ![Crear](./media/active-directory-saas-workday-tutorial/IC782928.png "Crear")

    f. Haga clic en **Create x509 Public Key**(Crear clave pública x509). 

    ![Crear](./media/active-directory-saas-workday-tutorial/IC782929.png "Crear")


1. En la sección **View x509 Public Key** (Ver clave pública x509), siga estos pasos: 
   
    ![Visualización de clave pública x509](./media/active-directory-saas-workday-tutorial/IC782930.png "Visualización de clave pública x509") 
   
    a. En el cuadro de texto **Nombre**, escriba el nombre del certificado (por ejemplo, *PPE\_SP*).
   
    b. En el cuadro de texto **Válido desde** , escriba el valor del atributo Válido desde del certificado.
   
    c.  En el cuadro de texto **Válido hasta** , escriba el valor del atributo Válido hasta del certificado.
   
    > [!NOTE]
    > Puede obtener la fecha válido desde y la fecha válido hasta del certificado descargado haciendo doble clic en él.  Las fechas se muestran bajo la pestaña **Detalles** .
    > 
    > 
   
    d. Cree un archivo **codificado en base&64;** a partir del certificado descargado.  
   
    > [!TIP]
    > Para obtener más información, consulte [Conversión de un certificado binario en un archivo de texto](http://youtu.be/PlgrzUZ-Y1o)
    > 
    > 
   
    e.  Abra el certificado codificado en base&64; en el Bloc de notas y luego copie el contenido del mismo.
   
    f.  En el cuadro de texto **Certificado** , pegue el contenido del portapapeles.
   
    g.  Haga clic en **Aceptar**.

2. Lleve a cabo los siguiente pasos: 
   
    ![Configuración de SSO](./media/active-directory-saas-workday-tutorial/IC7829351111.png "Configuración de SSO")
   
    a.  Habilite el **par de claves privadas x509**.
   
    b.  En el cuadro de texto **Id. de proveedor de servicios**, escriba **http://www.workday.com**.
   
    c.  Seleccione **Habilitar autenticación SAML iniciada por el proveedor de servicios**.
   
    d.  En el Portal de Azure clásico, en la página de diálogo **Configurar inicio de sesión único en Workday**, copie el valor de **Dirección URL del servicio de inicio de sesión único** y péguelo en el cuadro de texto **URL de servicio SSO de IdP**.
   
    e. Seleccione **No desinflar la solicitud de autenticación iniciada por el SP**.
   
    f. Como **Método de firma de solicitud de autenticación**, seleccione **SHA256**. 
   
    ![Método de firma de solicitud de autenticación](./media/active-directory-saas-workday-tutorial/IC782932.png "Método de firma de solicitud de autenticación") 
   
    g. Haga clic en **Aceptar**. 
   
    ![Aceptar](./media/active-directory-saas-workday-tutorial/IC782933.png "Aceptar")

3. En el Portal de Azure clásico, en la página **Configurar inicio de sesión único en Workday**, haga clic en **Siguiente**. 
   
    ![Configurar inicio de sesión único](./media/active-directory-saas-workday-tutorial/IC782934.png "Configurar inicio de sesión único")

4. En la página **Confirmación del inicio de sesión único**, haga clic en **Completar**. 
   
    ![Configurar inicio de sesión único](./media/active-directory-saas-workday-tutorial/IC782935111.png "Configurar inicio de sesión único")

## <a name="configuring-user-provisioning"></a>Configuración del aprovisionamiento de usuario
Para obtener un usuario de prueba aprovisionado en Workday, deberá ponerse en contacto con el equipo de soporte técnico de Workday.  
El equipo de soporte técnico de Workday creará el usuario por usted.

## <a name="assigning-users"></a>Asignación de usuarios
Para probar la configuración, debe conceder acceso a los usuarios de Azure AD a los que quiere permitir el uso de su aplicación.

### <a name="to-assign-users-to-workday-perform-the-following-steps"></a>Para asignar usuarios a Workday, lleve a cabo los siguientes pasos:
1. En el Portal de Azure clásico, cree una cuenta de prueba.

2. En la página de integración de aplicaciones de **Workday**, haga clic en **Asignar usuarios**.
   
    ![Asignar usuarios](./media/active-directory-saas-workday-tutorial/IC782935.png "Asignar usuarios")

3. Seleccione su usuario de prueba, haga clic en **Asignar** y en **Sí** para confirmar la asignación.
   
    ![Sí](./media/active-directory-saas-workday-tutorial/IC767830.png "Sí")

Si desea probar la configuración de inicio de sesión único, abra el Panel de acceso. Para obtener más información sobre el Panel de acceso, vea [Introducción al Panel de acceso](active-directory-saas-access-panel-introduction.md).


