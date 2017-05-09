---
title: Puerta de enlace de datos local | Microsoft Docs
description: "Necesitará una puerta de enlace local si el servidor de Analysis Services de Azure se debe conectar a orígenes de datos locales."
services: analysis-services
documentationcenter: 
author: minewiskan
manager: erikre
editor: 
tags: 
ms.assetid: cd596155-b608-4a34-935e-e45c95d884a9
ms.service: analysis-services
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: na
ms.date: 04/18/2017
ms.author: owend
ms.translationtype: Human Translation
ms.sourcegitcommit: 8c4e33a63f39d22c336efd9d77def098bd4fa0df
ms.openlocfilehash: e47fa44d87ad29054b833ee4bf201ac4d72e1e6e
ms.contentlocale: es-es
ms.lasthandoff: 04/20/2017


---
# <a name="on-premises-data-gateway"></a>Puerta de enlace de datos local
La puerta de enlace de datos local actúa como un puente, proporcionando una transferencia de datos segura entre orígenes de datos locales y el servidor de Azure Analysis Services en la nube.

La puerta de enlace se instala en un equipo de la red. Debe instalarse una puerta de enlace para cada servidor de Azure Analysis Services que tenga en la suscripción de Azure. Por ejemplo, si tiene dos servidores en la suscripción de Azure que se conectan a orígenes de datos locales, deberá instalar una puerta de enlace en dos equipos independientes de la red.

## <a name="requirements"></a>Requisitos
**Requisitos mínimos:**

* .NET Framework 4.5
* versión de 64 bits de Windows 7 o Windows Server 2008 R2 (o posterior)

**Se recomienda que use:**

* CPU de 8 núcleos
* 8 GB de memoria
* versión de 64 bits de Windows 2012 R2 (o posterior)

**Consideraciones importantes:**

* La puerta de enlace no se puede instalar en un controlador de dominio.
* Solo se puede instalar una puerta de enlace en un equipo.
* Instale la puerta de enlace en un equipo que permanezca encendido y no entre en suspensión. Si el equipo está apagado, el servidor de Azure Analysis Services no podrá conectarse a los orígenes de datos locales para actualizar los datos.
* No instale la puerta de enlace en un equipo conectado de forma inalámbrica a la red. Puede disminuir el rendimiento.
* Para cambiar el nombre del servidor de una puerta de enlace que ya se ha configurado, debe volver a instalar y configurar una nueva puerta de enlace.
* En algunos casos, los modelos tabulares se conectan a orígenes de datos mediante proveedores nativos como SQL Server Native Client (SQLNCLI11) pueden devolver un error. Para más información, consulte [Datasource connections](analysis-services-datasource.md) (Conexiones de orígenes de datos).

## <a name="supported-on-premises-data-sources"></a>Orígenes de datos locales admitidos
La puerta de enlace admite las conexiones entre el servidor de Azure Analysis Services y los siguientes orígenes de datos locales:

* SQL Server
* Almacenamiento de datos SQL
* APS
* Oracle
* Teradata

## <a name="download"></a>Descargar
 [Descargar la puerta de enlace](https://aka.ms/azureasgateway)

## <a name="install-and-configure"></a>Instalación y configuración
1. Ejecute la configuración.
2. Elija una ubicación de instalación y acepte los términos de licencia.
3. Inicie sesión en Azure.
4. Especifique el nombre de la instancia de Azure Analysis Server. Solo puede especificar un servidor por cada puerta de enlace. Haga clic en **Configurar** y estará listo para continuar.

    ![Inicio de sesión en Azure](./media/analysis-services-gateway/aas-gateway-configure-server.png)

## <a name="how-it-works"></a>Cómo funciona
La puerta de enlace se ejecuta como un servicio de Windows, llamado **Puerta de enlace de datos local**, en un equipo de red de la organización. La puerta de enlace que instala para su uso con Azure Analysis Services se basa en la misma puerta de enlace que se usa para otros servicios como Power BI, pero con algunas diferencias en la configuración.

![Cómo funciona](./media/analysis-services-gateway/aas-gateway-how-it-works.png)

Las consultas y el flujo de datos funciona de la siguiente manera:

1. El servicio en la nube crea una consulta con las credenciales cifradas para el origen de datos local. A continuación, se envía a una cola de la puerta de enlace para su procesamiento.
2. El servicio en la nube de puerta de enlace analiza la consulta e inserta la solicitud en la instancia de [Azure Service Bus](https://azure.microsoft.com/documentation/services/service-bus/).
3. La puerta de enlace de datos local sondea el bus de servicio de Azure en busca de solicitudes pendientes.
4. La puerta de enlace obtiene la consulta, descifra las credenciales y se conecta a los orígenes de datos con ellas.
5. La puerta de enlace envía la consulta al origen de datos para su ejecución.
6. Los resultados se devuelven desde el origen de datos a la puerta de enlace y luego se envían al servicio en la nube.

## <a name="windows-service-account"></a>Cuenta de servicio de Windows
La puerta de enlace de datos local está configurada para usar *NT SERVICE\PBIEgwService* para las credenciales de inicio de sesión del servicio de Windows. De manera predeterminada, tiene el derecho de iniciar sesión como un servicio en el contexto de la máquina en la que va a instalar la puerta de enlace. Esta credencial no es la misma cuenta utilizada para conectarse a orígenes de datos locales o a la cuenta de Azure.  

Si tiene problemas con el servidor proxy debido a la autenticación, puede cambiar la cuenta de servicio de Windows a un usuario de dominio o cuenta de servicio administrada.

## <a name="ports"></a>Puertos
La puerta de enlace crea una conexión de salida con el bus de servicio de Azure. Se comunica en los puertos de salida siguientes: TCP 443 (valor predeterminado), 5671, 5672 y del 9350 al 9354.  La puerta de enlace no requiere puertos de entrada.

Es recomendable crear una lista de direcciones IP permitidas para la región de datos del firewall. Puede descargar la [lista de direcciones IP del centro de datos de Microsoft Azure](https://www.microsoft.com/download/details.aspx?id=41653). La lista se actualiza semanalmente.

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
| *.servicebus.windows.net |443, 9350-9354 |Agentes de escucha de retransmisión de Bus de servicio sobre TCP (requiere 443 para la adquisición del token de Control de acceso) |
| *.frontend.clouddatahub.net |443 |HTTPS |
| *.core.windows.net |443 |HTTPS |
| login.microsoftonline.com |443 |HTTPS |
| *.msftncsi.com |443 |Se utiliza para probar la conectividad a Internet si el servicio Power BI no puede acceder a la puerta de enlace. |
| *.microsoftonline-p.com |443 |Se utiliza para la autenticación dependiendo de la configuración. |

### <a name="forcing-https-communication-with-azure-service-bus"></a>Cómo forzar la comunicación HTTPS con Azure Service Bus
Puede forzar a la puerta de enlace para que se comunique con Azure Service Bus mediante HTTPS en lugar de TCP directo. Sin embargo, esto puede reducir en gran medida el rendimiento. Debe modificar el archivo *Microsoft.PowerBI.DataMovement.Pipeline.GatewayCore.dll.config*. Cambie el valor de `AutoDetect` a `Https`. Este archivo se encuentra, de forma predeterminada, en *C:\Archivos de programa\Puerta de enlace de datos local*.

```
<setting name="ServiceBusSystemConnectivityModeString" serializeAs="String">
    <value>Https</value>
</setting>
```


## <a name="troubleshooting"></a>Solución de problemas
Internamente, la puerta de enlace de datos local usada para conectar Azure Analysis Services a sus orígenes de datos locales es la misma puerta de enlace que se usa con Power BI.

Si tiene problemas al instalar y configurar una puerta de enlace, no deje de consultar [Solución de problemas con la puerta de enlace de datos local](https://powerbi.microsoft.com/documentation/powerbi-gateway-onprem-tshoot/). Si cree que existe algún problema con el firewall, consulte las secciones relativas a firewall o proxy.

Si cree que existen problemas relacionados con el proxy o con la puerta de enlace, consulte [Configuración de proxy para la puerta de enlace de datos local](https://powerbi.microsoft.com/documentation/powerbi-gateway-proxy.md).

### <a name="telemetry"></a>Telemetría
La telemetría puede usarse para tareas de supervisión y solución de problemas. 

**Para activar la telemetría:**

1.    Compruebe el directorio de cliente de puerta de enlace de datos local en el equipo. Normalmente, su %systemdrive%\Program Files\puerta de enlace de datos local. O bien, puede abrir una consola de servicios y comprobar la ruta de acceso al archivo ejecutable: una propiedad del servicio de puerta de enlace de datos local.
2.    En el archivo Microsoft.PowerBI.DataMovement.Pipeline.GatewayCore.dll.config del directorio del cliente, cambie el valor SendTelemetry a true.
        
    ```
        <setting name="SendTelemetry" serializeAs="String">
                    <value>true</value>
        </setting>
    ```

3.    Guarde los cambios y reinicie el servicio de Windows: el servicio de puerta de enlace de datos local.




## <a name="next-steps"></a>Pasos siguientes
* [Administración de Analysis Services](analysis-services-manage.md)
* [Obtención de datos de Azure Analysis Services](analysis-services-connect.md)

