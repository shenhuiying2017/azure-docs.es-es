---
title: Uso de servicios de equilibrio de carga en la nube de Azure | Microsoft Docs
description: "En este tutorial se muestra cómo crear un escenario con la cartera de equilibrio de carga de Azure: Traffic Manager, Application Gateway y Load Balancer."
services: traffic-manager
documentationcenter: 
author: liumichelle
manager: vitinnan
editor: 
ms.assetid: f89be3be-a16f-4d47-bcae-db2ab72ade17
ms.service: traffic-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/27/2016
ms.author: limichel
translationtype: Human Translation
ms.sourcegitcommit: 5919c477502767a32c535ace4ae4e9dffae4f44b
ms.openlocfilehash: 90918f6bb23f0f4e3c009506e1b12c028c45f02b

---

# <a name="using-load-balancing-services-in-the-azure-cloud"></a>Uso de servicios de equilibrio de carga en la nube de Azure

## <a name="introduction"></a>Introducción

Microsoft Azure proporciona varios servicios para administrar cómo se distribuye el tráfico de red y se equilibra la carga. Puede usarlos por separado o combinar sus métodos en función de sus necesidades específicas para construir la solución óptima.

En este tutorial, primero se define un caso de uso de cliente y se muestra cómo se puede mejorar la solidez y el rendimiento con la cartera de equilibrio de carga de Azure siguiente: Traffic Manager, Application Gateway y Load Balancer. A continuación, se proporcionan instrucciones paso a paso para crear una implementación con redundancia geográfica que distribuya el tráfico a máquinas virtuales y proporcione capacidades para administrar distintos tipos de solicitudes.

En un nivel conceptual, cada uno de estos servicios desempeña un rol distinto en la jerarquía de equilibrio de carga.

1. **Traffic Manager** proporciona equilibrio de carga de DNS global.  Examina las solicitudes de DNS entrantes y responde con un punto de conexión en buen estado y de conformidad con la directiva de enrutamiento que el cliente haya seleccionado. Entre las opciones de método de enrutamiento, se encuentra el enrutamiento de rendimiento para enviar al solicitante al punto de conexión más cercano en términos de latencia, el enrutamiento de prioridad para dirigir todo el tráfico a un punto de conexión con otros como reserva y el enrutamiento round-robin ponderado, que distribuye el tráfico según la ponderación asignada a cada punto de conexión. El cliente se conecta directamente a ese punto de conexión. Azure Traffic Manager detecta cuándo un punto de conexión está en mal estado y redirige a los clientes a otra instancia en buen estado. Consulte la [documentación de Azure Traffic Manager](traffic-manager-overview.md) para más información sobre el servicio.
2. **Application Gateway** proporciona un controlador de entrega de aplicaciones (ADC) como servicio, así como diversas funcionalidades de equilibrio de carga de nivel 7 para la aplicación. Permite a los clientes optimizar la productividad de las granjas de servidores web traspasando la carga de la terminación SSL con mayor actividad de la CPU a Application Gateway. Otras funcionalidades de enrutamiento de nivel 7 son la distribución round robin del tráfico entrante, la afinidad de sesiones basada en cookies, el enrutamiento basado en ruta de acceso de URL y la capacidad de hospedar varios sitios web tras una única instancia de Application Gateway. Application Gateway puede configurarse como una puerta de enlace orientada a Internet, una puerta de enlace solo para uso interno o una combinación de las dos. Application Gateway está completamente administrada por Azure, es escalable y tiene una elevada disponibilidad. Cuenta con un amplio conjunto de funcionalidades de diagnóstico y registro, lo que facilita su administración.
3. **Load Balancer** es una parte integral de la pila de SDN de Azure. Load Balancer proporciona servicios de equilibrio de carga de nivel 4 con latencia baja y rendimiento alto para todos los protocolos UDP y TCP.  Administra conexiones entrantes y salientes.  Puede configurar puntos de conexión con equilibrio de carga públicos e internos y definir reglas para asignar las conexiones entrantes a destinos de grupo de back-end con opciones de sondeo de estado TCP y HTTP para administrar la disponibilidad del servicio.

## <a name="scenario"></a>Escenario

En este escenario de ejemplo, se usa un sitio web sencillo que sirve dos tipos de contenido: imágenes y páginas web representadas de forma dinámica. Este sitio web necesita tener redundancia geográfica y debería atender a sus usuarios desde la ubicación que tengan más cercana (latencia más baja). El desarrollador de aplicaciones ha decidido que las direcciones URL que coincidan con el patrón /images/* se sirvan desde un grupo dedicado de máquinas virtuales distintas del resto de la granja de servidores web.

Además, el grupo de máquinas virtuales predeterminado que sirve el contenido dinámico debe comunicarse con una base de datos de back-end hospedada en un clúster de alta disponibilidad. Toda la implementación se aprovisiona a través de Azure Resource Manager.

Utilizar Traffic Manager, Application Gateway y Load Balancer permite que este sitio web logre estos objetivos de diseño:

1. **Redundancia geográfica múltiple**: usando Traffic Manager, si una región queda inactiva, el tráfico se enruta sin problema a la siguiente región más adecuada sin intervención del propietario de la aplicación.
2. **Menor latencia**: dado que Azure Traffic Manager dirige automáticamente al cliente a la región más cercana, este experimenta una menor latencia al solicitar contenido de la página web.
3. **Escalabilidad independiente**: al separar la carga de trabajo de la aplicación web en función del tipo de contenido, el propietario de la aplicación puede escalar las cargas de trabajo de solicitudes manteniendo la independencia entre ellas. Application Gateway garantiza que el tráfico se enrute a los grupos correctos según las reglas especificadas y el estado de la aplicación.
4. **Equilibrio de carga interno**: con Load Balancer frente al clúster de alta disponibilidad, solo se expone a la aplicación el punto de conexión en buen estado y activo correspondiente para una base de datos.  Además, un administrador de base de datos puede optimizar la carga de trabajo si distribuye réplicas activas y pasivas en el clúster de forma independiente de la aplicación de front-end.  Load Balancer entrega conexiones al clúster de alta disponibilidad y garantiza que las únicas bases de datos que reciban solicitudes de conexión sean las que encuentren en buen estado.

En el siguiente diagrama se muestra la arquitectura de este escenario:

![Imagen del diagrama del escenario](./media/traffic-manager-load-balancing-azure/scenario-diagram.png)

> [!NOTE]
> Este ejemplo es solo una de las muchas configuraciones posibles de los servicios de equilibrio de carga que ofrece Azure. Azure Traffic Manager, Application Gateway y Load Balancer se pueden combinar de la forma que mejor se adapte a su necesidades de equilibrio de carga. Por ejemplo, si la descarga de SSL o el procesamiento de nivel 7 no son necesarios, se puede usar Load Balancer en lugar de Application Gateway.

## <a name="setting-up-the-load-balancing-stack"></a>Configuración de la pila de equilibrio de carga

### <a name="step-1-create-a-traffic-manager-profile"></a>Paso 1: Creación de un perfil de Traffic Manager

1. Vaya a Azure Portal, haga clic en **Nuevo** y busque en Marketplace "Perfil de Traffic Manager".
2. En la hoja "Crear perfil de Traffic Manager", rellene la información básica para crear un perfil de Traffic Manager:

   * **Nombre**: proporciona un nombre de prefijo DNS para el perfil de Traffic Manager.
   * **Método de enrutamiento**: seleccione la directiva de método de enrutamiento del tráfico. Para más información sobre los métodos, consulte [Métodos de enrutamiento de tráfico de Traffic Manager](traffic-manager-routing-methods.md).
   * **Suscripción**: la suscripción que contiene el perfil.
   * **Grupo de recursos**: el grupo de recursos que contiene el perfil de Traffic Manager; puede ser un grupo de recursos nuevo o uno existente.
   * **Ubicación del grupo de recursos**: el servicio Traffic Manager es global y no está limitado a una ubicación. Aun así, se debe especificar una región para el grupo donde se encuentren los metadatos asociados al perfil de Traffic Manager. Esta ubicación no afecta a la disponibilidad en tiempo de ejecución del perfil.

3. Haga clic en **Crear** para generar el perfil de Traffic Manager.

    ![Hoja de creación de perfil de Traffic Manager](./media/traffic-manager-load-balancing-azure/s1-create-tm-blade.png)

### <a name="step-2-create-the-application-gateways"></a>Paso 2: Creación de las puertas de enlace de aplicaciones

1. Vaya a Azure Portal y, en el menú de la izquierda, haga clic en **Nuevo** > **Redes** > **Application Gateway**.
2. A continuación, rellene la información básica de la puerta de enlace de aplicaciones. Cuando haya terminado, haga clic en Aceptar. La información necesaria para la configuración básica es:

   * **Nombre** : nombre de la puerta de enlace de aplicaciones.
   * **Tamaño de SKU** : es el tamaño de la puerta de enlace de aplicaciones, las opciones disponibles son (Pequeño, Mediano y Grande).
   * **Número de instancias** : el número de instancias, este valor debe ser un número comprendido entre 2 y 10.
   * **Grupo de recursos** : el grupo de recursos que mantiene la puerta de enlace de aplicaciones, puede ser un grupo de recursos existente o uno nuevo.
   * **Ubicación** : la región de la puerta de enlace de aplicaciones, es la misma ubicación en el grupo de recursos. La ubicación es importante, ya que la red virtual y la dirección IP pública deben estar en la misma ubicación que la puerta de enlace.

3. A continuación, defina la red virtual, la subred, la IP de front-end y las configuraciones de agente de escucha para la puerta de enlace de aplicaciones. En este escenario, la dirección IP de front-end es **pública** para permitir que se agregue como punto de conexión al perfil de Traffic Manager más adelante.
4. La siguiente configuración de puerta de enlace de aplicaciones que se debe establecer es la del agente de escucha. Si se usa http, no queda nada por configurar, así que se puede hacer clic en **Aceptar**. Para usar https, se requiere más configuración. Consulte [Creación de una puerta de enlace de aplicaciones](../application-gateway/application-gateway-create-gateway-portal.md), comenzando por el paso 9.

#### <a name="configure-url-routing-for-application-gateways"></a>Configuración del enrutamiento de direcciones URL para puertas de enlace de aplicaciones

Una puerta de enlace de aplicaciones configurada con una regla basada en ruta de acceso usa un patrón de ruta de acceso de la dirección URL de solicitud junto con la distribución round robin al elegir el grupo de back-end. En este escenario, se agrega una regla basada en ruta de acceso para dirigir a cualquier dirección URL con "/images/\*" al grupo de servidores de imagen. Para más detalles sobre la configuración del enrutamiento basado en ruta de acceso de dirección URL para una puerta de enlace de aplicaciones, consulte [Creación de una regla basada en la ruta de acceso para una puerta de enlace de aplicaciones](../application-gateway/application-gateway-create-url-route-portal.md).

![Diagrama de nivel web](./media/traffic-manager-load-balancing-azure/web-tier-diagram.png)

1. Desde el grupo de recursos, vaya a la instancia de Application Gateway creada en los pasos anteriores.
2. En Configuración, seleccione **Grupos de back-end** y seleccione Agregar para agregar las máquinas virtuales que desee asociar a los grupos de back-end del nivel web.
3. En la hoja "Agregar grupo de back-end", escriba el nombre del grupo de back-end y todas las direcciones IP de las máquinas que residen en él. En este escenario, se conectan dos grupos de servidores de back-end de máquinas virtuales.

    ![Agregar grupo de back-end para puertas de enlace de aplicaciones](./media/traffic-manager-load-balancing-azure/s2-appgw-add-bepool.png)

4. A continuación, en Configuración de la puerta de enlace de aplicaciones, seleccione **Reglas** y haga clic en el botón **Basada en ruta de acceso** para agregar una nueva regla.

    ![Agregar regla de ruta de acceso para puertas de enlace de aplicaciones](./media/traffic-manager-load-balancing-azure/s2-appgw-add-pathrule.png)

5. En la hoja "Agregar regla basada en ruta de acceso", proporcione la siguiente información para configurar la regla:

   * Configuración básica:
     + **Nombre**: nombre descriptivo de la regla accesible en el portal.
     + **Agente de escucha**: el agente de escucha que se utiliza para la regla.
     + **Grupo de back-end predeterminado**: el grupo de back-end que se usará para la regla predeterminada.
     + **Configuración de HTTP predeterminada**: la configuración de HTTP que se usará para la regla predeterminada.
   * Reglas basadas en ruta de acceso:
     + **Nombre**: un nombre descriptivo de la regla basada en ruta de acceso.
     + **Rutas de acceso**: la regla de ruta de acceso que se utiliza para reenviar el tráfico.
     + **Grupo de back-end**: el grupo de back-end que se usará con esta regla.
     + **Configuración de HTTP**: la configuración de HTTP que se usará con esta regla.

     > [!IMPORTANT]
     > Rutas de acceso: las rutas de acceso válidas deben comenzar por "/". El carácter comodín "\*" solo se permite al final. Algunos ejemplos válidos son /xyz, /xyz\* o /xyz/\*.

     ![Hoja Agregar regla basada en ruta de acceso para puertas de enlace de aplicaciones](./media/traffic-manager-load-balancing-azure/s2-appgw-pathrule-blade.png)

### <a name="step-3-add-application-gateways-to-the-traffic-manager-endpoints"></a>Paso 3: Incorporación de puertas de enlace de aplicaciones a los puntos de conexión de Traffic Manager

En este escenario, Traffic Manager está conectado a instancias de Application Gateway (como se ha configurado en los pasos anteriores) que residen en diferentes regiones. Ahora que están configuradas las puertas de enlace de aplicaciones, el paso siguiente consiste en conectarlas al perfil de Traffic Manager.

1. Vaya a su instancia del perfil de Traffic Manager (lo verá en su grupo de recursos o si busca el nombre del perfil de Traffic Manager desde "Todos los recursos").
2. En esta hoja, seleccione **Extremos** y **Agregar** para agregar un punto de conexión nuevo.

    ![Agregar punto de conexión en Traffic Manager](./media/traffic-manager-load-balancing-azure/s3-tm-add-endpoint.png)

3. En la hoja "Agregar punto de conexión", rellene la información para crear un punto de conexión.

   * **Tipo**: el tipo de punto de conexión cuya carga se va a equilibrar. En este escenario, se trata de un punto de conexión de Azure, ya que se va a conectar a las instancias de Application Gateway configuradas antes.
   * **Nombre**: nombre del punto de conexión final.
   * **Tipo de recurso de destino**: seleccione Dirección IP pública y, en la configuración "Recurso de destino" a continuación, seleccione la dirección IP pública de la instancia de Application Gateway configurada antes.

     ![Agregar punto de conexión en Traffic Manager](./media/traffic-manager-load-balancing-azure/s3-tm-add-endpoint-blade.png)

4. Desde este punto, puede probar la configuración accediendo a ella con el DNS de su perfil de Traffic Manager (en este ejemplo, TrafficManagerScenario.trafficmanager.net). Puede reenviar solicitudes, activar o desactivar máquinas virtuales o servidores web creados en distintas regiones y cambiar la configuración de perfil de Traffic Manager para probar la configuración.

### <a name="step-4-create-the-load-balancer"></a>Paso 4: Creación del equilibrador de carga

En este escenario, Load Balancer distribuye conexiones desde el nivel web hasta las bases de datos dentro de un clúster de alta disponibilidad.

Si el clúster de base de datos de alta disponibilidad usa SQL AlwaysOn, consulte [Configuración de uno o varios agentes de escucha de grupo de disponibilidad AlwaysOn](../virtual-machines/virtual-machines-windows-portal-sql-ps-alwayson-int-listener.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) para ver las instrucciones paso a paso.

Para más detalles sobre cómo configurar un equilibrador de carga interno, consulte [Creación de un equilibrador de carga interno en Azure Portal](../load-balancer/load-balancer-get-started-ilb-arm-portal.md).

1. Vaya a Azure Portal y, en el menú de la izquierda, haga clic en **Nuevo** > **Redes** > **Equilibrador de carga**.
2. En la hoja "Crear equilibrador de carga", escriba un nombre para el equilibrador de carga.
3. Establezca **Tipo** en Interno y elija la red virtual y la subred adecuadas para que resida el equilibrador de carga.
4. En **Asignación de direcciones IP**, seleccione Dinámica o Estática.
5. Después, en **Grupo de recursos**, elija el grupo de recursos para el equilibrador de carga.
6. En **Ubicación**, elija la región adecuada para el equilibrador de carga.
7. Por último, haga clic en **Crear** para generar el equilibrador de carga.

#### <a name="connect-backend-database-tier-to-load-balancer"></a>Conexión de un nivel de base de datos de back-end con Load Balancer

1. En el grupo de recursos, busque el equilibrador de carga que creó en los pasos anteriores.
2. En Configuración, haga clic en **Grupos de back-end** y en **Agregar** para agregar un nuevo grupo de back-end.
3. En la hoja "Agregar grupo de back-end", escriba el nombre del grupo de back-end.
4. Desde aquí, puede agregar al grupo de back-end máquinas individuales o un conjunto de disponibilidad.

   ![Agregar grupo para equilibrador de carga](./media/traffic-manager-load-balancing-azure/s4-ilb-add-bepool.png)

#### <a name="configure-a-probe"></a>Configuración de un sondeo

1. En Configuración para el equilibrador de carga, seleccione **Sondeos** y **Agregar** para agregar un nuevo sondeo.
2. En la hoja "Agregar sondeo", escriba un valor en **Nombre** para el sondeo.
3. Seleccione un valor en **Protocolo** para el sondeo. Para una base de datos, probablemente le convenga más un sondeo TCP que uno HTTP.   Para más información sobre los sondeos de Load Balancer, consulte [Sondeos de Load Balancer](../load-balancer/load-balancer-custom-probe-overview.md).
4. A continuación, escriba un valor en **Puerto** para la base de datos que se va a usar al acceder al sondeo.
5. En **Intervalo**, especifique la frecuencia de sondeo de la aplicación.
6. En **Umbral incorrecto**, especifique el número de errores de sondeo continuos que deben suceder para que se considere que la máquina virtual de back-end está en mal estado.
7. Haga clic en **Aceptar** para crear el sondeo.

   ![Sondeo de equilibrador de carga](./media/traffic-manager-load-balancing-azure/s4-ilb-add-probe.png)

#### <a name="configure-load-balancing-rules"></a>Configuración de las reglas de equilibrio de carga

1. En Configuración para el equilibrador de carga, seleccione **Reglas de equilibrio de carga** y **Agregar** para crear una regla.
2. En la hoja "Agregar regla de equilibrio de carga", escriba un valor en **Nombre** para la regla de equilibrio de carga.
3. Elija un valor en **Dirección IP de front-end** para el equilibrador de carga, **Protocolo** y **Puerto**.
4. En **Puerto back-end**, especifique el puerto que se usará en el grupo de back-end.
5. Seleccione un valor en **Grupo de back-end** y en **Sondeo** creados en los pasos anteriores para aplicarles la regla.
6. En **Persistencia de la sesión**, seleccione cómo desea que persistan las sesiones.
7. En **Tiempo de espera de inactividad**, especifique el número de minutos antes de que se produzca un tiempo de espera de inactividad.
8. Seleccione Deshabilitada o Habilitada para **IP flotante**.
9. Haga clic en **Aceptar** para crear la regla.

### <a name="step-5-connect-web-tier-vms-to-load-balancer"></a>Paso 5: Conexión de máquinas virtuales del nivel web con Load Balancer

Ahora se configuran la dirección IP y el puerto de front-end de Load Balancer en las aplicaciones que se ejecutan en las máquinas virtuales de nivel web para cualquier conexión de base de datos. Esta configuración es específica de la aplicación que se ejecuta en estas máquinas virtuales. Para configurar la dirección IP de destino y el puerto, consulte estos detalles en la documentación de la aplicación. Para buscar la dirección IP del front-end, vaya a Grupo de direcciones IP de front-end en la hoja Configuración de Load Balancer en Azure Portal.

![Grupo de direcciones IP de front-end en Load Balancer](./media/traffic-manager-load-balancing-azure/s5-ilb-frontend-ippool.png)

## <a name="next-steps"></a>Pasos siguientes

* [¿Qué es Traffic Manager?](traffic-manager-overview.md)
* [Introducción a Application Gateway](../application-gateway/application-gateway-introduction.md)
* [Información general sobre Azure Load Balancer](../load-balancer/load-balancer-overview.md)



<!--HONumber=Nov16_HO3-->


