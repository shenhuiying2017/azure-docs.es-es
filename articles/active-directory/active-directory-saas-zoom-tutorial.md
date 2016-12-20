---
title: "Tutorial: Integración de Azure Active Directory con Zoom | Microsoft Docs"
description: "Aprenda cómo usar Zoom con Azure Active Directory para habilitar el inicio de sesión único, el aprovisionamiento automatizado, etc."
services: active-directory
author: jeevansd
documentationcenter: na
manager: femila
ms.assetid: 0ebdab6c-83a8-4737-a86a-974f37269c31
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 11/15/2016
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 80668119e273aaaa7d36d2c093fba344ea50e3d8


---
# <a name="tutorial-azure-active-directory-integration-with-zoom"></a>Tutorial: Integración de Azure Active Directory con Zoom
El objetivo de este tutorial es mostrar la integración de Azure y Zoom.  
En la situación descrita en este tutorial se supone que ya cuenta con los elementos siguientes:

* Una suscripción de Azure válida
* Un inquilino de Zoom

Después de completar este tutorial, los usuarios de Azure AD que ha asignado a Zoom podrá realizar un inicio de sesión único en la aplicación en el sitio de la compañía de Zoom (inicio de sesión iniciado por el proveedor del servicio) o con la [Introducción al Panel de acceso](active-directory-saas-access-panel-introduction.md)

La situación descrita en este tutorial consta de los siguientes bloques de creación:

1. Habilitación de la integración de aplicaciones para Zoom
2. Configuración del inicio de sesión único
3. Configuración del aprovisionamiento de usuario
4. Asignación de usuarios

![Escenario](./media/active-directory-saas-zoom-tutorial/IC784693.png "Scenario")

## <a name="enabling-the-application-integration-for-zoom"></a>Habilitación de la integración de aplicaciones para Zoom
El objetivo de esta sección es describir cómo habilitar la integración de las aplicaciones para Zoom.

### <a name="to-enable-the-application-integration-for-zoom-perform-the-following-steps"></a>Siga estos pasos para habilitar la integración de aplicaciones para Zoom:
1. En el panel de navegación izquierdo del Portal de Azure clásico, haga clic en **Active Directory**.
   
   ![Active Directory](./media/active-directory-saas-zoom-tutorial/IC700993.png "Active Directory")
2. En la lista **Directory** , seleccione el directorio cuya integración desee habilitar.
3. Para abrir la vista de aplicaciones, haga clic en **Applications** , en el menú superior de la vista de directorios.
   
   ![Applications](./media/active-directory-saas-zoom-tutorial/IC700994.png "Applications")
4. Haga clic en **Agregar** en la parte inferior de la página.
   
   ![Agregar aplicación](./media/active-directory-saas-zoom-tutorial/IC749321.png "Add application")
5. En el cuadro de diálogo **¿Qué desea hacer?**, haga clic en **Agregar una aplicación de la galería**.
   
   ![Agregar una aplicación de la galería](./media/active-directory-saas-zoom-tutorial/IC749322.png "Add an application from gallerry")
6. En el **cuadro de búsqueda**, escriba **Zoom**.
   
   ![Galería de aplicaciones](./media/active-directory-saas-zoom-tutorial/IC784694.png "Application Gallery")
7. En el panel de resultados, seleccione **Zoom** y, luego, haga clic en **Completar** para agregar la aplicación.
   
   ![Zoom](./media/active-directory-saas-zoom-tutorial/IC784695.png "Zoom")

## <a name="configuring-single-sign-on"></a>Configuración del inicio de sesión único
El objetivo de esta sección es describir cómo se habilita la autenticación de los usuarios en Zoom con su cuenta de Azure AD usando el protocolo SAML basado en la federación.  
Como parte de este procedimiento, es necesario crear un archivo de certificado codificado en base 64.  
Si no está familiarizado con este procedimiento, consulte [Conversión de un certificado binario en un archivo de texto](http://youtu.be/PlgrzUZ-Y1o).

### <a name="to-configure-single-sign-on-perform-the-following-steps"></a>Siga estos pasos para configurar el inicio de sesión único:
1. En el Portal de Azure clásico, en la página de integración de la aplicación **Zoom**, haga clic en **Configurar inicio de sesión único** para abrir el cuadro de diálogo **Configurar inicio de sesión único**.
   
   ![Configurar inicio de sesión único](./media/active-directory-saas-zoom-tutorial/IC784696.png "Configure single sign-on")
2. En la página **How would you like users to sign on to Zoom** (¿Cómo desea que los usuarios inicien sesión en Zoom?), seleccione **Inicio de sesión único de Microsoft Azure AD** y, luego, haga clic en **Siguiente**.
   
   ![Configurar inicio de sesión único](./media/active-directory-saas-zoom-tutorial/IC784697.png "Configure single sign-on")
3. En la página **Configurar dirección URL de la aplicación**, en el cuadro de texto **URL de inicio de sesión de Zoom**, escriba su dirección URL con el siguiente patrón "*http://company.zoom.us*" y luego haga clic en **Siguiente**.
   
   ![Configurar dirección URL de la aplicación](./media/active-directory-saas-zoom-tutorial/IC784698.png "Configure App URL")
4. En la página **Configuración de inicio de sesión único en Zoom**, haga clic en **Descargar certificado** y luego guarde el archivo de certificado en el equipo.
   
   ![Configurar inicio de sesión único](./media/active-directory-saas-zoom-tutorial/IC784699.png "Configure single sign-on")
5. En otra ventana del explorador web, inicie sesión en su sitio de la compañía de Zoom como administrador.
6. Haga clic en la pestaña **Inicio de sesión único** .
   
   ![Inicio de sesión único](./media/active-directory-saas-zoom-tutorial/IC784700.png "Single sign-on")
7. Haga clic en la pestaña **Control de seguridad** y luego vaya a la configuración de **Inicio de sesión único**.
8. En la sección Inicio de sesión único, siga estos pasos:
   
   ![Inicio de sesión único](./media/active-directory-saas-zoom-tutorial/IC784701.png "Single sign-on")
   
   1. En el Portal de Azure clásico, en la página del cuadro de diálogo **Configurar inicio de sesión único en Zoom**, copie el valor de **Dirección URL del servicio de inicio de sesión único** y péguelo en el cuadro de texto **Dirección URL de la página de cierre de sesión**.
   2. En el Portal de Azure clásico, en la página del cuadro de diálogo **Configurar inicio de sesión único en Zoom**, copie el valor de **Dirección URL del servicio de cierre de sesión único** y péguelo en el cuadro de texto **Dirección URL de la página de cierre de sesión**.
   3. Cree un archivo **codificado en base 64** a partir del certificado descargado.  
      
      > [!TIP]
      > Para obtener más información, consulte [Conversión de un certificado binario en un archivo de texto](http://youtu.be/PlgrzUZ-Y1o)
      > 
      > 
   4. Abra el certificado codificado en base 64 en el Bloc de notas, copie su contenido en el Portapapeles y luego péguelo en el cuadro de texto **Certificado de proveedor de identidades** .
   5. En el Portal de Azure clásico, en la página del cuadro de diálogo **Configurar inicio de sesión único en Zoom**, copie el valor de **URL del emisor** y péguelo en el cuadro de texto **Emisor**.
   6. Haga clic en **Save**.
9. En el Portal de Azure clásico, seleccione la confirmación de configuración de inicio de sesión único y haga clic en **Completar** para cerrar el cuadro de diálogo **Configurar inicio de sesión único**.
   
   ![Configurar inicio de sesión único](./media/active-directory-saas-zoom-tutorial/IC784702.png "Configure single sign-on")

## <a name="configuring-user-provisioning"></a>Configuración del aprovisionamiento de usuario
Para permitir que los usuarios de Azure AD inicien sesión en Zoom, deben aprovisionarse a Zoom.  
En el caso de Zoom, el aprovisionamiento es una tarea manual.

### <a name="to-provision-a-user-accounts-perform-the-following-steps"></a>Para aprovisionar cuentas de usuario, realice estos pasos:
1. Inicie sesión en su sitio de la compañía de **Zoom** como administrador.
2. En la pestaña **Administración de cuentas** haga clic en **Administración de usuarios**.
3. En la sección Administración de usuarios, haga clic en **Agregar usuarios**.
   
   ![Administración de usuarios](./media/active-directory-saas-zoom-tutorial/IC784703.png "User management")
4. En la página **Agregar usuarios** , realice los pasos siguientes:
   
   ![Agregar usuarios](./media/active-directory-saas-zoom-tutorial/IC784704.png "Add users")
   
   1. Como **Tipo de usuario**, seleccione **Básico**.
   2. En el cuadro de texto **Correos electrónicos** , escriba la dirección de correo electrónico de una cuenta de AAD válida que quiera suministrar.
   3. Haga clic en **Agregar**.

> [!NOTE]
> Puede usar cualquier otra API o herramienta de creación de cuentas de usuario de Zoom ofrecida por Zoom para aprovisionar cuentas de usuario de AAD.
> 
> 

## <a name="assigning-users"></a>Asignación de usuarios
Para probar la configuración, debe conceder acceso a los usuarios de Azure AD a los que quiere permitir el uso de su aplicación.

### <a name="to-assign-users-to-zoom-perform-the-following-steps"></a>Para asignar usuarios a Zoom, lleve a cabo los siguientes pasos:
1. En el Portal de Azure clásico, cree una cuenta de prueba.
2. En la página de integración de la aplicación **Zoom**, haga clic en **Asignar usuarios**.
   
   ![Asignar usuarios](./media/active-directory-saas-zoom-tutorial/IC784705.png "Assign users")
3. Seleccione su usuario de prueba, haga clic en **Asignar** y en **Sí** para confirmar la asignación.
   
   ![Sí](./media/active-directory-saas-zoom-tutorial/IC767830.png "Yes")

Si desea probar la configuración de inicio de sesión único, abra el Panel de acceso. Para obtener más información sobre el Panel de acceso, vea [Introducción al Panel de acceso](active-directory-saas-access-panel-introduction.md).




<!--HONumber=Nov16_HO3-->


