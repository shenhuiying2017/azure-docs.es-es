---
title: "Comunicación segura con proxy inverso de Azure Service Fabric | Microsoft Docs"
description: "Configure el proxy inverso para habilitar la comunicación segura de un extremo a otro."
services: service-fabric
documentationcenter: .net
author: kavyako
manager: vipulm
ms.assetid: 
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: required
ms.date: 08/10/2017
ms.author: kavyako
ms.openlocfilehash: 568f9638c59282bcd7d3fae058a1588a889c22dc
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="connect-to-a-secure-service-with-the-reverse-proxy"></a>Conexión a un servicio seguro con el proxy inverso

En este artículo se explica cómo establecer una conexión segura entre el proxy inverso y los servicios, lo que crea un canal seguro de un extremo a otro.

La conexión a servicios seguros solo se admite cuando se configura el proxy inverso para escuchar en HTTPS. En el resto del documento se da por supuesto que esto es así.
Consulte [Proxy inverso en Azure Service Fabric](https://docs.microsoft.com/azure/service-fabric/service-fabric-reverseproxy) para configurar el proxy inverso en Service Fabric.

## <a name="secure-connection-establishment-between-the-reverse-proxy-and-services"></a>Establecimiento de una conexión segura entre el proxy inverso y los servicios 

### <a name="reverse-proxy-authenticating-to-services"></a>Autenticación del proxy inverso con los servicios:
El proxy inverso se identifica a sí mismo ante los servicios mediante su certificado, especificado con la propiedad ***reverseProxyCertificate*** en la [sección de tipos de recursos](../azure-resource-manager/resource-group-authoring-templates.md) del **clúster**. Los servicios pueden implementar la lógica para comprobar el certificado presentado por el proxy inverso. Los servicios pueden especificar los detalles del certificado de cliente aceptado como valores de configuración en el paquete de configuración. Esto se puede leer en tiempo de ejecución y puede utilizarse para validar el certificado presentado por el proxy inverso. Consulte [Administración de los parámetros de la aplicación en varios entornos](service-fabric-manage-multiple-environment-app-configuration.md) para agregar los valores de configuración. 

### <a name="reverse-proxy-verifying-the-services-identity-via-the-certificate-presented-by-the-service"></a>Comprobación por parte del proxy inverso de la identidad del servicio mediante el certificado presentado por el servicio:
Para realizar la validación de certificados de servidor de los certificados presentados por los servicios, el proxy inverso es compatible con una de las siguientes opciones: Ninguno, ServiceCommonNameAndIssuer y ServiceCertificateThumbprints.
Para seleccionar una de estas tres opciones, especifique el **ApplicationCertificateValidationPolicy** en la sección de parámetros del elemento ApplicationGateway/Http bajo [fabricSettings](service-fabric-cluster-fabric-settings.md).

```json
{
"fabricSettings": [
          ...
          {
            "name": "ApplicationGateway/Http",
            "parameters": [
              {
                "name": "ApplicationCertificateValidationPolicy",
                "value": "None"
              }
            ]
          }
        ],
        ...
}
```

Consulte la sección siguiente para obtener más información sobre la configuración adicional para cada una de estas opciones.

### <a name="service-certificate-validation-options"></a>Opciones de validación de certificados de servicio 

- **Ninguno**: el proxy inverso omite la comprobación del certificado de los servicios con proxy y establece la conexión segura. Este es el comportamiento predeterminado.
Especifique **ApplicationCertificateValidationPolicy** con el valor **Ninguno** en la sección de parámetros del elemento ApplicationGateway/Http.

- **ServiceCommonNameAndIssuer**: el proxy inverso comprueba que el certificado presentado por el servicio basándose en el nombre común del certificado y la huella digital del emisor inmediato. Para ello, especifique **ApplicationCertificateValidationPolicy** con valor **ServiceCommonNameAndIssuer** en la sección de parámetros del elemento ApplicationGateway/Http.

```json
{
"fabricSettings": [
          ...
          {
            "name": "ApplicationGateway/Http",
            "parameters": [
              {
                "name": "ApplicationCertificateValidationPolicy",
                "value": "ServiceCommonNameAndIssuer"
              }
            ]
          }
        ],
        ...
}
```

Para especificar la lista de nombres comunes de servicio y huellas digitales de emisor, agregue un elemento **ApplicationGateway/Http/ServiceCommonNameAndIssuer** bajo fabricSettings, tal y como se muestra a continuación. Pueden agregarse varios pares de huella digital de emisor y nombre común de certificado en el elemento de matriz de parámetros. 

Si el proxy inverso de punto de conexión al que se está conectando presenta un certificado cuya huella digital de emisor y nombre común coincide con cualquiera de los valores especificados aquí, se establece el canal SSL. Si no se puede realizar la coincidencia de los detalles del certificado, el proxy inverso produce un error en la solicitud del cliente con el código de estado 502 (Puerta de enlace incorrecta). La línea de estado HTTP también contendrá la frase "Certificado SSL no válido". 

```json
{
"fabricSettings": [
          ...
          {
             "name": "ApplicationGateway/Http/ServiceCommonNameAndIssuer",
            "parameters": [
              {
                "name": "WinFabric-Test-Certificate-CN1",
                "value": "b3 44 9b 01 8d 0f 68 39 a2 c5 d6 2b 5b 6c 6a c8 22 b4 22 11"
              },
              {
                "name": "WinFabric-Test-Certificate-CN2",
                "value": "b3 44 9b 01 8d 0f 68 39 a2 c5 d6 2b 5b 6c 6a c8 22 11 33 44"
              }
            ]
          }
        ],
        ...
}
```


- **ServiceCertificateThumbprints**: el proxy inverso comprobará el certificado de los servicios con proxy en función de su huella digital. Puede elegir este camino cuando los servicios estén configurados con certificados autofirmados: especifique el **ApplicationCertificateValidationPolicy** con el valor **ServiceCertificateThumbprints** en la sección de parámetros del elemento ApplicationGateway/Http.

```json
{
"fabricSettings": [
          ...
          {
            "name": "ApplicationGateway/Http",
            "parameters": [
              {
                "name": "ApplicationCertificateValidationPolicy",
                "value": "ServiceCertificateThumbprints"
              }
            ]
          }
        ],
        ...
}
```

Especifique también las huellas digitales con una entrada **ServiceCertificateThumbprints** en la sección de parámetros del elemento ApplicationGateway/Http. Pueden especificarse varias huellas digitales como una lista separada por comas en el campo de valor, tal y como se muestra a continuación:

```json
{
"fabricSettings": [
          ...
          {
            "name": "ApplicationGateway/Http",
            "parameters": [
                ...
              {
                "name": "ServiceCertificateThumbprints",
                "value": "78 12 20 5a 39 d2 23 76 da a0 37 f0 5a ed e3 60 1a 7e 64 bf,78 12 20 5a 39 d2 23 76 da a0 37 f0 5a ed e3 60 1a 7e 64 b9"
              }
            ]
          }
        ],
        ...
}
```

Si la huella digital del certificado del servidor se muestra en esta entrada de configuración, el proxy inverso realiza correctamente la conexión SSL. En caso contrario, se termina la conexión y se produce un error en la solicitud del cliente con un 502 (Puerta de enlace incorrecta). La línea de estado HTTP también contendrá la frase "Certificado SSL no válido".

## <a name="endpoint-selection-logic-when-services-expose-secure-as-well-as-unsecured-endpoints"></a>Lógica de selección de punto de conexión cuando los servicios exponen tanto puntos de conexión seguros como no seguros
Service Fabric admite la configuración de varios puntos de conexión para un servicio. Consulte [Especificación de los recursos en un manifiesto de servicio](service-fabric-service-manifest-resources.md).

El proxy inverso selecciona uno de los puntos de conexión al que reenviar la solicitud en función del parámetro de consulta **ListenerName**. Si no se especifica, puede elegir cualquier punto de conexión de la lista de puntos de conexión. Ahora puede ser un punto de conexión HTTP o HTTPS. Puede haber escenarios o requisitos en los que desee que el proxy inverso funcione en un "modo solo seguro"; es decir, no desea que el proxy inverso seguro reenvíe solicitudes a los puntos de conexión no seguros. Esto puede lograrse mediante la especificación de la entrada de configuración **SecureOnlyMode** con el valor **true** en la sección de parámetros del elemento ApplicationGateway/Http.   

```json
{
"fabricSettings": [
          ...
          {
            "name": "ApplicationGateway/Http",
            "parameters": [
                ...
              {
                "name": "SecureOnlyMode",
                "value": true
              }
            ]
          }
        ],
        ...
}
```

> 
> Cuando se trabaja en **SecureOnlyMode**, si el cliente ha especificado un **ListenerName** correspondiente a un punto de conexión HTTP (no protegido), el proxy inverso produce un error en la solicitud con un código de estado HTTP 404 (No encontrado).

## <a name="setting-up-client-certificate-authentication-through-the-reverse-proxy"></a>Configuración de la autenticación de certificados de cliente mediante el proxy inverso
Se produce la terminación SSL en el proxy inverso y se pierden todos los datos del certificado de cliente. Para que los servicios autentiquen el certificado de cliente, establezca el valor **ForwardClientCertificate** en la sección de parámetros del elemento ApplicationGateway/Http.

1. Cuando **ForwardClientCertificate** está establecido en **false**, el proxy inverso no solicitará el certificado de cliente durante el protocolo de enlace SSL con el cliente.
Este es el comportamiento predeterminado.

2. Cuando **ForwardClientCertificate** está establecido en **true**, el proxy inverso solicita el certificado del cliente durante el protocolo de enlace SSL con el cliente.
Luego, reenvía los datos del certificado de cliente en un encabezado HTTP personalizado denominado **X-Client-Certificate**. El valor del encabezado es la cadena en formato PEM con codificación base64 del certificado del cliente. El servicio puede completar correctamente la solicitud o no con el código de estado adecuado después de inspeccionar los datos del certificado.
Si el cliente no presenta un certificado, el proxy inverso reenvía un encabezado vacío y deja que el servicio gestione el caso.

> El proxy inverso es un simple reenviador. No validará en modo alguno el certificado del cliente.


## <a name="next-steps"></a>Pasos siguientes
* Consulte [Configure reverse proxy to connect to secure services](https://github.com/ChackDan/Service-Fabric/tree/master/ARM%20Templates/ReverseProxySecureSample#configure-reverse-proxy-to-connect-to-secure-services) (Configuración del proxy inverso para conectarse a servicios seguros) para ver las muestras de plantillas de Azure Resource Manager y para configurar el proxy inverso seguro con diferentes opciones de validación de certificados de servicio.
* Vea un ejemplo de comunicación HTTP entre los servicios de un [proyecto de ejemplo en GitHub](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started).
* [Llamadas a procedimiento remoto con la comunicación remota de Reliable Services](service-fabric-reliable-services-communication-remoting.md)
* [API web que usa OWIN en Reliable Services](service-fabric-reliable-services-communication-webapi.md)
* [Administración de certificados del clúster](service-fabric-cluster-security-update-certs-azure.md)
