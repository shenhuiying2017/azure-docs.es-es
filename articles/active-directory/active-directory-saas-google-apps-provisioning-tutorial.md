---
title: "Tutorial: Configuración de Google Apps para el aprovisionamiento automático de usuarios en Azure | Microsoft Docs"
description: "Obtenga información sobre cómo aprovisionar y cancelar automáticamente el aprovisionamiento de cuentas de usuario de Azure AD para Google Apps."
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: 6dbd50b5-589f-4132-b9eb-a53a318a64e5
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/01/2017
ms.author: jeedes
ms.openlocfilehash: a77b5b1fff670ed7698d0ef48fa63f8a8f9be819
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/11/2017
---
# <a name="tutorial-configure-google-apps-for-automatic-user-provisioning"></a>Tutorial: Configuración de Google Apps para aprovisionar a los usuarios automáticamente

El objetivo de este tutorial es explicar cómo aprovisionar y cancelar automáticamente el aprovisionamiento de cuentas de usuario de Azure Active Directory (Azure AD) para Google Apps.

## <a name="prerequisites"></a>Requisitos previos

En la situación que se describe en este tutorial se supone que ya cuenta con los elementos siguientes:

*   Un inquilino de Azure Active Directory.
*   Un inquilino válido para Google Apps for Work o Google Apps for Education. Puede usar una cuenta de evaluación gratuita para cualquiera de ambos servicios.
*   Una cuenta de usuario de Google Apps con permisos de administrador de equipo.

## <a name="assign-users-to-google-apps"></a>Asignación de usuarios a Google Apps

Azure Active Directory usa un concepto que se denomina "asignaciones" para determinar qué usuarios deben recibir acceso a determinadas aplicaciones. En el contexto de aprovisionamiento automático de cuentas de usuario, solo se sincronizarán los usuarios y grupos que se han "asignado" a una aplicación en Azure AD.

Antes de configurar y habilitar el servicio de aprovisionamiento, tiene que decidir qué usuarios o grupos de Azure AD necesitan acceso a la aplicación Google Apps. Una vez decidido, puede asignar estos usuarios a la aplicación Google Apps siguiendo las instrucciones de: [Asignación de un usuario o un grupo a una aplicación empresarial](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal).

> [!IMPORTANT]
> Se recomienda asignar un solo usuario de Azure AD a Google Apps para probar la configuración de aprovisionamiento. Asigne usuarios y grupos adicionales más tarde.

> Al asignar un usuario a Google Apps, debe seleccionar los roles **Usuario** o **Grupo** en el cuadro de diálogo de asignación. El rol **Acceso predeterminado** no funciona para realizar el aprovisionamiento.

## <a name="enable-automated-user-provisioning"></a>Habilitación del aprovisionamiento automático de usuarios

Esta sección le guiará a través del proceso de conexión de Azure AD a la API de aprovisionamiento de cuentas de usuario de Google Apps. También le ayuda a configurar el servicio de aprovisionamiento para crear, actualizar y deshabilitar cuentas de usuario asignado en Google Apps en función de la asignación de usuario y de grupo en Azure AD.

>[!TIP]
>También puede decidir habilitar el inicio de sesión único basado en SAML para Google Apps siguiendo las instrucciones de [Azure Portal](https://portal.azure.com). El inicio de sesión único puede configurarse independientemente del aprovisionamiento automático, aunque estas dos características se complementan entre sí.

### <a name="configure-automatic-user-account-provisioning"></a>Configurar el aprovisionamiento automático de cuentas de usuario

> [!NOTE]
> Otra opción viable para automatizar el aprovisionamiento de usuarios en Google Apps consiste en usar [Google Apps Directory Sync (GADS)](https://support.google.com/a/answer/106368?hl=en). GADS aprovisiona las identidades de Active Directory locales en Google Apps. En cambio, la solución en este tutorial realiza el aprovisionamiento de los usuarios de Azure Active Directory (nube) y a los grupos habilitados para correo en Google Apps. 

1. Inicie sesión en la [Consola de administración de Google Apps](http://admin.google.com/) con su cuenta de administrador y haga clic en **Security** (Seguridad). Si no ve el vínculo, puede estar oculto debajo del menú **More Controls** (Más controles) en la parte inferior de la pantalla.
   
    ![Selección de seguridad.][10]

2. En la página **Security** (Seguridad), haga clic en **API Reference** (Referencia de API).
   
    ![Selección de Referencia de API.][15]

3. Seleccione **Enable API access**.
   
    ![Selección de Referencia de API.][16]

    > [!IMPORTANT]
    > El nombre en Azure Active Directory de cada uno de los usuarios que desee aprovisionar en Google Apps *tiene que* estar vinculado a un dominio personalizado. Por ejemplo, Google Apps no acepta los nombres de usuario parecidos a bob@contoso.onmicrosoft.com. Por otro lado, bob@contoso.com se acepta. Puede cambiar el dominio de un usuario existente editando sus propiedades en Azure AD. En los pasos siguientes hemos incluido instrucciones sobre cómo establecer un dominio personalizado para Azure Active Directory y Google Apps.
      
4. Si todavía no ha agregado un nombre de dominio personalizado para Azure Active Directory, siga los pasos a continuación:
  
    a. En el panel de navegación izquierdo de [Azure Portal](https://portal.azure.com), seleccione **Azure Active Directory**. En la lista de directorios, seleccione el directorio. 

    b. Seleccione **Nombre de dominio** en el panel de navegación izquierdo y, después seleccione **Agregar**.
     
     ![Dominio](./media/active-directory-saas-google-apps-provisioning-tutorial/domain_1.png)

     ![Incorporación de un dominio](./media/active-directory-saas-google-apps-provisioning-tutorial/domain_2.png)

    c. Escriba el nombre del dominio en el campo **Nombre de dominio** . Este nombre de dominio debe ser el mismo que piensa usar para Google Apps. A continuación, seleccione el botón **Agregar dominio**.
     
     ![Nombre de dominio](./media/active-directory-saas-google-apps-provisioning-tutorial/domain_3.png)

    d. Seleccione **Siguiente** para ir a la página de comprobación. Para comprobar que es propietario de este dominio, edite los registros DNS del dominio según los valores proporcionados en esta página. Puede optar por realizar la comprobación con **registros MX** o **registros TXT** según lo que seleccione en la opción **Tipo de registro**. 
    
    Si desea instrucciones detalladas sobre cómo comprobar los nombres de dominio con Azure AD, consulte [Incorporación de su propio nombre de dominio a Azure AD](https://go.microsoft.com/fwLink/?LinkID=278919&clcid=0x409).
     
     ![Dominio](./media/active-directory-saas-google-apps-provisioning-tutorial/domain_4.png)

    e. Repita los pasos anteriores para todos los dominios que quiera agregar a su directorio.

    > [!NOTE]
    Para el aprovisionamiento de usuarios, el dominio personalizado de Google Apps debe coincidir con el nombre de dominio de la instancia de Azure AD de origen. Si no coinciden, puede resolver el problema si implementa la personalización de asignación de atributos.


5. Ahora que ha comprobado todos los dominios con Azure AD, tiene que volverlos a comprobar con Google Apps. Para cada dominio que no esté registrado aún con Google Apps, realice los pasos siguientes:
   
    a. En la [Consola de administración de Google Apps](http://admin.google.com/), haga clic en **Domains** (Dominios).
     
     ![Selección de dominios][20]

    b. Haga clic en **Add a domain or a domain alias** (Agregar un dominio o un alias de dominio).
     
     ![Adición de un nuevo dominio][21]

    c. Seleccione **Add another domain** (Añadir otro dominio) y escriba el nombre del dominio que desee agregar.
     
     ![Especificación de un nombre de dominio][22]

    d. Haga clic en **Continue and verify domain ownership** (Continuar y comprobar la propiedad del dominio). A continuación, siga los pasos para comprobar que posee el nombre de dominio. Para obtener instrucciones completas sobre cómo comprobar un dominio con Google Apps, consulte [Cómo elegir un método de verificación](https://support.google.com/webmasters/answer/35179).

    e. Repita los pasos anteriores para todos los dominios adicionales que se van a agregar a Google Apps.
     
     > [!WARNING]
     > Si cambia el dominio principal del inquilino de Google Apps y ya ha configurado el inicio de sesión único con Azure AD, tendrá que repetir el paso 3 en [Paso 2: Habilitar el inicio de sesión único](#step-two-enable-single-sign-on).
       
6. En la [Consola de administración de Google Apps](http://admin.google.com/), seleccione **Admin Roles** (Funciones de administrador).
   
     ![Selección de Google Apps][26]

7. Determine qué cuenta de administrador quiere usar para administrar el aprovisionamiento de usuarios. Para el **rol administrativo** de esa cuenta, edite los **privilegios** para ese rol. Asegúrese de que habilita todos los **privilegios de la API de administración** para que esta cuenta pueda usarse para el aprovisionamiento.
   
     ![Selección de Google Apps][27]
   
    > [!NOTE]
    > Si va a configurar un entorno de producción, la práctica recomendada es crear una nueva cuenta de administrador en Google Apps específicamente para este paso. Estas cuentas tienen que tener un rol de administrador asociado que tenga los privilegios necesarios de la API.
     
8. En [Azure Portal](https://portal.azure.com), vaya a la sección **Azure Active Directory**  > **Aplicaciones empresariales** > **Todas las aplicaciones**.

9. Si ya ha configurado Google Apps para el inicio de sesión único, busque la instancia de Google Apps mediante el campo de búsqueda. En caso contrario, haga clic en **Agregar** y busque **Google Apps** en la galería de aplicaciones. Seleccione **Google Apps** en los resultados de la búsqueda y agréguelo a la lista de aplicaciones.

10. Seleccione la instancia de Google Apps y, después, haga clic en la pestaña **Aprovisionamiento**.

11. Establezca el **modo de aprovisionamiento** en **Automático**. 

     ![Aprovisionamiento](./media/active-directory-saas-google-apps-provisioning-tutorial/provisioning.png)

12. En **Credenciales de administrador**, haga clic en **Autorizar**. Se abrirá un cuadro de diálogo de autorización de Google Apps en una nueva ventana del explorador.

13. Confirme que desea conceder permiso de Azure Active Directory para realizar cambios en el inquilino de Google Apps. Seleccione **Aceptar**.
    
     ![Confirme los permisos.][28]

14. En Azure Portal, seleccione **Probar conexión** para asegurarse de que Azure AD puede conectarse a la aplicación Google Apps. Si la conexión no se establece, asegúrese de que la cuenta de Google Apps tiene permisos de administrador de equipo. Luego vuelva a intentar el paso **Autorizar**.

15. Escriba la dirección de correo electrónico de una persona o grupo que debe recibir las notificaciones de error de aprovisionamiento en el campo **Correo electrónico de notificación**. Luego active la casilla.

16. Seleccione **Guardar.**

17. En la sección **Asignaciones**, seleccione **Synchronize Azure Active Directory Users to Google Apps** (Sincronizar usuarios de Azure Active Directory con Google Apps).

18. En la sección **Asignaciones de atributos**, revise los atributos de usuario que se sincronizarán entre Azure AD y Google Apps. Los atributos que son propiedades de **Coincidencia** se usan para buscar coincidencias con las cuentas de usuario de Google Apps con el objetivo de realizar operaciones de actualización. Para confirmar los cambios, seleccione **Guardar**.

19. Para habilitar el servicio de aprovisionamiento de Azure AD para Google Apps, cambie el **Estado de aprovisionamiento** a **Activado** en **Configuración**.

20. Seleccione **Guardar**.

Este proceso inicia la sincronización inicial de todos los usuarios y grupos asignados a Google Apps en la sección Usuarios y grupos. La sincronización inicial tardará más tiempo en realizarse que las posteriores, que se producen aproximadamente cada 20 minutos si se está ejecutando el servicio. 

Puede usar la sección **Detalles de sincronización** para supervisar el progreso y hacer clic en los vínculos a los informes de actividad de aprovisionamiento. Estos informes describen todas las acciones que lleva a cabo el servicio de aprovisionamiento en su aplicación Google Apps.

## <a name="additional-resources"></a>Recursos adicionales

* [Administración del aprovisionamiento de cuentas de usuario para aplicaciones empresariales](active-directory-saas-tutorial-list.md)
* [¿Qué es el acceso a aplicaciones y el inicio de sesión único con Azure Active Directory?](active-directory-appssoaccess-whatis.md)
* [Configuración del inicio de sesión único](active-directory-saas-google-apps-tutorial.md)



<!--Image references-->

[10]: ./media/active-directory-saas-google-apps-provisioning-tutorial/gapps-security.png
[15]: ./media/active-directory-saas-google-apps-provisioning-tutorial/gapps-api.png
[16]: ./media/active-directory-saas-google-apps-provisioning-tutorial/gapps-api-enabled.png
[20]: ./media/active-directory-saas-google-apps-provisioning-tutorial/gapps-domains.png
[21]: ./media/active-directory-saas-google-apps-provisioning-tutorial/gapps-add-domain.png
[22]: ./media/active-directory-saas-google-apps-provisioning-tutorial/gapps-add-another.png
[24]: ./media/active-directory-saas-google-apps-provisioning-tutorial/gapps-provisioning.png
[25]: ./media/active-directory-saas-google-apps-provisioning-tutorial/gapps-provisioning-auth.png
[26]: ./media/active-directory-saas-google-apps-provisioning-tutorial/gapps-admin.png
[27]: ./media/active-directory-saas-google-apps-provisioning-tutorial/gapps-admin-privileges.png
[28]: ./media/active-directory-saas-google-apps-provisioning-tutorial/gapps-auth.png
