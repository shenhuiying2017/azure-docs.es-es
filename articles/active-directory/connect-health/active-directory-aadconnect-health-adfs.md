---
title: Uso de Azure AD Connect Health con AD FS | Microsoft Docs
description: "Esta es la página de Azure AD Connect Health sobre cómo supervisar la infraestructura de AD FS local."
services: active-directory
documentationcenter: 
author: karavar
manager: mtillman
editor: curtand
ms.assetid: dc0e53d8-403e-462a-9543-164eaa7dd8b3
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 07/18/2017
ms.author: billmath
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 834dbbd0be30181de1a71df05d2867be0e1c59b4
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/11/2017
---
# <a name="monitor-ad-fs-using-azure-ad-connect-health"></a>Supervisión de AD FS mediante Azure AD Connect Health
La siguiente documentación es específica de la supervisión de la infraestructura de AD FS con Azure AD Connect Health. Para más información sobre la supervisión de Azure AD Connect (Sync) con Azure AD Connect Health, consulte [Uso de Azure AD Connect Health para sincronización](active-directory-aadconnect-health-sync.md). Para obtener información adicional sobre la supervisión de Active Directory Domain Services con Azure AD Connect Health, consulte [Using Azure AD Connect Health with AD DS](active-directory-aadconnect-health-adds.md)(Uso de Azure AD Connect Health con AD DS).

## <a name="alerts-for-ad-fs"></a>Alertas de AD FS
La sección Alertas de Azure AD Connect Health proporciona la lista de alertas activas. Cada alerta incluye información pertinente, pasos de resolución y vínculos a documentación relacionada.

Puede hacer doble clic en una alerta activa o una alerta resuelta para que se abra una hoja nueva con información adicional, pasos que puede seguir para resolver la alerta y vínculos a documentación relevante. También puede ver datos históricos sobre las alertas resueltas en el pasado.

![portal de Azure AD Connect Health](./media/active-directory-aadconnect-health/alert2.png)

## <a name="usage-analytics-for-ad-fs"></a>Análisis de uso de AD FS
Análisis de uso de Azure AD Connect Health analiza el tráfico de autenticación de los servidores de federación. Puede hacer doble clic en la casilla de análisis de uso para que se abra la hoja de análisis de uso, que le mostrará las métricas y las agrupaciones.

> [!NOTE]
> Para poder utilizar el análisis de uso con AD FS, debe asegurarse de que esté habilitada la auditoría de AD FS. Para obtener más información, consulte [Habilitación de la auditoría para AD FS](active-directory-aadconnect-health-agent-install.md#enable-auditing-for-ad-fs).
>
>

![portal de Azure AD Connect Health](./media/active-directory-aadconnect-health/report1.png)

Para seleccionar otras métricas, especifique un intervalo de tiempo. Para cambiar la agrupación, haga clic con el botón derecho en el gráfico de análisis de uso y seleccione Editar gráfico. A continuación, puede especificar el intervalo de tiempo, seleccionar una métrica diferente y cambiar la agrupación. Puede ver la distribución del tráfico de autenticación según diferentes "métricas" y agrupar cada métrica con los correspondientes parámetros "Agrupar por" que se describen en la sección siguiente:

**Métrica: número total de solicitudes**: el número total de solicitudes procesadas por los servidores de AD FS.

|Agrupar por | ¿Qué significa la agrupación y por qué es útil? |
| --- | --- |
| Todo | Muestra el recuento del número total de solicitudes procesadas por todos los servidores de AD FS.|
| Application | Agrupa el número total de solicitudes en función del usuario de confianza de destino. Esta agrupación es útil para comprender qué aplicación está recibiendo tráfico y qué porcentaje del tráfico total recibe. |
|  Server |Agrupa el número total de solicitudes según el servidor que procesó la solicitud. Esta agrupación es útil para comprender la distribución de la carga de tráfico total.
| Unión al área de trabajo |Agrupa el número total de solicitudes en función de si las solicitudes proceden o no de dispositivos que están unidos al área de trabajo (conocidos). Esta agrupación es útil para comprender si el acceso a sus recursos se realiza con dispositivos que son desconocidos para la infraestructura de identidades. |
|  Método de autenticación | Agrupa el número total de solicitudes en función del método de autenticación utilizado para la autenticación. Esta agrupación es útil para comprender el método de autenticación común que se utiliza para la autenticación. A continuación, se indican los métodos de autenticación posibles  <ol> <li>Autenticación integrada de Windows (Windows)</li> <li>Autenticación basada en formularios (formularios)</li> <li>SSO (inicio de sesión único)</li> <li>Autenticación de certificados X509 (certificado)</li> <br>Si los servidores de federación reciben la solicitud con una cookie de SSO, esa solicitud se considera como SSO (inicio de sesión único). En estos casos, si la cookie es válida, no se pide al usuario que proporcione credenciales y obtiene acceso a la aplicación sin problemas. Este comportamiento es habitual si tiene varios usuarios de confianza protegidos por los servidores de federación. |
| Ubicación de red | Agrupa el número total de solicitudes en función de la ubicación de red del usuario. Puede ser intranet o extranet. Esta agrupación es útil para saber qué porcentaje del tráfico es de intranet y cuál de extranet. |


**Métrica: número total de solicitudes con error**: número total de solicitudes procesadas por el servicio de federación. (Esta métrica solo está disponible en AD FS para Windows Server 2012 R2)

|Agrupar por | ¿Qué significa la agrupación y por qué es útil? |
| --- | --- |
| Tipo de error | Muestra el número de errores en función de los tipos de error predefinidos. La agrupación es útil para comprender cuáles son los tipos de errores comunes. <ul><li>Nombre de usuario o contraseña incorrectos: errores por nombre de usuario o contraseña incorrectos.</li> <li>"Bloqueo de extranet": errores producidos cuando se reciben solicitudes de un usuario cuyo acceso a la extranet está bloqueado. </li><li> "Contraseña caducada": errores producidos cuando un usuario inicia sesión con una contraseña caducada.</li><li>"Cuenta deshabilitada": errores producidos cuando un usuario inicia sesión con una cuenta deshabilitada.</li><li>"Autenticación de dispositivos": errores producidos cuando un usuario no se puede autenticar mediante la Autenticación de dispositivos.</li><li>"Autenticación de certificado de usuario": errores producidos cuando un usuario no se puede autenticar porque el certificado no es válido.</li><li>"MFA": errores producidos cuando un usuario no se puede autenticar mediante Multi-Factor Authentication.</li><li>"Otra credencial": "Autorización de emisión": problemas producidos por errores de autorización.</li><li>"Delegación de emisión": problemas producidos por errores de delegación de emisión.</li><li>"Aceptación de tokens": errores producidos cuando ADFS rechaza el token de un proveedor de identidades de terceros.</li><li>"Protocolo": error producido por errores de protocolo.</li><li>"Desconocido": detectar todas. Otros errores que no encajan en las categorías definidas.</li> |
| Server | Agrupa los errores en función del servidor. Esta agrupación es útil para comprender la distribución de errores entre servidores. Una distribución desigual podría indicar que un servidor presenta un estado defectuoso. |
| Ubicación de red | Agrupa los errores en función de la ubicación de red de las solicitudes (intranet frente a extranet). Esta agrupación es útil para comprender qué tipo de solicitud está fallando. |
|  Application | Agrupa los errores en función de la aplicación de destino (usuario de confianza). Esta agrupación es útil para comprender qué aplicación de destino está experimentando una mayor cantidad de errores. |

**Métrica: recuento de usuarios**: el número medio de usuarios únicos que se autentican activamente mediante AD FS.

|Agrupar por | ¿Qué significa la agrupación y por qué es útil? |
| --- | --- |
|Todo |Esta métrica proporciona un recuento del número medio de usuarios mediante el servicio de federación en el intervalo de tiempo seleccionado. Los usuarios no están agrupados. <br>El promedio depende del intervalo de tiempo seleccionado. |
| Application |Agrupa el número medio de usuarios en función de la aplicación de destino (usuario de confianza). Esta agrupación es útil para comprender cuántos usuarios utilizan una aplicación y determinar qué aplicación. |

## <a name="performance-monitoring-for-ad-fs"></a>Supervisión del rendimiento de AD FS
Supervisión de rendimiento de Azure AD Connect Health proporciona información de supervisión sobre métricas. Si selecciona la casilla Supervisión, se abrirá una hoja nueva con información detallada sobre las métricas.

![portal de Azure AD Connect Health](./media/active-directory-aadconnect-health/perf1.png)

Al seleccionar la opción Filtro en la parte superior de la hoja, puede filtrar por servidor para ver las métricas de un servidor individual. Para cambiar las métricas, haga clic con el botón derecho en el diagrama de supervisión en la hoja de supervisión y seleccione Editar gráfico (o seleccione el botón del mismo nombre). En la nueva hoja que se abre, puede seleccionar métricas adicionales en la lista desplegable y especificar un intervalo de tiempo para la visualización de los datos de rendimiento.

## <a name="reports-for-ad-fs"></a>Informes de AD FS
Azure AD Connect Health proporciona informes sobre la actividad y el rendimiento de AD FS. Estos informes ayudan a los administradores a comprender mejor las actividades en sus servidores de AD FS.

### <a name="top-50-users-with-failed-usernamepassword-logins"></a>Primeros 50 usuarios con errores de inicio de sesión por nombre de usuario y contraseña no válidos
Una de las causas comunes de los errores de solicitud de autenticación en un servidor de AD FS es una solicitud con credenciales no válidas, es decir, un nombre de usuario o una contraseña incorrectos. Esto le suele pasar a los usuarios debido a contraseñas olvidadas o complejas, o a errores tipográficos.

Pero hay otros motivos que pueden provocar que los servidores de AD FS tengan que controlar un número inesperado de solicitudes como, por ejemplo: una aplicación que almacena en la memoria caché las credenciales de un usuario y estas expiran o un usuario malintencionado que intenta iniciar sesión en una cuenta con una serie de contraseñas conocidas. Estos dos ejemplos son motivos válidos que podrían dar lugar a un aumento repentino de las solicitudes.

Azure AD Connect Health para ADFS proporciona un informe con los primeros 50 usuarios con errores de intento de inicio de sesión por nombre de usuario o una contraseña no válidos. Este informe se ha logrado procesando los eventos de auditoría generados por todos los servidores de AD FS de las granjas.

![portal de Azure AD Connect Health](./media/active-directory-aadconnect-health-adfs/report1a.png)

En este informe, tiene un acceso sencillo a los elementos de información siguientes:

* Nº total de solicitudes con error por nombre de usuario o contraseña incorrectos en los últimos 30 días
* Número promedio de usuarios con error de inicio de sesión por nombre de usuario o contraseña incorrectos por día.

Al hacer clic en esta parte, se abre la hoja del informe principal que proporciona información adicional. Esta hoja incluye un gráfico con información de tendencias para ayudar a establecer una línea base sobre las solicitudes con contraseña o nombre de usuario incorrecto. Además, proporciona la lista de los 50 usuarios principales junto con el número de intentos fallidos.

El gráfico ofrece la siguiente información:

* El número total diario de inicios de sesión erróneos debido a un nombre de usuario o una contraseña incorrectos.
* El número total diario de usuarios únicos con errores de inicio de sesión.
* Dirección IP del cliente de la última solicitud

![portal de Azure AD Connect Health](./media/active-directory-aadconnect-health-adfs/report3a.png)

El informe ofrece la siguiente información:

| Elemento de informe | Description |
| --- | --- |
| Id. de usuario |Muestra el identificador de usuario que se usó. Este valor es lo que el usuario escribió, que en algunos casos es el identificador de usuario equivocado que se va a utilizar. |
| Intentos con error |Muestra el número total de intentos con error para ese identificador de usuario específico. La tabla está ordenada por el mayor número de intentos con error en orden descendente. |
| Último error |Muestra la marca de tiempo del momento en que se produjo el último error. |
| Última IP con error |Muestra la dirección IP del cliente de la última solicitud incorrecta. |

> [!NOTE]
> Este informe se actualiza automáticamente cada dos horas con la información recopilada durante este período. Como resultado, puede que el informe no incluya los intentos de inicio de sesión de las últimas dos horas.
>
>

## <a name="related-links"></a>Vínculos relacionados
* [Azure AD Connect Health](active-directory-aadconnect-health.md)
* [Instalación del agente de Azure AD Connect Health](active-directory-aadconnect-health-agent-install.md)
* [Operaciones de Azure AD Connect Health](active-directory-aadconnect-health-operations.md)
* [Uso de Azure AD Connect Health para sincronización](active-directory-aadconnect-health-sync.md)
* [Uso de Azure AD Connect Health con AD DS](active-directory-aadconnect-health-adds.md)
* [Preguntas más frecuentes de Azure AD Connect Health](active-directory-aadconnect-health-faq.md)
* [Historial de versiones de Azure AD Connect Health](active-directory-aadconnect-health-version-history.md)