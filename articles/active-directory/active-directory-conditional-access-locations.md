---
title: "Condiciones de ubicación del acceso condicional de Azure Active Directory | Microsoft Docs"
description: "Obtenga información sobre el uso de la condición de ubicación para controlar el acceso a las aplicaciones en la nube en función de la ubicación de la red del usuario."
services: active-directory
keywords: acceso condicional a aplicaciones, acceso condicional con Azure AD, acceso seguro a recursos de empresa, directivas de acceso condicional
documentationcenter: 
author: MarkusVi
manager: mtillman
editor: 
ms.assetid: 8c1d978f-e80b-420e-853a-8bbddc4bcdad
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 01/11/2018
ms.author: markvi
ms.reviewer: nigu
ms.openlocfilehash: 47b4d70c991bd618ea4ea6e5d2fd1dea86911798
ms.sourcegitcommit: 828cd4b47fbd7d7d620fbb93a592559256f9d234
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/18/2018
---
# <a name="location-conditions-in-azure-active-directory-conditional-access"></a>Condiciones de ubicación del acceso condicional de Azure Active Directory 

Con el [acceso condicional de Azure Active Directory (Azure AD)](active-directory-conditional-access-azure-portal.md), puede controlar el modo en que los usuarios autorizados pueden acceder a las aplicaciones en la nube. La condición de ubicación de una directiva de acceso condicional le permite asociar la configuración de los controles de acceso a las ubicaciones de red de los usuarios.

En este artículo se proporciona la información necesaria para configurar la condición de ubicación. 

## <a name="locations"></a>Ubicaciones

Azure AD habilita el inicio de sesión único en dispositivos, aplicaciones y servicios desde cualquier ubicación de la red pública de Internet. Gracias a la condición de ubicación, podrá controlar el acceso a las aplicaciones en la nube en función de la ubicación de la red de un usuario. Estos son los casos de uso más comunes de la condición de ubicación:

- Requerir la autenticación multifactor a los usuarios que accedan a un servicio desde fuera de la red corporativa  

- Bloquear el acceso a los usuarios que accedan a un servicio desde determinados países o regiones. 

Una ubicación es una etiqueta para una ubicación de red que representa una ubicación con nombre o IP de autenticación multifactor de confianza.


## <a name="named-locations"></a>Ubicaciones con nombre 

Gracias a las ubicaciones con nombre, puede crear agrupaciones lógicas de intervalos de direcciones IP, países y regiones. 

 Una ubicación con nombre tiene los siguientes componentes:

![Ubicaciones](./media/active-directory-conditional-access-locations/42.png)

- **Nombre**: el nombre para mostrar de una ubicación con nombre.

- **Intervalos de IP**: uno o varios intervalos de direcciones IP en formato CIDR.

- **Marcar como ubicación de confianza**: marca que puede establecer a una ubicación con nombre para indicar una ubicación de confianza. Las ubicaciones de confianza son áreas de red que controla el departamento de TI. Además del acceso condicional, Azure Identity Protection y los informes de seguridad de Azure AD también usan las ubicaciones con nombre de confianza para reducir los [falsos positivos](active-directory-reporting-risk-events.md#impossible-travel-to-atypical-locations-1).

- **País o regiones**: esta opción le permite seleccionar uno o más países o regiones para definir una ubicación con nombre. 

- **Incluir áreas desconocidas**: algunas direcciones IP no están asignadas a ningún país determinado. Esta opción le permite elegir si estas direcciones IP deberían estar incluidas en la ubicación con nombre. Podrían estar activas si debe aplicarse la directiva con la ubicación con nombre a las ubicaciones desconocidas.

El número de ubicaciones con nombre que se pueden configurar está restringido por el tamaño del objeto relacionado en Azure AD. Puede configurar:

- Una ubicación con nombre de hasta 1200 intervalos IP.

- Un máximo de 90 ubicaciones con nombre con un intervalo IP asignado a cada una.




## <a name="trusted-ips"></a>IP de confianza

También puede configurar los intervalos de direcciones IP que representen a la intranet local de su organización en la [configuración del servicio de la autenticación multifactor](https://account.activedirectory.windowsazure.com/usermanagement/mfasettings.aspx). Esta función le permite configurar hasta 50 intervalos de direcciones IP. Los intervalos de direcciones IP están en formato CIDR. Para más información, vea [IP de confianza](../multi-factor-authentication/multi-factor-authentication-whats-next.md#trusted-ips).  

Si ha configurado IP de confianza, se mostrarán como **IP de confianza de MFA** en la lista de ubicaciones de la condición de ubicación.   

### <a name="skipping-multi-factor-authentication"></a>Omisión de la autenticación multifactor

En la página de configuración del servicio de la autenticación multifactor, podrá identificar a los usuarios de la intranet corporativa seleccionando **Omitir la autenticación multifactor para solicitudes de usuarios federados en mi intranet**. Esta configuración indica que la notificación interna de la red corporativa, la cual emiten los Servicios de federación de Active Directory (AD FS), es de confianza y se utiliza para identificar al usuario como si estuviera en la red corporativa. Para obtener más información, consulte [Enable the Trusted IPs feature by using conditional access](../multi-factor-authentication/multi-factor-authentication-whats-next.md#enable-the-trusted-ips-feature-by-using-conditional-access) (Habilitar la función de direcciones IP de confianza utilizando el acceso condicional).

Tras activar esta opción, incluida la ubicación con nombre, las **IP de confianza de MFA** se aplicarán a todas las directivas que tengan esta casilla activada.

Para las aplicaciones móviles y de escritorio que hayan tenido sesiones de larga duración, el acceso condicional se vuelve a evaluar periódicamente. El valor predeterminado es de una hora. Cuando la notificación dentro de la red corporativa solo se emite en el momento de la autenticación inicial, puede que Azure AD no tenga ninguna lista de intervalos de IP de confianza. En este caso, es más difícil determinar si el usuario sigue conectado a la red corporativa:

1. Compruebe si la dirección IP del usuario está en uno de los intervalos IP de confianza.

2. Compruebe si los tres primeros octetos de la dirección IP del usuario coinciden con los 3 primeros octetos de la dirección IP de la autenticación inicial. La dirección IP se compara con la autenticación inicial, ya que fue entonces cuando se emitió la notificación dentro de la red corporativa y se validó la ubicación del usuario.

Si se produce un error en los dos pasos, se considera que el usuario ya no está conectado a ninguna dirección IP de confianza.



## <a name="location-condition-configuration"></a>Configuración de la condición de ubicación

Al configurar la condición de ubicación, puede distinguir entre:

- Cualquier ubicación 
- Todas las ubicaciones de confianza
- Ubicaciones seleccionadas

![Ubicaciones](./media/active-directory-conditional-access-locations/01.png)

### <a name="any-location"></a>Cualquier ubicación

De manera predeterminada, al seleccionar **Cualquier ubicación**, se aplicará una directiva a todas las direcciones IP, lo que incluye cualquier dirección de Internet. Esta configuración no está limitada a las direcciones IP que haya configurado como ubicación con nombre. Al seleccionar **Cualquier ubicación**, todavía puede excluir determinadas ubicaciones de una directiva. Por ejemplo, puede aplicar una directiva a todas las ubicaciones (excepto en aquellas que sean de confianza) para establecer el ámbito en todas, a excepción de la red corporativa.

### <a name="all-trusted-locations"></a>Todas las ubicaciones de confianza

Esta opción se aplica a:

- Todas las ubicaciones que se hayan marcado como ubicación de confianza
- **IP de confianza de MFA** (si están configuradas)


### <a name="selected-locations"></a>Ubicaciones seleccionadas

Con esta opción, puede seleccionar una o varias ubicaciones con nombre. Para una directiva a la que se aplicará esta configuración, el usuario debe conectarse desde cualquiera de las ubicaciones seleccionadas. Al hacer clic en **Seleccionar**, se abrirá el control de selección de red con nombre que muestra la lista de redes con nombre. En la lista también se muestra si la ubicación de red se ha marcado como de confianza. La ubicación con nombre llamada **IP de confianza de MFA** se utiliza para incluir los valores de IP que pueden configurarse en la página de configuración del servicio de la autenticación multifactor.

## <a name="what-you-should-know"></a>Qué debería saber

### <a name="when-is-a-location-evaluated"></a>¿Cuando se evalúa una ubicación?

Las directivas de acceso condicional se evalúan cuando: 

- Un usuario inicia sesión por primera vez 

- Azure AD emite un token para la aplicación en la nube en la que se ha establecido la directiva de acceso condicional. 

De manera predeterminada, Azure AD emite un token cada hora. Tras salir de la red corporativa, la directiva se aplica a las aplicaciones que usan la autenticación moderna en un plazo de una hora.


### <a name="user-ip-address"></a>Dirección IP del usuario

La dirección IP que se utiliza en la evaluación de directivas es la dirección IP pública del usuario. Para los dispositivos conectados a una red privada, esta no es la dirección IP de cliente del dispositivo del usuario conectado a la intranet, sino la dirección que utiliza la red para conectarse a la red pública de Internet. 

### <a name="bulk-uploading-and-downloading-of-named-locations"></a>Carga y descarga masiva de ubicaciones con nombre

Al crear o actualizar ubicaciones con nombre, en el caso de las actualizaciones masivas, puede cargar o descargar un archivo CSV con los intervalos IP. Una carga reemplaza los intervalos IP que aparecen en la lista por los del archivo. Cada fila del archivo contiene un intervalo de direcciones IP en formato CIDR. 


### <a name="cloud-proxies-and-vpns"></a>Servidores proxy en la nube y soluciones VPN 

Cuando utiliza un proxy hospedado en la nube o una solución VPN, la dirección IP que utiliza Azure AD al evaluar una directiva es la dirección IP del proxy. No se utiliza el encabezado X-Forwarded-For (XFF) que contiene la dirección IP pública del usuario, ya que no hay ninguna validación de que provenga de un origen de confianza y podría ser un método de falsificación de la dirección IP. 

Cuando se implementa un proxy de la nube, puede utilizarse una directiva que se utiliza para requerir un dispositivo unido a un dominio, o la notificación interna de corpnet desde AD FS.



### <a name="api-support-and-powershell"></a>Compatibilidad con la API y PowerShell 

La API y PowerShell no son compatibles todavía con las ubicaciones con nombre o las directivas de acceso condicional.





## <a name="next-steps"></a>pasos siguientes

- Si quiere saber cómo configurar una directiva de acceso condicional, consulte [Get started with conditional access in Azure Active Directory](active-directory-conditional-access-azure-portal-get-started.md) (Introducción al acceso condicional en Azure Active Directory).

- Si está listo para configurar directivas de acceso condicional para su entorno, consulte [Procedimientos recomendados para el acceso condicional en Azure Active Directory](active-directory-conditional-access-best-practices.md). 
