---
title: "Solución del problema de que los puntos de conexión de la red CDN de Azure devuelven el estado 404 | Microsoft Docs"
description: "Solucione problemas de los códigos de error 404 con los puntos de conexión de la red CDN de Azure."
services: cdn
documentationcenter: 
author: zhangmanling
manager: erikre
editor: 
ms.assetid: b588a1eb-ab69-4fc7-ae4d-157c3e46f4a8
ms.service: cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/23/2017
ms.author: mazha
ms.openlocfilehash: f59fbd18413fb44026d8c92b7f6940ed2f8a00a8
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/11/2017
---
# <a name="troubleshooting-cdn-endpoints-returning-404-statuses"></a>Solución de problemas de redes CDN que devuelven errores 404
Este artículo le ayudará a solucionar los problemas con los [puntos de conexión de redes CDN](cdn-create-new-endpoint.md) que devuelven errores 404.

Si necesita más ayuda en cualquier punto de este artículo, puede ponerse en contacto con los expertos de Azure en [los foros de MSDN Azure o de desbordamiento de pila](https://azure.microsoft.com/support/forums/). Como alternativa, también puede registrar un incidente de soporte técnico de Azure. Vaya al [sitio de soporte técnico de Azure](https://azure.microsoft.com/support/options/) y haga clic en **Obtener soporte técnico**.

## <a name="symptom"></a>Síntoma
Ha creado un perfil de red CDN y un punto de conexión, pero el contenido no parece estar disponible en la red CDN.  Los usuarios que intentan tener acceso al contenido a través de la URL de la red CDN reciben códigos de estado HTTP 404. 

## <a name="cause"></a>Causa
Hay varias causas posibles, por nombrar algunas:

* El origen del archivo no es visible para la red CDN
* El punto de conexión no está configurado correctamente, lo cual provoca que la red CDN busque en el lugar incorrecto
* El host rechaza el encabezado de host de la red CDN
* El punto de conexión no ha tenido tiempo de propagarse a través de la red CDN

## <a name="troubleshooting-steps"></a>Pasos para solucionar problemas
> [!IMPORTANT]
> Después de crear un punto de conexión de red CDN, este no estará disponible para su uso de forma inmediata; el registro puede tardar en propagarse a través de la red CDN.  Para los perfiles de la <b>red CDN de Azure de Akamai</b> , la propagación normalmente se completa en un minuto.  Para los perfiles de la <b>red CDN de Azure de Verizon</b>, la propagación normalmente se completará en 90 minutos, pero en algunos casos puede tardar más tiempo.  Si realiza los pasos detallados en este documento y sigue obteniendo errores 404, espere unas horas para probar nuevamente antes de abrir una incidencia de soporte técnico.
> 
> 

### <a name="check-the-origin-file"></a>Comprobación del archivo de origen
En primer lugar, debemos comprobar que el archivo que desea almacenar en caché está disponible en el origen y es accesible públicamente.  La forma más rápida de hacerlo es abrir un explorador en una sesión de In-Private o Incognito y buscar directamente el archivo.  Simplemente escriba o pegue la dirección URL en el cuadro de dirección y vea si esto da como resultado el archivo esperado.  En este ejemplo, se va a usar un archivo de una cuenta de Almacenamiento de Azure, accesible en `https://cdndocdemo.blob.core.windows.net/publicblob/lorem.txt`.  Como puede ver, este supera correctamente la prueba.

![¡Éxito!](./media/cdn-troubleshoot-endpoint/cdn-origin-file.png)

> [!WARNING]
> Aunque esta es la manera más rápida y sencilla de comprobar que el archivo está disponible públicamente, algunas configuraciones de red de su organización podrían dar la impresión de que este archivo está disponible públicamente cuando, en realidad, solo está visible para los usuarios de la red (incluso si está hospedado en Azure).  Si tiene un explorador externo desde el que pueda probar como, por ejemplo, un dispositivo móvil que no esté conectado a la red de su organización o una máquina virtual en Azure, esa sería la mejor opción.
> 
> 

### <a name="check-the-origin-settings"></a>Comprobación de la configuración de origen
Ahora que hemos comprobado que el archivo está disponible públicamente en Internet, debemos comprobar la configuración de origen.  En el [Portal de Azure](https://portal.azure.com), vaya al perfil de la red CDN y haga clic en el punto de conexión cuyos problemas desea solucionar.  En la hoja **Punto de conexión** resultante, haga clic en el origen.  

![Hoja de punto de conexión con origen resaltado](./media/cdn-troubleshoot-endpoint/cdn-endpoint.png)

Aparecerá la hoja **Origen** . 

![Hoja de origen](./media/cdn-troubleshoot-endpoint/cdn-origin-settings.png)

#### <a name="origin-type-and-hostname"></a>Tipo de origen y nombre del host
Compruebe que el **tipo de origen** es correcto y compruebe el **nombre de host de origen**.  En este ejemplo, `https://cdndocdemo.blob.core.windows.net/publicblob/lorem.txt`, la parte del nombre de host de la dirección URL es `cdndocdemo.blob.core.windows.net`.  Como puede ver en la captura de pantalla, esto es correcto.  Para orígenes de Almacenamiento de Azure, Aplicación web y Servicio en la nube, el campo **Nombre de host de origen** es una lista desplegable, por lo que no es necesario preocuparse por escribirlo correctamente.  Sin embargo, si utiliza un origen personalizado, es *absolutamente necesario* que el nombre de host se haya escrito correctamente.

#### <a name="http-and-https-ports"></a>Puertos HTTP y HTTPS
Los otros puntos que hay que comprobar aquí son los **puertos HTTP** y **HTTPS**.  En la mayoría de los casos, los puertos 80 y 443 son correctos y no requerirá ningún cambio.  Sin embargo, si el servidor de origen está escuchando en un puerto diferente, este deberá estar representado aquí.  Si no está seguro, simplemente mire la dirección URL del archivo de origen.  Las especificaciones de HTTP y HTTPS especifican los puertos 80 y 443 como los valores predeterminados. En mi dirección URL, `https://cdndocdemo.blob.core.windows.net/publicblob/lorem.txt`, no se especifica un puerto, por lo que se supone que el valor predeterminado es 443 y la configuración es correcta.  

Sin embargo, supongamos que la dirección URL para el archivo de origen que ha comprobado anteriormente es `http://www.contoso.com:8080/file.txt`.  Tenga en cuenta el `:8080` al final del segmento de nombre de host.  Indica al explorador que use el puerto `8080` para conectarse al servidor web en `www.contoso.com`, por lo que tendrá que escribir 8080 en el campo **Puerto HTTP**.  Es importante tener en cuenta que esta configuración de puertos solo afecta a qué puerto usa el punto de conexión para recuperar la información del origen.

> [!NOTE]
> **red CDN de Azure de Akamai** no permiten el intervalo completo de puertos TCP para los orígenes.  Para ver una lista de los puertos de origen que no están permitidos, consulte [Azure CDN from Akamai Allowed Origin Ports](https://msdn.microsoft.com/library/mt757337.aspx)(Puertos de origen permitidos de la red CDN de Azure de Akamai).  
> 
> 

### <a name="check-the-endpoint-settings"></a>Comprobación de la configuración del punto de conexión
En la hoja **Punto de conexión**, haga clic en el botón **Configurar**.

![Hoja de punto de conexión con el botón Configurar resaltado](./media/cdn-troubleshoot-endpoint/cdn-endpoint-configure-button.png)

Aparece la hoja **Configurar** del punto de conexión.

![Hoja Configurar](./media/cdn-troubleshoot-endpoint/cdn-configure.png)

#### <a name="protocols"></a>Protocolos
En **Protocolos**, compruebe que está seleccionado el protocolo utilizado por los clientes.  El mismo protocolo utilizado por el cliente será el que se utilice para acceder al origen, por lo que es importante que los puertos de origen se hayan configurado correctamente en la sección anterior.  El punto de conexión solo escucha en los puertos HTTP y HTTPS predeterminados (80 y 443), con independencia de los puertos de origen.

Volvamos a nuestro ejemplo hipotético con `http://www.contoso.com:8080/file.txt`.  Como recordará, Contoso especificó `8080` como puerto HTTP, pero supongamos también que se especificó `44300` como puerto HTTPS.  Si se creó un punto de conexión denominado `contoso`, el nombre de host del punto de conexión de la red CDN sería `contoso.azureedge.net`.  Una solicitud para `http://contoso.azureedge.net/file.txt` es una solicitud HTTP, por lo que el punto de conexión utilizará HTTP en el puerto 8080 para recuperar el archivo del origen.  Una solicitud segura a través de HTTPS, `https://contoso.azureedge.net/file.txt`, hará que el punto de conexión use HTTPS en el puerto 44300 al recuperar el archivo del origen.

#### <a name="origin-host-header"></a>Encabezado del host de origen
El **encabezado de host de origen** es el valor del encabezado del host que se envía al origen con cada solicitud.  En la mayoría de los casos, debe ser el mismo que el **nombre de host de origen** que se comprobó anteriormente.  Un valor incorrecto en este campo normalmente no causará errores 404, pero puede provocar otros errores 4xx, dependiendo de lo que el origen espera.

#### <a name="origin-path"></a>Ruta de acceso de origen
Por último, debemos comprobar la **ruta de acceso de origen**.  De manera predeterminada esta estará en blanco.  Solo debe usar este campo si quiere restringir el ámbito de los recursos hospedados en el origen que desea que estén disponibles en la red CDN.  

Por ejemplo, en mi punto de conexión, quiero que todos los recursos de mi cuenta de almacenamiento estén disponibles, por lo que he dejado la **ruta de acceso de origen** en blanco.  Esto significa que una solicitud a `https://cdndocdemo.azureedge.net/publicblob/lorem.txt` genera una conexión desde el punto de conexión a `cdndocdemo.core.windows.net` que solicita `/publicblob/lorem.txt`.  De la misma forma, una solicitud para `https://cdndocdemo.azureedge.net/donotcache/status.png` genera que el punto de conexión solicite `/donotcache/status.png` del origen.

Pero ¿qué ocurre si no deseo utilizar la red CDN para cada ruta de acceso del origen?  Supongamos que solo deseo exponer la ruta de acceso de `publicblob` .  Si se escribe */publicblob* en el campo **Ruta de acceso de origen**, el punto de conexión insertará */publicblob* antes de cada solicitud realizada al origen,  lo que significa que la solicitud para `https://cdndocdemo.azureedge.net/publicblob/lorem.txt` tomará la parte de la solicitud de la dirección URL, `/publicblob/lorem.txt`, y anexará `/publicblob` al principio. Esto producirá una solicitud para `/publicblob/publicblob/lorem.txt` desde el origen.  Si no se puede resolver esa ruta de acceso en un archivo real, el origen devolverá un error 404.  La dirección URL correcta para recuperar lorem.txt en este ejemplo sería realmente `https://cdndocdemo.azureedge.net/lorem.txt`.  Tenga en cuenta que no incluimos la ruta de acceso de */publicblob*, porque la parte de la solicitud de la dirección URL es `/lorem.txt` y el punto de conexión agrega `/publicblob`, lo que hace que `/publicblob/lorem.txt` sea la solicitud que se pasa al origen.

