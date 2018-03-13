---
title: "Adición de Azure Active Directory con Servicios conectados en Visual Studio | Microsoft Docs"
description: "Adición de Azure Active Directory mediante el cuadro de diálogo Agregar servicios conectados de Visual Studio"
services: visual-studio-online
documentationcenter: na
author: kraigb
manager: ghogen
editor: 
ms.assetid: f599de6b-e369-436f-9cdc-48a0165684cb
ms.service: active-directory
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/01/2017
ms.author: kraigb
ms.openlocfilehash: a767c93fb271f3aa33d9556c69c511bcac7cb0d5
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="adding-an-azure-active-directory-by-using-connected-services-in-visual-studio"></a>Adición de Azure Active Directory con Servicios conectados en Visual Studio
Mediante el uso de Azure Active Directory (Azure AD), puede admitir el inicio de sesión único (SSO) para aplicaciones web ASP.NET MVC o la autenticación de Active Directory en los servicios de API web. Con la autenticación de Azure Active Directory, sus usuarios pueden usar sus cuentas desde Azure Active Directory para conectarse a sus aplicaciones web. Las ventajas de la autenticación de Azure Active Directory con la API web incluyen seguridad de datos mejorada al exponer una API desde una aplicación web. Con Azure AD, no es necesario que administre un sistema de autenticación independiente con su propia administración de cuentas y usuarios.

## <a name="prerequisites"></a>Requisitos previos
- Cuenta de Azure: si todavía no tiene ninguna cuenta de Azure, puede [registrarse para una evaluación gratuita](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A261C142F) o [activar las ventajas de suscriptor de Visual Studio](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F).

### <a name="connect-to-azure-active-directory-using-the-connected-services-dialog"></a>Conexión con Azure Active Directory mediante el cuadro de diálogo Servicios conectados
1. En Visual Studio, cree o abra un proyecto de ASP.NET MVC o un proyecto de API web de ASP.NET.

1. En el Explorador de soluciones, haga clic con el botón derecho en el nodo **Servicios conectados** y, en el menú contextual, seleccione **Agregar Servicios conectados**.

1. En la página **Servicios conectados**, seleccione **Autenticación con Azure Active Directory**.
   
    ![Página Servicios conectados](./media/vs-azure-tools-connected-services-add-active-directory/connected-services-add-active-directory.png)

1. En la página de **introducción** del Asistente para **configurar la autenticación de Azure AD**, seleccione **Siguiente**.
   
    ![Página de introducción](./media/vs-azure-tools-connected-services-add-active-directory/configure-azure-ad-wizard-1.png)

1. En la página **Inicio de sesión único** del Asistente para **configurar la autenticación de Azure AD**, seleccione un dominio en la lista desplegable de **dominios**. La lista de dominios contiene todos los dominios a los que pueden tener acceso las cuentas enumeradas en el cuadro de diálogo Configuración de cuenta. Como alternativa, puede escribir un nombre de dominio si no encuentra el que busca, por ejemplo, `mydomain.onmicrosoft.com`. Puede elegir la opción para crear una nueva aplicación de Azure Active Directory o usar la configuración de una aplicación de Azure Active Directory existente. Cuando termine, seleccione **Siguiente**.
   
    ![Página de inicio de sesión único](./media/vs-azure-tools-connected-services-add-active-directory/configure-azure-ad-wizard-2.png)

1. En la página de **acceso a directorios** del Asistente para **configurar la autenticación de Azure AD**, asegúrese de que la opción **Leer datos de directorio** está activada. 
   
    ![Página de acceso a directorios](./media/vs-azure-tools-connected-services-add-active-directory/configure-azure-ad-wizard-3.png)

1. Seleccione **Finalizar** para agregar el código de configuración y las referencias que se necesitan para habilitar su proyecto para la autenticación de Azure AD. Puede ver el dominio de Active Directory en [Azure Portal](http://go.microsoft.com/fwlink/p/?LinkID=525040).

1. Visual Studio mostrará un artículo sobre [qué ha ocurrido](#how-your-project-is-modified) para mostrarle las modificaciones del proyecto. Si quiere comprobar que todo funcionó, abra uno de los archivos de configuración modificados y compruebe que la configuración mencionada en el artículo existe. 

## <a name="how-your-project-is-modified"></a>¿Cómo se modifica el proyecto?
Al ejecutar el asistente, Visual Studio agrega Azure Active Directory y referencias asociadas a su proyecto. También se modifican los archivos de configuración y archivos de código de su proyecto para agregar compatibilidad con Azure AD. Las modificaciones específicas realizadas por Visual Studio dependen del tipo de proyecto. Para información detallada sobre cómo se modifican los proyectos de ASP.NET MVC, consulte [¿Qué ha ocurrido? - Proyectos de MVC](http://go.microsoft.com/fwlink/p/?LinkID=513809). Para los proyectos de API web, consulte [¿Qué ha ocurrido? - Proyectos de API web](http://go.microsoft.com/fwlink/p/?LinkId=513810).

## <a name="next-steps"></a>Pasos siguientes
* [Foro de MSDN para Azure Active Directory](https://social.msdn.microsoft.com/forums/azure/home?forum=WindowsAzureAD)
* [Documentación de Azure Active Directory](https://azure.microsoft.com/documentation/services/active-directory/)
* [Entrada de blog: Introducción a Azure Active Directory](http://blogs.msdn.com/b/brunoterkaly/archive/2014/03/03/introduction-to-windows-azure-active-directory.aspx)

