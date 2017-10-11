---
title: "Tutorial: Configuración de Slack para aprovisionar automáticamente usuarios con Azure Active Directory | Microsoft Docs"
description: "Obtenga información sobre cómo configurar Azure Active Directory para aprovisionar y cancelar automáticamente el aprovisionamiento de cuentas de usuario de Slack."
services: active-directory
documentationcenter: 
author: asmalser-msft
writer: asmalser-msft
manager: sakula
ms.assetid: d4ca2365-6729-48f7-bb7f-c0f5ffe740a3
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/20/2017
ms.author: asmalser-msft
ms.reviewer: asmalser
ms.openlocfilehash: 3cb49a4abb26c34a938c963c4cf326b5ccd490de
ms.sourcegitcommit: 02e69c4a9d17645633357fe3d46677c2ff22c85a
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 08/03/2017
---
# <a name="tutorial-configuring-slack-for-automatic-user-provisioning"></a>Tutorial: Configuración de Slack para aprovisionar automáticamente usuarios


El objetivo de este tutorial es explicar los pasos que debe realizar en Slack y Azure AD para aprovisionar y cancelar automáticamente el aprovisionamiento de cuentas de usuario de Azure AD para Slack. 

## <a name="prerequisites"></a>Requisitos previos

En la situación descrita en este tutorial se supone que ya cuenta con los elementos siguientes:

*   Un inquilino de directorio de Azure Active Directory
*   Un inquilino de Slack con el [plan Plus](https://aadsyncfabric.slack.com/pricing) o uno superior habilitado 
*   Una cuenta de usuario de Slack con permisos de administrador de equipo 

Nota: La integración del aprovisionamiento de Azure AD se basa en la [API de Slack SCIM](https://api.slack.com/scim), que está disponible para los equipos de Slack con el plan Plus o uno superior.

## <a name="assigning-users-to-slack"></a>Asignación de usuarios a Slack

Azure Active Directory usa un concepto que se denomina "asignaciones" para determinar qué usuarios deben recibir acceso a determinadas aplicaciones. En el contexto de aprovisionamiento de cuentas de usuario automático, solo se sincronizarán los usuarios y grupos que se han "asignado" a una aplicación de Azure AD. 

Antes de configurar y habilitar el servicio de aprovisionamiento, debe decidir qué usuarios o grupos de Azure AD representan a los usuarios que necesitan acceder a la aplicación de Slack. Una vez decidido, puede asignar estos usuarios a la aplicación de Slack siguiendo estas instrucciones:

[Asignar un usuario o grupo a una aplicación empresarial](active-directory-coreapps-assign-user-azure-portal.md)

### <a name="important-tips-for-assigning-users-to-slack"></a>Sugerencias importantes para asignar usuarios a Slack

*   Se recomienda asignar un solo usuario de Azure AD a Slack para probar la configuración de aprovisionamiento. Más tarde, se pueden asignar otros usuarios o grupos.

*   Al asignar un usuario a Slack, debe seleccionar los roles **Usuario** o Grupo en el cuadro de diálogo de asignación. El rol "Acceso predeterminado" no funciona para realizar el aprovisionamiento.


## <a name="configuring-user-provisioning-to-slack"></a>Configuración del aprovisionamiento de usuarios en Slack 

Esta sección lo guía a través de los pasos necesarios para conectar la API de aprovisionamiento de su cuenta de usuario de Slack, así como para configurar el servicio de aprovisionamiento con el fin de crear, actualizar y deshabilitar cuentas de usuario asignadas de Slack en función de la asignación de grupos y usuarios Azure AD.

**Sugerencia**: También puede decidir habilitar el inicio de sesión único basado en SAML para Slack siguiendo las instrucciones de (Azure Portal)[https://portal.azure.com]. El inicio de sesión único puede configurarse independientemente del aprovisionamiento automático, aunque estas dos características se complementan entre sí.


### <a name="to-configure-automatic-user-account-provisioning-to-slack-in-azure-ad"></a>Para configurar el aprovisionamiento de cuentas de usuario automático para Slack en Azure AD, siga estos pasos:


1)  En [Azure Portal](https://portal.azure.com), vaya a la sección **Azure Active Directory > Aplicaciones empresariales > Todas las aplicaciones**.

2) Si ya ha configurado Slack para el inicio de sesión único, busque la instancia de Slack mediante el campo de búsqueda. En caso contrario, seleccione **Agregar** y busque **Slack** en la Galería de aplicaciones. Seleccione Slack en los resultados de búsqueda y agrégalo a la lista de aplicaciones.

3)  Seleccione la instancia de Slack y, después, seleccione la pestaña **Aprovisionamiento**.

4)  Establezca el **modo de aprovisionamiento** en **Automático**.

![Aprovisionamiento de Slack](./media/active-directory-saas-slack-provisioning-tutorial/Slack1.PNG)

5)  En **Credenciales de administrador**, haga clic en **Autorizar**. Se abrirá un cuadro de diálogo de autorización de Slack en una nueva ventana del explorador. 

6) En esa nueva ventana, inicie sesión en Slack con su cuenta de administrador de equipo. En el cuadro de diálogo de autorización que aparece, seleccione el equipo de Slack para el que desea habilitar el aprovisionamiento y, luego, seleccione **Autorizar**. Cuando termina, vuelva a Azure Portal para completar la configuración de aprovisionamiento.

![Cuadro de diálogo de autorización](./media/active-directory-saas-slack-provisioning-tutorial/Slack3.PNG)

7) En Azure Portal, haga clic en **Probar conexión** para asegurarse de que Azure AD puede conectarse a la aplicación de Slack. Si se produce un error de conexión, asegúrese de que su cuenta de Slack tiene permisos de administrador de equipo y vuelva a intentar el paso de "Autorizar".

8) Escriba la dirección de correo electrónico de una persona o grupo que debe recibir las notificaciones de error aprovisionamiento en el campo **Correo electrónico de notificación** y active la casilla que aparece a continuación.

9) Haga clic en **Guardar**. 

10) En la sección Asignaciones, seleccione **Synchronize Azure Active Directory Users to** (Sincronizar usuarios de Azure Active Directory con Slack).

11) En la sección **Asignaciones de atributos**, revise los atributos de usuario que se sincronizarán de Azure AD a Slack. Tenga en cuenta que los atributos seleccionados como propiedades de **Coincidencia** se usarán para establecer coincidencias con las cuentas de usuario de Slack con el objetivo de realizar operaciones de actualización. Seleccione el botón Guardar para confirmar los cambios.

12) Para habilitar el aprovisionamiento del servicio de aprovisionamiento de Azure AD para Slack, cambie el **estado de aprovisionamiento** a **Activado** en la sección **Configuración**.

13) Haga clic en **Guardar**. 

Esta acción iniciará la sincronización inicial de todos los usuarios y grupos asignados a Slack en la sección Usuarios y grupos. Tenga en cuenta que la sincronización inicial tardará más tiempo en realizarse que las posteriores, que se duran aproximadamente cada 10 minutos si se está ejecutando el servicio. Puede usar la sección **Detalles de sincronización** para supervisar el progreso y hacer clic en los vínculos a los informes de actividad de aprovisionamiento, que describen todas las acciones que ha llevado a cabo el servicio de aprovisionamiento en la aplicación de Slack.

## <a name="optional-configuring-group-object-provisioning-to-slack"></a>[Opcional] Configuración del aprovisionamiento de objetos de grupo para Slack 

Si lo desea, puede habilitar el aprovisionamiento de objetos de grupo de Azure AD para Slack. No es lo mismo que la asignación de grupos de usuarios, en que el objeto de grupo real, además de sus miembros, se replicará desde Azure AD a Slack. Por ejemplo, si tiene un grupo denominado "Mi grupo" en Azure AD, se creará un grupo idéntico denominado "Mi grupo" en Slack.

### <a name="to-enable-provisioning-of-group-objects"></a>Para habilitar el aprovisionamiento de objetos de grupo, siga estos pasos:

1) En la sección Asignaciones, seleccione **Synchronize Azure Active Directory Groups to** (Sincronizar grupos de Azure Active Directory con Slack).

2) En la hoja Asignación de atributos, establezca Habilitado en Sí.

3) En la sección **Asignaciones de atributos**, revise los atributos de grupo que se sincronizarán de Azure AD a Slack. Tenga en cuenta que los atributos seleccionados como propiedades de **Coincidencia** se usarán para establecer coincidencias con los grupos de Slack con el objetivo de realizar operaciones de actualización. 

4) Haga clic en **Guardar**.

Como resultado, todos los objetos de grupo asignados a Slack en la sección **Usuarios y grupos** se sincronizarán por completo de Azure AD a Slack. Puede usar la sección **Detalles de sincronización** para supervisar el progreso y hacer clic en los vínculos a los informes de actividad de aprovisionamiento, que describen todas las acciones que ha llevado a cabo el servicio de aprovisionamiento en la aplicación de Slack.


## <a name="additional-resources"></a>Recursos adicionales

* [Administración del aprovisionamiento de cuentas de usuario para aplicaciones empresariales](active-directory-enterprise-apps-manage-provisioning.md)
* [¿Qué es el acceso a aplicaciones y el inicio de sesión único con Azure Active Directory?](active-directory-appssoaccess-whatis.md)
