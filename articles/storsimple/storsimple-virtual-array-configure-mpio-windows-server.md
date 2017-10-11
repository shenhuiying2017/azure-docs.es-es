---
title: "Configuración de MPIO en un host conectado a StorSimple Virtual Array | Microsoft Docs"
description: "Describe cómo configurar la E/S de múltiples rutas (MPIO) para una instancia de StorSimple Virtual Array conectada a un host que ejecuta Windows Server 2012 R2."
services: storsimple
documentationcenter: 
author: alkohli
manager: carmonm
editor: 
ms.assetid: 5b7a7f99-ee5b-4b7d-ab32-483a5a1fa504
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 05/01/2017
ms.author: alkohli
ms.openlocfilehash: c75c6ed40754aee964e2b68f4f569dc1422507f2
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/11/2017
---
# <a name="configure-multipath-io-on-windows-server-host-for-the-storsimple-virtual-array"></a>Configuración de E/S de múltiples rutas en el host de Windows Server para la matriz Virtual de StorSimple
## <a name="overview"></a>Información general
Este artículo describe cómo instalar la característica E/S de múltiples rutas (MPIO) en el host de Windows Server, aplicar una configuración específica solo para volúmenes de StorSimple y, a continuación, comprobar MPIO para volúmenes de StorSimple. En el procedimiento se supone que la matriz virtual de StorSimple 1200 con dos interfaces de red está conectada a un host de Windows Server con dos interfaces de red. La información contenida en este artículo se aplica solo a la matriz virtual. Para más información sobre los dispositivos de la serie 8000 de StorSimple, vaya a [Configuración de MPIO para el host de StorSimple](storsimple-configure-mpio-windows-server.md). 

La característica MPIO en Windows Server ayuda a crear configuraciones de almacenamiento de alta disponibilidad y de tolerancia a fallos. MPIO usa componentes de ruta de acceso físicas redundantes (es decir, adaptadores, cables y conmutadores) para crear rutas de acceso lógicas entre el servidor y el dispositivo de almacenamiento. Si se produce un error de componente, lo que haría que la ruta de acceso lógica no funcionara, la lógica de múltiples rutas usa una ruta alternativa para E/S a fin de que las aplicaciones puedan seguir teniendo acceso a los datos. Además, dependiendo de la configuración de MPIO, puede mejorar el rendimiento al volver a equilibrar la carga entre estas rutas de acceso. Para más información, consulte [Introducción a E/S de múltiples rutas](https://technet.microsoft.com/library/cc725907.aspx "Introducción a E/S de múltiples rutas and features").

Para la alta disponibilidad de la solución StorSimple, configure MPIO en los hosts de Windows Server conectados a StorSimple Virtual Array (modelo 1200). Los servidores de host pueden tolerar errores de interfaz, red o vínculo. 

Realice estos pasos para configurar MPIO: 

* Requisitos previos de configuración
* Paso 1: instalar MPIO en el host de Windows Server
* Paso 2: configurar MPIO para volúmenes de StorSimple
* Paso 3: montar los volúmenes de StorSimple en el host

En las siguientes secciones se detallan cada uno de estos pasos.

## <a name="prerequisites"></a>Requisitos previos
En esta sección se detallan los requisitos previos de configuración para la matriz virtual y el host de Windows Server.

### <a name="on-windows-server-host"></a>En el host de Windows Server
* Asegúrese de que el host de Windows Server tiene dos interfaces de red habilitadas.

### <a name="on-storsimple-virtual-array"></a>En StorSimple Virtual Array
* La matriz virtual debe estar configurada como un servidor iSCSI. Para más información, consulte [Implementación de StorSimple Virtual Array: configurar el dispositivo virtual como servidor iSCSI](storsimple-virtual-array-deploy3-iscsi-setup.md). Debe habilitarse una o más interfaces de red en la matriz.   
* Las interfaces de red en la matriz virtual deben ser accesibles desde el host de Windows Server.
* Deben crearse uno o más volúmenes en la matriz virtual de StorSimple. Para más información, consulte [Adición de un volumen](storsimple-virtual-array-deploy3-iscsi-setup.md#step-3-add-a-volume) en StorSimple Virtual Array. En este procedimiento, hemos creado tres volúmenes (uno anclado localmente y dos volúmenes en capas, tal como se muestra a continuación) en la matriz virtual.
  
    ![mpio0](./media/storsimple-virtual-array-configure-mpio-windows-server/mpio0.png)

### <a name="hardware-configuration-for-storsimple-virtual-array"></a>Configuración de hardware para StorSimple Virtual Array
La siguiente ilustración muestra la configuración de hardware para múltiples rutas de alta disponibilidad y equilibrio de carga para el host de Windows Server y StorSimple Virtual Array usados en este procedimiento.

![Configuración de hardware de mpio](./media/storsimple-virtual-array-configure-mpio-windows-server/1200hardwareconfig.png)

Como se muestra en la ilustración anterior:

* StorSimple Virtual Array aprovisionado en Hyper-V es un dispositivo activo de nodo único configurado como un servidor iSCSI.
* Se habilitan dos interfaces de red virtual en la matriz. En la interfaz de usuario web local de su matriz virtual, compruebe que hay dos interfaces de red habilitadas, para lo que debe navegar a **Configuración de red**, como se muestra a continuación:
  
    ![Interfaces de red habilitadas en 1200](./media/storsimple-virtual-array-configure-mpio-windows-server/mpio9.png)
  
    Tenga en cuenta las direcciones IPv4 de las interfaces de red habilitadas (Ethernet, Ethernet 2 de forma predeterminada) y guárdelas para su uso posterior en el host.
* Se habilitan dos interfaces de red en el host de Windows Server. Si las interfaces conectadas para el host y la matriz están en la misma subred, habrá 4 rutas de acceso disponibles. Este era el caso de este procedimiento. Sin embargo, si cada interfaz de red en la interfaz en la interfaz del host y la matriz están en una subred IP diferente (y no enrutable), solo estarán disponibles dos rutas de acceso.

## <a name="step-1-install-mpio-on-the-windows-server-host"></a>Paso 1: instalar MPIO en el host de Windows Server
MPIO es una característica opcional en Windows Server y no se instala de forma predeterminada. Se debe instalar como una característica a través del Administrador del servidor. Haga lo siguiente para instalar esta característica en el host de Windows Server.

[!INCLUDE [storsimple-install-mpio-windows-server-host](../../includes/storsimple-install-mpio-windows-server-host.md)]

## <a name="step-2-configure-mpio-for-storsimple-volumes"></a>Paso 2: configurar MPIO para volúmenes de StorSimple
MPIO tiene que configurarse de forma que identifique los volúmenes de StorSimple. Haga lo siguiente para configurar MPIO para reconocer volúmenes de StorSimple.

[!INCLUDE [storsimple-configure-mpio-volumes](../../includes/storsimple-configure-mpio-volumes.md)]

## <a name="step-3-mount-storsimple-volumes-on-the-host"></a>Paso 3: montar los volúmenes de StorSimple en el host
Después de configurar MPIO en Windows Server, los volúmenes creados en la matriz de StorSimple se pueden montar para que puedan usar MPIO para la redundancia. Haga lo siguiente para montar un volumen.

#### <a name="to-mount-volumes-on-the-host"></a>Para montar volúmenes en el host
1. Abra la ventana **Propiedades del iniciador iSCSI** en el host de Windows Server. Vaya a **Administrador del servidor > Panel > Herramientas > Iniciador iSCSI**.
2. En el cuadro de diálogo **Propiedades del iniciador iSCSI**, haga clic en **Detección** y, después, en **Detectar portal de destino**.
3. Haga lo siguiente en el cuadro de diálogo **Detectar portal de destino** :
   
    1. Escriba la dirección IP de la primera interfaz de red habilitada en StorSimple Virtual Array. De forma predeterminada, el valor de configuración sería **Ethernet**. 
    2. Haga clic en **Aceptar** para volver al cuadro de diálogo **Propiedades del iniciador iSCSI**.
     
    > [!IMPORTANT]
    > Si está usando una red privada para las conexiones iSCSI, escriba la dirección IP del puerto DATA que esté conectado a la red privada.
     
4. Repita los pasos 2 y 3 para una segunda interfaz de red (por ejemplo, Ethernet 2) en la matriz. 
5. Seleccione la pestaña **Destinos** del cuadro de diálogo **Propiedades del iniciador iSCSI**. Para la matriz virtual, debería ver la superficie de cada volumen como un destino en **Destinos detectados**. En este caso, se detectan tres destinos (correspondiente a tres volúmenes).
   
    ![mpio1](./media/storsimple-virtual-array-configure-mpio-windows-server/mpio1.png)
6. Haga clic en **Conectar** para establecer una sesión iSCSI con StorSimple Virtual Array. Aparecerá el cuadro de diálogo **Conectarse al destino** . Active la casilla **Habilitar múltiples rutas** . Haga clic en **Avanzadas**.
   
    ![mpio2](./media/storsimple-virtual-array-configure-mpio-windows-server/mpio2.png)
7. Haga lo siguiente en el cuadro de diálogo **Configuración avanzada** :                                        
   
    1. En la lista desplegable **Adaptador local**, seleccione **Iniciador iSCSI de Microsoft**.
    2. En la lista desplegable **IP de iniciador** , seleccione la dirección IP del host.
    3. En la lista desplegable **IP del portal de destino** , seleccione la dirección IP de la interfaz de la matriz.
    4. Haga clic en **Aceptar** para volver al cuadro de diálogo **Propiedades del iniciador iSCSI**.
     
        ![mpio3](./media/storsimple-ova-configure-mpio-windows-server/mpio3.png)

8. Haga clic en **Propiedades**. 
   
    ![mpio4](./media/storsimple-ova-configure-mpio-windows-server/mpio4.png)

9. En el cuadro de diálogo **Propiedades**, haga clic en **Agregar sesión**.
   
   ![mpio5](./media/storsimple-ova-configure-mpio-windows-server/mpio5.png)
10. En el cuadro de diálogo **Conectarse al destino**, active la casilla **Habilitar múltiples rutas**. Haga clic en **Avanzadas**.
11. En el cuadro de diálogo **Configuración avanzada** :                                        
    
    1. En la lista desplegable **Adaptador local** , seleccione Iniciador iSCSI de Microsoft.

    2. En la lista desplegable **IP de iniciador** , seleccione la dirección IP correspondiente al host. En este caso, estamos conectando dos interfaces de red en la matriz o a una única interfaz de red en el host. Por lo tanto, esta interfaz es la misma que la proporcionada para la primera sesión.

    3. En la lista desplegable **IP del portal de destino** , seleccione la dirección IP de la segunda interfaz de datos habilitada en la matriz.

    4. Haga clic en **Aceptar** para volver al cuadro de diálogo Propiedades del iniciador iSCSI. Agregó una segunda sesión al destino.
      
       ![mpio11](./media/storsimple-virtual-array-configure-mpio-windows-server/mpio11.png)
    
    5. Después de agregar las sesiones (rutas de acceso) que quiera, en el cuadro de diálogo **Propiedades del iniciador iSCSI**, seleccione el destino y haga clic en **Propiedades**. En la pestaña Sesiones del cuadro de diálogo **Propiedades** , fíjese en los cuatro identificadores de sesión, que se corresponden con las posibles permutaciones de ruta de acceso. Para cancelar una sesión, active la casilla situada junto a un identificador de sesión y, luego, haga clic en **Desconectar**.

    6. Para ver los dispositivos presentados en las sesiones, seleccione la pestaña **Dispositivos** . Para configurar la directiva MPIO de un dispositivo seleccionado, haga clic en **MPIO**.

    7. Aparecerá el cuadro de diálogo **Detalles**. En la pestaña **MPIO**, puede seleccionar la configuración de **Directiva de equilibrio de carga** apropiada. También puede ver el tipo de ruta de acceso **Activo** o **En espera**.
12. Repita los pasos del 8 al 11 para agregar más sesiones (rutas de acceso) al destino. Con dos interfaces en el host y dos en la matriz virtual, puede agregar un total de cuatro sesiones para cada destino. 
    
    ![mpio14](./media/storsimple-virtual-array-configure-mpio-windows-server/mpio14.png)
13. Debe repetir estos pasos para cada volumen (superficies como destino).
    
    ![mpio 15](./media/storsimple-virtual-array-configure-mpio-windows-server/mpio15.png)
14. Abra **Administración de equipos**; para ello, vaya a **Administrador del servidor > Panel > Administración de equipos**. En el panel izquierdo, haga clic en **Almacenamiento > Administración de discos**. Los volúmenes creados en StorSimple Virtual Array que pueda ver este host aparecerán en **Administración de discos** como discos nuevos.
15. Inicialice el disco y cree otro volumen. Durante el proceso de formato, seleccione un tamaño de unidad de asignación (AUS) de 64 KB. Repita el proceso para todos los volúmenes disponibles.
    
    ![Administración de discos](./media/storsimple-virtual-array-configure-mpio-windows-server/mpio20.png)
16. En **Administración de discos**, haga clic con el botón derecho en el **disco** y seleccione **Propiedades**.
17. En el cuadro de diálogo **Propiedades del dispositivo de disco de múltiples rutas**, haga clic en la pestaña **MPIO**.
    
    ![Propiedades de disco](./media/storsimple-virtual-array-configure-mpio-windows-server/mpio21.png)
18. En la sección **Nombre DSM**, haga clic en **Detalles** y compruebe que los parámetros están establecidos en los valores predeterminados. Los parámetros predeterminados son los siguientes:
    
    * Período de comprob. de ruta = 30
    * Número de reintentos = 3
    * Período de eliminación PDO = 20
    * Intervalo de reintento = 1
    * Comprobación de ruta habilitada = Sin seleccionar
    
    > [!NOTE]
    > **No modifique los parámetros predeterminados.**
   
## <a name="next-steps"></a>Pasos siguientes
Más información sobre el [uso del servicio StorSimple Device Manager para administrar StorSimple Virtual Array](storsimple-virtual-array-manager-service-administration.md).

