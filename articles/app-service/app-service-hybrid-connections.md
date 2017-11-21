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
ms.date: 10/20/2017
ms.author: ccompy
ms.openlocfilehash: f0b148cb9c304c54b47be9601740e7634462d59b
ms.sourcegitcommit: f8437edf5de144b40aed00af5c52a20e35d10ba1
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/03/2017
---
# <a name="azure-app-service-hybrid-connections"></a>Hybrid Connections de Azure App Service #

## <a name="overview"></a>Información general ##

Hybrid Connections es tanto un servicio de Azure como una característica de Azure App Service.  Como servicio tiene un uso y unas funcionalidades que van más allá de aquellas de las que Azure App Service puede sacar provecho.  Para más información acerca de Hybrid Connections y su uso fuera de Azure App Service puede comenzar aquí, [Protocolo de conexiones híbridas de Azure Relay][HCService]

En Azure App Service, las conexiones híbridas se pueden utilizar para acceder a recursos de aplicaciones en otras redes. Proporciona acceso DESDE una aplicación A punto de conexión de la aplicación.  No permiten una posibilidad alternativa de acceder a su aplicación.  Dado que se utiliza en App Service, cada conexión híbrida se correlaciona con una combinación única de host y puerto TCP.  Esto significa que el punto de conexión híbrida puede estar en cualquier sistema operativo y en cualquier aplicación, siempre y cuando se llame a un puerto de escucha TCP. Las conexiones híbridas desconocen o no les importa cuál es el protocolo de aplicación o aquello a lo que accede.  Solo se encargan de proporcionar acceso a la red.  


## <a name="how-it-works"></a>Cómo funciona ##
La característica de conexiones híbridas consta de dos llamadas salientes a Service Bus Relay.  Hay una conexión desde una biblioteca del host en el que la aplicación se ejecuta en App Service y, después, hay una conexión desde el Administrador de conexiones híbridas (HCM) a Service Bus Relay.  HCM es un servicio de retransmisión que se implementa en la red que hospeda el recurso al que intenta acceder. 

Mediante las dos conexiones unidas, la aplicación tiene un túnel TCP a una combinación fija de host:puerto al otro lado del HCM.  La conexión usa TLS 1.2 para la seguridad y las claves SAS para la autenticación y autorización.    

![Flujo de nivel alto de conexión híbrida][1]

Cuando la aplicación realiza una solicitud DNS que coincide con un punto de conexión híbrida configurado, el tráfico TCP saliente se redirige por la conexión híbrida,  

> [!NOTE]
> lo que significa que debe intentar usar siempre un nombre DNS para la conexión híbrida.  Cierto software cliente no lleva a cabo una búsqueda de DNS si el punto de conexión utiliza una dirección IP.
>
>

Hay dos tipos de conexiones híbridas, las conexiones híbridas nuevas que se ofrecen como un servicio en Azure Relay y las antiguas conexiones híbridas de BizTalk.  En el portal, a las antiguas conexiones híbridas de BizTalk se les denomina conexiones híbridas clásicas.  En este mismo documento encontrará más información acerca de ellas.

### <a name="app-service-hybrid-connection-benefits"></a>Beneficios de usar las conexiones híbridas de App Service ###

El uso de la funcionalidad de conexiones híbridas reporta varios beneficios, entre los que se cuentan los siguientes:

- Las aplicaciones pueden acceder de forma segura a los servicios y sistemas locales.
- La característica no requiere ningún punto de conexión al que se pueda acceder a través de Internet.
- Se configura de forma rápida y sencilla. 
- Cada conexión híbrida coincide con una combinación única de host:puerto, lo que es un aspecto excelente de la seguridad.
- Normalmente no requiere vulnerabilidades de seguridad en el firewall, ya que todas las conexiones salen a través de puertos web estándar.
- Dado que la característica está en el nivel de la red, da igual el lenguaje que use la aplicación y la tecnología que emplee el punto de conexión.
- Se puede utilizar para proporcionar acceso en varias redes desde una única aplicación. 

### <a name="things-you-cannot-do-with-hybrid-connections"></a>Cosas que no se pueden hacer con las conexiones híbridas ###

Algunas de las cosas que no se pueden hacer con conexiones híbridas son:

- montar una unidad;
- usar UDP;
- acceder a servicios basados en TCP que usen puertos dinámicos, como el modo pasivo FTP o el modo pasivo extendido;
- proporcionar soporte para LDAP, ya que a veces requiere UDP;
- proporcionar soporte para Active Directory

## <a name="adding-and-creating-a-hybrid-connection-in-your-app"></a>Adición y creación de una conexión híbrida en una aplicación ##

Las conexiones híbridas se pueden crear mediante la aplicación de App Service o desde Azure Realy, en Azure Portal.  Es muy recomendable crear las conexiones híbridas mediante la aplicación de App Service que quiera usar con ellas.  Para crear una conexión híbrida, vaya a [Azure Portal][portal] y seleccione su aplicación.  Seleccione **Redes > Configure los extremos de la conexión híbrida**.  Desde ahí puede ver las conexiones híbridas que están configuradas para su aplicación  

![Lista de conexiones híbridas][2]

Para agregar una conexión híbrida nueva, haga clic en Agregar conexión híbrida.  En la interfaz de usuario que se abre se enumeran las conexiones híbridas que ya se han creado.  Para agregar una o varias de estas conexiones a su aplicación, haga clic en las que desee y pulse **Agregar conexión híbrida seleccionada**.  

![Portal de conexiones híbridas][3]

Si quiere crear una conexión híbrida nueva, haga clic en **Crear conexión híbrida nueva**.  Desde aquí, puede especificar: 

- El nombre del punto de conexión
- El nombre de host del punto de conexión
- Puerto del punto de conexión
- El espacio de nombres de Service Bus que quiere usar

![Create a hybrid connection][4]

Cada conexión híbrida está asociada a un espacio de nombres de Service Bus y cada uno de estos espacios se encuentra en una región de Azure.  Es importante probar y usar un espacio de nombres de Service Bus que se encuentre en la misma región que la aplicación, con el fin de evitar una latencia de red excesiva.

Si desea quitar una conexión híbrida de una aplicación, haga clic con el botón derecho en ella y seleccione **Desconectar**.  

Una vez que una conexión híbrida se agrega a una aplicación, con solo hacer clic en ella puede ver su información detallada. 

![Detalles de conexión híbrida][5]

### <a name="creating-a-hybrid-connection-in-the-azure-relay-portal"></a>Creación de una conexión híbrida en el portal de Azure Relay ###

Además de la experiencia del portal dentro de la aplicación, existen también la posibilidad de crear conexiones híbridas en el portal de Azure Relay. Una conexión híbrida debe satisfacer dos criterios para poder usarse en Azure App Service. Así, debe:

* Requerir autorización del cliente
* Tener un elemento de metadatos llamado punto de conexión que contenga una combinación de host:puerto como valor

## <a name="hybrid-connections-and-app-service-plans"></a>Conexiones híbridas y planes de App Service ##

Las conexiones híbridas solo están disponibles en las SKU de precios de nivel Básico, Estándar, Premium y Aislado.  No hay límites asociados al plan de precios.  

> [!NOTE] 
> Solo se pueden crear nuevas conexiones híbridas basadas en Azure Relay. No se pueden crear nuevas conexiones híbridas de BizTalk.
>

| Plan de precios | Número de conexiones híbridas que se pueden usar en el plan |
|----|----|
| Básica | 5 |
| Estándar | 25 |
| Premium | 200 |
| Aislado | 200 |

Dado que existen restricciones en el plan de App Service, también hay una interfaz de usuario en el plan que muestra el número de conexiones híbridas que se usan y las aplicaciones que lo hacen.  

![Propiedades de nivel de aplicación de App Service][6]

Para ver los detalles de una conexión híbrida, haga clic en ella.  En las propiedades que se muestran aquí, puede ver toda la información que tenía en la vista de aplicación, y también puede ver el número de aplicaciones del mismo plan de App Service que usan esa conexión híbrida.

Aunque hay un límite en el número de puntos de conexión híbrida que se pueden usar en un plan de App Service, cada conexión híbrida se puede usar en cualquier número de aplicaciones de dicho plan.  En otras palabras, una sola conexión híbrida que se use en cinco aplicaciones diferentes en un plan de App Service cuenta como 1 conexión híbrida.

Hay costos adicionales derivados del uso de conexiones híbridas.  Para más información sobre los precios de las conexiones híbridas, vaya aquí: [Precios de Service Bus][sbpricing].

## <a name="hybrid-connection-manager"></a>Hybrid Connection Manager ##

Para que las conexiones híbridas funcionen, necesita un agente de retransmisión en la red que hospeda el punto de conexión híbrida.  Dicho agente de retransmisión se denomina Hybrid Connection Manager (HCM).  Esta herramienta puede descargarse desde la interfaz de usuario de **Redes > Configure los extremos de la conexión híbrida** disponible en la aplicación en [Azure Portal][portal].  

Esta herramienta se ejecuta en Windows Server 2012 y en versiones posteriores de Windows.  Una vez instalado, el HCM se ejecuta como servicio.  Este servicio se conecta a Azure Service Bus Relay en función de los puntos de conexión configurados.  Las conexiones del HCM tienen salida a Azure por el puerto 443.    

El HCM tiene una interfaz de usuario para configurarlo.  Después de instalar el HCM, debe ejecutar el archivo HybridConnectionManagerUi.exe que se encuentra en el directorio de instalación del Administrador de conexiones híbridas, para mostrar la interfaz de usuario.  Para acceder fácilmente en Windows 10, escriba *Interfaz de usuario de Hybrid Connection Manager* en el cuadro de búsqueda.  

![Portal de conexiones híbridas][7]

Cuando se inicia la interfaz de usuario de HCM, lo primero que se ve es una tabla donde se muestran todas las conexiones híbridas configuradas con esta instancia de HCM.  Si quiere realizar algún cambio, tendrá que autenticarse con Azure. 

Para agregar una o varias conexiones híbridas a HCM:

1. Inicie la interfaz de usuario de HCM.
1. Haga clic en Configure other hybrid connectio (Configurar otra conexión híbrida). ![Adición de una conexión híbrida al HCM][8]

1. Inicie sesión con su cuenta de Azure
1. Elija una suscripción
1. Haga clic en las conexiones híbridas que desea que retransmita el HCM. ![Selección de una HC][9].

1. Haga clic en Guardar

En este momento, verá las conexiones híbridas que ha agregado.  También puede hacer clic en la conexión híbrida configurada y ver detalles acerca de ella.

![Detalles de la conexión híbrida][10]

Para que HCM pueda admitir las conexiones híbridas con las que está configurado, necesita:

- Acceso de TCP a Azure a través de los puertos 80 y 443.
- Acceso de TCP al punto de conexión híbrida
- Posibilidad de realizar búsquedas de DNS en el host del punto de conexión y el espacio de nombres de Azure Service Bus

El HCM admite tanto conexiones híbridas nuevas como las conexiones híbridas de BizTalk antiguas.

> [!NOTE]
> Azure Relay depende de Web Sockets para la conectividad. Esta funcionalidad solo está disponible en Windows Server 2012 o versiones más recientes. Por este motivo, el Administrador de conexiones híbridas no se admite en versiones anteriores de Windows Server 2012.
>

### <a name="redundancy"></a>Redundancia ###

Cada HCM puede admitir varias conexiones híbridas.  Además, varios HCM pueden admitir cualquier conexión híbrida dada.  El comportamiento predeterminado es operar el tráfico por turnos en los HCM configurados en cualquier punto de conexión dado.  Si quiere alta disponibilidad en las conexiones híbridas de la red, solo haya que crear instancias de varios HCM en máquinas diferentes. 

### <a name="manually-adding-a-hybrid-connection"></a>Adición manual de una conexión híbrida ###

Si desea que alguien de fuera de su suscripción hospede una instancia de HCM para una conexión híbrida determinada, puede compartir con dicho usuario la cadena de conexión de puerta de enlace de la conexión híbrida.  Esto se puede ver en las propiedades de una conexión híbrida en [Azure Portal][portal]. Para usar dicha cadena, haga clic en el botón **Enter Manually** (Especificar manualmente) del HCM y péguela en la cadena de conexión de puerta de enlace.


## <a name="troubleshooting"></a>Solución de problemas ##

El estado de conexión de una conexión híbrida significa que al menos un HCM está configurado con esa conexión híbrida y es capaz de conectarse a Azure.  Si en el estado de la conexión híbrida no pone **Conectado**, la conexión híbrida no está configurada en ningún HCM que tenga acceso a Azure.

La razón principal por la que los clientes no se pueden conectar a su punto de conexión es porque este se especificó mediante una dirección IP, en lugar de un nombre DNS.  Si la aplicación no puede acceder al punto de conexión deseado y ha usado una dirección IP, utilice un nombre DNS que sea válido en el host en el que se ejecuta HCM.  Otros aspectos que se deben comprobar son que el nombre DNS se resuelva satisfactoriamente en el host en el que se ejecuta el HCM y que haya conectividad desde el host donde se ejecuta el HCM al punto de conexión híbrida.  

Hay una herramienta en App Service que se puede invocar desde la consola e herramientas avanzadas (Kudu) llamada tcpping.  Esta herramienta puede indicarle si tienen acceso a un punto de conexión TCP, pero no indica si tiene acceso a un punto de conexión híbrida.  Cuando se utiliza en la consola con un punto de conexión híbrida, un ping correcto solo indicará que tiene configurada una conexión híbrida para la aplicación que usa esa combinación de host:puerto.  

## <a name="biztalk-hybrid-connections"></a>Conexiones híbridas de BizTalk ##

La funcionalidad antigua de conexiones híbridas de BizTalk está cerrada a las nuevas conexiones híbridas de BizTalk.  Aunque puede seguir usando las conexiones híbridas de BizTalk ya existentes con sus aplicaciones, debe migrar a las nuevas conexiones híbridas que usan Azure Relay.  Entre las ventajas del nuevo servicio, con respecto a la versión de BizTalk son:

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
