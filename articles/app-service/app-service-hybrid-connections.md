---
title: Hybrid Connections de Azure App Service | Microsoft Docs
description: "Creación y uso de Hybrid Connections para acceder a recursos de redes distintas"
services: app-service
documentationcenter: 
author: ccompy
manager: stefsch
editor: 
ms.assetid: 66774bde-13f5-45d0-9a70-4e9536a4f619
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/22/2017
ms.author: ccompy
ms.openlocfilehash: fef9e7b280387934cb093f51b4c8aa134a3b87e7
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="azure-app-service-hybrid-connections"></a>Hybrid Connections de Azure App Service #

## <a name="overview"></a>Información general ##

Hybrid Connections es tanto un servicio de Azure como una característica de Azure App Service.  Como servicio tiene un uso y unas funcionalidades que van más allá de las que Azure App Service puede sacar provecho.  Para más información acerca de Hybrid Connections y su uso fuera de Azure App Service puede comenzar aquí, [Protocolo de conexiones híbridas de Azure Relay][HCService]

En Azure App Service, las conexiones híbridas se pueden utilizar para acceder a recursos de aplicaciones en otras redes. Proporciona acceso DESDE una aplicación A punto de conexión de la aplicación.  No habilita una funcionalidad alternativa para acceder a la aplicación.  Dado que se utiliza en App Service, cada conexión híbrida se correlaciona con una combinación única de host y puerto TCP,  lo que significa que el punto de conexión de la conexión híbrida puede estar en cualquier sistema operativo y en cualquier aplicación, siempre que acceda a un puerto de escucha TCP. Hybrid Connections no sabe lo que es el protocolo de aplicaciones sabe ni a qué accede.  Simplemente ofrece acceso a la red.  


## <a name="how-it-works"></a>Cómo funciona ##
La característica de conexiones híbridas consta de dos llamadas salientes a Service Bus Relay.  Hay una conexión desde una biblioteca del host en el que la aplicación se ejecuta en App Service y, después, hay una conexión desde Hybrid Connection Manager (HCM) a Service Bus Relay.  HCM es un servicio de retransmisión que se implementa en el hospedaje de la red 

A través de las dos conexiones unidas la aplicación tiene un túnel TCP a una combinación de host:puerto fija en el otro lado del HCM.  La conexión usa TLS 1.2 para la seguridad y las claves SAS para la autenticación y autorización.    

![][1]

Cuando la aplicación realiza una solicitud DNS que coincide con un punto de conexión híbrida configurado, el tráfico TCP saliente se redirigirán por la conexión híbrida,  

> [!NOTE]
> lo que significa que debe intentar usar siempre un nombre DNS para la conexión híbrida.  Cierto software cliente no lleva a cabo una búsqueda de DNS si el punto de conexión utiliza una dirección IP.
>
>

Hay dos tipos de conexiones híbridas, las conexiones híbridas nuevas que se ofrecen como un servicio en Azure Relay y las antiguas conexiones híbridas de BizTalk.  En el portal, a las antiguas conexiones híbridas de BizTalk se les denomina conexiones híbridas clásicas.  En este mismo documento encontrará más información acerca de ellas.

### <a name="app-service-hybrid-connection-benefits"></a>Beneficios de usar las conexiones híbridas de App Service ###

El uso de la funcionalidad de conexiones híbridas reporta varios beneficios, entre los que se cuentan los siguientes

- Las aplicaciones pueden acceder de forma segura a los servicios y sistemas locales.
- La característica no requiere ningún punto de conexión al que se pueda acceder a través de Internet.
- Se configura de forma rápida y sencilla.  
- Cada conexión híbrida coincide con una combinación de host: puerto única, lo que es un aspecto excelente de la seguridad.
- Normalmente no requiere vulnerabilidades de seguridad en el firewall, ya que todas las conexiones salen a través de puertos web estándar.
- Dado que la característica está en el nivel de la red da igual el lenguaje que use la aplicación y la tecnología que emplee el punto de conexión.
- Se puede utilizar para proporcionar acceso en varias redes desde una única aplicación. 

### <a name="things-you-cannot-do-with-hybrid-connections"></a>Cosas que no se pueden hacer con las conexiones híbridas ###

Estas operaciones no se pueden realizar con las conexiones híbridas:

- montar una unidad;
- usar UDP;
- acceder a servicios basados en TCP que usen puertos dinámicos, como el modo pasivo de FTP o el modo pasivo extendido;
- proporcionar soporte para LDAP, ya que a veces requiere UDP;
- proporcionar soporte para Active Directory

## <a name="adding-and-creating-a-hybrid-connection-in-your-app"></a>Adición y creación de una conexión híbrida en una aplicación ##

Las conexiones híbridas pueden crearse a través del portal de la aplicación o desde el portal del servicio Hybrid Connection.  Se recomienda encarecidamente usar el portal de la aplicación para crear las conexiones híbridas que deseen usarse con la aplicación.  Para crear una conexión híbrida, vaya a [Azure Portal][portal] y entre en la interfaz de usuario de su aplicación.  Seleccione **Redes > Configure los extremos de la conexión híbrida**.  Desde ahí se puede ver las conexiones híbridas que se configuran con la aplicación  

![][2]

Para agregar una conexión híbrida nueva, haga clic en Agregar conexión híbrida.  En la interfaz de usuario que se abre se enumeran las conexiones híbridas que ya se han creado.  Para agregar una o varias a la aplicación, haga clic en ellas y pulse **Agregar conexión híbrida seleccionada**.  

![][3]

Si desea crear una conexión híbrida nueva, haga clic en **Crear conexión híbrida nueva**.  En el cuadro que aparece, especifique el: 

- nombre del punto de conexión
- nombre de host del punto de conexión
- puerto del punto de conexión
- espacio de nombres del bus de servicio que se desea usar

![][4]

Cada conexión híbrida está asociado a un espacio de nombres del bus de servicio y cada uno de estos espacios está en una región de Azure.  Es importante probar y usar un espacio de nombres del bus de servicio que se encuentre en la misma región que la aplicación, con el fin de evitar una latencia de red excesiva.

Si desea quitar una conexión híbrida de una aplicación, haga clic con el botón derecho en ella y seleccione **Desconectar**.  

Una vez que una conexión híbrida se agrega a una aplicación web, puede ver los detalles solo debe hacer clic en ella.  

![][5]

## <a name="hybrid-connections-and-app-service-plans"></a>Conexiones híbridas y planes de App Service ##

Las únicas conexiones híbridas que puede establecer ahora son las conexiones híbridas nuevas.  Solo están disponibles en las SKU de precios de nivel Básico, Estándar, Premium y Aislado.  No hay límites asociados al plan de precios.  

| Plan de precios | Número de conexiones híbridas se pueden usar en el plan |
|----|----|
| Básico | 5 |
| Estándar | 25 |
| Premium | 200 |
| Aislado | 200 |

Dado que existen restricciones en el plan de App Service también hay una interfaz de usuario en el plan que muestra el número de conexiones híbridas que se usan y las aplicaciones que lo hacen.  

![][6]

Igual que sucede con la vista de la aplicación, para ver los detalles de la conexión híbrida es preciso hacer clic en ella.  En las propiedades que se muestran a continuación puede ver toda la información que tenía en la vista de aplicación, pero también puede ver el número de aplicaciones del mismo plan de App Service que usan esa conexión híbrida.

![][7]

Aunque hay un límite en el número de puntos de conexión de las conexiones híbridas que se pueden usar en un plan de App Service, cada conexión híbrida se puede usar en cualquier número de aplicaciones de dicho plan.  Es decir que si se tiene una conexión híbrida que se ha usado en cinco aplicaciones distintas de un plan de App Service, no deja de ser una sola conexión híbrida.

Hay un costo adicional para las conexiones híbridas que van más allá de solo poder usarse en las SKU Básico, Estándar, Premium o Aislado.  Para más información acerca de los precios de las conexiones híbridas, vaya aquí: [Precios de Service Bus][sbpricing].

## <a name="hybrid-connection-manager"></a>Hybrid Connection Manager ##

Para que las conexiones híbridas funcionen, necesita un agente de retransmisión en la red que hospeda el punto de conexión de la conexión híbrida.  Dicho agente de retransmisión se denomina Hybrid Connection Manager (HCM).  Esta herramienta puede descargarse desde la interfaz de usuario de **Redes > Configure los extremos de la conexión híbrida** usuario disponible en la aplicación en [Azure Portal][portal].  

Esta herramienta se ejecuta en Windows Server 2008 R2 y en las versiones posteriores de Windows.  Una vez instalado, el HCM se ejecuta como servicio.  Este servicio se conecta a la retransmisión del bus de servicio de Azure basándose en los puntos de conexión configurados.  Las conexiones del HCM salen por los puertos 80 y 443.    

El HCM tiene una interfaz de usuario para configurarlo.  Después de instalar HCM para mostrar la interfaz de usuario debe ejecutar el archivo HybridConnectionManagerUi.exe que se encuentra en el directorio de instalación de Hybrid Connection Manager.  Para acceder fácilmente en Windows 10, escriba *Interfaz de usuario de Hybrid Connection Manager* en el cuadro de búsqueda.  

Cuando se inicia la interfaz de usuario de HCM, lo primero que se ve es una tabla que enumera todas las conexiones híbridas configuradas con esta instancia de HCM.  Si desea realizar algún cambio, tendrá que autenticarse con Azure. 

Para agregar una o varias conexiones híbridas a HCM:

1. Inicie la interfaz de usuario de HCM.
1. Haga clic en Configure other hybrid connection![][8] (Configurar otra conexión híbrida).

1. Inicie sesión con su cuenta de Azure
1. Elija una suscripción
1. Haga clic en las conexiones híbridas en las que desea que este HCM retransmita![][9]

1. Haga clic en Guardar

En este momento, verá las conexiones híbridas que ha agregado.  También puede hacer clic en la conexión híbrida configurada y ver detalles acerca de ella.

![][10]

Para que HCM pueda dar soporte a las conexiones híbridas con las que está configurado, necesita:

- Acceso de TCP a Azure a través de los puertos 80 y 443.
- Acceso de TCP al punto de conexión de la conexión híbrida.
- Capacidad para realizar búsquedas de DNS en el host del punto de conexión y el espacio de nombres de Azure ServiceBus

HCM admite tanto conexiones híbridas nuevas como las conexiones híbridas de BizTalk antiguas.

### <a name="redundancy"></a>Redundancia ###

Cada HCM puede admitir varias conexiones híbridas.  Además, varios HCM pueden admitir cualquier conexión híbrida dada.  El comportamiento predeterminado es operar el tráfico por turnos en los HCM configurados en cualquier punto de conexión dado.  Si desea alta disponibilidad en las conexiones híbridas de la red, solo haya que crear instancias de varios HCM en equipos diferentes. 

### <a name="manually-adding-a-hybrid-connection"></a>Adición manual de una conexión híbrida ###

Si desea que alguien de fuera de su suscripción hospede una instancia de HCM para una conexión híbrida determinado, puede compartir con dicho usuario la cadena de conexión de la puerta de enlace de la conexión híbrida.  Esto se puede ver en las propiedades de una conexión híbrida en [Azure Portal][portal]. Para utilizar dicha cadena, haga clic en el botón **Configure manually** (Configurar manualmente) de HCM y péguela en la cadena de conexión de puerta de enlace.


## <a name="troubleshooting"></a>Solución de problemas ##

Si la conexión híbrida se configura con una aplicación en ejecución y hay al menos un HCM que tiene dicha conexión híbrida configurada, en el portal el estado aparecerá como **Connected** (Conectado).  Si no aparece **así**, la aplicación está inactiva o HCM no se puede conectar a Azure a través de los puertos 80 o 443.  

La razón principal por la que los clientes no se pueden conectar a su punto de conexión es porque este se especificó mediante una dirección IP, en lugar de un nombre DNS.  Si la aplicación no puede acceder al punto de conexión deseado y ha usado una dirección IP, utilice un nombre DNS que sea válido en el host en el que se ejecuta HCM.  Otras operaciones que se deben comprobar son que el nombre DNS se resuelve satisfactoriamente en el host en el que HCM se ejecuta y que hay con el host en el que HCM se ejecuta en el punto de conexión de la conexión híbrida.  

Hay una herramienta en App Service que se puede invocar desde la consola llamada tcpping.  Dicha herramienta puede indicarle si tienen acceso a un punto de conexión de TCP, pero no indica si tiene acceso a un punto de conexión de una conexión híbrida.  Si se utiliza en la consola en un punto de conexión de la conexión híbrida, un ping correcto solo indicará que tiene una conexión híbrida configurada para la aplicación que use la combinación host: puerto.  

## <a name="biztalk-hybrid-connections"></a>Conexiones híbridas de BizTalk ##

La funcionalidad de las conexiones híbridas de BizTalk antiguas se ha cerrado para otras operaciones de creación de conexiones híbridas de BizTalk.  Puede seguir usando las conexiones híbridas de BizTalk preexistentes con sus aplicaciones, pero debe realizar la migración al nuevo servicio.  Entre las ventajas del nuevo servicio, con respecto a la versión de BizTalk son:

- No se requieren cuentas de BizTalk adicionales.
- La versión de TLS es la 1.2, en lugar de la 1.0 que era en las conexiones híbridas de BizTalk.
- La comunicación se realiza a través de los puertos 80 y 443 y se usa un nombre DNS para acceder a Azure, en lugar de direcciones IP y un intervalo de otros puertos adicionales.  

Para agregar una conexión híbrida de BizTalk a una aplicación, vaya a la aplicación en [Azure Portal][portal] y haga clic en **Redes > Configure los extremos de la conexión híbrida**.  En la tabla de conexiones híbridas clásicas, haga clic en **Agregar conexión híbrida clásica**.  Ahí verá una lista de las conexiones híbridas de BizTalk.  


<!--Image references-->
[1]: ./media/app-service-hybrid-connections/hybridconn-connectiondiagram.png
[2]: ./media/app-service-hybrid-connections/hybridconn-portal.png
[3]: ./media/app-service-hybrid-connections/hybridconn-addhc.png
[4]: ./media/app-service-hybrid-connections/hybridconn-createhc.png
[5]: ./media/app-service-hybrid-connections/hybridconn-properties.png
[6]: ./media/app-service-hybrid-connections/hybridconn-aspproperties.png
[7]: ./media/app-service-hybrid-connections/hybridconn-hcm.png
[8]: ./media/app-service-hybrid-connections/hybridconn-hcmadd.png
[9]: ./media/app-service-hybrid-connections/hybridconn-hcmadded.png
[10]: ./media/app-service-hybrid-connections/hybridconn-hcmdetails.png

<!--Links-->
[HCService]: http://docs.microsoft.com/azure/service-bus-relay/relay-hybrid-connections-protocol/
[portal]: http://portal.azure.com/
[oldhc]: http://docs.microsoft.com/azure/biztalk-services/integration-hybrid-connection-overview/
[sbpricing]: http://azure.microsoft.com/pricing/details/service-bus/

