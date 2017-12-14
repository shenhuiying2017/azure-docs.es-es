---
title: "Configuración de Network Performance Monitor para circuitos Azure ExpressRoute (versión preliminar) | Microsoft Docs"
description: "Configure Network Performance Monitor para los circuitos Azure ExpressRoute. (versión preliminar)"
documentationcenter: na
services: expressroute
author: cherylmc
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: expressroute
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/13/2017
ms.author: cherylmc
ms.openlocfilehash: 6a03986288fdb6acaf234a8abf690f728d160fd7
ms.sourcegitcommit: 922687d91838b77c038c68b415ab87d94729555e
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/13/2017
---
# <a name="configure-network-performance-monitor-for-expressroute-preview"></a>Configuración de Network Performance Monitor para ExpressRoute (versión preliminar)

Network Performance Monitor es una solución que supervisa la conectividad entre las implementaciones de nube de Azure y las ubicaciones de local (sucursales, etcétera) de supervisión de red en la nube. Network Performance Monitor forma parte de Microsoft Operations Management Suite (OMS) Network Performance Monitor ahora ofrece una extensión para ExpressRoute que le permite supervisar el rendimiento de la red a través de circuitos ExpressRoute configurados para usar el emparejamiento privado. Cuando configura Network Performance Monitor para ExpressRoute, puede detectar problemas de red para identificar y eliminar.

Puede:

* Supervisar la latencia y pérdida a través de varias redes virtuales y establecer alertas

* Supervisar todas las rutas de acceso (incluidas las rutas de acceso redundantes) en la red

* Solucionar problemas de red transitorios y a un momento dato que son difíciles de replicar

* Ayudar a determinar un segmento específico de la red que es responsable de la degradación del rendimiento

* Obtener el rendimiento por red virtual (si tiene agentes instalados en cada red virtual)

* Ver el estado del sistema ExpressRoute desde un momento dado anterior

## <a name="regions"></a>Regiones admitidas

Puede supervisar circuitos ExpressRoute en cualquier parte del mundo mediante un área de trabajo que se hospeda en una de las siguientes regiones:

* Europa occidental 
* Este de EE. UU. 
* Sudeste de Asia 

## <a name="workflow"></a>Flujo de trabajo

Se instalan agentes de supervisión en varios servidores, tanto en el entorno local como en Azure. Los agentes se comunican entre sí, pero no envían datos, envían paquetes de protocolo de enlace TCP. La comunicación entre los agentes permite a Azure asignar la topología de red y la ruta de acceso que puede tomar el tráfico.

1. Cree un área de trabajo de Network Performance Monitor en una de las [regiones admitidas](#regions).
2. Instalación y configuración de agentes de software: 
    * Instale agentes de supervisión en los servidores locales y las máquinas virtuales de Azure.
    * Configure las opciones en los servidores de agente de supervisión para permitir que estos agentes se comuniquen. (Por ejemplo, abrir puertos del firewall).
3. Configure las reglas de grupo de seguridad de red (NSG) para permitir que el agente de supervisión instalado en las máquinas virtuales de Azure se comuniquen con agentes de supervisión locales.
4. Solicitud de incorporar el área de trabajo de Network Performance Monitor a la lista blanca.
5. Configure la supervisión de la detección automática y administre las redes que están visibles en Network Performance Monitor.

Si ya usa Network Performance Monitor para supervisar otros servicios u objetos y ya dispone de un área de trabajo en una de las regiones admitidas, puede omitir el paso 1 y 2 y empezar la configuración en el paso 3.

## <a name="configure"></a>Paso 1: Creación de un área de trabajo

1. En [Azure Portal](https://portal.azure.com), busque "Network Performance Monitor" en la lista de servicios de **Marketplace**. Cuando se devuelva el resultado, haga clic para abrir la página **Network Performance Monitor**.

  ![portal](.\media\how-to-npm\3.png)<br><br>
2. En la parte inferior de la página **Network Performance Monitor** principal, haga clic en **Crear** para abrir la página **Network Performance Monitor - Crear nueva solución**. Haga clic en **Área de trabajo de OMS - Seleccionar un área de trabajo** para abrir la página Áreas de trabajo. Haga clic en **+Crear área de trabajo nueva** para abrir la página Área de trabajo.
3. En la página **Área de trabajo de OMS**, seleccione **Crear nuevo** y configure las siguientes opciones:

  * Área de trabajo de OMS: escriba un nombre para el área de trabajo.
  * Suscripción : si tiene varias suscripciones, elija la que desea asociar con el área de trabajo nueva.
  * Grupo de recursos: cree un grupo de recursos o use uno existente.
  * Ubicación: debe seleccionar una [región admitida](#regions).
  * Plan de tarifa: seleccione "Gratis"
  
  >[!NOTE]
  >El circuito ExpressRoute podría estar en cualquier parte del mundo y no tiene que estar en la misma región que el área de trabajo.
  >


  ![área de trabajo](.\media\how-to-npm\4.png)<br><br>
4. Haga clic en **Aceptar** para guardar e implementar la plantilla de configuración. Una vez validada la plantilla, haga clic en **Crear** para implementar el área de trabajo.
5. Una vez implementado el área de trabajo, vaya al recurso **NetworkMonitoring(name)** que ha creado. Valide la configuración y haga clic en **La solución necesita una configuración adicional**.

  ![configuración adicional](.\media\how-to-npm\5.png)
6. En la página **Bienvenido a Network Performance Monitor**, seleccione **Use TCP para transacciones sintéticas**  y después haga clic en **Enviar**. Las transacciones de TCP se usan solo para realizar e interrumpir la conexión. No se envía ningún dato en estas conexiones TCP.

  ![TCP para transacciones sintéticas](.\media\how-to-npm\6.png)

## <a name="agents"></a>Paso 2: Instalación y configuración de agentes

### <a name="download"></a>2.1: Descarga del archivo de instalación del agente

1. En la página **Configuración de Network Performance Monitor - Configuración TCP** para el recurso, en la sección **Instalar los agentes de OMS**, haga clic en el agente que se corresponde con el procesador del servidor y descargue el archivo de instalación.

  >[!NOTE]
  >El agente debe instalarse en Windows Server (2008 SP1 o posterior). No se admite la supervisión de circuitos ExpressRoute mediante el sistema operativo de escritorio Windows y el sistema operativo Linux. 
  >
  >
2. Después, copie los valores de **Id. de área de trabajo** y **Clave principal** en el Bloc de notas.
3. En la sección **Configurar agentes**, descargue el script de PowerShell. El script de PowerShell le ayuda a abrir el puerto de firewall pertinente para las transacciones TCP.

  ![Script de PowerShell](.\media\how-to-npm\7.png)

### <a name="installagent"></a>2.2: Instalación de un agente de supervisión en cada servidor de supervisión

Le recomendamos que instale al menos dos agentes en cada lado de la conexión de ExpressRoute (por ejemplo, en el entorno local y en las redes virtuales de Azure) para obtener redundancia. Use los pasos siguientes para instalar agentes:

1. Ejecute **el programa de instalación** para instalar el agente en cada servidor que va a utilizar para la supervisión de ExpressRoute. El servidor que se usa para la supervisión puede ser una máquina virtual o encontrase en un entorno local y debe tener acceso a Internet. Debe instalar al menos un agente local y uno en cada segmento de red que desea supervisar en Azure.
2. En la página **principal**, haga clic en **Siguiente**.
3. En la página **Términos de licencia**, lea la licencia y haga clic en **Acepto**.
4. En la página **Carpeta de destino**, cambie o mantenga la carpeta de instalación predeterminada y haga clic en **Siguiente**.
5. En la página **Opciones de instalación del agente**, puede elegir conectar el agente a Azure Log Analytics (OMS) o a Operations Manager. O bien, puede dejar las opciones en blanco si desea configurar el agente más adelante. Tras efectuar la selección, haga clic en **Siguiente**.

  * Si decide conectar el agente a **Azure Log Analytics (OMS)**, pegue el **identificador del área de trabajo** y la **clave del área de trabajo** (clave principal) que copió en el Bloc de notas durante la sección anterior. A continuación, haga clic en **Siguiente**.

    ![Identificador y clave](.\media\how-to-npm\8.png)
  * Si decide conectar el agente a **Operations Manager**, en la página **Configuración del grupo de administración**, escriba el **nombre del grupo de administración**, el **servidor de administración** y el **puerto del servidor de administración**. A continuación, haga clic en **Siguiente**.

    ![Operations Manager](.\media\how-to-npm\9.png)
  * En la página **Cuenta de acción del agente**, seleccione la cuenta de **sistema local** o un **dominio o cuenta del equipo local**. A continuación, haga clic en **Siguiente**.

    ![Cuenta](.\media\how-to-npm\10.png)
6. En la página **Preparado para instalar**, revise las opciones seleccionadas y haga clic en **Instalar**.
7. En la página **La configuración finalizó correctamente**, haga clic en **Finalizar**.
8. Una vez completado el proceso, Microsoft Monitoring Agent aparece en el Panel de control. Allí puede revisar la configuración y comprobar que el agente esté conectado a Visión operativa (OMS). Cuando se conecta a OMS, el agente muestra un mensaje similar al siguiente: **Microsoft Monitoring Agent se conectó correctamente al servicio Microsoft Operations Management Suite**.

### <a name="proxy"></a>2.3: Configuración de los valores de proxy (opcional)

Si está utilizando un proxy web para acceder a Internet, siga estos pasos para configurar el proxy para Microsoft Monitoring Agent. Realice estos pasos para cada servidor. Si tiene muchos servidores que necesite configurar, le resultará más fácil usar un script para automatizar este proceso. Si es así, consulte [Para configurar el proxy para Microsoft Monitoring Agent mediante un script](../log-analytics/log-analytics-windows-agent.md).

Para configurar el proxy para Microsoft Monitoring Agent mediante el Panel de control:

1. Abra el **Panel de control**.
2. Abra **Microsoft Monitoring Agent**.
3. Haga clic en la pestaña **Configuración de proxy**.
4. Seleccione **Usar un servidor proxy** y escriba la dirección URL y el número de puerto, si uno es necesario. Si el servidor proxy requiere autenticación, escriba el nombre de usuario y la contraseña para acceder al servidor proxy.

  ![proxy](.\media\how-to-npm\11.png)

### <a name="verifyagent"></a>2.4: Comprobación de la conectividad del agente

Puede comprobar fácilmente si los agentes se están comunicando.

1. En un servidor con el agente de supervisión, abra el **Panel de Control**.
2. Abra **Microsoft Monitoring Agent**.
3. Haga clic en la pestaña **Azure Log Analytics (OMS)**.
4. En la columna **Estado**, verá que el agente se conectó correctamente al servicio Operations Management Suite.

  ![status](.\media\how-to-npm\12.png)

### <a name="firewall"></a>2.5: Apertura de los puertos de firewall en los servidores de agente de supervisión

Para usar el protocolo TCP, debe abrir los puertos de firewall a fin de asegurarse de que los agentes de supervisión puedan comunicarse.

Puede ejecutar un script de PowerShell que crea las claves del Registro que necesita Network Performance Monitor, así como crear las reglas del firewall de Windows para permitir que los agentes de supervisión establezcan conexiones TCP entre sí. Las claves del Registro que crea el script también especifican si se deben escribir los registros de depuración y la ruta del archivo de registro. Asimismo, define el puerto TCP del agente empleado para establecer la comunicación. El script establece automáticamente los valores de estas claves, por lo que no debe cambiarlas manualmente.

El puerto 8084 se abre de forma predeterminada. Puede utilizar un puerto personalizado especificando el parámetro "portNumber" en el script. Sin embargo, si lo hace, debe especificar el mismo puerto para todos los servidores en los que se ejecuta el script.

>[!NOTE]
>El script de PowerShell "EnableRules" solo configura reglas del firewall de Windows en el servidor donde se ejecuta. Si tiene un firewall de red, debe asegurarse de que permite el tráfico destinado al puerto TCP que esté utilizando Monitor de rendimiento de red.
>
>

En los servidores de agente, abra una ventana de PowerShell con privilegios administrativos. Ejecute el script [EnableRules](https://gallery.technet.microsoft.com/OMS-Network-Performance-04a66634) de PowerShell (que descargó anteriormente). No utilice ningún parámetro.

  ![PowerShell_Script](.\media\how-to-npm\script.png)

## <a name="opennsg"></a>Paso 3: Configuración de las reglas del grupo de seguridad de red

Para supervisar los servidores de agente que se encuentran en Azure, debe configurar las reglas del grupo de seguridad de red (NSG) para permitir el tráfico TCP en un puerto utilizado por Network Performance Monitor para las transacciones sintéticas. El puerto predeterminado es 8084. Esto permite que un agente de supervisión instalado en la máquina virtual de Azure se comunique con un agente de supervisión local.

Para más información, consulte [Grupos de seguridad de red](../virtual-network/virtual-networks-create-nsg-arm-portal.md).

## <a name="whitelist"></a>Paso 4: Solicitud de poner en la lista blanca el área de trabajo

>[!NOTE]
>Asegúrese de que ha instalado los agentes (tanto el agente de servidor local como el agente de servidor de Azure) y que ha ejecutado el script de PowerShell antes de continuar con este paso.
>
>

Antes de empezar a usar la característica de supervisión de ExpressRoute de Network Performance Monitor, debe solicitar que el área de trabajo esté en la lista blanca. [Haga clic aquí para ir a la página y rellene el formulario de solicitud](https://aka.ms/npmcohort). (Sugerencia: Puede que desee abrir este vínculo en una nueva ventana o pestaña). El proceso de creación de listas blancas puede tardar un día laborable o más. Cuando se complete la creación de listas blancas, recibirá un correo electrónico.

## <a name="setupmonitor"></a>Paso 5: Configuración de Network Performance Monitor para la supervisión de ExpressRoute

>[!WARNING]
>No continúe hasta que el área de trabajo se encuentre en la lista blanca y reciba un correo electrónico de confirmación.
>
>

Después de completar las secciones anteriores y de comprobar que ha estado en la lista blanca, puede configurar la supervisión.

1. Navegue hasta el icono de información general de Network Performance Monitor, vaya a la página **Todos los recursos** y haga clic en el área de trabajo de Network Performance Monitor que se encuentra en la lista blanca.

  ![área de trabajo de Network Performance Monitor](.\media\how-to-npm\npm.png)
2. Haga clic en el icono de introducción de **Network Performance Monitor** para que aparezca el panel. El panel contiene una página de ExpressRoute, que muestra que ExpressRoute está en un estado "no configurado". Haga clic en **Configuración de características** para abrir la página de configuración de Network Performance Monitor.

  ![configuración de características](.\media\how-to-npm\npm2.png)
3. En la página de configuración, navegue a la pestaña "Emparejamientos de ExpressRoute", ubicada en el panel del lado izquierdo. Haga clic en **Detectar ahora**.

  ![detectar](.\media\how-to-npm\13.png)
4. Cuando se completa la detección, verá las reglas para el nombre único del circuito y el nombre de la red virtual. Inicialmente, estas reglas están deshabilitadas. Habilite las reglas, después, seleccione los agentes de supervisión y los valores de umbral.

  ![reglas](.\media\how-to-npm\14.png)
5. Después de habilitar las reglas y de seleccionar los valores y los agentes que desea supervisar, hay una espera entre 30 y 60 minutos aproximadamente para que los valores que se va a empezar a llenarse y los iconos de **Supervisión de ExpressRoute** están disponibles. Cuando se vean los iconos de supervisión, los circuitos ExpressRoute y los recursos de conexión van a estar supervisados por Network Performance Monitor.

  ![iconos de supervisión](.\media\how-to-npm\15.png)

## <a name="explore"></a>Paso 6: Visualización de los iconos de supervisión

### <a name="dashboard"></a>Página Network Performance Monitor

La página Network Performance Monitor contiene una página de ExpressRoute que muestra una introducción del estado de emparejamientos y circuitos de ExpressRoute.

  ![Panel](.\media\how-to-npm\dashboard.png)

### <a name="circuits"></a>Lista de circuitos

Para ver una lista de todos los circuitos ExpressRoute supervisados, haga clic en el icono **Circuitos ExpressRoute**. Puede seleccionar un circuito y ver su estado de mantenimiento, gráficos de tendencias de pérdida de paquetes, uso de ancho de banda y latencia. Los gráficos son interactivos. Puede seleccionar un período de tiempo personalizado para trazar los gráficos. Puede arrastrar el mouse sobre un área del gráfico para acercar y ver los puntos de datos con precisión.

  ![circuit_list](.\media\how-to-npm\circuits.png)

#### <a name="trend"></a>Tendencia de rendimiento, latencia y pérdida

Los gráficos de ancho de banda, latencia y pérdida son interactivos. Puede acercar cualquier sección de estos gráficos con los controles de mouse. También puede ver los datos de ancho de banda, latencia y pérdida para otros intervalos; para ello, haga clic en **Fecha/hora**, que se encuentra debajo del botón Acciones en la parte superior izquierda.

![tendencia](.\media\how-to-npm\16.png)

### <a name="peerings"></a>Lista de emparejamientos

Al hacer clic en el icono **Emparejamientos privados** en el panel, se muestra una lista de todas las conexiones a redes virtuales a través de emparejamientos privados. Aquí, puede seleccionar una conexión de red virtual y ver su estado de mantenimiento, gráficos de tendencias de pérdida de paquetes, uso de ancho de banda y latencia.

  ![lista de circuitos](.\media\how-to-npm\peerings.png)

### <a name="topology"></a>Topología de circuito

Para ver la topología de circuito, haga clic en el icono **Topología**. Esto le llevará a la vista de topología del circuito o emparejamiento seleccionado. El diagrama de topología proporciona la latencia para cada segmento de la red y cada salto de tres capas se representa mediante un nodo del diagrama. Si se hace clic en un salto, se mostrarán más detalles sobre él.
Para aumentar el nivel de visibilidad para incluir saltos locales, mueva el control deslizante debajo de **Filtros**. Si se mueve el control deslizante hacia la izquierda o derecha, se aumenta o disminuye el número de saltos en el gráfico de topología. La latencia por cada segmento está visible, lo que permite un aislamiento más rápido de los segmentos de latencia elevada en la red.

![filters](.\media\how-to-npm\topology.png)

#### <a name="detailed-topology-view-of-a-circuit"></a>Vista de topología detallada de un circuito

Esta vista muestra conexiones de red virtual.
![topología detallada](.\media\how-to-npm\17.png)
