---
title: "Implementación de la puerta de enlace de fábrica conectada al Conjunto de aplicaciones de IoT de Azure | Microsoft Docs"
description: "Cómo implementar una puerta de enlace en Windows o Linux para habilitar la conectividad a la solución preconfigurada de fábrica conectada."
services: 
suite: iot-suite
documentationcenter: na
author: dominicbetts
manager: timlt
editor: 
ms.service: iot-suite
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/22/2017
ms.author: dobett
ms.translationtype: Human Translation
ms.sourcegitcommit: e7da3c6d4cfad588e8cc6850143112989ff3e481
ms.openlocfilehash: e8774cc290847d48ecdc5dcdac1f2533fdc7d072
ms.contentlocale: es-es
ms.lasthandoff: 05/16/2017


---

# <a name="deploy-a-gateway-on-windows-or-linux-for-the-connected-factory-preconfigured-solution"></a>Implementación de una puerta de enlace en Windows o Linux para la solución preconfigurada de fábrica conectada

Los pasos que se indican en este artículo muestran cómo implementar una puerta de enlace con Docker en Windows o Linux. La puerta de enlace habilita la conectividad con la solución preconfigurada de fábrica conectada.

> [!NOTE]
> El software de puerta de enlace que se ejecuta en el contenedor de Docker es [Azure IoT Edge].

## <a name="windows-deployment"></a>Implementación de Windows

> [!NOTE]
> Si todavía no tiene un dispositivo de puerta de enlace, Microsoft le recomienda comprar una puerta de enlace comercial con uno de nuestros asociados. Visite el [catálogo de dispositivos IoT de Azure] para ver una lista de los dispositivos de puerta de enlace compatibles con la solución de fábrica conectada. Siga las instrucciones que se incluyen con el dispositivo para configurar la puerta de enlace. Como alternativa, puede usar las instrucciones siguientes para establecer manualmente una de las puertas de enlace existentes.

### <a name="install-docker"></a>Instalación de Docker

Instale [Docker para Windows] en el dispositivo de puerta de enlace Windows. Durante la configuración de Windows Docker, seleccione una unidad en el equipo host para compartirla con Docker. En la captura de pantalla siguiente se muestra el uso compartido de la unidad D en el sistema Windows:

![Instalación de Docker][img-install-docker]

Luego, cree una carpeta llamada **docker** en la raíz de la unidad compartida.
También puede realizar este paso después de instalar docker desde el menú **Configuración**.

### <a name="configure-the-gateway"></a>Configuración de la puerta de enlace

1. Necesita la cadena de conexión **iothubowner** de la implementación de fábrica conectada del Conjunto de aplicaciones de IoT de Azure para completar la implementación de la puerta de enlace. En [Azure Portal], vaya a IoT Hub en el grupo de recursos que se creó cuando implementó la solución de fábrica conectada. Haga clic en **Directivas de acceso compartido** para tener acceso a la cadena de conexión **iothubowner**:

    ![Búsqueda de la cadena de conexión IoT Hub][img-hub-connection]

    Copie el valor **Connection string--primary key**.

1. Configure la puerta de enlace de la instancia de IoT Hub mediante la ejecución de los dos módulos de puerta de enlace **una vez** desde un símbolo del sistema con:

    `docker run -it --rm -h <ApplicationName> -v //D/docker:/build/src/GatewayApp.NetCore/bin/Debug/netcoreapp1.0/publish/CertificateStores -v //D/docker:/root/.dotnet/corefx/cryptography/x509stores microsoft/iot-gateway-opc-ua:1.0.0 <ApplicationName> "<IoTHubOwnerConnectionString>"`

    `docker run -it --rm -v //D/docker:/mapped microsoft/iot-gateway-opc-ua-proxy:0.1.3 -i -c "<IoTHubOwnerConnectionString>" -D /mapped/cs.db`

    * **&lt;ApplicationName&gt;** es el nombre de la aplicación OPC UA que la puerta de enlace crea con formato **publisher.&lt;nombre de dominio completo&gt;**. Por ejemplo, **publisher.microsoft.com**.
    * **&lt;IoTHubOwnerConnectionString&gt;** es la cadena de conexión **iothubowner** que se copió en el paso anterior. Esta cadena de conexión solo se usa en este paso y no la necesita nuevamente.

    La carpeta D:\\docker folder asignada (el argumento `-v`) se usa luego para conservar los dos certificados X.509 usados por los módulos de la puerta de enlace.

### <a name="run-the-gateway"></a>Ejecución de la puerta de enlace

1. Use estos comandos para reiniciar la puerta de enlace:

    `docker run -it --rm -h <ApplicationName> --expose 62222 -p 62222:62222 -v //D/docker:/build/src/GatewayApp.NetCore/bin/Debug/netcoreapp1.0/publish/Logs -v //D/docker:/build/src/GatewayApp.NetCore/bin/Debug/netcoreapp1.0/publish/CertificateStores -v //D/docker:/shared -v //D/docker:/root/.dotnet/corefx/cryptography/x509stores -e \_GW\_PNFP="/shared/publishednodes.JSON" microsoft/iot-gateway-opc-ua:1.0.0 <ApplicationName>`

    `docker run -it --rm -v //D/docker:/mapped microsoft/iot-gateway-opc-ua-proxy:0.1.3 -D /mapped/cs.db`

1. Por motivos de seguridad, los dos certificados X.509 que se conservan en la carpeta D:\\docker contienen la clave privada. El acceso a esta carpeta se debe limitar a las credenciales (habitualmente, **Administradores**) que se usan para ejecutar el contenedor Docker. Haga clic con el botón derecho en la carpeta D:\\docker, elija **Propiedades**, **Seguridad** y, luego, **Editar**. Conceda a los **administradores** el control total y quite a todos los demás:

    ![Concesión de permisos para el recurso compartido Docker][img-docker-share]

1. Compruebe la conectividad de la red. Intente hacer ping a la puerta de enlace. En un símbolo del sistema, escriba el comando `ping publisher.<your fully qualified domain name>`. Si el destino es inaccesible, agregue la dirección IP y el nombre de la puerta de enlace al archivo de hosts de la puerta de enlace. El archivo de hosts está en la carpeta "Windows\\System32\\drivers\\etc".

1. A continuación, intente conectarse al publicador a través de un cliente OPC UA local que se ejecute en la puerta de enlace. La dirección URL del punto de conexión de OPC UA es `opc.tcp://publisher.<your fully qualified domain name>:62222`. Si no tiene un cliente OPC UA, puede descargar un [cliente OPC UA de código abierto].

1. Una vez que complete correctamente estas pruebas locales, vaya a la página **Connect your own OPC UA Server** (Conexión de su propio servidor OPC UA) en el portal de solución de fábrica conectada. Escriba la dirección URL del punto de conexión de publicador (`tcp://publisher.<your fully qualified domain name>:62222`) y haga clic en **Conectar**. Recibirá una advertencia de certificado; luego, haga clic en **Proceed** (Continuar). A continuación, recibirá un error que indica que el publicador no confía en el cliente web UA. Para solucionar el error, copie el certificado de **cliente web UA** desde la carpeta "D:\\docker\\Rejected Certificates\\certs" a la carpeta "D:\\docker\\UA Applications\\certs" en la puerta de enlace. No necesita reiniciar la puerta de enlace. Repita este paso. Ahora puede conectarse a la puerta de enlace desde la nube y está preparado para agregar servidores OPC UA a la solución.

### <a name="add-your-opc-ua-servers"></a>Incorporación de los servidores OPC UA

1. Vaya a la página **Connect your own OPC UA Server** (Conexión con su propio servidor OPC UA) en el portal de solución de fábrica conectada. Siga los mismos pasos de la sección anterior para establecer la confianza entre el portal de fábrica conectada y el servidor OPC UA. Este paso establece una confianza mutua de los certificados desde el portal de factoría conectada al servidor OPC UA y crea una conexión.

1. Examine el árbol de nodos de OPC UA del servidor OPC UA, haga clic con el botón derecho en los nodos OPC y seleccione **Publicar**. Para que la publicación funcione de esta manera, el servidor OPC UA y el publicador deben estar en la misma red. En otras palabras, si el nombre de dominio completo del publicador es **publisher.mydomain.com**, el nombre de dominio completo del servidor OPC UA debe ser, por ejemplo, **myopcuaserver.mydomain.com**. Si la configuración es distinta, puede agregar nodos manualmente al archivo publishesnodes.json que se encuentra en la carpeta D:\\docker. El archivo publishesnodes.json se genera automáticamente cuando se publica correctamente un nodo OPC por primera vez.

1. Ahora la telemetría fluye desde el dispositivo de puerta de enlace. Puede ver la telemetría en la vista **Factory Locations** (Ubicaciones de fábrica) del portal de fábrica conectada en **New Factory** (Nueva fábrica).

## <a name="linux-deployment"></a>Implementación de Linux

> [!NOTE]
> Si todavía no tiene un dispositivo de puerta de enlace, Microsoft le recomienda comprar una puerta de enlace comercial con uno de nuestros asociados. Visite el [catálogo de dispositivos IoT de Azure] para ver una lista de los dispositivos de puerta de enlace compatibles con la solución de fábrica conectada. Siga las instrucciones que se incluyen con el dispositivo para configurar la puerta de enlace. Como alternativa, puede usar las instrucciones siguientes para establecer manualmente una de las puertas de enlace existentes.

[Instale Docker] en el dispositivo de puerta de enlace Linux.

### <a name="configure-the-gateway"></a>Configuración de la puerta de enlace

1. Necesita la cadena de conexión **iothubowner** de la implementación de fábrica conectada del Conjunto de aplicaciones de IoT de Azure para completar la implementación de la puerta de enlace. En [Azure Portal], vaya a IoT Hub en el grupo de recursos que se creó cuando implementó la solución de fábrica conectada. Haga clic en **Directivas de acceso compartido** para tener acceso a la cadena de conexión **iothubowner**:

    ![Búsqueda de la cadena de conexión IoT Hub][img-hub-connection]

    Copie el valor **Connection string--primary key**.

1. Configure la puerta de enlace de la instancia de IoT Hub mediante la ejecución de los dos módulos de puerta de enlace **una vez** desde un shell con:

    `sudo docker run -it --rm -h <ApplicationName> -v /shared:/build/src/GatewayApp.NetCore/bin/Debug/netcoreapp1.0/publish/ -v /shared:/root/.dotnet/corefx/cryptography/x509stores microsoft/iot-gateway-opc-ua:1.0.0 <ApplicationName> "<IoTHubOwnerConnectionString>"`

    `sudo docker run --rm -it -v /shared:/mapped microsoft/iot-gateway-opc-ua-proxy:0.1.3 -i -c "<IoTHubOwnerConnectionString>" -D /mapped/cs.db`

    * **&lt;ApplicationName&gt;** es el nombre de la aplicación OPC UA que la puerta de enlace crea con formato **publisher.&lt;nombre de dominio completo&gt;**. Por ejemplo, **publisher.microsoft.com**.
    * **&lt;IoTHubOwnerConnectionString&gt;** es la cadena de conexión **iothubowner** que se copió en el paso anterior. Esta cadena de conexión solo se usa en este paso y no la necesita nuevamente.

    La carpeta /shared asignada (el argumento `-v`) se usa luego para conservar los dos certificados X.509 usados por los módulos de la puerta de enlace.

### <a name="run-the-gateway"></a>Ejecución de la puerta de enlace

1. Use estos comandos para reiniciar la puerta de enlace:

    `sudo docker run -it -h <ApplicationName> --expose 62222 -p 62222:62222 –-rm -v /shared:/build/src/GatewayApp.NetCore/bin/Debug/netcoreapp1.0/publish/Logs -v /shared:/build/src/GatewayApp.NetCore/bin/Debug/netcoreapp1.0/publish/CertificateStores -v /shared:/shared -v /shared:/root/.dotnet/corefx/cryptography/x509stores -e _GW_PNFP="/shared/publishednodes.JSON" microsoft/iot-gateway-opc-ua:1.0.0 <ApplicationName>`

    `sudo docker run -it -v /shared:/mapped microsoft/iot-gateway-opc-ua-proxy:0.1.3 -D /mapped/cs.db`

1. Por motivos de seguridad, los dos certificados X.509 que se conservan en la carpeta /shared contienen la clave privada. El acceso a esta carpeta se debe limitar a las credenciales que se usan para ejecutar el contenedor Docker. Para establecer los permisos solo para **raíz**, use el comando de shell `chmod` en la carpeta.

1. Compruebe la conectividad de la red. Intente hacer ping a la puerta de enlace. En un shell, escriba el comando `ping publisher.<your fully qualified domain name>`. Si el destino es inaccesible, agregue la dirección IP y el nombre de la puerta de enlace al archivo de hosts de la puerta de enlace. El archivo de hosts está en la carpeta /etc.

1. A continuación, intente conectarse al publicador a través de un cliente OPC UA local que se ejecute en la puerta de enlace. La dirección URL del punto de conexión de OPC UA es `opc.tcp://publisher.<your fully qualified domain name>:62222`. Si no tiene un cliente OPC UA, puede descargar un [cliente OPC UA de código abierto].

1. Una vez que complete correctamente estas pruebas locales, vaya a la página **Connect your own OPC UA Server** (Conexión de su propio servidor OPC UA) en el portal de solución de fábrica conectada. Escriba la dirección URL del punto de conexión de publicador (`tcp://publisher.<your fully qualified domain name>:62222`) y haga clic en **Conectar**. Recibirá una advertencia de certificado; luego, haga clic en **Proceed** (Continuar). A continuación, recibirá un error que indica que el publicador no confía en el cliente web UA. Para solucionar el error, copie el certificado de **cliente web UA** desde la carpeta "/shared/Rejected Certificates/certs" a la carpeta "/shared/UA Applications/certs" en la puerta de enlace. No necesita reiniciar la puerta de enlace. Repita este paso. Ahora puede conectarse a la puerta de enlace desde la nube y está preparado para agregar servidores OPC UA a la solución.

### <a name="add-your-opc-ua-servers"></a>Incorporación de los servidores OPC UA

1. Vaya a la página **Connect your own OPC UA Server** (Conexión con su propio servidor OPC UA) en el portal de solución de fábrica conectada. Siga los mismos pasos de la sección anterior para establecer la confianza entre el portal de fábrica conectada y el servidor OPC UA. Este paso establece una confianza mutua de los certificados desde el portal de factoría conectada al servidor OPC UA y crea una conexión.

1. Examine el árbol de nodos de OPC UA del servidor OPC UA, haga clic con el botón derecho en los nodos OPC y seleccione **Publicar**. Para que la publicación funcione de esta manera, el servidor OPC UA y el publicador deben estar en la misma red. En otras palabras, si el nombre de dominio completo del publicador es **publisher.mydomain.com**, el nombre de dominio completo del servidor OPC UA debe ser, por ejemplo, **myopcuaserver.mydomain.com**. Si la configuración es distinta, puede agregar nodos manualmente al archivo publishesnodes.json que se encuentra en la carpeta /shared. El archivo publishesnodes.json se genera automáticamente cuando se publica correctamente un nodo OPC por primera vez.

1. Ahora la telemetría fluye desde el dispositivo de puerta de enlace. Puede ver la telemetría en la vista **Factory Locations** (Ubicaciones de fábrica) del portal de fábrica conectada en **New Factory** (Nueva fábrica).

## <a name="next-steps"></a>Pasos siguientes

Para más información sobre la arquitectura de la solución preconfigurada de fábrica conectada, consulte el [tutorial de la solución preconfigurada de fábrica conectada][lnk-walkthrough].

[img-install-docker]: ./media/iot-suite-connected-factory-gateway-deployment/image1.png
[img-hub-connection]: ./media/iot-suite-connected-factory-gateway-deployment/image2.png
[img-docker-share]: ./media/iot-suite-connected-factory-gateway-deployment/image3.png

[Docker para Windows]: https://www.docker.com/docker-windows
[catálogo de dispositivos IoT de Azure]: https://catalog.azureiotsuite.com/?q=opc
[Azure Portal]: http://portal.azure.com/
[cliente OPC UA de código abierto]: https://github.com/OPCFoundation/UA-.NETStandardLibrary/tree/master/SampleApplications/Samples/Client.Net4
[Instale Docker]: https://www.docker.com/community-edition#/download
[lnk-walkthrough]: iot-suite-overview.md
[Azure IoT Edge]: https://github.com/Azure/iot-edge
