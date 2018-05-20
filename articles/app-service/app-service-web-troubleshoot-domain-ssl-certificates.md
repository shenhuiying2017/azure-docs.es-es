---
title: Solucionar problemas de dominio y certificado SSL en aplicaciones web de Azure | Microsoft Docs
description: Solucionar problemas de dominio y certificado SSL en aplicaciones web de Azure
services: app-service\web
documentationcenter: ''
author: genlin
manager: cshepard
editor: ''
tags: top-support-issue
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/18/2018
ms.author: genli
ms.openlocfilehash: ba21475b771f1688c0a3f2f34c8d961fba5cd182
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 04/28/2018
---
# <a name="troubleshoot-domain-and-ssl-certificate-problems-in-azure-web-apps"></a>Solucionar problemas de dominio y certificado SSL en aplicaciones web de Azure

En este artículo se indican los problemas que pueden surgir al configurar el dominio o el certificado SSL para las aplicaciones web de Azure. También se describen las posibles causas de estos problemas y sus resoluciones.

Si necesita más ayuda en cualquier momento con este artículo, puede ponerse en contacto con los expertos de Azure en [los foros de MSDN Azure y de desbordamiento de pila](https://azure.microsoft.com/support/forums/). Como alternativa, también puede registrar un incidente de soporte técnico de Azure. Vaya al [sitio de soporte técnico de Azure](https://azure.microsoft.com/support/options/) y haga clic en **Obtener soporte técnico**.

## <a name="certificate-problems"></a>Problemas con el certificado

### <a name="unable-to-add-bind-ssl-certificate-to-a-web-app"></a>No se puede agregar un certificado SSL de enlace a una aplicación web 

### <a name="symptom"></a>Síntoma

Cuando agrega un enlace SSL, recibe el mensaje de error siguiente:

**Error al agregar enlace SSL. No se puede configurar el certificado para la IP virtual existente porque otra IP virtual ya usa el certificado.**

### <a name="cause"></a>Causa

Este problema puede producirse si tiene varios enlaces SSL basados en IP para la misma dirección IP en varias aplicaciones web. Por ejemplo, la aplicación web A tiene un enlace SSL basado en IP con un certificado antiguo. La aplicación web B tiene un enlace SSL basado en IP con un certificado nuevo para la misma dirección IP. Cuando se actualice el enlace SSL de la aplicación web con el nuevo certificado, se producirá este error porque se usa la misma dirección IP para otra aplicación. 

### <a name="solution"></a>Solución 

Para corregirlo, use uno de los siguientes métodos:

- Elimine el enlace SSL basado en IP de la aplicación web que utiliza el certificado antiguo. 
- Cree un nuevo enlace SSL basado en IP que use el nuevo certificado.

### <a name="unable-to-delete-a-certificate"></a>No se puede eliminar un certificado 

### <a name="symptom"></a>Síntoma

Al intentar eliminar un certificado, recibirá el mensaje de error siguiente:

**No se puede eliminar el certificado porque se está utilizando actualmente en un enlace SSL. Es necesario quitar el enlace SSL para eliminar el certificado.**

### <a name="cause"></a>Causa

Este problema puede producirse si otra aplicación web usa el certificado.

### <a name="solution"></a>Solución

Quite el enlace SSL para el certificado de las aplicaciones web. A continuación, intente eliminar el certificado. Si todavía no puede eliminarlo, borre la caché del explorador de Internet y vuelva a abrir Azure Portal en una nueva ventana del explorador. Luego intente eliminar el certificado.

### <a name="unable-to-purchase-an-app-service-certificate"></a>No se puede adquirir un certificado de App Service 

### <a name="symptom"></a>Síntoma
No puede adquirir un [certificado de App Service](./web-sites-purchase-ssl-web-site.md) desde Azure Portal.

### <a name="cause-and-solution"></a>Causa y solución
Este problema puede producirse por alguno de los siguientes motivos:

- El plan de App Service es "Gratis" o "Compartido". No se admite SSL para estos planes de tarifa. 

    **Solución**: Actualice el plan de App Service para la aplicación web a "Estándar".

- La suscripción no tiene una tarjeta de crédito válida.

    **Solución**: Agregue una tarjeta de crédito válida a la suscripción. 

- La oferta de suscripción no es compatible con la adquisición de un certificado de App Service, como Microsoft Student.  

    **Solución**: Actualice su suscripción. 

- La suscripción ha alcanzado el límite máximo de adquisiciones permitidas en una suscripción.

    **Solución**: Los certificados de App Service tienen un límite de 10 adquisiciones de certificado para los tipos de suscripción de pago por uso y EA. Para otros tipos de suscripción, el límite es 3. Para aumentar el límite, póngase en contacto con el [soporte técnico de Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).
- El certificado de App Service se ha marcado como fraude. Recibe el siguiente mensaje de error: "Su certificado se ha marcado por posible fraude. La solicitud está actualmente bajo revisión. Si el certificado no se puede volver a utilizar en 24 horas, póngase en contacto con el soporte técnico de Azure".

    **Solución**: Si el certificado se ha marcado como fraude y no se resuelve en 24 horas, siga estos pasos:

    1. Inicie sesión en [Azure Portal](https://portal.azure.com).
    2. Vaya a **Certificados de App Service** y seleccione el certificado.
    3. Seleccione **Configuración del certificado** > **Paso 2: Comprobar** > **Verificación de dominio**. Esto envía un aviso por correo electrónico al proveedor de certificados de Azure para que resuelva el problema.

## <a name="domain-problems"></a>Problemas de dominio

### <a name="purchased-ssl-certificate-for-wrong-domain"></a>Adquisición de certificado SSL para un dominio incorrecto

### <a name="symptom"></a>Síntoma

Ha adquirido un certificado de App Service para un dominio incorrecto y no puede actualizar el certificado para usar el dominio correcto.

### <a name="solution"></a>Solución

- Elimine ese certificado y, a continuación, compre un certificado nuevo.
- Si el certificado actual que utiliza el dominio incorrecto se encuentra en el estado "Emitido", también se facturará ese certificado. Los certificados de App Service no son reembolsables, pero puede ponerse en contacto con el [soporte técnico de Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) para ver si hay otras opciones. 

### <a name="app-service-certificate-was-renewed-but-still-shows-the-old-certificate"></a>Se renovó el certificado de App Service, pero se sigue mostrando el certificado antiguo 

### <a name="symptom"></a>Síntoma

Se renovó el certificado de App Service, pero la aplicación web que usa el certificado de App Service sigue usando el certificado antiguo. Además, recibe la advertencia de que se requiere el protocolo HTTPS.

### <a name="cause"></a>Causa 
App Service web ejecuta un trabajo en segundo plano cada ocho horas que sincroniza el recurso de certificado, si hay algún cambio. Por eso, cuando se alterna o actualiza un certificado, la aplicación sigue recuperando, en ocasiones, el certificado antiguo y no el recién actualizado. Esto se debe a que el trabajo de sincronización del recurso de certificado no se ha ejecutado todavía. 
 
### <a name="solution"></a>Solución

Puede forzar una sincronización del certificado:

1. Inicie sesión en [Azure Portal](https://portal.azure.com). Seleccione **Certificados de App Service** y luego el certificado.
2. Haga clic en **Regenerar claves y sincronizar** y, a continuación, en **Sincronizar**. Este proceso tarda algo de tiempo en finalizar. 
3. Cuando se complete la sincronización, verá la siguiente notificación: "Se actualizaron correctamente todos los recursos con el certificado más reciente".

### <a name="domain-verification-is-not-working"></a>La comprobación del dominio no funciona 

### <a name="symptom"></a>Síntoma 
Es necesario comprobar el dominio para poder usar el certificado de App Service. Al hacer clic en **Comprobar**, se produce un error en el proceso.

### <a name="solution"></a>Solución
Compruebe manualmente el dominio mediante la adición de un registro TXT:
 
1.  Vaya al proveedor del Servicio de nombres de dominio (DNS) que hospeda el nombre de dominio.
2.  Agregue un registro TXT para el dominio que use el valor del token de dominio que se muestra en Azure Portal. 

Espere unos minutos a que tenga lugar la propagación de DNS y haga clic en el botón **Actualizar** para desencadenar la comprobación. 

El método alternativo para la comprobación manual es el "Método de página web HTML" que se puede usar para permitir que la entidad de certificación confirme la propiedad de dominio del dominio para el que se emitió el certificado.

1.  Cree un archivo HTML denominado {Domain Verification Token}.html. 
2.  El contenido de este archivo debe ser el valor del token de comprobación de dominio.
3.  Cargue este archivo en la raíz del servidor web que hospeda el dominio.
4.  Haga clic en **Actualizar** para comprobar el estado del certificado. La comprobación podría tardar unos minutos en finalizar.

Por ejemplo, si compra un certificado estándar para azure.com con el token de comprobación de dominio "1234abcd", una solicitud web realizada a http://azure.com/1234abcd.html devolvería 1234abcd. 

> [!IMPORTANT]
> Una orden de certificado dispone de tan solo 15 días para completar la operación de comprobación de dominio. Una vez transcurridos esos 15 días, la entidad de certificación denegará el certificado y no se le cobrará el certificado. En esta situación, elimine el certificado y vuelva a intentarlo.
>
> 

### <a name="unable-to-purchase-a-domain"></a>No se puede adquirir un dominio

### <a name="symptom"></a>Síntoma
No se puede adquirir un dominio de aplicación web o un dominio de App Service en Azure Portal.

### <a name="cause-and-solution"></a>Causa y solución

Este problema se produce por uno de los siguientes motivos:

- No aparece ninguna tarjeta de crédito en la suscripción de Azure o la tarjeta de crédito no es válida.

    **Solución**: Agregue una tarjeta de crédito válida a la suscripción, si no hay ninguna.

- Si no es el propietario de la suscripción, puede que no tenga permiso para comprar el dominio.

    **Solución**: [Agregue el rol de propietario](../billing/billing-add-change-azure-subscription-administrator.md) a su cuenta o póngase en contacto con el administrador de suscripciones para obtener permisos para adquirir un dominio.
- Ha alcanzado el límite de adquisición de dominios de su suscripción. El límite actual es de 20.

    **Solución**: Para solicitar una elevación del límite, póngase en contacto con el [soporte técnico de Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).
- El tipo de suscripción de Azure no admite la adquisición de dominio de App Service.

    **Solución**: Actualice la suscripción de Azure a otros tipos de suscripción, como una suscripción de pago por uso.

### <a name="unable-to-add-a-hostname-to-web-app"></a>No se puede agregar un nombre de host a la aplicación web 

### <a name="symptom"></a>Síntoma

Cuando agrega un nombre de host, el proceso no puede validar y comprobar el dominio.

### <a name="cause"></a>Causa 

Este problema se produce por uno de los siguientes motivos:

- No tiene permiso para agregar un nombre de host.

    **Solución**: Compruebe con el administrador de suscripciones que tiene permiso para agregar un nombre de host.
- No se pudo comprobar la propiedad del dominio.

    **Solución**: Compruebe si el registro CNAME o A están configurados correctamente. Para asignar un dominio personalizado a la aplicación web, cree un registro CNAME o A. Si quiere usar el dominio raíz, debe usar registros A y TXT:

    |Tipo de registro|Host|Apunta a|
    |------|------|-----|
    |Una |@|Dirección IP de la aplicación web|
    |TXT|@|<nombre-aplicación>.azurewebsites.net|
    |CNAME|www|<nombre-aplicación>.azurewebsites.net|

### <a name="dns-cannot-be-resolved"></a>El DNS no se puede resolver

### <a name="symptom"></a>Síntoma

Recibe un mensaje de error "The DNS record could not be located" (No se pudo localizar el registro DNS).

### <a name="cause"></a>Causa
Este problema se produce por uno de los siguientes motivos:

- No ha expirado el período de vida (TTL). Compruebe la configuración de DNS del dominio para determinar el valor de TTL y espere a que expire el período.
- La configuración de DNS es incorrecta.

### <a name="solution"></a>Solución
- Espere 48 horas para que este problema se resuelva por sí mismo.
- Si puede cambiar la configuración de TTL en la configuración de DNS, cambie el valor a 5 minutos para ver si esto resuelve el problema.
- Use [WhatsmyDNS.net](https://www.whatsmydns.net/) para comprobar que su dominio apunta a la dirección IP de la aplicación web. Si no es así, configure el registro A con la dirección IP correcta de la aplicación web.

### <a name="restore-a-deleted-domain"></a>Restaurar un usuario eliminado 

### <a name="symptom"></a>Síntoma
El dominio ya no está visible en Azure Portal.

### <a name="cause"></a>Causa 
El propietario de la suscripción puede haber eliminado accidentalmente el dominio.

### <a name="solution"></a>Solución
Si hace menos de siete días que se eliminó el dominio, aún no se ha iniciado el proceso de eliminación del dominio. En este caso, puede volver a comprar el mismo dominio en Azure Portal bajo la misma suscripción (asegúrese de escribir el nombre de dominio exacto en el cuadro de búsqueda). No se le volverá a cobrar por este dominio. Si el dominio se eliminó hace más de siete días, póngase en contacto con el [soporte técnico de Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) para obtener ayuda y restaurar el dominio.

### <a name="custom-domain-returns-404-or-site-inaccessible"></a>El dominio personalizado devuelve 404 o sitio inaccesible 

### <a name="symptom"></a>Síntoma

Cuando va al sitio usando el nombre de dominio personalizado, recibe el mensaje de error siguiente:

**Error 404-Web app not found** (Error 404: No se encontró la aplicación web).


### <a name="cause-and-solution"></a>Causa y solución

**Causa 1** 

En el dominio personalizado configurado falta un registro A o CNAME. 

**Solución para la causa 1**

- Si agregó un registro A, asegúrese de que también se agregó un registro TXT. Para más información, vea [Crear el registro A](./app-service-web-tutorial-custom-domain.md#create-the-a-record).
- Si no tiene que usar el dominio raíz de la aplicación web, se recomienda usar un registro CNAME en lugar de un registro A.
- No use un registro CNAME y un registro A para el mismo dominio. Puede causar conflictos y evitar que el dominio se resuelva. 

**Causa 2** 

El explorador de Internet podría seguir almacenando en caché la dirección IP antigua del dominio. 

**Solución para la causa 2**

Desactive el explorador. Para dispositivos Windows, puede ejecutar el comando `ipconfig /flushdns`. Use [WhatsmyDNS.net](https://www.whatsmydns.net/) para comprobar que su dominio apunta a la dirección IP de la aplicación web. 

### <a name="unable-to-add-subdomain"></a>No se puede agregar un subdominio 

### <a name="symptom"></a>Síntoma

No se puede agregar un nuevo nombre de host a una aplicación web para asignar un subdominio.

### <a name="solution"></a>Solución

- Compruebe con el administrador de suscripciones que tiene permisos para agregar un nombre de host a la aplicación web.
- Si necesita más subdominios, se recomienda que cambie el dominio que hospeda Azure DNS. Mediante el uso de Azure DNS, puede agregar 500 nombres de host a la aplicación web. Para más información, vea el artículo sobre [agregar un subdominio](https://blogs.msdn.microsoft.com/waws/2014/10/01/mapping-a-custom-subdomain-to-an-azure-website/).











 


















