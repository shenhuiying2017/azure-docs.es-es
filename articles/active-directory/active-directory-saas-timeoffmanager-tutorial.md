---
title: "Tutorial: Integración de Azure Active Directory con TimeOffManager | Microsoft Docs"
description: "Aprenda cómo usar TimeOffManager con Azure Active Directory para habilitar el inicio de sesión único, el aprovisionamiento automatizado, etc."
services: active-directory
author: jeevansd
documentationcenter: na
manager: femila
ms.assetid: 3685912f-d5aa-4730-ab58-35a088fc1cc3
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 02/16/2017
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: a028279bbc209b9bf9402813b687752f0320ce52
ms.openlocfilehash: 55a13e90ed5f8fd59a93bba507769e56594de568
ms.lasthandoff: 02/28/2017


---
# <a name="tutorial-azure-active-directory-integration-with-timeoffmanager"></a>Tutorial: Integración de Azure Active Directory con TimeOffManager
El objetivo de este tutorial es mostrar la integración de Azure y TimeOffManager.  

En la situación descrita en este tutorial se supone que ya cuenta con los elementos siguientes:

* Una suscripción de Azure válida
* Una suscripción habilitada para el inicio de sesión único en TimeOffManager

Después de completar este tutorial, los usuarios de Azure AD que asignó a TimeOffManager podrán realizar un inicio de sesión único en la aplicación en el sitio de la compañía de TimeOffManager (inicio de sesión iniciado por el proveedor de servicios) o con las instrucciones que se indican en [Introducción al Panel de acceso](active-directory-saas-access-panel-introduction.md).

La situación descrita en este tutorial consta de los siguientes bloques de creación:

* Habilitación de la integración de aplicaciones para TimeOffManager
* Configuración del inicio de sesión único (SSO)
* Configuración del aprovisionamiento de usuario
* Asignación de usuarios

![Escenario](./media/active-directory-saas-timeoffmanager-tutorial/IC795909.png "Escenario")

## <a name="enable-the-application-integration-for-timeoffmanager"></a>Habilitación de la integración de aplicaciones para TimeOffManager
El objetivo de esta sección es describir cómo se habilita la integración de aplicaciones para TimeOffManager.

**Siga estos pasos para habilitar la integración de aplicaciones para TimeOffManager:**

1. En el panel de navegación izquierdo del Portal de Azure clásico, haga clic en **Active Directory**.
   
   ![Active Directory](./media/active-directory-saas-timeoffmanager-tutorial/IC700993.png "Active Directory")
2. En la lista **Directory** , seleccione el directorio cuya integración desee habilitar.
3. Para abrir la vista de aplicaciones, haga clic en **Applications** , en el menú superior de la vista de directorios.
   
   ![Aplicaciones](./media/active-directory-saas-timeoffmanager-tutorial/IC700994.png "Aplicaciones")
4. Haga clic en **Agregar** en la parte inferior de la página.
   
   ![Agregar aplicaciones](./media/active-directory-saas-timeoffmanager-tutorial/IC749321.png "Agregar aplicaciones")
5. En el cuadro de diálogo **¿Qué desea hacer?**, haga clic en **Agregar una aplicación de la galería**.
   
   ![Agregar una aplicación de la galería](./media/active-directory-saas-timeoffmanager-tutorial/IC749322.png "Agregar una aplicación de la galería")
6. En el **cuadro de búsqueda**, escriba **TimeOffManager**.
   
   ![Galería de aplicaciones](./media/active-directory-saas-timeoffmanager-tutorial/IC795910.png "Galería de aplicaciones")
7. En el panel de resultados, seleccione **TimeOffManager** y haga clic en **Completar** para agregar la aplicación.
   
   ![TimeOffManager](./media/active-directory-saas-timeoffmanager-tutorial/IC795911.png "TimeOffManager")

## <a name="configure-single-sign-on"></a>Configurar inicio de sesión único
El objetivo de esta sección es describir cómo se habilita la autenticación de usuarios en TimeOffManager con su cuenta de Azure AD mediante la federación basada en el protocolo SAML.  

Como parte de este procedimiento, es necesario cargar un certificado codificado en base&64; en su inquilino de TimeOffManager. Si no está familiarizado con este procedimiento, consulte [Conversión de un certificado binario en un archivo de texto](http://youtu.be/PlgrzUZ-Y1o).

### <a name="to-configure-single-sign-on-perform-the-following-steps"></a>Siga estos pasos para configurar el inicio de sesión único:
1. En el Portal de Azure clásico, en la página de integración de aplicaciones de **TimeOffManager**, haga clic en **Configurar inicio de sesión único** para abrir el cuadro de diálogo **Configurar inicio de sesión único**.
   
   ![Configurar inicio de sesión único](./media/active-directory-saas-timeoffmanager-tutorial/IC795912.png "Configurar inicio de sesión único")
2. En la página **¿Cómo desea que los usuarios inicien sesión en TimeOffManager?**, seleccione **Inicio de sesión único de Microsoft Azure AD** y haga clic en **Siguiente**.
   
   ![Configurar inicio de sesión único](./media/active-directory-saas-timeoffmanager-tutorial/IC795913.png "Configurar inicio de sesión único")
3. En la página **Configurar dirección URL de la aplicación**, en el cuadro de texto **Dirección URL de respuesta de TimeOffManager**, escriba la dirección URL de AssertionConsumerService de TimeOffManager (por ejemplo: "*Example: https://www.timeoffmanager.com/cpanel/sso/consume.aspx?company\_id=IC34216*" y haga clic en **Siguiente**.
   
   ![Configurar dirección URL de la aplicación](./media/active-directory-saas-timeoffmanager-tutorial/IC795914.png "Configurar dirección URL de la aplicación")
   
   Puede obtener la dirección URL de respuesta de la página de configuración del inicio de sesión único de TimeOffManager.
   
   ![Configuración de inicio de sesión único](./media/active-directory-saas-timeoffmanager-tutorial/IC795915.png "Configuración de inicio de sesión único")
4. En la página **Configuración de inicio de sesión único en TimeOffManager**, para descargar el certificado, haga clic en **Descargar certificado** y guarde el archivo de certificado en el equipo.
   
   ![Configurar inicio de sesión único](./media/active-directory-saas-timeoffmanager-tutorial/IC795916.png "Configurar inicio de sesión único")
5. En otra ventana del explorador web, inicie sesión como administrador en el sitio de la compañía de TimeOffManager.
6. Vaya a **Cuenta \> Opciones de cuenta \> Configuración de inicio de sesión único**.
   
   ![Configuración de inicio de sesión único](./media/active-directory-saas-timeoffmanager-tutorial/IC795917.png "Configuración de inicio de sesión único")
7. En la sección **Configuración del inicio de sesión único** , siga estos pasos:
   
   ![Configuración de inicio de sesión único](./media/active-directory-saas-timeoffmanager-tutorial/IC795918.png "Configuración de inicio de sesión único")
  1.  Cree un archivo **codificado en base&64;** a partir del certificado descargado.  
   
       >[!TIP] 
       >Para más información, vea [Conversión de un certificado binario en un archivo de texto](http://youtu.be/PlgrzUZ-Y1o).
       > 
  2.  Abra el certificado codificado en base&64; en el Bloc de notas, copie su contenido en el Portapapeles y luego pegue todo el certificado en el cuadro de texto **Certificado X.509** .
  3.  En el Portal de Azure clásico, en la página de diálogo **Configurar inicio de sesión único en TimeOffManager**, copie el valor de **URL del emisor** y péguelo en el cuadro de texto **Idp Issuer** (Emisor de IdP).
  4.  En el Portal de Azure clásico, en la página de diálogo **Configurar inicio de sesión único en TimeOffManager**, copie el valor de **Dirección URL de inicio de sesión remoto** y péguelo en el cuadro de texto **IdP Endpoint URL** (Dirección URL de punto de conexión de IdP).
  5.  En **Aplicar SAML**, seleccione **No**.
  6.  En **Crear usuarios automáticamente**, seleccione **Sí**.
  7.  En el Portal de Azure clásico, en la página de diálogo **Configurar inicio de sesión único en TimeOffManager**, copie el valor de **Dirección URL de cierre de sesión remoto** y péguelo en el cuadro de texto **Logout URL** (URL de cierre de sesión). A continuación, haga clic en **Guardar cambios**.

1. En el Portal de Azure clásico, en la página de diálogo **Configurar inicio de sesión único en TimeOffManager**, seleccione la confirmación de configuración de inicio de sesión único y haga clic en **Completar**.
   
   ![Configurar inicio de sesión único](./media/active-directory-saas-timeoffmanager-tutorial/IC795919.png "Configurar inicio de sesión único")
2. En el menú de la parte superior, haga clic en **Atributos** to open the **SAML Token Atributos** .
   
   ![Atributos](./media/active-directory-saas-timeoffmanager-tutorial/IC795920.png "Atributos")
3. Para agregar las asignaciones de los atributos necesarios, realice los pasos siguientes:
   
   ![Atributos de token de SAML](./media/active-directory-saas-timeoffmanager-tutorial/123.png "Atributos de token de SAML")
   
   | Nombre del atributo | Valor de atributo |
   | --- | --- |
   | Email |User.mail |
   | Firstname |User.givenname |
   | Lastname |User.surname |
  1.  En cada fila de datos de la tabla anterior, haga clic en **agregar atributo de usuario**.
  2.  En el cuadro de texto **Nombre de atributo** , escriba el nombre de atributo que se muestra para la fila.
  3.  En el cuadro de texto **Valor de atributo** , seleccione el valor de atributo que se muestra para la fila.
  4.  Haga clic en **Complete**.
4. Haga clic en **Aplicar cambios**.

## <a name="configure-user-provisioning"></a>Configurar aprovisionamiento de usuarios
Para permitir que los usuarios de Azure AD inicien sesión en TimeOffManager, deben aprovisionarse en TimeOffManager.  

TimeOffManager admite aprovisionamiento de usuarios justo a tiempo. No hay ningún elemento de acción para usted.  

Los usuarios se agregan automáticamente durante el primer inicio de sesión mediante el inicio de sesión único.

>[!NOTE]
>Puede usar cualquier otra API o herramienta de creación de cuentas de usuario de TimeOffManager ofrecida por TimeOffManager para aprovisionar cuentas de usuario de AAD.
> 

## <a name="assign-users"></a>Asignar usuarios
Para probar la configuración, debe conceder acceso a los usuarios de Azure AD a los que quiere permitir el uso de su aplicación.

**Para asignar usuarios a TimeOffManager, lleve a cabo los siguientes pasos:**

1. En el Portal de Azure clásico, cree una cuenta de prueba.
2. En la página de integración de aplicaciones de **TimeOffManager** haga clic en **Asignar usuarios**.
   
   ![Asignar usuarios](./media/active-directory-saas-timeoffmanager-tutorial/IC795922.png "Asignar usuarios")
3. Seleccione su usuario de prueba, haga clic en **Asignar** y en **Sí** para confirmar la asignación.
   
   ![Sí](./media/active-directory-saas-timeoffmanager-tutorial/IC767830.png "Sí")

Si desea probar la configuración de inicio de sesión único, abra el Panel de acceso. Para obtener más información sobre el Panel de acceso, vea [Introducción al Panel de acceso](active-directory-saas-access-panel-introduction.md).


