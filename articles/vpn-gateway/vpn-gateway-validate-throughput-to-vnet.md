---
title: "Validación del rendimiento de la VPN en una red Microsoft Azure Virtual Network | Microsoft Docs"
description: "El objetivo de este documento es ayudar a un usuario a validar el rendimiento de red de sus recursos locales en una máquina virtual de Azure."
services: vpn-gateway
documentationcenter: na
author: chadmath
manager: jasmc
editor: 
tags: azure-resource-manager,azure-service-management
ms.assetid: 
ms.service: vpn-gateway
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/08/2017
ms.author: radwiv;chadmat;genli
ms.openlocfilehash: e7e3c641791e7c72f5c2d6f8ecf674d1d7ee7ffa
ms.sourcegitcommit: b07d06ea51a20e32fdc61980667e801cb5db7333
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/08/2017
---
# <a name="how-to-validate-vpn-throughput-to-a-virtual-network"></a>Validación del rendimiento de la VPN en una red virtual

Una conexión a la puerta de enlace de la VPN le permite establecer una conectividad segura y entre entornos locales entre su red virtual dentro de Azure y la infraestructura local de TI.

Este artículo muestra cómo validar el rendimiento de red de los recursos locales en una máquina virtual de Azure. También proporciona orientación para la solución de errores.

>[!NOTE]
>Este artículo se ha creado para ayudar a diagnosticar problemas comunes y solucionarlos. Si no puede resolver el problema mediante el uso de la siguiente información, [póngase en contacto con soporte técnico](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).
>
>

## <a name="overview"></a>Información general

La conexión a la puerta de enlace de la VPN afecta a los siguientes componentes:

- Dispositivo VPN local (vea una lista de [dispositivos VPN validados)](vpn-gateway-about-vpn-devices.md#devicetable).
- Internet público
- Azure VPN Gateway
- MV de Azure

El siguiente diagrama muestra la conectividad lógica de una red local en una red virtual de Azure a través de VPN.

![Conectividad lógica de la red del cliente en la red MSFT mediante VPN](./media/vpn-gateway-validate-throughput-to-vnet/VPNPerf.png)

## <a name="calculate-the-maximum-expected-ingressegress"></a>Cálculo de la entrada/salida máxima esperada

1.  Determine los requisitos de rendimiento de la línea de base de la aplicación.
2.  Establezca los límites de rendimiento de la puerta de enlace de VPN de Azure. Para obtener ayuda, vea la sección "Rendimiento agregado por el tipo de VPN y SKU" de [Planeamiento y diseño de VPN Gateway](vpn-gateway-plan-design.md).
3.  Determine la [Guía de rendimiento de la máquina virtual de Azure](../virtual-machines/virtual-machines-windows-sizes.md) para el tamaño de la máquina virtual.
4.  Establezca el ancho de banda del proveedor de servicios de Internet (ISP).
5.  Calcule el rendimiento esperado: menor ancho de banda de (VM, puerta de enlace e ISP) * 0,8.

Si el rendimiento calculado no cumple con los requisitos de rendimiento de línea de base de la aplicación, debe aumentar el ancho de banda del recurso que identificó como el cuello de botella. Para cambiar el tamaño de una instancia de Azure VPN Gateway, vea [Cambio de una SKU de puerta de enlace](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpn-gateway-settings.md#gwsku). Para cambiar el tamaño de una máquina virtual, vea [Cambio del tamaño de una VM](../virtual-machines/virtual-machines-windows-resize-vm.md). Si no obtiene el ancho de banda de Internet previsto, también puede ponerse en contacto con su ISP.

## <a name="validate-network-throughput-by-using-performance-tools"></a>Validación del rendimiento de red mediante el uso de herramientas de rendimiento

Esta validación debe realizarse durante las horas de poca actividad, ya que la saturación de rendimiento del túnel VPN durante las pruebas provoca que no se produzcan resultados precisos.

La herramienta que se usa para esta prueba es iPerf, que funciona en Windows y Linux, y tiene los modos de cliente y servidor. Está limitada a 3 GB/s para máquinas virtuales de Windows.

Esta herramienta no lleva a cabo operaciones de lectura/escritura en el disco. Solo se produce el tráfico TCP generado automáticamente de un extremo a otro. Generó estadísticas basadas en la experimentación que mide el ancho de banda disponible entre los nodos de cliente y servidor. Cuando se realiza la prueba entre dos nodos, uno actúa como el servidor y el otro como un cliente. Una vez completada esta prueba, se recomienda que invierta los roles para probar el rendimiento de carga y descarga en ambos nodos.

### <a name="download-iperf"></a>Descarga de iPerf
Descargue [iPerf](https://iperf.fr/download/iperf_3.1/iperf-3.1.2-win64.zip). Para más información, vea la [documentación de Ambari](https://iperf.fr/iperf-doc.php).

 >[!NOTE]
 >Los productos de terceros que se tratan en este artículo están fabricados por compañías independientes de Microsoft. Microsoft no otorga ninguna garantía, implícita o de otro tipo, sobre el rendimiento o la confiabilidad de estos productos.
 >
 >

### <a name="run-iperf-iperf3exe"></a>Ejecución de iPerf (iperf3.exe)
1. Habilite una regla NSG/ACL que permita el tráfico (para la prueba de la dirección IP pública en la máquina virtual de Azure).

2. En ambos nodos, habilite una excepción de firewall para el puerto 5001.

    **Windows:** Ejecute el comando siguiente como administrador:

    ```CMD
    netsh advfirewall firewall add rule name="Open Port 5001" dir=in action=allow protocol=TCP localport=5001
    ```

    Para quitar la regla cuando la prueba esté completa, ejecute este comando:

    ```CMD
    netsh advfirewall firewall delete rule name="Open Port 5001" protocol=TCP localport=5001
    ```
    </br>
    **Azure Linux**: las imágenes de Azure Linux tienen firewalls permisivos. Si hay una aplicación que realiza la escucha en un puerto, se permite el tráfico. Las imágenes personalizadas que se protegen pueden necesitar puertos abiertos de forma explícita. Los firewalls de nivel de sistema operativo Linux comunes incluyen `iptables`, `ufw`, o `firewalld`.

3. En el nodo de servidor, cambie al directorio donde se extrae iperf3.exe. A continuación, ejecute iPerf en el modo de servidor y configúrela para que escuche el puerto 5001 como los siguientes comandos:

     ```CMD
     cd c:\iperf-3.1.2-win65

     iperf3.exe -s -p 5001
     ```

4. En el nodo de cliente, cambie al directorio donde se extrae la herramienta iperf y luego ejecute el siguiente comando:

    ```CMD
    iperf3.exe -c <IP of the iperf Server> -t 30 -p 5001 -P 32
    ```

    El cliente está induciendo tráfico en el puerto 5001 al servidor durante 30 segundos. La marca '-P ' que indica que estamos usando 32 conexiones simultáneas en el nodo de servidor.

    La siguiente pantalla muestra el resultado de este ejemplo:

    ![Salida](./media/vpn-gateway-validate-throughput-to-vnet/06theoutput.png)

5. (OPCIONAL) Para conservar los resultados de pruebas, ejecute este comando:

    ```CMD
    iperf3.exe -c IPofTheServerToReach -t 30 -p 5001 -P 32  >> output.txt
    ```

6. Después de completar los pasos anteriores, ejecute los mismos pasos con los roles invertidos, de manera que el nodo de servidor se ahora el cliente y viceversa.

## <a name="address-slow-file-copy-issues"></a>Solución de problemas de copia de archivos lenta
Puede experimentar una copia de archivos lenta cuando use el Explorador de Windows o arrastre suelte en una sesión RDP. Este problema suele ser debido a uno o ambos de los siguientes factores:

- Las aplicaciones de copia de archivos, como el Explorador de Windows y RDP, no utilizan varios subprocesos al copiar archivos. Para mejorar el rendimiento, utilice una aplicación de copia de archivos de multiproceso como [Richcopy](https://technet.microsoft.com/en-us/magazine/2009.04.utilityspotlight.aspx) para copiar archivos mediante 16 o 32 subprocesos. Para cambiar el número de subprocesos de copia de archivos en Richcopy, haga clic en **Acción** > **Opciones de copia** > **Copia de archivos**.<br><br>
![Problemas de copia de archivos lenta](./media/vpn-gateway-validate-throughput-to-vnet/Richcopy.png)<br>
- Velocidad de lectura/escritura del disco de VM insuficiente. Para más información, vea [Solución de problemas de Azure Storage](../storage/common/storage-e2e-troubleshooting.md).

## <a name="on-premises-device-external-facing-interface"></a>Interfaz con orientación externa del dispositivo local
Si la dirección IP orientada a Internet del dispositivo VPN local se incluye en la definición de la [red local](vpn-gateway-howto-site-to-site-resource-manager-portal.md#LocalNetworkGateway) en Azure, puede experimentar la incapacidad para ofrecer la VPN, desconexiones esporádicas o problemas de rendimiento.

## <a name="checking-latency"></a>Comprobación de la latencia
Use tracert para realizar un seguimiento del dispositivo perimetral de Microsoft Azure para determinar si hay retrasos superiores a 100 ms entre saltos.

Desde la red local, ejecute *tracert* en la dirección VIP de la máquina virtual o puerta de enlace de Azure. Cuando se devuelva solo *, habrá alcanzado el borde de Azure. Cuando se devuelvan nombres DNS que incluyan "MSN", habrá alcanzado la red troncal de Microsoft.<br><br>
![Comprobación de la latencia](./media/vpn-gateway-validate-throughput-to-vnet/08checkinglatency.png)

## <a name="next-steps"></a>Pasos siguientes
Para obtener más información, consulte los siguientes artículos:

- [Optimización del rendimiento de red en las máquinas virtuales de Azure](../virtual-network/virtual-network-optimize-network-bandwidth.md)
- [Ayuda y soporte técnico de Microsoft](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)
