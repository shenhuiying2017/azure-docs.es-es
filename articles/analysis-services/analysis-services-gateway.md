---
title: Puerta de enlace de datos local | Microsoft Docs
description: "Necesitará una puerta de enlace local si el servidor de Analysis Services de Azure se debe conectar a orígenes de datos locales."
services: analysis-services
documentationcenter: 
author: minewiskan
manager: kfile
editor: 
tags: 
ms.assetid: cd596155-b608-4a34-935e-e45c95d884a9
ms.service: analysis-services
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: na
ms.date: 10/30/2017
ms.author: owend
ms.openlocfilehash: 0b11c005ddcf4a3416104e7cef39a7ce97957ba3
ms.sourcegitcommit: d41d9049625a7c9fc186ef721b8df4feeb28215f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/02/2017
---
# <a name="connecting-to-on-premises-data-sources-with-azure-on-premises-data-gateway"></a>Conexión a orígenes de datos locales con la puerta de enlace de datos local de Azure
La puerta de enlace de datos local actúa como un puente, ya que proporciona una transferencia de datos segura entre orígenes de datos locales y los servidores de Azure Analysis Services en la nube. Además de funcionar con varios servidores de Azure Analysis Services en la misma región, la versión más reciente de la puerta de enlace también funciona con Azure Logic Apps, Power BI, Power Apps y Microsoft Flow. Puede asociar varios servicios de la misma región con una sola puerta de enlace. 

El proceso de instalación de la puerta de enlace consta de cuatro partes:

- **Descargar y ejecutar el programa de instalación**: en este paso se instala un servicio de puerta de enlace en un equipo de la organización. También se inicia sesión en Azure con la cuenta de Azure AD del [inquilino](https://msdn.microsoft.com/library/azure/jj573650.aspx#BKMK_WhatIsAnAzureADTenant). No se pueden usar cuentas B2B (invitadas) de Azure.

- **Registrar la puerta de enlace**: en este paso se especifica el nombre y la clave de recuperación de la puerta de enlace y se selecciona la región, con lo que la puerta de enlace se registra en con el servicio en la nube de la puerta de enlace. El recurso de puerta de enlace **debe estar registrado en la misma región** que los servidores de Analysis Services. 

- **Crear un recurso de puerta de enlace en Azure**: en este paso se crea un recurso de puerta de enlace en una suscripción de Azure.

- **Conectar los servidores a los recursos de puerta de enlace**: una vez que tiene un recurso de puerta de enlace en la suscripción, puede empezar a conectar los servidores a él. Puede conectar varios servidores y otros recursos, siempre que se encuentren en la región.

Para comenzar inmediatamente, consulte [Instalación y configuración de una puerta de enlace de datos local](analysis-services-gateway-install.md).

## <a name="how-it-works"></a>Funcionamiento
La puerta de enlace que se instala en un equipo de la organización funciona como un servicio de Windows, **Puerta de enlace de datos local**. Este servicio local se registra en el servicio en la nube de puerta de enlace a través de Azure Service Bus. Luego se crea un servicio en la nube de puerta de enlace del recurso de puerta de enlace para la suscripción de Azure. Posteriormente, los servidores de servicios de Azure Analysis Services se conectan al recurso de puerta de enlace. Cuando los modelos del servidor necesitan conectarse a los orígenes de datos locales para realizar consultas o procesamiento, un flujo de datos y consultas atraviesa el recurso de puerta de enlace, Azure Service Bus, el servicio de puerta de enlace de datos local y los orígenes de datos. 

![Cómo funciona](./media/analysis-services-gateway/aas-gateway-how-it-works.png)

Flujo de datos y consultas:

1. El servicio en la nube crea una consulta con las credenciales cifradas para el origen de datos local. A continuación, se envía a una cola de la puerta de enlace para su procesamiento.
2. El servicio en la nube de puerta de enlace analiza la consulta e inserta la solicitud en la instancia de [Azure Service Bus](https://azure.microsoft.com/documentation/services/service-bus/).
3. La puerta de enlace de datos local sondea el bus de servicio de Azure en busca de solicitudes pendientes.
4. La puerta de enlace obtiene la consulta, descifra las credenciales y se conecta a los orígenes de datos con ellas.
5. La puerta de enlace envía la consulta al origen de datos para su ejecución.
6. Los resultados se devuelven desde el origen de datos a la puerta de enlace y luego se envían al servicio en la nube y al servidor.

## <a name="windows-service-account"></a>Cuenta de servicio de Windows
La puerta de enlace de datos local está configurada para usar *NT SERVICE\PBIEgwService* para las credenciales de inicio de sesión del servicio de Windows. De manera predeterminada, tiene el derecho de iniciar sesión como un servicio en el contexto de la máquina en la que va a instalar la puerta de enlace. Esta credencial no es la misma cuenta utilizada para conectarse a orígenes de datos locales o a la cuenta de Azure.  

Si tiene problemas con el servidor proxy debido a la autenticación, puede cambiar la cuenta de servicio de Windows a un usuario de dominio o cuenta de servicio administrada.

## <a name="ports"></a>Puertos
La puerta de enlace crea una conexión de salida con el bus de servicio de Azure. Se comunica en los puertos de salida siguientes: TCP 443 (valor predeterminado), 5671, 5672 y del 9350 al 9354.  La puerta de enlace no requiere puertos de entrada.

Se recomienda crear una lista de direcciones IP permitidas de la región de datos en el firewall. Puede descargar la [lista de direcciones IP del centro de datos de Microsoft Azure](https://www.microsoft.com/download/details.aspx?id=41653). La lista se actualiza semanalmente.

> [!NOTE]
> Las direcciones IP mostradas en la lista de direcciones IP del centro de datos de Azure están en notación CIDR. Por ejemplo, 10.0.0.0/24 no significa de 10.0.0.0 a 10.0.0.24. Conozca más información acerca de la [notación CIDR](http://whatismyipaddress.com/cidr).
>
>

Éstos son los nombres de dominio completos utilizados por la puerta de enlace.

| Nombres de dominio | Puertos de salida | Descripción |
| --- | --- | --- |
| *.powerbi.com |80 |HTTP que se utiliza para descargar el instalador. |
| *.powerbi.com |443 |HTTPS |
| *. analysis.windows.net |443 |HTTPS |
| *.login.windows.net |443 |HTTPS |
| *.servicebus.windows.net |5671-5672 |Advanced Message Queuing Protocol (AMQP) |
| *.servicebus.windows.net |443, 9350-9354 |Agentes de escucha en Service Bus Relay sobre TCP (requiere 443 para la adquisición del token de Access Control) |
| *.frontend.clouddatahub.net |443 |HTTPS |
| *.core.windows.net |443 |HTTPS |
| login.microsoftonline.com |443 |HTTPS |
| *.msftncsi.com |443 |Se utiliza para probar la conectividad a Internet si el servicio Power BI no puede acceder a la puerta de enlace. |
| *.microsoftonline-p.com |443 |Se utiliza para la autenticación dependiendo de la configuración. |

### <a name="force-https"></a>Forzar la comunicación HTTPS con Azure Service Bus
Puede hacer que la puerta de enlace se comunique con Azure Service Bus mediante HTTPS en lugar de TCP directo. Sin embargo, de ese modo puede reducir en gran medida el rendimiento. Debe modificar el archivo *Microsoft.PowerBI.DataMovement.Pipeline.GatewayCore.dll.config* cambiando el valor de `AutoDetect` por `Https`. Este archivo suele encontrarse en *C:\Archivos de programa\Puerta de enlace de datos local*.

```
<setting name="ServiceBusSystemConnectivityModeString" serializeAs="String">
    <value>Https</value>
</setting>
```

## <a name="faq"></a>Preguntas más frecuentes

### <a name="general"></a>General

**P**: ¿Necesito una puerta de enlace para orígenes de datos en la nube como, por ejemplo, Azure SQL Database? <br/>
**R:** No. Solo se necesitan puertas de enlace para conectarse a orígenes de datos locales.

**P**: ¿La puerta de enlace debe estar instalada en la misma máquina que el origen de datos? <br/>
**R:** No. Lo único que necesita la puerta de enlace es poder conectarse al servidor, normalmente en la misma red.

<a name="why-azure-work-school-account"></a>

**P**: ¿Por qué hay que usar una cuenta profesional o educativa para iniciar sesión? <br/>
**R**: Solo puede usar una cuenta profesional o educativa para instalar la puerta de enlace de datos local. Esa cuenta debe estar en el mismo inquilino que la suscripción donde va a configurar el recurso de la puerta de enlace. Su cuenta de inicio de sesión se almacena en un inquilino administrado por Azure Active Directory (Azure AD). Por lo general, el nombre principal de usuario (UPN) de la cuenta de Azure AD coincide con la dirección de correo electrónico.

**P**: ¿Dónde se almacenan mis credenciales? <br/>
**R**: Las credenciales que especifique para un origen de datos se cifran y almacenan en el servicio en la nube de la puerta de enlace. Las credenciales se descifran en la puerta de enlace de datos local.

**P**: ¿Hay algún requisito con respecto al ancho de banda de red? <br/>
**R**: Es aconsejable que la conexión de red tenga un buen rendimiento. Cada entorno es diferente, y la cantidad de datos que se envíe afecta a los resultados. Si usa ExpressRoute, podrá ayudar a garantizar un nivel de rendimiento entre los centros de datos locales y los de Azure.
Puede usar la aplicación Azure Speed Test (desarrollada por un tercero) para medir su rendimiento.

**P**: ¿cuál es la latencia para la ejecución de consultas en un origen de datos desde la puerta de enlace? ¿Cuál es la mejor arquitectura? <br/>
**R**: Para reducir la latencia de red, instale la puerta de enlace lo más cerca posible del origen de datos. Si puede instalar la puerta de enlace en el propio origen de datos, esta proximidad minimizará la latencia introducida. Tenga en cuenta también los centros de datos. Por ejemplo, si el servicio usa el centro de datos del oeste de EE. UU. y tiene SQL Server hospedado en una máquina virtual de Azure, esta debería encontrarse también en el oeste de EE. UU. Esta proximidad minimiza la latencia y evita los cargos de salida en la máquina virtual de Azure.

**P**: ¿Cómo se devuelven los resultados a la nube? <br/>
**R**: Los resultados se envían a través de Azure Service Bus.

**P**: ¿Hay alguna conexión de entrada a la puerta de enlace desde la nube? <br/>
**R:** No. La puerta de enlace usa conexiones de salida al bus de servicio de Azure.

**P**: ¿Qué sucede si bloqueo las conexiones de salida? ¿Qué tengo que abrir? <br/>
**R**: Consulte los puertos y los hosts que usa la puerta de enlace.

**P**: ¿Cómo se llama el servicio real de Windows?<br/>
**R**: En los servicios, la puerta de enlace se llama servicio de puerta de enlace de datos local.

**P**: ¿Se puede ejecutar el servicio de Windows de puerta de enlace con una cuenta de Azure Active Directory? <br/>
**R:** No. El servicio de Windows debe tener una cuenta de Windows válida. De forma predeterminada, el servicio se ejecuta con el SID de servicio NT SERVICE\PBIEgwService.

**P**: ¿Cómo adquiero una puerta de enlace? <br/>
**R**: Para adquirir una puerta de enlace (ejecutando Configurar/Cambiar en Panel de control > Programas) tiene que se propietario del recurso de la puerta de enlace en Azure y disponer de una clave de recuperación. Los propietarios del recurso de puerta de enlace se pueden configurar en Control de acceso.

### <a name="high-availability"></a>Alta disponibilidad y recuperación ante desastres

**P**: ¿Cuáles son las opciones de recuperación ante desastres disponibles? <br/>
**R**: Puede usar la clave de recuperación para restaurar o mover una puerta de enlace. Cuando instale la puerta de enlace, especifique la clave de recuperación.

**P**: ¿Qué ventaja aporta la clave de recuperación? <br/>
**R**: La clave de recuperación proporciona una forma de migrar o recuperar la configuración de la puerta de enlace después de un desastre.

## <a name="troubleshooting"></a>Solución de problemas

**P**: ¿Por qué no veo mi puerta de enlace en la lista de instancias de puerta de enlace al intentar crear el recurso de puerta de enlace de Azure? <br/>
**R**: Hay dos razones posibles. La primera es que el recurso ya se ha creado para la puerta de enlace actual o en otra suscripción. Para eliminar esa posibilidad, enumere los recursos del tipo **Puertas de enlace de datos locales** en el portal. Asegúrese de seleccionar todas las suscripciones al enumerar todos los recursos. Tenga en cuenta que una vez que se crea el recurso, la puerta de enlace no aparecerá en la lista de instancias de puerta de enlace en la experiencia del portal de creación de recursos de puerta de enlace. La segunda posibilidad es que la identidad de Azure AD del usuario que ha instalado la puerta de enlace es distinta de la del usuario que inició sesión en Azure Portal. Para resolver este problema, inicie sesión el portal mediante la misma cuenta que el usuario que ha instalado la puerta de enlace.

**P**: ¿Cómo se pueden ver las consultas que se envían al origen de datos local? <br/>
**R**: Puede habilitar el seguimiento de consultas, que incluye las consultas que se envían. No olvide devolver el seguimiento de consultas al valor original cuando haya terminado de solucionar problemas. Si lo deja activado, crea registros de mayor tamaño.

También puede examinar las herramientas de que dispone su origen de datos para el seguimiento de consultas. Por ejemplo, puede utilizar Eventos extendidos o SQL Profiler en SQL Server y Analysis Services.

**P**: ¿Dónde están los registros de la puerta de enlace? <br/>
**R**: Consulte la sección Registros en este mismo artículo.

### <a name="update"></a>Actualización a la versión más reciente

Pueden surgir muchos problemas cuando la versión de la puerta de enlace deja de estar actualizada. Como buena práctica general, asegúrese de que está usando la versión más reciente. Si no ha actualizado la puerta de enlace durante un mes o más, debería plantearse instalar su versión más reciente y comprobar si puede reproducir el problema.

### <a name="error-failed-to-add-user-to-group--2147463168-pbiegwservice-performance-log-users"></a>Error: Error al agregar el usuario al grupo. (Usuarios del registro de rendimiento de-2147463168 PBIEgwService)

Es posible que reciba este error si intenta instalar la puerta de enlace en un controlador de dominio, lo cual no se admite. Asegúrese de implementar la puerta de enlace en una máquina que no sea un controlador de dominio.

## <a name="logs"></a>Registros

Los archivos de registro son un recurso importante a la hora de solucionar problemas.

#### <a name="enterprise-gateway-service-logs"></a>Registros del servicio de puerta de enlace empresarial

`C:\Users\PBIEgwService\AppData\Local\Microsoft\On-premises data gateway\<yyyyymmdd>.<Number>.log`

#### <a name="configuration-logs"></a>Registros de configuración

`C:\Users\<username>\AppData\Local\Microsoft\On-premises data gateway\GatewayConfigurator.log`




#### <a name="event-logs"></a>Registros de eventos

Puede encontrar los registros de Data Management Gateway y PowerBIGateway en **Registros de aplicaciones y servicios**.


## <a name="telemetry"></a>Telemetría
La telemetría puede usarse para tareas de supervisión y solución de problemas. De forma predeterminada

**Para activar la telemetría:**

1.  Compruebe el directorio de cliente de puerta de enlace de datos local en el equipo. Normalmente, es **%systemdrive%\Program Files\On-premises data gateway**. O bien, puede abrir una consola de servicios y comprobar la ruta de acceso al archivo ejecutable: una propiedad del servicio de puerta de enlace de datos local.
2.  En el archivo Microsoft.PowerBI.DataMovement.Pipeline.GatewayCore.dll.config del directorio del cliente, cambie el valor SendTelemetry a true.
        
    ```
        <setting name="SendTelemetry" serializeAs="String">
                    <value>true</value>
        </setting>
    ```

3.  Guarde los cambios y reinicie el servicio de Windows: el servicio de puerta de enlace de datos local.




## <a name="next-steps"></a>Pasos siguientes
* [Instalación y configuración de una puerta de enlace de datos local](analysis-services-gateway-install.md)   
* [Administración de Analysis Services](analysis-services-manage.md)
* [Obtención de datos de Azure Analysis Services](analysis-services-connect.md)
