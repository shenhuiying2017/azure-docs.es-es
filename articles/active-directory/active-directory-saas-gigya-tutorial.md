---
title: "Tutorial: Integración de Azure Active Directory con Gigya | Microsoft Doc"
description: "Aprenda a usar Gigya con Azure Active Directory para habilitar el inicio de sesión único, el aprovisionamiento automático, etc."
services: active-directory
author: jeevansd
documentationcenter: na
manager: femila
ms.assetid: 2c7d200b-9242-44a5-ac8a-ab3214a78e41
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 02/14/2017
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: bf5588885de9c280eb70712dbf800efe509ee912
ms.openlocfilehash: 96eb66f35a91e769418d179b0997cd32e02cdc97


---
# <a name="tutorial-azure-active-directory-integration-with-gigya"></a>Tutorial: integración de Azure Active Directory con Gigya
El objetivo de este tutorial es mostrar la integración de Azure y Gigya.  
En la situación descrita en este tutorial se supone que ya cuenta con los elementos siguientes:

* Una suscripción de Azure válida
* Una suscripción habilitada para el inicio de sesión único en Gigya

Después de completar este tutorial, los usuarios de Azure AD que haya asignado a Gigya podrán realizar un inicio de sesión único en la aplicación en el sitio de la compañía de Gigya (inicio de sesión iniciado por el proveedor de servicios) o desde [Introducción al Panel de acceso](active-directory-saas-access-panel-introduction.md).

La situación descrita en este tutorial consta de los siguientes bloques de creación:

1. Habilitación de la integración de aplicaciones para Gigya
2. Configuración del inicio de sesión único
3. Configuración del aprovisionamiento de usuario
4. Asignación de usuarios

![Configurar inicio de sesión único](./media/active-directory-saas-gigya-tutorial/IC789512.png "Configurar inicio de sesión único")

## <a name="enabling-the-application-integration-for-gigya"></a>Habilitación de la integración de aplicaciones para Gigya
El objetivo de esta sección es describir cómo se habilita la integración de aplicaciones para Gigya.

### <a name="to-enable-the-application-integration-for-gigya-perform-the-following-steps"></a>Siga estos pasos para habilitar la integración de aplicaciones para Gigya:
1. En el panel de navegación izquierdo del Portal de Azure clásico, haga clic en **Active Directory**.
   
    ![Active Directory](./media/active-directory-saas-gigya-tutorial/IC700993.png "Active Directory")

2. En la lista **Directory** , seleccione el directorio cuya integración desee habilitar.

3. Para abrir la vista de aplicaciones, haga clic en **Applications** , en el menú superior de la vista de directorios.
   
    ![Aplicaciones](./media/active-directory-saas-gigya-tutorial/IC700994.png "Aplicaciones")

4. Haga clic en **Agregar** en la parte inferior de la página.
   
    ![Agregar aplicaciones](./media/active-directory-saas-gigya-tutorial/IC749321.png "Agregar aplicaciones")

5. En el cuadro de diálogo **¿Qué desea hacer?**, haga clic en **Agregar una aplicación de la galería**.
   
    ![Agregar una aplicación de la galería](./media/active-directory-saas-gigya-tutorial/IC749322.png "Agregar una aplicación de la galería")

6. En el **cuadro de búsqueda**, escriba **Gigya**.
   
    ![Galería de aplicaciones](./media/active-directory-saas-gigya-tutorial/IC789513.png "Galería de aplicaciones")

7. En el panel de resultados, seleccione **Gigya** y luego haga clic en **Completar** para agregar la aplicación.
   
    ![Gigya](./media/active-directory-saas-gigya-tutorial/IC789527.png "Gigya")
   
## <a name="configuring-single-sign-on"></a>Configuración del inicio de sesión único

El objetivo de esta sección es describir cómo permitir a los usuarios autenticarse en Gigya con su cuenta de Azure AD mediante federación basada en el protocolo SAML.  
Como parte de este procedimiento, es necesario crear un archivo de certificado codificado en base&64;.  
Si no está familiarizado con este procedimiento, consulte [Conversión de un certificado binario en un archivo de texto](http://youtu.be/PlgrzUZ-Y1o).

### <a name="to-configure-single-sign-on-perform-the-following-steps"></a>Siga estos pasos para configurar el inicio de sesión único:
1. En el Portal de Azure clásico, en la página de integración de la aplicación **Gigya**, haga clic en **Configurar inicio de sesión único** para abrir el cuadro de diálogo **Configurar inicio de sesión único**.
   
    ![Configurar inicio de sesión único](./media/active-directory-saas-gigya-tutorial/IC789528.png "Configurar inicio de sesión único")

2. En la página **¿Cómo desea que los usuarios inicien sesión en Gigya?**, seleccione **Inicio de sesión único de Microsoft Azure AD** y, después, haga clic en **Siguiente**.
   
    ![Configurar inicio de sesión único](./media/active-directory-saas-gigya-tutorial/IC789529.png "Configurar inicio de sesión único")

3. En la página **Configurar dirección URL de la aplicación**, en el cuadro de texto **URL de inicio de sesión de Gigya**, escriba su dirección URL con el siguiente patrón "*http://company.gigya.com*" y luego haga clic en **Siguiente**.
   
    ![Configurar dirección URL de la aplicación](./media/active-directory-saas-gigya-tutorial/IC789530.png "Configurar dirección URL de la aplicación")

4. En la página **Configuración de inicio de sesión único en Gigya**, haga clic en **Descargar certificado** y guarde el archivo de certificado en el equipo.
   
    ![Configurar inicio de sesión único](./media/active-directory-saas-gigya-tutorial/IC789531.png "Configurar inicio de sesión único")

5. En otra ventana del explorador web, inicie sesión en el sitio de la compañía de Gigya como administrador.

6. Vaya **Settings \> SAML Login** (Configuración > Inicio de sesión de SAML), y luego haga clic en el botón **Add** (Agregar).
   
    ![Inicio de sesión SAML](./media/active-directory-saas-gigya-tutorial/IC789532.png "Inicio de sesión SAML")

7. En la sección **Inicio de sesión de SAML** , realice los pasos siguientes:
   
    ![Configuración de SAML](./media/active-directory-saas-gigya-tutorial/IC789533.png "Configuración de SAML")
   
    a. En el cuadro de texto **Name** (Nombre), escriba el nombre de la configuración.
   
    b. En el Portal de Azure clásico, en la página del cuadro de diálogo **Configurar inicio de sesión único en Gigya**, copie el valor de **URL del emisor** y péguelo en el cuadro de texto **Issuer** (Emisor).
   
    c. En el Portal de Azure clásico, en la página de diálogo **Configurar inicio de sesión único en Gigya**, copie el valor de **Dirección URL del servicio de inicio de sesión único** y péguelo en el cuadro de texto **Single Sign-On Service URL** (Dirección URL del servicio de inicio de sesión único).
   
    d. En el Portal de Azure clásico, en la página del cuadro de diálogo **Configurar inicio de sesión único en Gigya**, copie el valor de **Formato de identificador de nombre** y péguelo en el cuadro de texto **Name ID Format** (Formato de identificador de nombre).
   
    e. Cree un archivo **codificado en base&64;** a partir del certificado descargado.
      
    > [!TIP]
    > Para obtener más información, consulte [Conversión de un certificado binario en un archivo de texto](http://youtu.be/PlgrzUZ-Y1o)
    > 
    > 
   
    f. Abra el certificado codificado en base&64; en el Bloc de notas, copie el contenido del mismo en el Portapapeles y luego péguelo en el cuadro de texto **Certificado X.509** .
   
    g. Haga clic en **Guardar configuración**.

8. En el Portal de Azure clásico, seleccione la confirmación de configuración de inicio de sesión único y haga clic en **Completar** para cerrar el cuadro de diálogo **Configurar inicio de sesión único**.
   
    ![Configurar inicio de sesión único](./media/active-directory-saas-gigya-tutorial/IC789534.png "Configurar inicio de sesión único")
   
## <a name="configuring-user-provisioning"></a>Configuración del aprovisionamiento de usuario

Para permitir que los usuarios de Azure AD inicien sesión en Gigya, deben aprovisionarse en Gigya.  
En el caso de Gigya, el aprovisionamiento es una tarea manual.

### <a name="to-provision-a-user-accounts-perform-the-following-steps"></a>Para aprovisionar cuentas de usuario, realice estos pasos:
1. Inicie sesión en el sitio de la compañía de **Gigya** como administrador.
2. Vaya a **Admin \> Manage Users** (Administrador > Administrar usuarios) y, a continuación, haga clic en **Invite Users** (Invitar a usuarios).
   
    ![Administración de usuarios](./media/active-directory-saas-gigya-tutorial/IC789535.png "Administración de usuarios")

3. En el cuadro de diálogo Invite Users (Invitar a usuarios), realice los pasos siguientes:
   
    ![Invitar a usuarios](./media/active-directory-saas-gigya-tutorial/IC789536.png "Invitar a usuarios")
   
    a. En el cuadro de texto **Correo electrónico** , escriba el alias de correo electrónico de la cuenta válida de Azure Active Directory que quiera aprovisionar.
    
    b. Haga clic en **Invitar a usuario**.
      
    > [!NOTE]
    > El titular de la cuenta de Azure Active Directory recibirá un mensaje de correo electrónico con un vínculo para confirmar la cuenta antes de que se active.
    > 
    > 

 

## <a name="assigning-users"></a>Asignación de usuarios
Para probar la configuración, debe conceder acceso a los usuarios de Azure AD a los que quiere permitir el uso de su aplicación.

### <a name="to-assign-users-to-gigya-perform-the-following-steps"></a>Para asignar usuarios a Gigya, lleve a cabo los siguientes pasos:
1. En el Portal de Azure clásico, cree una cuenta de prueba.
2. En la página de integración de aplicaciones de **Gigya**, haga clic en **Asignar usuarios**.
   
    ![Asignar usuarios](./media/active-directory-saas-gigya-tutorial/IC789537.png "Asignar usuarios")

3. Seleccione su usuario de prueba, haga clic en **Asignar** y en **Sí** para confirmar la asignación.
   
    ![Sí](./media/active-directory-saas-gigya-tutorial/IC767830.png "Sí")

Si desea probar la configuración de inicio de sesión único, abra el Panel de acceso. Para obtener más información sobre el Panel de acceso, vea [Introducción al Panel de acceso](active-directory-saas-access-panel-introduction.md).




<!--HONumber=Feb17_HO2-->


