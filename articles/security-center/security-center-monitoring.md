<properties
   pageTitle="Supervisión del estado de seguridad en el Centro de seguridad de Azure | Microsoft Azure"
   description="Este documento le ayuda a comenzar a trabajar con las funcionalidades de supervisión en el Centro de seguridad de Azure."
   services="security-center"
   documentationCenter="na"
   authors="YuriDio"
   manager="swadhwa"
   editor=""/>

<tags
   ms.service="security-center"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="12/16/2015"
   ms.author="yurid"/>

#Supervisión del estado de seguridad en el Centro de seguridad de Azure
Este documento le ayuda a usar las funcionalidades de supervisión en el Centro de seguridad de Azure para supervisar el cumplimiento de las directivas.

> [AZURE.NOTE]La información de este documento se aplica a la versión preliminar del Centro de seguridad de Azure.

## ¿Qué es el Centro de seguridad de Azure?
El Centro de seguridad de Azure ayuda a evitar, detectar y responder a amenazas con más visibilidad y control de la seguridad en sus recursos de Azure. Ofrece administración de directivas y supervisión de la seguridad integrada en las suscripciones, ayuda a detectar las amenazas que podrían pasar desapercibidas y funciona con un amplio ecosistema de soluciones de seguridad.

##¿Qué es la supervisión del estado de seguridad?
Con frecuencia se piensa que supervisar es observar y esperar que se produzca un evento, para así poder reaccionar ante la situación. La supervisión de seguridad se refiere a contar con una estrategia proactiva que audita los recursos a fin de identificar los sistemas que no cumplen con los estándares o los procedimientos recomendados de la organización.

##Supervisión del estado de seguridad
Después de habilitar las [directivas de seguridad](security-center-policies.md) de los recursos de la suscripción, el Centro de seguridad de Azure analizará la seguridad de los recursos para identificar posibles vulnerabilidades. A pesar de que la información sobre la configuración de la red está disponible de inmediato, es posible que demore una hora o más en tener a su disposición la información sobre la configuración de las máquinas virtuales, como el estado de las actualizaciones de seguridad y la configuración del SO. Puede consultar el estado de seguridad de sus recursos, además de cualquier problema que exista, en las hojas de Estado de seguridad de los recursos. También puede ver una lista de esos problemas en las hojas de Recomendaciones.

Para más información sobre cómo aplicar las recomendaciones, lea [Implementación de recomendaciones de seguridad en el Centro de seguridad de Azure](security-center-recommendations.md).

El icono **Estado de los recursos** le permite supervisar el estado de seguridad de los recursos. Verá una serie de problemas con una gravedad alta y media que requieren atención, tal como se muestra a continuación:

![Estado de los recursos](./media/security-center-monitoring/security-center-monitoring-fig1.png)

Las directivas de seguridad habilitadas afectarán los tipos de controles que se supervisan aquí. Si el Centro de seguridad de Azure identifica una vulnerabilidad que se debe abordar, como una VM donde faltan actualizaciones de seguridad o una subred sin un [grupo de seguridad de red](virtual-networks-nsg.md), aparecerá identificada aquí.

###Máquina virtual
Cuando hace clic en **Máquinas virtuales** en el icono **Estado de los recursos**, se abrirá la hoja **Máquinas virtuales** con más detalles sobre los pasos de incorporación y prevención, además de una lista de las VM que supervisa el Centro de seguridad de Azure, tal como aparece a continuación:

![Actualización del sistema faltante por VM](./media/security-center-monitoring/security-center-monitoring-fig2.png)

Cuando se abra esta hoja, verá tres secciones. En cada sección, puede seleccionar una opción individual para ver más detalles sobre el paso recomendado para abordar el problema.

Por ejemplo, si hace clic en **Actualizaciones faltantes del sistema** en **Pasos de prevención**, se abrirá la hoja **Actualizaciones faltantes del sistema** con la lista de VM donde faltan revisiones y el nivel de gravedad de cada una, tal como se muestra a continuación:

![Prevención de actualización del sistema faltante](./media/security-center-monitoring/security-center-monitoring-fig3.png)

Para ver los detalles de las actualizaciones recomendadas, haga clic en el nombre de la VM. Se abre una hoja nueva para esa VM con la lista de las actualizaciones que faltan:

![Actualización del sistema recomendada por VM](./media/security-center-monitoring/security-center-monitoring-fig4.png)

> [AZURE.NOTE]Las recomendaciones de seguridad son las mismas que aparecen en la hoja Recomendaciones. Vea el artículo [Implementación de recomendaciones de seguridad en el Centro de seguridad de Azure](security-center-recommendations.md) para más información sobre cómo solucionar las recomendaciones. Esto no se aplica solo a las VM, sino que también a todos los recursos disponibles en el icono Estado de los recursos.

###Redes
El estado de prevención para las redes muestra las redes virtuales que supervisa el Centro de seguridad de Azure. Cuando haga clic en **Redes** en el icono Estado de los recursos, se abrirá la hoja **Redes** con más detalles, tal como aparece a continuación:

![Actualización del sistema recomendada por VM](./media/security-center-monitoring/security-center-monitoring-fig5.png)

De manera similar a la información de estado de los recursos de las máquinas virtuales, esta hoja brinda una lista resumida de los problemas en la parte superior de la hoja y una lista de las redes supervisadas en la parte inferior.

En la sección de desglose del estado de las redes, aparecen los pasos de prevención, como [ACL en puntos de conexión](virtual-machines-set-up-endpoints.md) no habilitados, [Grupos de seguridad de red](virtual-networks-nsg.md) no habilitados y Subredes en buen estado. Puede hacer clic en cualquiera de estos pasos para obtener más detalles y llevar a cabo otras acciones para solucionar el problema.

Un ejemplo de esto es la alerta **NSG en subredes no habilitados**. Si hace clic en esta alerta, se abrirá la hoja **Configurar grupos de seguridad de red faltantes para subredes**. Ahí verá una descripción de una subred que no tiene habilitado el grupo de seguridad de red, además de la lista de las tarjetas de interfaz de red que pertenecen a esta subred. Seleccione la subred donde desea aplicar el grupo de seguridad y haga clic en la opción **Configurar NSG**. Se abrirá la hoja **Elegir grupo de seguridad de red**, tal como aparece a continuación:

![Actualización del sistema recomendada por VM](./media/security-center-monitoring/security-center-monitoring-fig6.png)

###SQL
Cuando hace clic en **SQL** en el icono **Estado de los recursos**, se abrirá la hoja **SQL** con las recomendaciones relacionadas a problemas como una auditoría no habilitada, un cifrado de datos transparente no habilitado y el estado general de la base de datos. La parte inferior de la hoja muestra el desglose de SQL Server y las bases de datos que pertenecen a cada servidor, tal como aparece a continuación:

![Actualización del sistema recomendada por VM](./media/security-center-monitoring/security-center-monitoring-fig7.png)

Puede hacer clic en cualquiera de estas recomendaciones para obtener más detalles y llevar a cabo otras acciones para solucionar el problema. El siguiente ejemplo muestra la expansión de la habilitación de la auditoría en los servidores de SQL Server.

![Actualización del sistema recomendada por VM](./media/security-center-monitoring/security-center-monitoring-fig8.png)

###Aplicaciones
Si la carga de trabajo de Azure tiene aplicaciones en las [VM del Administrador de recursos](resource-manager-deployment-model.md) con puertos web expuestos (puertos TCP 80 y 443), el Centro de seguridad de Azure puede supervisarlos para identificar posibles problemas de seguridad y recomendar pasos para su corrección. Cuando haga clic en el icono **Aplicaciones**, se abrirá la hoja **Aplicaciones** con una serie de recomendaciones en la sección de pasos de prevención y, además, mostrará el desglose de la aplicación por host/IP virtual, tal como aparece a continuación:

![Actualización del sistema recomendada por VM](./media/security-center-monitoring/security-center-monitoring-fig9.png)

Al igual que lo que ocurre con las otras recomendaciones para los recursos anteriores, puede hacer clic en ella para ver más detalles sobre el problema y cómo solucionarlo. El ejemplo que aparece en la ilustración siguiente es una aplicación que se identificó como **Aplicación web insegura**. Cuando selecciona la aplicación que se consideró como no segura, se abrirá otra hoja con la opción disponible que, en este caso, es habilitar el Firewall de aplicaciones web.

![Actualización del sistema recomendada por VM](./media/security-center-monitoring/security-center-monitoring-fig10.png)

## Pasos siguientes
En este documento, aprendió a usar las funcionalidades de supervisión en el Centro de seguridad de Azure. Para obtener más información sobre el Centro de seguridad de Azure, consulte los siguientes recursos:

- [Establecimiento de directivas de seguridad en el Centro de seguridad de Azure](security-center-policies.md): obtenga información sobre cómo configurar los ajustes de seguridad en el Centro de seguridad de Azure.
- [Administración y respuesta a las alertas de seguridad en el Centro de seguridad de Azure](security-center-managing-and-responding-alerts.md): obtenga información sobre cómo administrar y responder a alertas de seguridad.
- [Preguntas más frecuentes sobre el Centro de seguridad de Azure](security-center-faq.md): busque preguntas frecuentes sobre el uso del servicio.
- [Blog de seguridad de Azure](http://blogs.msdn.com/b/azuresecurity/): encuentre entradas de blog sobre el cumplimiento y la seguridad de Azure.

<!---HONumber=AcomDC_1217_2015-->