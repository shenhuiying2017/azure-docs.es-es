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
   ms.date="01/22/2016"
   ms.author="yurid"/>

#Supervisión del estado de seguridad en el Centro de seguridad de Azure
Este documento le ayuda a usar las funcionalidades de supervisión en el Centro de seguridad de Azure para supervisar el cumplimiento de las directivas.

> [AZURE.NOTE] La información de este documento se aplica a la versión preliminar del Centro de seguridad de Azure.

## ¿Qué es el Centro de seguridad de Azure?
El Centro de seguridad de Azure ayuda a evitar, detectar y responder a amenazas con más visibilidad y control de la seguridad en sus recursos de Azure. Ofrece administración de directivas y supervisión de la seguridad integrada en las suscripciones, ayuda a detectar las amenazas que podrían pasar desapercibidas y funciona con un amplio ecosistema de soluciones de seguridad.

##¿Qué es la supervisión del estado de seguridad?
Con frecuencia se piensa que supervisar es observar y esperar que se produzca un evento, para así poder reaccionar ante la situación. La supervisión de seguridad se refiere a contar con una estrategia proactiva que audita los recursos a fin de identificar los sistemas que no cumplen con los estándares o los procedimientos recomendados de la organización.

##Supervisión del estado de seguridad
Después de habilitar las [directivas de seguridad](security-center-policies.md) de los recursos de la suscripción, el Centro de seguridad de Azure analizará la seguridad de los recursos para identificar posibles vulnerabilidades. A pesar de que la información sobre la configuración de la red está disponible de inmediato, es posible que demore una hora o más en tener a su disposición la información sobre la configuración de las máquinas virtuales, como el estado de las actualizaciones de seguridad y la configuración del SO. Puede consultar el estado de seguridad de sus recursos, además de cualquier problema que exista, en las hojas de **Estado de seguridad de los recursos**. También puede ver una lista de esos problemas en las hojas de **Recomendaciones**.

Para más información sobre cómo aplicar las recomendaciones, lea [Implementación de recomendaciones de seguridad en el Centro de seguridad de Azure](security-center-recommendations.md).

El icono **Estado de los recursos** le permite supervisar el estado de seguridad de los recursos. Verá una serie de problemas con una gravedad alta y media que requieren atención, tal como se muestra a continuación:

![Estado de los recursos](./media/security-center-monitoring/security-center-monitoring-fig1-new.png)

Las directivas de seguridad habilitadas afectarán los tipos de controles que se supervisan aquí. Si el Centro de seguridad de Azure identifica una vulnerabilidad que se debe abordar, como una VM donde faltan actualizaciones de seguridad o una subred sin un [grupo de seguridad de red](virtual-networks-nsg.md), aparecerá identificada aquí.

###Máquina virtual
Cuando hace clic en **Máquinas virtuales** en el icono **Estado de los recursos**, se abrirá la hoja **Máquinas virtuales** con más detalles sobre los pasos de incorporación y prevención, además de una lista de las VM que supervisa el Centro de seguridad de Azure, tal como aparece a continuación:

![Actualización del sistema faltante por VM](./media/security-center-monitoring/security-center-monitoring-fig2-new.png)

Cuando se abra esta hoja, verá tres secciones:

- Pasos para la incorporación
- Recomendaciones sobre máquinas virtuales
- Máquinas virtuales

En cada sección, puede seleccionar una opción individual para ver más detalles sobre el paso recomendado para abordar el problema. Las siguientes secciones tratarán estas áreas de forma más detallada.

####Pasos para la incorporación
Esta sección muestra la cantidad total de máquinas virtuales que se inicializaron para la recopilación de datos y su estado actual. Una vez que la máquina virtual ha inicializado la recopilación de datos, estarán listos para recibir las directivas de seguridad del centro de seguridad de Azure. Al hacer clic en esta entrada, la hoja **Inicialización de la recopilación de datos** se abrirá y podrá ver el nombre de las máquinas virtuales y el estado actual de la recopilación de datos como en la columna de **estado de la instalación** como se muestra a continuación:

![Estado de la inicialización](./media/security-center-monitoring/security-center-monitoring-fig3-new.png)


####Recomendaciones sobre máquinas virtuales
Esta sección contiene un conjunto de recomendaciones para cada máquina virtual supervisada por el centro de seguridad de Azure. La primera columna contiene la descripción de la recomendación, la segunda la cantidad total de máquinas virtuales que se ven afectadas por esas recomendaciones y la tercera columna muestra la gravedad, tal como se muestra a continuación:

![Recomendaciones de máquina virtual](./media/security-center-monitoring/security-center-monitoring-fig4-new.png)

Cada recomendación tiene un conjunto de acciones que se pueden realizar una vez que haga clic en ella. Por ejemplo, si hace clic en **Actualizaciones faltantes del sistema**, se abrirá la hoja **Actualizaciones faltantes del sistema** con la lista de VM donde faltan revisiones y el nivel de gravedad de cada una, tal como se muestra a continuación:

![Actualizaciones del sistema que faltan](./media/security-center-monitoring/security-center-monitoring-fig5-new.png)

La hoja Actualizaciones faltantes del sistema mostrará una tabla con la siguiente información:

- **MÁQUINA VIRTUAL**: El nombre de la máquina virtual en la que faltan actualizaciones.
- **ACTUALIZACIONES DEL SISTEMA**: La cantidad de actualizaciones del sistema que faltan.
- **TIEMPO DE ÚLTIMO ANÁLISIS**: Última vez que el centro de seguridad de Azure analizó la máquina virtual en busca de actualizaciones.
- **ESTADO**: el estado actual de la recomendación: 
	- **Abierta**: no solucionada aún
	- **En curso**: la recomendación se aplica actualmente a esos recursos; no se requiere ninguna acción de su parte.
	- **Resuelta**: la recomendación ya se completó (cuando el estado es Resuelta, el color de la línea aparece atenuado).
- **GRAVEDAD**: Describe la gravedad de esa recomendación determinada: 
	- **Alta**: existe una vulnerabilidad en un recurso importante (aplicación, VM, grupo de seguridad de red) y requiere atención.
	- **Media**: Se requieren pasos adicionales o no críticos para completar un proceso o eliminar una vulnerabilidad.
	- **Baja**: se trata de una vulnerabilidad que se debe abordar, pero no requiere una atención inmediata. (De manera predeterminada, no se muestran las recomendaciones bajas pero, si opta por verlas, puede filtrar según Recomendaciones bajas).

Para ver los detalles de las actualizaciones recomendadas, haga clic en el nombre de la VM. Se abre una hoja nueva para esa VM con la lista de las actualizaciones que faltan, como se muestra a continuación:

![Actualizaciones del sistema que faltan por máquina virtual](./media/security-center-monitoring/security-center-monitoring-fig6-new.png)

> [AZURE.NOTE] Las recomendaciones de seguridad son las mismas que aparecen en la hoja Recomendaciones. Vea el artículo [Implementación de recomendaciones de seguridad en el Centro de seguridad de Azure](security-center-recommendations.md) para más información sobre cómo solucionar las recomendaciones. Esto no se aplica solo a las VM, sino que también a todos los recursos disponibles en el icono Estado de los recursos.

####Máquinas virtuales
La sección de máquinas virtuales ofrece una visión general de todas las máquinas virtuales y recomendaciones. Cada columna de la recomendación representa un conjunto de recomendaciones, tal como se muestra a continuación:

![Máquinas virtuales](./media/security-center-monitoring/security-center-monitoring-fig7-new.png)

El icono que aparece en cada recomendación le ayuda a identificar rápidamente qué máquinas virtuales necesitan atención y para qué tipo de recomendación.

En el ejemplo anterior, una máquina virtual tiene una recomendación crítica sobre antimalware. Para obtener más información acerca de la máquina virtual, haga clic en ella y se abrirá una nueva hoja que representará esta máquina virtual, tal como se muestra a continuación:

![Detalles de seguridad de la máquina virtual](./media/security-center-monitoring/security-center-monitoring-fig8-new.png)

Esta hoja tiene los detalles de seguridad de la máquina virtual. En la parte inferior de esta hoja puede ver la acción recomendada y la gravedad de cada recomendación.

###Redes
El estado de prevención para las redes muestra las redes virtuales que supervisa el Centro de seguridad de Azure. Cuando haga clic en **Redes** en el icono Estado de los recursos, se abrirá la hoja **Redes** con más detalles, tal como aparece a continuación:

![Redes](./media/security-center-monitoring/security-center-monitoring-fig9-new.png)

Una vez que abra esta hoja, verá dos secciones: - Recomendaciones de redes - Redes.
 
En cada sección, puede seleccionar una opción individual para ver más detalles sobre el paso recomendado para abordar el problema. Las siguientes secciones tratarán estas áreas de forma más detallada.

####Recomendaciones de redes

De manera similar a la información de estado de los recursos de las máquinas virtuales, esta hoja brinda una lista resumida de los problemas en la parte superior de la hoja y una lista de las redes supervisadas en la parte inferior.

En la sección de desglose del estado de las redes, aparecen los pasos de prevención, como [ACL en puntos de conexión](virtual-machines-set-up-endpoints.md) no habilitados, [Grupos de seguridad de red](virtual-networks-nsg.md) no habilitados, Subredes en buen estado y **Acceso a NSG no restringido**. Al hacer clic en una de estas recomendaciones, se abrirá una nueva hoja con más detalles acerca de la recomendación, como se muestra en el ejemplo siguiente:

![Extremo](./media/security-center-monitoring/security-center-monitoring-fig10-new.png)

En este ejemplo, la hoja **Restricción del acceso a través del punto de conexión externo** tiene una lista de grupos de seguridad de red (NSG) que forman parte de esta alerta, la subred y la red asociadas con que este NSG, el estado actual de esta recomendación y la gravedad. Si hace clic en el grupo de seguridad de red, se abrirá otra hoja, como se muestra a continuación:

![Restricción del punto de conexión](./media/security-center-monitoring/security-center-monitoring-fig11-new.png)

Esta hoja tiene la información del grupo de seguridad de red, la ubicación y la lista de reglas de entrada habilitados actualmente. La parte inferior de esta hoja tiene la máquina virtual que está asociada a este grupo de seguridad de red. Si desea habilitar las reglas de entrada para bloquear un puerto no deseado que esté actualmente abierto o cambiar el origen de la regla de entrada actual, puede hacer clic en el botón **Editar regla de entrada** en la parte superior de la hoja.

####Redes
En la sección **Redes** tiene una vista jerárquica del grupo de recursos, la subred y la interfaz de red asociada a la máquina virtual, como se muestra a continuación:

![Árbol de red](./media/security-center-monitoring/security-center-monitoring-fig121-new.png)

En esta sección divide [Administrador de recursos basado en las máquinas virtuales de máquinas virtuales clásicas](resource-manager-deployment-model.md). Esto le ayudará a identificar rápidamente si las capacidades de red de la administración de servicios de Azure (ASM/clásico) o la administración de recursos de Azure (ARM) están disponibles para la máquina virtual. Si decide obtener acceso a las propiedades de una tarjeta de la interfaz de red desde esta ubicación, tendrá que expandir la subred y hacer clic en el nombre de la máquina virtual. Si realiza esta acción para una máquina virtual basada en administrador de recursos, aparecerá una nueva hoja similar a la siguiente:

![Árbol de red](./media/security-center-monitoring/security-center-monitoring-fig13-new.png)

Esta hoja tiene un resumen de la tarjeta de la interfaz de red y las recomendaciones actuales para ella. Si la máquina virtual que seleccionó es una máquina virtual clásica, aparecerá una nueva hoja con diferentes opciones, como se muestra a continuación:

![ACL](./media/security-center-monitoring/security-center-monitoring-fig14-new.png)

Esta hoja le permite realizar cambios para el puerto público o privado y le permite crear una ACL para esta máquina virtual.

###SQL
Cuando hace clic en **SQL** en el icono **Estado de los recursos**, se abrirá la hoja SQL con las recomendaciones relacionadas a problemas como una auditoría no habilitada, un cifrado de datos transparente no habilitado y el estado general de la base de datos.

![Estado de los recursos SQL](./media/security-center-monitoring/security-center-monitoring-fig15-new.png)

Puede hacer clic en cualquiera de estas recomendaciones para obtener más detalles y llevar a cabo otras acciones para solucionar el problema. En el ejemplo siguiente aparece la expansión de la recomendación **Auditoría de base de datos no habilitada**.

![Estado de los recursos SQL](./media/security-center-monitoring/security-center-monitoring-fig16-new.png)

En la hoja **Habilitación de auditoría sobre bases de datos SQL** tiene una lista de las bases de datos SQL, el servidor en el que se encuentran, si esta configuración se ha heredado del servidor o si es exclusiva en esta base de datos, el estado actual de este problema y la gravedad. Al hacer clic en la base de datos para solucionar esta recomendación, se abrirá la hoja **Auditoría y detección de amenazas**, como se muestra a continuación:

![Estado de los recursos SQL](./media/security-center-monitoring/security-center-monitoring-fig17-new.png)

Para habilitar esta capacidad, simplemente seleccione **ON** en la opción **Auditoría** opción y, a continuación, haga clic en **Guardar**.

###Aplicaciones
Si la carga de trabajo de Azure tiene aplicaciones en las [VM del Administrador de recursos](resource-manager-deployment-model.md) con puertos web expuestos (puertos TCP 80 y 443), el Centro de seguridad de Azure puede supervisarlos para identificar posibles problemas de seguridad y recomendar pasos para su corrección. Cuando haga clic en el icono **Aplicaciones**, se abrirá la hoja **Aplicaciones** con una serie de recomendaciones en la sección de pasos de prevención y, además, mostrará el desglose de la aplicación por host/IP virtual, tal como aparece a continuación:

![Estado de la seguridad de las aplicaciones](./media/security-center-monitoring/security-center-monitoring-fig18-new.png)

Al igual que lo que ocurre con las otras recomendaciones para los recursos anteriores, puede hacer clic en ella para ver más detalles sobre el problema y cómo solucionarlo. El ejemplo que aparece en la ilustración siguiente es una aplicación que se identificó como **Aplicación web insegura**. Cuando selecciona la aplicación que se consideró como no segura, se abrirá otra hoja con la siguiente opción disponible:

![Aplicaciones](./media/security-center-monitoring/security-center-monitoring-fig19-new.png)

La hoja **Aplicaciones web no seguras** mostrará una lista de todas las máquinas virtuales que contienen aplicaciones que no se consideran seguras. La lista muestra el nombre de la máquina virtual, el estado actual del problema y la seguridad. Si hace clic en esta aplicación web, se abrirá la hoja **Adición de un firewall de aplicación web** con opciones para que pueda instalar un WAF(firewall de aplicaciones web) de terceros como se muestra a continuación:

![Adición de WAF](./media/security-center-monitoring/security-center-monitoring-fig20-new.png)

## Pasos siguientes
En este documento, aprendió a usar las funcionalidades de supervisión en el Centro de seguridad de Azure. Para obtener más información sobre el Centro de seguridad de Azure, consulte los siguientes recursos:

- [Establecimiento de directivas de seguridad en el Centro de seguridad de Azure](security-center-policies.md): obtenga información sobre cómo configurar los ajustes de seguridad en el Centro de seguridad de Azure.
- [Administración y respuesta a las alertas de seguridad en el Centro de seguridad de Azure](security-center-managing-and-responding-alerts.md): obtenga información sobre cómo administrar y responder a alertas de seguridad.
- [Preguntas más frecuentes sobre el Centro de seguridad de Azure](security-center-faq.md): Encuentre las preguntas más frecuentes sobre el uso del servicio.
- [Blog de seguridad de Azure](http://blogs.msdn.com/b/azuresecurity/): encuentre entradas de blog sobre el cumplimiento y la seguridad de Azure.

<!---HONumber=AcomDC_0128_2016-->