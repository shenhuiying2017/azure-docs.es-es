---
title: "Guía de solución de problemas de Azure Security Center | Microsoft Docs"
description: Este documento ayuda a solucionar problemas en Azure Security Center.
services: security-center
documentationcenter: na
author: YuriDio
manager: swadhwa
editor: 
ms.assetid: 44462de6-2cc5-4672-b1d3-dbb4749a28cd
ms.service: security-center
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/14/2017
ms.author: yurid
translationtype: Human Translation
ms.sourcegitcommit: d8956072460ba8629bb852e7b5d3e5155c3711e3
ms.openlocfilehash: fe2d32e3c20c3e91954a6d00294ec018e8da0f2b


---
# <a name="azure-security-center-troubleshooting-guide"></a>Guía de solución de problemas de Azure Security Center
Esta guía está destinada a profesionales de tecnologías de la información (TI), analistas de seguridad de la información y administradores de la nube cuyas organizaciones utilizan Azure Security Center y necesitan solucionar problemas relacionados con Security Center.

## <a name="troubleshooting-guide"></a>Guía de solución de problemas
Esta guía explica cómo solucionar problemas relacionados con Security Center. La mayoría de las soluciones de problemas en Security Center se realizan examinando primero el [registro de auditoría](https://azure.microsoft.com/updates/audit-logs-in-azure-preview-portal/) del componente afectado. A través de los registros de auditoría puede determinar:

* Qué operaciones se han llevado a cabo
* Quién inició la operación
* Cuándo tuvo lugar la operación
* El estado de la operación
* Los valores de otras propiedades que podrían ayudarle en la investigación de la operación

El registro de auditoría contiene todas las operaciones de escritura (PUT, POST, DELETE) realizadas en los recursos, pero no incluye las operaciones de lectura (GET).

## <a name="troubleshooting-monitoring-agent-installation-in-windows"></a>Solución de problemas de instalación del agente de supervisión en Windows
El agente de supervisión de Security Center se utiliza para realizar la recopilación de datos. Una vez habilitada la recopilación de datos e instalado correctamente el agente en el equipo de destino, los siguientes procesos deben estar en ejecución:

* ASMAgentLauncher.exe: agente de supervisión de Azure 
* ASMMonitoringAgent.exe: extensión Supervisión de seguridad de Azure
* ASMSoftwareScanner.exe: administrador de análisis de Azure

La extensión Supervisión de seguridad de Azure busca diversas configuraciones relevantes para la seguridad y recopila registros de seguridad de la máquina virtual. El administrador de análisis se utilizará como un detector de revisiones.

Si la instalación se realizó correctamente, debería ver una entrada similar a la siguiente en los Registros de auditoría para la máquina virtual de destino:

![Registros de auditoría](./media/security-center-troubleshooting-guide/security-center-troubleshooting-guide-fig1.png)

También puede obtener más información sobre el proceso de instalación leyendo los registros del agente, que se encuentran en *%systemdrive%\windowsazure\logs* (ejemplo: C:\WindowsAzure\Logs).

> [!NOTE]
> Si el comportamiento del agente de Azure Security Center no es correcto, debe reiniciar la máquina virtual de destino, ya que no hay ningún comando para detener e iniciar al agente.


Si sigue experimentando problemas con la recopilación de datos, puede completar los pasos siguientes para desinstalar al agente:

1. En **Azure Portal**, seleccione la máquina virtual que experimenta problemas de recopilación de datos y haga clic en **Extensiones**.
2. Haga clic con el botón derecho en **Microsoft.Azure.Security.Monitoring** y seleccione **Desinstalar**.

![Eliminación del agente](./media/security-center-troubleshooting-guide/security-center-troubleshooting-guide-fig4.png)

La extensión Supervisión de seguridad de Azure debería reinstalarse automáticamente en unos minutos.

## <a name="troubleshooting-monitoring-agent-installation-in-linux"></a>Solución de problemas de instalación del agente de supervisión en Linux
Al solucionar problemas de instalación del agente de máquina virtual en un sistema Linux, debe asegurarse de que la extensión se descargó en /var/lib/waagent/. Puede ejecutar el comando siguiente para comprobar si se ha instalado:

`cat /var/log/waagent.log` 

Los otros archivos de registro que puede consultar para la solución de problemas son: 

* /var/log/mdsd.err
* /var/log/azure/

En un sistema de trabajo debería ver una conexión con el proceso mdsd en TCP 29130. Se trata del registro syslog comunicándose con el proceso de mdsd. Para validar este comportamiento, ejecute el comando siguiente:

`netstat -plantu | grep 29130`

## <a name="troubleshooting-problems-loading-the-dashboard"></a>Solución de problemas al cargar el panel

Si experimenta problemas al cargar el panel de Security Center, asegúrese de que el usuario que registra la suscripción a Security Center (es decir, el primer usuario que abrió Security Center con la suscripción) y el usuario que desearía activar la recopilación de datos son *propietarios* o *colaboradores* en la suscripción. A partir de ese momento, los usuarios *lectores* en la suscripción también podrán ver el panel, las alertas, las recomendaciones y las directivas.

## <a name="contacting-microsoft-support"></a>Contacto con el soporte técnico de Microsoft
Algunos problemas pueden identificarse mediante las instrucciones proporcionadas en este artículo; también puede encontrar otros en el [foro](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureSecurityCenter)público de Security Center. Sin embargo, si necesita más información para solucionar el problema, puede abrir una nueva solicitud de soporte técnico mediante **Azure Portal**, como se indica a continuación: 

![Soporte técnico de Microsoft](./media/security-center-troubleshooting-guide/security-center-troubleshooting-guide-fig2.png)

## <a name="see-also"></a>Consulte también
En este documento ha aprendido a configurar directivas de seguridad en el Centro de seguridad de Azure. Para obtener más información sobre el Centro de seguridad de Azure, consulte los siguientes recursos:

* [Guía de planeamiento y operaciones de Azure Security Center](security-center-planning-and-operations-guide.md) : aprenda a planear y conozca las consideraciones de diseño necesarias para usar Azure Security Center.
* [Supervisión del estado de seguridad en Azure Security Center](security-center-monitoring.md) : obtenga información sobre cómo supervisar el estado de los recursos de Azure.
* [Administración y respuesta a las alertas de seguridad en Azure Security Center](security-center-managing-and-responding-alerts.md) : obtenga información sobre cómo administrar y responder a alertas de seguridad.
* [Supervisión de las soluciones de asociados con Azure Security Center](security-center-partner-solutions.md) : aprenda a supervisar el estado de mantenimiento de las soluciones de asociados.
* [Preguntas más frecuentes sobre Azure Security Center](security-center-faq.md) : encuentre las preguntas más frecuentes sobre el uso del servicio.
* [Blog de seguridad de Azure](http://blogs.msdn.com/b/azuresecurity/) : encuentre entradas de blog sobre el cumplimiento y la seguridad de Azure.




<!--HONumber=Feb17_HO3-->


