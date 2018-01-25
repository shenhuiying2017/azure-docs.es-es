---
ms.assetid: 
title: "Guía de las limitaciones de Azure Key Vault | Microsoft Docs"
ms.service: key-vault
author: lleonard-msft
ms.author: alleonar
manager: mbaldwin
ms.date: 06/21/2017
ms.openlocfilehash: cc39a5ee466d2e1661281a32010c3d3201c4a0af
ms.sourcegitcommit: 2a70752d0987585d480f374c3e2dba0cd5097880
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/19/2018
---
# <a name="azure-key-vault-throttling-guidance"></a>Guía de las limitaciones de Azure Key Vault

La limitación es un proceso que se inicia y que limita el número de llamadas simultáneas al servicio de Azure para evitar el uso excesivo de recursos. Azure Key Vault se ha diseñado para controlar un volumen muy alto de solicitudes. Si tiene lugar un número excesivo de solicitudes, la limitación de las solicitudes del cliente le ayuda a mantener un rendimiento óptimo y la confiabilidad del servicio Azure Key Vault.

Las limitaciones varían según el escenario. Por ejemplo, si está realizando un gran volumen de escrituras, la posibilidad de limitación es mayor que si solo realiza lecturas.

## <a name="how-does-key-vault-handle-its-limits"></a>¿Cómo maneja Key Vaults sus límites?

Los límites de servicio en Key Vault sirven para evitar el uso incorrecto de los recursos y garantizar la calidad de servicio para todos los clientes de Key Vault. Cuando se supera un umbral de servicio, Key Vault limita las solicitudes sucesivas de ese cliente durante un período de tiempo. Cuando esto sucede, Key Vault devuelve el código de estado HTTP 429 (demasiadas solicitudes) y se producirá un error en las solicitudes. Además, las solicitudes con error que devuelven un código 429 cuentan para la limitación cuyo seguimiento realiza Key Vault. 

Si tiene un caso empresarial válido para limitaciones superiores, póngase en contacto con nosotros.


## <a name="how-to-throttle-your-app-in-response-to-service-limits"></a>Limitación de la aplicación en respuesta a los límites de servicio

A continuación se muestran los **procedimientos recomendados** para la limitación de la aplicación:
- Reduzca el número de operaciones por solicitud.
- Reduzca la frecuencia de las solicitudes.
- Evite los reintentos inmediatos. 
    - Todas las solicitudes se acumulan en los límites de utilización.

Al implementar el control de errores de la aplicación, utilice el código de error HTTP 429 para detectar la necesidad de limitación del cliente. Si la solicitud vuelve a enviar un código de error HTTP 429, todavía se está produciendo un límite de servicio Azure. Continúe usando el método de limitación del cliente recomendado y vuelva a intentar la solicitud hasta que lo consiga.

### <a name="recommended-client-side-throttling-method"></a>Método de limitación del cliente recomendado

En el código de error HTTP 429, comience la limitación del cliente mediante un enfoque de retroceso exponencial:

1. Espere 1 segundo, reintente la solicitud
2. Si todavía está limitado, espere 2 segundos, reintente la solicitud
3. Si todavía está limitado, espere 4 segundos, reintente la solicitud
4. Si todavía está limitado, espere 8 segundos, reintente la solicitud
5. Si todavía está limitado, espere 16 segundos, reintente la solicitud

En este momento, se deben no está recibiendo los códigos de respuesta HTTP 429.

## <a name="see-also"></a>Otras referencias

Para obtener una orientación más profunda de la limitación en Microsoft Cloud, consulte [Patrón de limitación](https://docs.microsoft.com/azure/architecture/patterns/throttling).

