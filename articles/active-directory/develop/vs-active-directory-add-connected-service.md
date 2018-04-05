---
title: Adición de Azure Active Directory con Servicios conectados en Visual Studio | Microsoft Docs
description: Adición de Azure Active Directory mediante el cuadro de diálogo Agregar servicios conectados de Visual Studio
services: visual-studio-online
documentationcenter: na
author: ghogen
manager: douge
editor: ''
ms.assetid: f599de6b-e369-436f-9cdc-48a0165684cb
ms.service: active-directory
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/12/2018
ms.author: ghogen
ms.openlocfilehash: 882ba1c7ea8ef6889bc9ad20031070cd54100026
ms.sourcegitcommit: 34e0b4a7427f9d2a74164a18c3063c8be967b194
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 03/30/2018
---
# <a name="adding-an-azure-active-directory-by-using-connected-services-in-visual-studio"></a>Adición de Azure Active Directory con Servicios conectados en Visual Studio

Mediante el uso de Azure Active Directory (Azure AD), puede admitir el inicio de sesión único (SSO) para aplicaciones web ASP.NET MVC o la autenticación de Active Directory en los servicios de API web. Con la autenticación de Azure AD, los usuarios pueden usar sus cuentas desde Azure Active Directory para conectarse a las aplicaciones web. Las ventajas de la autenticación de Azure AD con la API web incluyen seguridad de datos mejorada al exponer una API desde una aplicación web. Con Azure AD, no es necesario que administre un sistema de autenticación independiente con su propia administración de cuentas y usuarios.

En este artículo y sus artículos complementarios se proporcionan detalles acerca del uso de la característica Servicio conectado de Visual Studio para Active Directory. La funcionalidad está disponible en Visual Studio 2017 y Visual Studio 2015.

En la actualidad, el servicio conectado de Active Directory no es compatible con aplicaciones de ASP.NET Core.

## <a name="prerequisites"></a>requisitos previos

- Cuenta de Azure: si todavía no tiene ninguna cuenta de Azure, puede [registrarse para una evaluación gratuita](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A261C142F) o [activar las ventajas de suscriptor de Visual Studio](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F).

### <a name="connect-to-azure-active-directory-using-the-connected-services-dialog"></a>Conexión con Azure Active Directory mediante el cuadro de diálogo Servicios conectados

1. En Visual Studio, cree o abra un proyecto de ASP.NET MVC o un proyecto de API web de ASP.NET. Puede usar las plantillas MVC, API Web, Aplicación de una sola página, Aplicación de API de Azure, Aplicación móvil de Azure y Servicio móvil de Azure.

1. Seleccione el comando de menú **Proyecto > Agregar servicio conectado...** o haga doble clic en el nodo **Servicios conectados** que se encuentra bajo el proyecto en el Explorador de soluciones.

1. En la página **Servicios conectados**, seleccione **Autenticación con Azure Active Directory**.

    ![Página Servicios conectados](./media/vs-azure-active-directory/connected-services-add-active-directory.png)

1. En la página **Introducción**, seleccione **Siguiente**. Si ve errores en esta página, consulte [Diagnóstico de errores con el servicio conectado de Azure Active Directory](vs-active-directory-error.md).

    ![Página de introducción](./media/vs-azure-active-directory/configure-azure-ad-wizard-1.png)

1. En la página **Inicio de sesión único**, seleccione un dominio desde la lista desplegable **Dominio**. La lista contiene todos los dominios accesibles para las cuentas enumeradas en el cuadro de diálogo de configuración de la cuenta de Visual Studio (**Archivo > Configuración de la cuenta...**). Como alternativa, puede escribir un nombre de dominio si no encuentra el que busca, por ejemplo, `mydomain.onmicrosoft.com`. Puede elegir la opción para crear una nueva aplicación de Azure Active Directory o usar la configuración de una aplicación de Azure Active Directory existente. Cuando termine, seleccione **Siguiente**.

    ![Página de inicio de sesión único](./media/vs-azure-active-directory/configure-azure-ad-wizard-2.png)

1. En la página **Acceso al directorio**, seleccione la opción **Leer datos del directorio** según sea necesario. Los desarrolladores suelen incluir esta opción.

    ![Página de acceso a directorios](./media/vs-azure-active-directory/configure-azure-ad-wizard-3.png)

1. Seleccione **Finalizar** para iniciar las modificaciones realizadas en el proyecto para habilitar la autenticación de Azure AD. Visual Studio muestra el progreso durante este tiempo:

    ![Progreso del servicio conectado de Active Directory](./media/vs-azure-active-directory/active-directory-connected-service-output.png)

1. Una vez completado el proceso, Visual Studio abre el explorador con uno de los siguientes artículos, según corresponda para el tipo de proyecto:

    - [Introducción a proyectos MVC de .NET](vs-active-directory-dotnet-getting-started.md)
    - [Introducción a proyectos de WebAPI](vs-active-directory-webapi-getting-started.md)

1. También puede ver el dominio de Active Directory en [Azure Portal](http://go.microsoft.com/fwlink/p/?LinkID=525040).

## <a name="how-your-project-is-modified"></a>¿Cómo se modifica el proyecto?

Cuando agrega el servicio conectado en el asistente, Visual Studio agrega Azure Active Directory y las referencias asociadas a su proyecto. También se modifican los archivos de configuración y archivos de código de su proyecto para agregar compatibilidad con Azure AD. Las modificaciones específicas realizadas por Visual Studio dependen del tipo de proyecto. Consulte los artículos siguientes para más información:

- [¿Qué le ha ocurrido a mi proyecto MVC de .NET?](vs-active-directory-dotnet-what-happened.md)
- [¿Qué le ha ocurrido a mi proyecto de API Web?](vs-active-directory-webapi-what-happened.md)

## <a name="next-steps"></a>Pasos siguientes

- [Escenarios de autenticación para Azure Active Directory](active-directory-authentication-scenarios.md)
- [Adición de inicio de sesión con Microsoft a una aplicación web ASP.NET](guidedsetups/active-directory-aspnetwebapp-v1.md)
