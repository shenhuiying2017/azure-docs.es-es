---
title: DNS en Azure Stack | Microsoft Docs
description: DNS en Azure Stack
services: azure-stack
documentationcenter: 
author: ScottNapolitan
manager: byronr
editor: 
ms.assetid: 
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 9/25/2017
ms.author: victorh
ms.openlocfilehash: ac93b5eb4228cef373428b7b69932d5993d54fa0
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="dns-in-azure-stack"></a>DNS en Azure Stack

*Se aplica a: Sistemas integrados de Azure Stack y Azure Stack Development Kit*

Azure Stack incluye las siguientes características de DNS:
* Compatibilidad con la resolución de nombres de host DNS
* Crear y administrar registros y zonas DNS con API

## <a name="support-for-dns-hostname-resolution"></a>Compatibilidad con la resolución de nombres de host DNS
Puede especificar una etiqueta de nombre de dominio DNS para un recurso de IP pública, lo que crea una asignación para *domainnamelabel.location*.cloudapp.azurestack.external en la dirección IP pública de los servidores DNS que administra Azure Stack.  

Por ejemplo, si crea un recurso de IP pública con **contoso** como una etiqueta de nombre de dominio en la ubicación local de Azure Stack, el nombre de dominio completo (FQDN) **contoso.local.cloudapp.azurestack.external** se resolverá en la dirección IP pública del recurso. Puede usar este FQDN para crear un registro CNAME de dominio personalizado que apunte a la dirección IP pública en Azure Stack.

> [!IMPORTANT]
> Cada etiqueta de nombre de dominio que se cree debe ser única dentro de su ubicación de Azure Stack.

Si crea la dirección IP pública mediante el portal, tendrá el siguiente formato:

![Creación de una dirección IP pública](media/azure-stack-whats-new-dns/image01.png)

Esta configuración es útil si quiere asociar una dirección IP pública a un recurso de carga equilibrada. Por ejemplo, podría tener una solicitud de procesamiento de equilibrador de carga desde una aplicación web. Detrás del equilibrador de carga, se encuentra un sitio web ubicado en una o más máquinas virtuales. Ahora puede tener acceso al sitio de web de carga equilibrada mediante un nombre DNS, en lugar de con una dirección IP.

## <a name="create-and-manage-dns-zones-and-records-using-api"></a>Crear y administrar registros y zonas DNS con API
Puede crear y administrar registros y zonas DNS en Azure Stack.  

Azure Stack proporciona un servicio DNS como Azure mediante API que son coherentes con las API de DNS de Azure.  Al hospedar los dominios en DNS de Azure Stack, puede administrar los registros DNS con las mismas credenciales, API, herramientas, facturación y soporte técnico que con los demás servicios de Azure. 

Por razones obvias, la infraestructura de DNS de Azure Stack es más compacta que la de Azure. Por lo tanto, el ámbito, la escala y el rendimiento dependen de la escala de la implementación de Azure Stack y del entorno donde se implementa.  Por lo tanto, aspectos como el rendimiento, la disponibilidad, la distribución global y la alta disponibilidad pueden variar de una implementación a otra.

## <a name="comparison-with-azure-dns"></a>Comparación con el DNS de Azure
El DNS de Azure Stack es similar al DNS de Azure, con dos importantes excepciones:
* **No admite registros AAAA**.

    Azure Stack NO admite registros AAAA porque no es compatible con las direcciones IPv6.  Se trata de una diferencia clave entre el DNS de Azure y de Azure Stack.
* **No es multiinquilino**.

    A diferencia de Azure, el servicio DNS en Azure Stack no es multiinquilino. Por este motivo, cada inquilino no puede crear la misma zona DNS. Solo la primera suscripción que intenta crear la zona lo consigue, mientras que se produce un error en el resto de solicitudes posteriores.  Se trata de un problema conocido, y una diferencia clave entre el DNS de Azure y de Azure Stack. El problema se solucionará en una futura versión.

Además, hay algunas pequeñas diferencias en cómo el DNS de Azure Stack implementa las etiquetas, los metadatos, las etiquetas ETag y los límites.

La siguiente información se aplica específicamente al DNS de Azure Stack y varía ligeramente del DNS de Azure. Para obtener más información acerca del DNS de Azure, consulte [Zonas y registros DNS](../../dns/dns-zones-records.md) en el sitio de documentación de Microsoft Azure.

### <a name="tags-metadata-and-etags"></a>Etiquetas, metadatos y etiquetas de identidad

**Etiquetas**

El DNS de Azure Stack admite el uso de etiquetas de Azure Resource Manager en recursos de zona DNS. No admite etiquetas en conjuntos de registros de DNS, aunque, como alternativa, se admiten "metadatos" en estos tipos de conjuntos, como se explica a continuación.

**Metadata**

Como alternativa a las etiquetas de conjuntos de registros, el DNS de Azure Stack admite la anotación de conjuntos de registros mediante "metadatos". De forma similar a las etiquetas, los metadatos permiten asociar pares nombre-valor a cada conjunto de registros. Esto puede ser útil, por ejemplo, para registrar el propósito de cada conjunto de registros. A diferencia de las etiquetas, los metadatos no se pueden usar para proporcionar una vista filtrada de la factura de Azure ni se pueden especificar en una directiva de Azure Resource Manager.

**Etiquetas de identidad**

Supongamos que dos personas o dos procesos tratan de modificar un registro DNS al mismo tiempo. ¿Cuál gana? ¿Y el ganador sabe que ha sobrescrito cambios creados por otra persona?

El DNS de Azure Stack usa etiquetas de identidad para administrar de forma segura los cambios simultáneos realizados al mismo recurso. Las etiquetas de entidad (ETag) son independientes de las "etiquetas" de Azure Resource Manager. Cada recurso DNS (zona o conjunto de registros) tiene un valor de Etag asociado a él. Siempre que se recupera un recurso, también se recupera su Etag. Al actualizar un recurso, puede elegir entre devolver el valor de ETag para que el DNS de Azure Stack pueda comprobar que dicho valor del servidor coincide. Puesto que cada actualización a un recurso conlleva la regeneración de Etag, una incoherencia de Etag indica que se ha producido un cambio simultáneo. Los valores de ETag también se pueden usar al crear un recurso para asegurarse de que este no existe aún.

De forma predeterminada, PowerShell para DNS de Azure Stack utiliza valores de ETag para bloquear los cambios simultáneos a zonas y conjuntos de registros. El modificador *-Overwrite* se puede usar para suprimir las comprobaciones de ETag, en cuyo caso, se sobrescribirán todos los cambios simultáneos que se hayan producido.

En el nivel de la API de REST de DNS de Azure Stack, los valores de ETag se especifican mediante los encabezados HTTP. Su comportamiento se indica en la siguiente tabla:

| Encabezado | Comportamiento|
|--------|---------|
| Ninguna   | PUT siempre se realiza correctamente (sin comprobaciones de ETag)|
| If-match| PUT solo se realiza correctamente si el recurso existe y ETag coincide|
| If-match *| PUT solo se realiza correctamente si el recurso existe|
| If-none-match *| PUT solo se realiza correctamente si el recurso no existe|

### <a name="limits"></a>límites

Se aplican los límites predeterminados siguientes cuando se usa el DNS de Azure Stack:

| Recurso| Límite predeterminado|
|---------|--------------|
| Zonas por suscripción| 100|
| Conjuntos de registros por zona| 5000|
| Registros por conjunto de registros| 20 ||

## <a name="next-steps"></a>Pasos siguientes
[Presentación de iDNS para Azure Stack](azure-stack-understanding-dns.md)
