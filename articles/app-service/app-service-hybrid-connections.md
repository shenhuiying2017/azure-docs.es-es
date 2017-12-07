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
ms.openlocfilehash: 677642e4e97523ed71ff5857ae27263743dca535
ms.sourcegitcommit: cfd1ea99922329b3d5fab26b71ca2882df33f6c2
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/30/2017
---
# <a name="azure-app-service-hybrid-connections"></a>Hybrid Connections de Azure App Service #

Conexiones híbridas es tanto un servicio de Azure como una característica de Azure App Service. Como servicio tiene un uso y unas funcionalidades que van más allá de aquellas que se usan en App Service. Para más información sobre Conexiones híbridas y su uso fuera de App Service, consulte [Protocolo de Conexiones híbridas de Azure Relay][HCService].

En App Service, Conexiones híbridas se puede usar para acceder a recursos de aplicaciones en otras redes. Proporciona acceso desde la aplicación a un punto de conexión de la aplicación. No permiten una posibilidad alternativa de acceder a su aplicación. Dado que se utiliza en App Service, cada conexión híbrida se correlaciona con una combinación única de host y puerto TCP. Esto significa que el punto de conexión híbrida puede estar en cualquier sistema operativo y en cualquier aplicación, siempre y cuando se acceda a un puerto de escucha TCP. La característica Conexiones híbridas no sabe lo que es el protocolo de aplicaciones ni sabe a qué se accede. Simplemente ofrece acceso a la red.  


## <a name="how-it-works"></a>Cómo funciona ##
La característica Conexiones híbridas consta de dos llamadas salientes a Azure Service Bus Relay. Existe una conexión desde una biblioteca en el host donde la aplicación se ejecuta en App Service. También hay una conexión desde el Administrador de conexiones híbridas (HCM) a Service Bus Relay. HCM es un servicio de retransmisión que se implementa en la red que hospeda el recurso al que intenta acceder. 

Mediante las dos conexiones unidas, la aplicación tiene un túnel TCP a una combinación fija de host:puerto al otro lado del HCM. La conexión usa TLS 1.2 para la seguridad y las claves de firma de acceso compartido (SAS) para la autenticación y la autorización.    

![Diagrama del flujo de alto nivel de conexión híbrida][1]

Cuando la aplicación realiza una solicitud DNS que coincide con un punto de conexión híbrida configurado, el tráfico TCP saliente se redirige por la conexión híbrida,  

> [!NOTE]
> lo que significa que debe intentar usar siempre un nombre DNS para la conexión híbrida. Cierto software cliente no lleva a cabo una búsqueda de DNS si el punto de conexión utiliza una dirección IP.
>
>

La característica Conexiones híbridas tiene dos tipos: las conexiones híbridas que se ofrecen como un servicio en Service Bus Relay y las conexiones híbridas anteriores de Azure BizTalk Services. En el portal, a estas últimas se les denomina Conexiones híbridas clásicas. Más adelante en este artículo puede encontrar más información sobre ellas.

### <a name="app-service-hybrid-connection-benefits"></a>Beneficios de usar las conexiones híbridas de App Service ###

El uso de la funcionalidad de conexiones híbridas reporta varios beneficios, entre los que se cuentan los siguientes:

- Las aplicaciones pueden acceder de forma segura a los servicios y sistemas locales.
- La característica no requiere ningún punto de conexión al que se pueda acceder a través de Internet.
- Se configura de forma rápida y sencilla. 
- Cada conexión híbrida coincide con una combinación única de host:puerto, lo que resulta útil para la seguridad.
- Normalmente no requiere vulnerabilidades de seguridad en el firewall, porque todas las conexiones salen a través de puertos web estándar.
- Dado que la característica está en el nivel de la red, da igual el lenguaje que use la aplicación y la tecnología que emplee el punto de conexión.
- Se puede utilizar para proporcionar acceso en varias redes desde una única aplicación. 

### <a name="things-you-cannot-do-with-hybrid-connections"></a>Cosas que no se pueden hacer con las conexiones híbridas ###

Algunas de las cosas que no se pueden hacer con conexiones híbridas son:

- Montar una unidad.
- Usar UDP.
- Acceder a servicios basados en TCP que usen puertos dinámicos, como el modo pasivo FTP o el modo pasivo extendido.
- Admitir LDAP, porque a veces se requiere UDP.
- Admitir Active Directory.

## <a name="add-and-create-hybrid-connections-in-your-app"></a>Incorporación y creación de Conexiones híbridas en la aplicación ##

Puede crear conexiones híbridas mediante la aplicación de App Service o desde Azure Realy, en Azure Portal. Le recomendamos crear las conexiones híbridas mediante la aplicación de App Service que quiera usar con ellas. Para crear una conexión híbrida, vaya a [Azure Portal][portal] y seleccione su aplicación. Seleccione **Redes** > **Configure los puntos de conexión de la conexión híbrida**. Desde ahí puede ver las conexiones híbridas que están configuradas para su aplicación.  

![Captura de pantalla de la lista de conexiones híbridas][2]

Para agregar una conexión híbrida nueva, seleccione **Agregar conexión híbrida**.  Verá una lista de las conexiones híbridas que ya creó. Para agregar una o varias de ellas en la aplicación, seleccione las que desee y, luego, seleccione **Agregar conexión híbrida seleccionada**.  

![Captura de pantalla del portal de conexiones híbridas][3]

Si quiere crear una conexión híbrida, seleccione **Crear conexión híbrida nueva**. Especifique: 

- El nombre del punto de conexión.
- El nombre de host del punto de conexión.
- El puerto del punto de conexión.
- El espacio de nombres de Service Bus que desea usar.

![Captura de pantalla del cuadro de diálogo Crear conexión híbrida nueva][4]

Cada conexión híbrida está asociada a un espacio de nombres de Service Bus y cada uno de estos espacios se encuentra en una región de Azure. Es importante intentar usar un espacio de nombres de Service Bus que se encuentre en la misma región que la aplicación, para evitar una latencia de red excesiva.

Si desea quitar una conexión híbrida de una aplicación, haga clic con el botón derecho en ella y seleccione **Desconectar**.  

Cuando se agrega una conexión híbrida a la aplicación, puede seleccionarla para ver su información detallada. 

![Captura de pantalla de los detalles de las conexiones híbridas][5]

### <a name="create-a-hybrid-connection-in-the-azure-relay-portal"></a>Creación de una conexión híbrida en el portal de Azure Relay ###

Además de la experiencia del portal dentro de la aplicación, puede crear conexiones híbridas en el portal de Azure Relay. Para que App Service use una conexión híbrida, debe:

* Requerir autorización del cliente.
* Tener un elemento de metadatos llamado punto de conexión que contenga una combinación de host:puerto como valor.

## <a name="hybrid-connections-and-app-service-plans"></a>Conexiones híbridas y planes de App Service ##

La característica Conexiones híbridas solo está disponible en las SKU de precios de nivel Básico, Estándar, Premium y Aislado. No hay límites asociados al plan de precios.  

> [!NOTE] 
> Solo se pueden crear nuevas conexiones híbridas basadas en Azure Relay. No se pueden crear nuevas conexiones híbridas de BizTalk.
>

| Plan de precios | Número de conexiones híbridas que se pueden usar en el plan |
|----|----|
| Básica | 5 |
| Estándar | 25 |
| Premium | 200 |
| Aislado | 200 |

Observe que el plan de App Service muestra cuántas conexiones híbridas se usan y qué aplicaciones las usan.  

![Captura de pantalla de las propiedades del plan de App Service][6]

Seleccione la conexión híbrida para ver detalles. Puede consultar toda la información que vio en la vista de la aplicación. También puede ver cuántas otras aplicaciones en el mismo plan usan esa conexión híbrida.

Hay un límite en el número de puntos de conexión de las conexiones híbridas que se pueden usar en un plan de App Service. Sin embargo, cada conexión híbrida se puede usar en cualquier cantidad de aplicaciones de ese plan. Por ejemplo, una conexión híbrida única que se usa en cinco aplicaciones distintas en un plan de App Service cuenta como una conexión híbrida.

Hay costos adicionales derivados del uso de conexiones híbridas. Consulte los detalles en los [precios de Service Bus][sbpricing].

## <a name="hybrid-connection-manager"></a>Hybrid Connection Manager ##

La característica Conexiones híbridas requiere un agente de retransmisión en la red que hospeda el punto de conexión de la conexión híbrida. Dicho agente de retransmisión se denomina Hybrid Connection Manager (HCM). Para descargar HCM, desde la aplicación en [Azure Portal][portal], seleccione **Redes** > **Configure los puntos de conexión de la conexión híbrida**.  

Esta herramienta se ejecuta en Windows Server 2012 y versiones posteriores. Una vez que se instala, HCM se ejecuta como un servicio que se conecta a Service Bus Relay, según los puntos de conexión configurados. Las conexiones del HCM salen a Azure a través del puerto 443.    

Después de instalar HCM, puede ejecutar HybridConnectionManagerUi.exe para usar la interfaz de usuario de la herramienta. Este archivo se encuentra en el directorio de instalación del Administrador de conexiones híbridas. En Windows 10, también puede simplemente buscar la *interfaz de usuario del Administrador de conexiones híbridas* en el cuadro de búsqueda.  

![Captura de pantalla de Administrador de conexiones híbridas][7]

Cuando inicia la interfaz de usuario de HCM, lo primero que se ve es una tabla donde se muestran todas las conexiones híbridas configuradas con esta instancia de HCM. Si desea hacer algún cambio, primero debe autenticarse con Azure. 

Para agregar una o varias conexiones híbridas a HCM:

1. Inicie la interfaz de usuario de HCM.
1. Seleccione **Configure another Hybrid Connection** (Configurar otra conexión híbrida).
![Captura de pantalla de configuración de conexiones híbridas nuevas][8]

1. Inicie sesión con su cuenta de Azure.
1. Elija una suscripción.
1. Seleccione las conexiones híbridas que desea que retransmita el HCM.
![Captura de pantalla de las conexiones híbridas][9]

1. Seleccione **Guardar**.

Ahora puede ver las conexiones híbridas que agregó. También puede seleccionar la conexión híbrida configurada para ver detalles.

![Captura de pantalla de los detalles de la conexión híbrida][10]

Para que HCM pueda admitir las conexiones híbridas con las que se configuró, necesita:

- Acceso de TCP a Azure a través de los puertos 80 y 443.
- Acceso de TCP al punto de conexión de la conexión híbrida.
- La capacidad de realizar búsquedas de DNS en el host del punto de conexión y el espacio de nombres de Service Bus.

HCM admite tanto conexiones híbridas nuevas como las conexiones híbridas de BizTalk.

> [!NOTE]
> Azure Relay depende de Web Sockets para la conectividad. Esta funcionalidad solo está disponible en Windows Server 2012 o versiones posteriores. Por este motivo, HCM no se admite en versiones anteriores de Windows Server 2012.
>

### <a name="redundancy"></a>Redundancia ###

Cada HCM puede admitir varias conexiones híbridas. Además, varios HCM pueden admitir cualquier conexión híbrida dada. El comportamiento predeterminado es enrutar el tráfico a través de los HCM configurados en cualquier punto de conexión dado. Si desea alta disponibilidad en las conexiones híbridas de la red, ejecute varias instancias de HCM en máquinas independientes. 

### <a name="manually-add-a-hybrid-connection"></a>Incorporación manual de una conexión híbrida ###

Para permitir que alguien de fuera de su suscripción hospede una instancia de HCM para una conexión híbrida determinada, comparta con dicho usuario la cadena de conexión de puerta de enlace de la conexión híbrida. Esto se puede ver en las propiedades de una conexión híbrida en [Azure Portal][portal]. Para usar dicha cadena, seleccione **Enter Manually** (Especificar manualmente) en el HCM y pegue la cadena de conexión de puerta de enlace.


## <a name="troubleshooting"></a>Solución de problemas ##

El estado "Conectado" significa que hay al menos una instancia de HCM configurada con esa conexión híbrida y es capaz de conectarse a Azure. Si en el estado de la conexión híbrida no indica **Conectado**, la conexión híbrida no está configurada en ningún HCM que tenga acceso a Azure.

La razón principal por la que los clientes no se pueden conectar a su punto de conexión es porque este se especificó mediante una dirección IP en lugar de un nombre DNS. Si la aplicación no puede acceder al punto de conexión deseado y ha usado una dirección IP, utilice un nombre DNS que sea válido en el host en el que se ejecuta HCM. Compruebe también que el nombre DNS se resuelve adecuadamente en el host donde se ejecuta el HCM. Confirme que existe conectividad desde el host donde se ejecuta el HCM al punto de conexión de la conexión híbrida.  

En App Service, es posible invocar la herramienta tcpping desde la consola de herramientas avanzadas (Kudu). Esta herramienta puede indicarle si tienen acceso a un punto de conexión TCP, pero no indica si tiene acceso a un punto de conexión híbrida. Cuando usa la herramienta de la consola en un punto de conexión de la conexión híbrida, simplemente confirma que usa una combinación de host:puerto.  

## <a name="biztalk-hybrid-connections"></a>Conexiones híbridas de BizTalk ##

La funcionalidad anterior de Conexiones híbridas de BizTalk se cerró para las nuevas conexiones híbridas de BizTalk. Aunque puede seguir usando las conexiones híbridas de BizTalk ya existentes con sus aplicaciones, debe migrar a las nuevas conexiones híbridas que usan Azure Relay. Entre las ventajas del nuevo servicio, con respecto a la versión de BizTalk son:

- No se requieren cuentas de BizTalk adicionales.
- La versión de TLS debe ser 1.2 en lugar de 1.0.
- La comunicación se realiza a través de los puertos 80 y 443 y usa un nombre DNS para acceder a Azure, en lugar de direcciones IP y un intervalo de puertos adicionales.  

Para agregar una conexión híbrida de BizTalk existente a la aplicación, vaya a la aplicación en [Azure Portal][portal] y seleccione **Redes** > **Configure los puntos de conexión de la conexión híbrida**. En la tabla de conexiones híbridas clásicas, seleccione **Agregar conexión híbrida clásica**. Podrá ver una lista de las conexiones híbridas de BizTalk.  


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
