---
title: "Solución de problemas de estado degradado en Administrador de tráfico de Azure"
description: "Cómo solucionar problemas de perfiles de Administrador de tráfico cuando se muestra como muestra un estado degradado."
services: traffic-manager
documentationcenter: 
author: kumudd
manager: timlt
ms.assetid: 8af0433d-e61b-4761-adcc-7bc9b8142fc6
ms.service: traffic-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/03/2017
ms.author: kumud
ms.openlocfilehash: b1d00fb84695d2289f37647f55a7c56cf28c8c96
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="troubleshooting-degraded-state-on-azure-traffic-manager"></a>Solución de problemas de estado degradado en el Administrador de tráfico de Azure

En este artículo se describe cómo solucionar problemas de un perfil de Azure Traffic Manager que muestra un estado degradado. Para este escenario, considere que ha configurado un perfil de Traffic Manager orientado a algunos de sus servicios hospedados cloudapp.net. Si el estado de su instancia de Traffic Manager muestra **Degradado**, entonces el estado de uno o varios puntos de conexión puede ser **Degradado**:

![estado de punto de conexión degradado](./media/traffic-manager-troubleshooting-degraded/traffic-manager-degradedifonedegraded.png)

Si el estado de su instancia de Traffic Manager muestra **Inactivo**, ambos puntos de conexión pueden estar **deshabilitados**:

![Estado inactivo de Traffic Manager](./media/traffic-manager-troubleshooting-degraded/traffic-manager-inactive.png)

## <a name="understanding-traffic-manager-probes"></a>Descripción de los sondeos de Traffic Manager

* Traffic Manager considera que un punto de conexión está EN LÍNEA solo cuando el sondeo recibe una respuesta HTTP 200 desde la ruta de acceso del sondeo. Cualquier otra respuesta que no sea la respuesta 200 es un error.
* Un redireccionamiento 30x producirá un error incluso si la dirección URL redirigida devuelve una respuesta 200.
* En los sondeos HTTPs, se omiten los errores de certificado.
* No importa el contenido real de la ruta de acceso del sondeo, siempre y cuando se devuelva una respuesta 200. Realizar un sondeo de una dirección URL en algún contenido estático, como "/favicon.ico" es una técnica habitual. Los contenidos dinámicos, como las páginas ASP, puede que no siempre devuelvan una respuesta 200, incluso si la aplicación está en buen estado.
* Un procedimiento recomendado consiste en establecer la ruta de acceso del sondeo en algo que tenga una lógica suficiente para determinar si el sitio está activo o inactivo. En el ejemplo anterior, al establecer la ruta de acceso en "/favicon.ico", solo prueba que w3wp.exe está respondiendo. Puede que este sondeo no indique que la aplicación web está funcionando correctamente. Sería una mejor opción establecer una ruta de acceso a algo como "/Probe.aspx" que tiene lógica para determinar el estado del sitio. Por ejemplo, podría usar contadores de rendimiento para medir el uso de la CPU o el número de solicitudes con error. O bien, podría intentar acceder a los recursos de la base de datos o al estado de la sesión para asegurarse de que la aplicación web funciona.
* Si se degradan todos los puntos de conexión de un perfil, Traffic Manager los tratará a todos como correctos y enrutará el tráfico a todos ellos. Este comportamiento garantiza que los problemas con el mecanismo de sondeo no darán lugar a una interrupción completa del servicio.

## <a name="troubleshooting"></a>Solución de problemas

Para solucionar un error de sondeo, necesita una herramienta que muestre el código de estado HTTP devuelto desde la dirección URL de sondeo. Hay muchas herramientas disponibles que le muestran la respuesta HTTP sin formato.

* [Fiddler](http://www.telerik.com/fiddler)
* [curl](https://curl.haxx.se/)
* [wget](http://gnuwin32.sourceforge.net/packages/wget.htm)

Además, puede usar la pestaña Red de las herramientas de depuración de F12 de Internet Explorer para ver las respuestas HTTP.

En este ejemplo queremos ver la respuesta de la dirección URL de sondeo: http://watestsdp2008r2.cloudapp.net:80/Probe. El siguiente ejemplo de PowerShell ilustra el problema.

```powershell
Invoke-WebRequest 'http://watestsdp2008r2.cloudapp.net/Probe' -MaximumRedirection 0 -ErrorAction SilentlyContinue | Select-Object StatusCode,StatusDescription
```

Salida de ejemplo:

    StatusCode StatusDescription
    ---------- -----------------
           301 Moved Permanently

Observe que hemos recibido una respuesta de redirección. Como se indicó anteriormente, cualquier StatusCode distinto de 200 se considera un error. Traffic Manager cambia el estado de punto de conexión a Sin conexión. Para resolver el problema, compruebe la configuración del sitio web para asegurarse de que se puede devolver el StatusCode apropiado desde la ruta de acceso del sondeo. Vuelva a configurar el sondeo de Traffic Manager para que señale a una ruta de acceso que devuelva una respuesta 200.

Si el sondeo usa el protocolo HTTPS, es posible que tenga que deshabilitar la comprobación de certificados para evitar errores SSL/TLS durante la prueba. Las siguientes instrucciones de PowerShell deshabilitan la validación de certificados para la sesión actual de PowerShell:

```powershell
add-type @"
using System.Net;
using System.Security.Cryptography.X509Certificates;
public class TrustAllCertsPolicy : ICertificatePolicy {
    public bool CheckValidationResult(
    ServicePoint srvPoint, X509Certificate certificate,
    WebRequest request, int certificateProblem) {
    return true;
    }
}
"@
[System.Net.ServicePointManager]::CertificatePolicy = New-Object TrustAllCertsPolicy
```

## <a name="next-steps"></a>Pasos siguientes

[Información acerca de los métodos de enrutamiento del tráfico del Administrador de tráfico](traffic-manager-routing-methods.md)

[¿Qué es el Administrador de tráfico?](traffic-manager-overview.md)

[Servicios en la nube](http://go.microsoft.com/fwlink/?LinkId=314074)

[Azure Web Apps](https://azure.microsoft.com/documentation/services/app-service/web/)

[Operaciones del Administrador de tráfico (referencia de la API de REST)](http://go.microsoft.com/fwlink/?LinkId=313584)

[Cmdlets de Azure Traffic Manager][1]

[1]: https://msdn.microsoft.com/library/mt125941(v=azure.200).aspx
