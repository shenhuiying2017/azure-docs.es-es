---
title: Uso del paquete de contenido de Power BI de Azure Active Directory | Microsoft Docs
description: Aprenda a usar el paquete de contenido de Power BI de Azure Active Directory.
services: active-directory
author: MarkusVi
manager: femila
ms.assetid: addd60fe-d5ac-4b8b-983c-0736c80ace02
ms.service: active-directory
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 07/15/2017
ms.author: markvi
ms.reviewer: dhanyahk
ms.openlocfilehash: 5c642bb814a279756e8391f12fdc86b6ec0b4a8f
ms.sourcegitcommit: 02e69c4a9d17645633357fe3d46677c2ff22c85a
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 08/03/2017
---
# <a name="how-to-use-the-azure-active-directory-power-bi-content-pack"></a>Uso del paquete de contenido de Power BI de Azure Active Directory

Comprender cómo los usuarios adoptan y usan las características de Azure Active Directory es fundamental para un administrador de TI. Permite planear la infraestructura de TI y la comunicación para aumentar la utilización y sacar el máximo partido de las características de AAD. El paquete de contenido de Power BI para Azure Active Directory ofrece la capacidad de analizar con mayor profundidad los datos para comprender cómo puede usarlos para recopilar información más completa sobre qué sucede con su entorno Azure Active Directory para las diversas funcionalidades de las que depende en gran medida.  Con la integración de las API de Azure Active Directory en Power BI, puede descargar fácilmente los paquetes de contenido pregenerados y obtener información sobre todas las actividades dentro de su entorno Azure Active Directory por medio de la completa experiencia de visualización que Power BI ofrece. Puede crear su propio panel y compartirlo fácilmente con cualquier persona de su organización. 

En este tema se proporcionan instrucciones paso a paso para instalar y usar el paquete de contenido en su entorno.

## <a name="installation"></a>Instalación  

**Para instalar el paquete de contenido de Power BI:**

1. Inicie sesión en [Power BI](https://app.powerbi.com/groups/me/getdata/services) con su cuenta de Power BI (es la misma que la de Office 365 o Azure AD).

2. En la parte inferior del panel de navegación izquierdo, seleccione **Obtener datos**.

    ![Paquete de contenido de Power BI de Azure Active Directory](./media/active-directory-reporting-power-bi-content-pack-how-to/01.png)
 
3. En el cuadro **Servicios**, haga clic en **Obtener**.
   
    ![Paquete de contenido de Power BI de Azure Active Directory](./media/active-directory-reporting-power-bi-content-pack-how-to/02.png)

4.  Busque **Azure Active Directory**.

    ![Paquete de contenido de Power BI de Azure Active Directory](./media/active-directory-reporting-power-bi-content-pack-how-to/03.png)
 
5.  Cuando se le solicite, escriba su identificador de inquilino de Azure AD y haga clic en **Siguiente**.

    > [!TIP] 
    > Una forma rápida de obtener el identificador del inquilino de Office 365 o Azure AD es iniciar sesión en el portal de Azure AD, profundizar en el directorio y copiar el identificador de la siguiente dirección URL: https://manage.windowsazure.com/woodgroveonline.com#Workspaces/ActiveDirectoryExtension/Directory/<tenantid>/directoryQuickStart

    ![Paquete de contenido de Power BI de Azure Active Directory](./media/active-directory-reporting-power-bi-content-pack-how-to/04.png) 

6.  Haga clic en **Iniciar sesión**. 
 
    ![Paquete de contenido de Power BI de Azure Active Directory](./media/active-directory-reporting-power-bi-content-pack-how-to/05.png) 



7.  Escriba su nombre de usuario y contraseña, y haga clic en **Iniciar sesión**.
 
    ![Paquete de contenido de Power BI de Azure Active Directory](./media/active-directory-reporting-power-bi-content-pack-how-to/06.png) 

8.  En el cuadro de diálogo de consentimiento de la aplicación, haga clic en **Aceptar**.
 
9.  Cuando se haya creado el panel de registros de actividad de Azure Active Directory, haga clic en él.
 
    ![Paquete de contenido de Power BI de Azure Active Directory](./media/active-directory-reporting-power-bi-content-pack-how-to/08.png) 

## <a name="what-can-i-do-with-this-content-pack"></a>¿Qué puedo hacer con este paquete de contenido?

Antes de explicar qué puede hacer con este paquete de contenido, aquí tiene un breve resumen de los distintos informes que incluye el paquete de contenido. Los datos de los informes cubren los **últimos 30 días**.

### <a name="reports-included-in-this-version-of-azure-active-directory-logs-content-pack"></a>Informes incluidos en esta versión del paquete de contenido de registros de Azure Active Directory

**Informe de utilización y tendencias de aplicaciones**: obtenga información sobre las aplicaciones que se usan en su organización y cuáles se usan más y cuándo. Este informe se puede usar para recopilar información sobre cómo se usa una aplicación que acabe de implementar en su organización o para averiguar qué aplicaciones son populares. Al hacerlo, puede mejorar la utilización si ve si no se usa la aplicación.

**Inicios de sesión por ubicación y usuarios**: obtenga información sobre todos los inicios de sesión llevados a cabo con la identidad de Azure y sobre la identidad de los usuarios. Con esto, puede profundizar en los inicios de sesión individuales y responder a preguntas como:

- ¿Desde dónde inició sesión este usuario?
- ¿Qué usuario tiene la mayor cantidad de inicios de sesión y desde dónde inicia sesión? 
- ¿Fue el inicio de sesión correcto?  
 
Para profundizar en los detalles, haga clic en una fecha o ubicación concretas.

**Usuarios únicos por aplicación**: obtenga una vista de todos los usuarios únicos de una aplicación determinada. Esto incluye solo los usuarios que hayan iniciado sesión "*correctamente*" en una aplicación.

**Inicios de sesión de dispositivo**: obtenga una vista del tipo de sistema operativo y los exploradores que los usuarios están utilizando en su organización con información detallada sobre los usuarios, lo que incluye:

- User Name
- Dirección IP
- Ubicación 
- Estado de inicio de sesión 

Con este informe, puede comprender los diversos perfiles de dispositivo usados dentro de su organización y determinar directivas de dispositivo en función de lo que se use.

**Embudo de SSPR**: obtenga una descripción de cómo se llevan a cabo los restablecimientos de contraseña en su organización. Eche un vistazo a cuántos restablecimientos de contraseña se intentaron con la herramienta SSPR y cuántos se realizaron correctamente. Profundice más en los restablecimientos de contraseña con error mediante el embudo de SSPR y comprenda por qué se produjeron determinados errores. Este informe ofrece una visión más detallada de cómo se usa la herramienta SSPR dentro de su organización para que pueda tomar las decisiones correctas.

## <a name="customizing-azure-ad-activity-content-pack"></a>Personalización del paquete de contenido de actividad de Azure AD

**Cambio de la visualización**: para cambiar la visualización de un informe, haga clic en **Editar informe** y seleccione la visualización que desee.
 
![Paquete de contenido de Power BI de Azure Active Directory](./media/active-directory-reporting-power-bi-content-pack-how-to/09.png) 
 
![Paquete de contenido de Power BI de Azure Active Directory](./media/active-directory-reporting-power-bi-content-pack-how-to/10.png) 

**Inclusión de campos adicionales**: puede agregar un campo al informe o quitarlo seleccionando el objeto visual que desea agregar o quitar en el campo. En el ejemplo siguiente, se agrega el campo "Estado de inicio de sesión" a la vista de tabla. 
 
![Paquete de contenido de Power BI de Azure Active Directory](./media/active-directory-reporting-power-bi-content-pack-how-to/11.png) 

**Anclado de visualizaciones al panel**: puede personalizar el panel e incluir sus propias visualizaciones en el informe y anclarlo al panel. En el ejemplo siguiente, se agrega un filtro nuevo denominado "Estado de inicio de sesión" y se incluye en el informe. También se cambia la visualización de gráfico de barras a gráfico de líneas y se puede anclar este nuevo objeto visual al panel.

![Paquete de contenido de Power BI de Azure Active Directory](./media/active-directory-reporting-power-bi-content-pack-how-to/12.png) 

![Paquete de contenido de Power BI de Azure Active Directory](./media/active-directory-reporting-power-bi-content-pack-how-to/13.png) 
 

 


**Compartir su panel**: una vez que haya creado el contenido que desea, puede compartir el panel con los usuarios de su organización. Recuerde que, una vez que comparta el informe, pueden ver los campos que haya seleccionado en el informe.
 
![Paquete de contenido de Power BI de Azure Active Directory](./media/active-directory-reporting-power-bi-content-pack-how-to/14.png) 



## <a name="scheduling-a-daily-refresh-of-your-power-bi-report"></a>Programación de una actualización diaria de su informe de Power BI

Para programar una actualización diaria de su informe de Power BI, vaya a **Conjuntos de datos > Configuración > Programar actualización** y configúrelo como se muestra a continuación.
 
![Paquete de contenido de Power BI de Azure Active Directory](./media/active-directory-reporting-power-bi-content-pack-how-to/15.png) 

## <a name="updating-to-newer-version-of-content-pack"></a>Actualización a una versión más reciente del paquete de contenido

Si desea actualizar el paquete de contenido para obtener una versión más reciente:

- Descargue el nuevo paquete de contenido y configúrelo según las instrucciones de este artículo.

- Una vez configurado, vaya a **Origen de datos > Configuración > Credenciales de origen de datos** y vuelva a escribir sus credenciales, tal y como se muestra a continuación.

    ![Paquete de contenido de Power BI de Azure Active Directory](./media/active-directory-reporting-power-bi-content-pack-how-to/16.png) 

Tan pronto como la nueva versión del paquete de contenido esté funcionando, puede quitar la versión anterior, si es necesario, eliminando los informes subyacentes y los conjuntos de datos asociados con ese paquete de contenido.

## <a name="still-having-issues"></a>¿Sigue teniendo problemas? 

Consulte nuestra [guía de solución de problemas](active-directory-reporting-troubleshoot-content-pack.md). Para obtener ayuda general con Power BI, visite estos [artículos de Ayuda](https://powerbi.microsoft.com/en-us/documentation/powerbi-service-get-started/).
 

## <a name="next-steps"></a>Pasos siguientes

Para obtener información general sobre los informes, consulte [Informes de Azure Active Directory](active-directory-reporting-azure-portal.md).
