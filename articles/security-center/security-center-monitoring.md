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
   ms.topic="hero-article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="07/19/2016"
   ms.author="yurid"/>

#Supervisión del estado de seguridad en el Centro de seguridad de Azure
Este documento le ayuda a usar las funcionalidades de supervisión del Centro de seguridad de Azure para supervisar el cumplimiento de las directivas.

> [AZURE.NOTE] La información de este documento se aplica a la versión preliminar del Centro de seguridad de Azure.

##¿Qué es la supervisión del estado de seguridad?
Con frecuencia se piensa que supervisar es observar y esperar que se produzca un evento, para así poder reaccionar ante la situación. La supervisión de seguridad se refiere a contar con una estrategia proactiva que audita los recursos a fin de identificar los sistemas que no cumplen con los estándares o los procedimientos recomendados de la organización.

##Supervisión del estado de seguridad
Después de habilitar las [directivas de seguridad](security-center-policies.md) de los recursos de una suscripción, Security Center analizará la seguridad de los recursos para identificar vulnerabilidades potenciales. La información acerca de la configuración de la red está disponible de inmediato; sin embargo, la información acerca de la configuración de las máquinas virtuales, como el estado de las actualizaciones de seguridad y la configuración del sistema operativo, puede tardar una hora, o más, en estar disponible. Puede consultar el estado de seguridad de sus recursos, además de cualquier problema que exista, en las hojas de **Estado de seguridad de los recursos**. También puede ver una lista de esos problemas en las hojas de **Recomendaciones**.

Para más información sobre cómo aplicar las recomendaciones, lea [Implementación de recomendaciones de seguridad en el Centro de seguridad de Azure](security-center-recommendations.md).

El icono **Estado de seguridad del recurso** permite supervisar el estado de seguridad de los recursos. En el ejemplo siguiente puede ver varios problemas con una gravedad alta y media que requieren atención. Las directivas de seguridad habilitadas afectarán a los tipos de controles que se supervisan.

![Estado de los recursos](./media/security-center-monitoring/security-center-monitoring-fig1-new3.png)

Si Security Center identifica una vulnerabilidad que se debe abordar, como una máquina virtual donde faltan actualizaciones de seguridad o una subred sin un [grupo de seguridad de red](../virtual-network/virtual-networks-nsg.md), se enumerará aquí.

###Supervisión de máquinas virtuales
Al hacer clic en **Máquinas virtuales** en el icono **Estado de seguridad del recurso**, se abrirá la hoja **Máquinas virtuales**, donde encontrará más detalles sobre los pasos de incorporación y prevención, así como una lista de las máquinas virtuales que supervisa Security Center, tal como se muestra a continuación.

![Actualización del sistema faltante por VM](./media/security-center-monitoring/security-center-monitoring-fig2-2-new.png)

- Pasos para la incorporación
- Recomendaciones sobre máquinas virtuales
- Máquinas virtuales

En cada sección puede seleccionar una opción individual para ver más detalles sobre el paso recomendado para abordar el problema. Las siguientes secciones tratarán estas áreas de forma más detallada.

####Pasos para la incorporación
En esta sección se muestra el número total de máquinas virtuales que se inicializaron para la recopilación de datos y su estado actual. Una vez que se haya inicializado la recopilación de datos en todas las máquinas virtuales, estarán listas para recibir las directivas de seguridad del Centro de seguridad. Al hacer clic en esta entrada, se abre la hoja **Inicializando la recolección de datos**, donde podrá ver los nombres de las máquinas virtuales y el estado actual de la recopilación de datos en la columna **ESTADO DE INICIALIZACIÓN**, como se muestra a continuación.

![Estado de la inicialización](./media/security-center-monitoring/security-center-monitoring-fig3-new.png)


####Recomendaciones sobre máquinas virtuales
Esta sección contiene un conjunto de recomendaciones para cada máquina virtual supervisada por el centro de seguridad de Azure. La primera columna enumera la recomendación, la segunda el número total de máquinas virtuales afectadas por dicha recomendación y la tercera muestra la gravedad del problema, tal como se ilustra a continuación.

![Recomendaciones de máquina virtual](./media/security-center-monitoring/security-center-monitoring-fig4-2-new.png)

> [AZURE.NOTE] Solo las máquinas virtuales con al menos un punto de conexión público se muestran en la hoja de estado de red en la lista de topología de red.

Cada recomendación tiene un conjunto de acciones que se pueden realizar una vez que haga clic en ella. Por ejemplo, si hace clic en **Faltan las actualizaciones del sistema**, se abrirá la hoja **Faltan las actualizaciones del sistema**. En dicha hoja se enumeran las máquinas virtuales a las que faltan revisiones y la gravedad de la actualización que falta, como se muestra a continuación.

![Actualizaciones del sistema que faltan](./media/security-center-monitoring/security-center-monitoring-fig5-new.png)

La hoja **Faltan las actualizaciones del sistema** mostrará una tabla con la siguiente información:

- **MÁQUINA VIRTUAL**: el nombre de la máquina virtual en la que faltan actualizaciones.
- **ACTUALIZACIONES DEL SISTEMA**: el número actualizaciones del sistema que faltan.
- **HORA DE LA ÚLTIMA DETECCIÓN**: la hora en que Security Center realizó el último examen de la máquina virtual en busca de actualizaciones.
- **ESTADO**: el estado actual de la recomendación:
	- **Abierta**: la recomendación aún no se ha abordado.
	- **En curso**: la recomendación se aplica actualmente a los recursos; no se requiere ninguna acción de su parte.
	- **Resuelta**: la recomendación se completó (cuando el problema se ha resuelto, la entrada aparece atenuada).
- **GRAVEDAD**: describe la gravedad de una recomendación concreta:
	- **Alta**: existe una vulnerabilidad en un recurso importante (aplicación, VM, grupo de seguridad de red) y requiere atención.
	- **Media**: para completar un proceso o eliminar una vulnerabilidad se requieren pasos adicionales o no críticos.
	- **Baja**: es preciso abordar una vulnerabilidad, pero esta no requiere una atención inmediata. (De manera predeterminada no se muestran las recomendaciones bajas, pero si desea verlas, puede filtrar por ellas).

Para ver los detalles de las recomendaciones, haga clic en el nombre de la VM. Se abre una hoja nueva de la VM con la lista de las actualizaciones, tal como se muestra a continuación.

![Actualizaciones del sistema que faltan por máquina virtual](./media/security-center-monitoring/security-center-monitoring-fig6-new.png)

> [AZURE.NOTE] Las recomendaciones de seguridad son las mismas que aparecen en la hoja Recomendaciones. Vea el artículo [Implementación de recomendaciones de seguridad en el Centro de seguridad de Azure](security-center-recommendations.md) para más información sobre cómo solucionar las recomendaciones. Esto no se aplica solo a las VM, sino que también a todos los recursos disponibles en el icono Estado de los recursos.

####Sección Máquinas virtuales
La sección de máquinas virtuales ofrece una visión general de todas las máquinas virtuales y recomendaciones. Cada columna representa un conjunto de recomendaciones, tal como se muestra a continuación:

![Máquinas virtuales](./media/security-center-monitoring/security-center-monitoring-fig7-new.png)

El icono que aparece en cada recomendación le ayuda a identificar rápidamente tanto las máquinas virtuales que necesitan atención como el tipo de recomendación que necesitan.

En el ejemplo anterior, una VM tiene una recomendación crítica acerca de los programas antimalware. Para más información acerca de la VM, haga clic en ella. Se abrirá una nueva hoja que representa la VM, tal como se muestra a continuación.

![Detalles de seguridad de la máquina virtual](./media/security-center-monitoring/security-center-monitoring-fig8-new.png)

Esta hoja tiene los detalles de seguridad de la máquina virtual. En la parte inferior de la hoja puede ver la acción recomendada y la gravedad de cada problema.

#### Sección Servicios en la nube (versión preliminar)
El estado de mantenimiento de los servicios en la nube se incluye en el icono de estado de seguridad de las máquinas virtuales. Cuando la versión del sistema operativo no está actualizada se crea una recomendación, como se muestra a continuación:

![Servicios en la nube](./media/security-center-monitoring/security-center-monitoring-fig8-new2.png)

Tendrá que seguir los pasos de la recomendación para actualizar la versión del sistema operativo. Por ejemplo, si hace clic en la alerta roja en uno de los roles web (ejecuta Windows Server con la aplicación web implementada automáticamente en IIS) o el rol de trabajo (ejecuta Windows Server con la aplicación web implementada automáticamente en IIS), se abrirá una nueva hoja con más detalles sobre esta recomendación, como se muestra a continuación:

![Detalles del servicio en la nube](./media/security-center-monitoring/security-center-monitoring-fig8-new3.png)

Para ver una explicación más preceptiva con respecto a esta recomendación, haga clic en Actualizar **Versión del SO** en la columna **DESCRIPCIÓN**. Se abrirá la hoja **Update OS version (Preview)** [Actualizar versión del SO (versión preliminar)] con más detalles.

![Recomendaciones de servicios en la nube](./media/security-center-monitoring/security-center-monitoring-fig8-new4.png)

### Supervisión de redes virtuales
Al hacer clic en **Redes** en el icono **Estado de seguridad del recurso**, se abrirá la hoja **Redes**, donde encontrará más detalles, tal como se muestra a continuación:

![Redes](./media/security-center-monitoring/security-center-monitoring-fig9-new3.png)

####Recomendaciones de redes

De manera similar a la información del estado de los recursos de las máquinas virtuales, esta hoja proporciona una lista resumida de los problemas en la parte superior de la hoja y una lista de las redes supervisadas en la parte inferior.

La sección de desglose del estado de las redes enumera los potenciales problemas de seguridad y ofrece recomendaciones. Entre los posibles problemas se pueden incluir:

- Firewall de próxima generación (NGFW) no instalado
- Grupos de seguridad de red (NSG) en subredes no habilitados
- NSG en máquinas virtuales no habilitados
- Restringir el acceso externo por medio de puntos de conexión externos públicos
- Puntos de conexión con conexión a Internet correctos

Al hacer clic en una de las recomendaciones, se abrirá una nueva hoja con más detalles acerca de ella, como se muestra en el ejemplo siguiente.

![Restricción del punto de conexión](./media/security-center-monitoring/security-center-monitoring-fig11-new2.png)

En este ejemplo, la hoja **Configure Missing Network Security Groups for Subnets** (Configurar grupos de seguridad de red que faltan para subredes) tiene una lista de subredes y máquinas virtuales que carecen de protección con NSG. Si hace clic en la subred a la que desea aplicar el NSG, se abre otra hoja.

En la hoja **Elegir grupo de seguridad de red**, seleccione el grupo de seguridad de red más adecuado para la subred o cree uno.

####Sección Internet facing endpoints

En la sección **Internet facing endpoints** (Puntos de conexión con conexión a Internet), verá las máquinas virtuales que están configuradas con un punto de conexión con conexión a Internet y su estado actual.

![Punto de conexión con conexión a Internet](./media/security-center-monitoring/security-center-monitoring-fig121-new5.png)

Esta tabla incluye el nombre del punto de conexión que representa la máquina virtual, la dirección IP con conexión a Internet, el estado de gravedad actual del NSG y el NGFW. La tabla está ordenada por gravedad, tal como se describe a continuación:
- Rojo (arriba): alta prioridad; se debe solucionar de inmediato.
- Naranja: prioridad media; se debe solucionar lo antes posible.
- Verde (al final): estado de mantenimiento.

####Sección Networking topology

En la sección **Networking topology** (Topología de red), se ofrece una vista jerárquica de los recursos, como se muestra a continuación:

![Topología de red](./media/security-center-monitoring/security-center-monitoring-fig121-new4.png)

Esta tabla está ordenada (máquinas virtuales y subredes) por gravedad, como se describe a continuación:
- Rojo (arriba): alta prioridad; se debe solucionar de inmediato.
- Naranja: prioridad media; se debe solucionar lo antes posible.
- Verde (al final): estado de mantenimiento.

En esta vista de topología, el primer nivel tiene [redes virtuales](../virtual-network/virtual-networks-overview.md), [puertas de enlace de red virtual](../vpn-gateway/vpn-gateway-site-to-site-create.md) y la [red virtual (clásica)](../virtual-network/virtual-networks-create-vnet-classic-pportal.md). El segundo nivel tiene subredes y el tercero, las máquinas virtuales que pertenecen a esas subredes. La columna derecha contiene el estado actual del grupo de seguridad de red (NSG) para esos recursos. En el ejemplo siguiente se ve el resultado de seleccionar la máquina virtual VM-CL-W1:

![Árbol de red](./media/security-center-monitoring/security-center-monitoring-fig13-new2.png)

En la parte inferior de esta hoja, aparecen las recomendaciones para la máquina virtual, parecidas a lo que se ha descrito antes. Puede hacer clic en una recomendación para ver más información o aplicar la configuración o el control de seguridad necesario.

###Supervisión de recursos de SQL
Al hacer clic en **SQL** en el icono **Estado de seguridad del recurso**, se abrirá la hoja SQL con recomendaciones para problemas como que la auditoría o el cifrado de datos transparente no estén habilitados. También tiene las recomendaciones sobre el estado general de la base de datos.

![Estado de los recursos SQL](./media/security-center-monitoring/security-center-monitoring-fig15-new.png)

Puede hacer clic en cualquiera de estas recomendaciones para obtener más detalles sobre las acciones que deben llevarse a cabo para solucionar el problema. El ejemplo siguiente muestra la expansión de la recomendación **Auditoría de la base de datos no habilitada**.

![Estado de los recursos SQL](./media/security-center-monitoring/security-center-monitoring-fig16-new.png)

La hoja **Habilitar la auditoría en bases de datos SQL** contiene la siguiente información:

- Una lista de bases de datos SQL.
- El servidor en el que se encuentran.
- Información sobre si esta configuración se heredó del servidor o si es única en la base de datos.
- El estado actual.
- La gravedad del problema.

Al hacer clic en la base de datos para abordar esta recomendación, se abrirá la hoja **Auditoría y detección de amenazas**, como se muestra a continuación.

![Estado de los recursos SQL](./media/security-center-monitoring/security-center-monitoring-fig17-new.png)

Para habilitar la auditoría, simplemente seleccione **Activar** en la opción **Auditoría** y haga clic en **Guardar**.

###Supervisión de aplicaciones
Si la carga de trabajo de Azure tiene aplicaciones en las [máquinas virtuales de Resource Manager](../resource-manager-deployment-model.md) con puertos web expuestos (puertos TCP 80 y 443), Security Center puede supervisarlos para identificar posibles problemas de seguridad y recomendar pasos para su corrección. Al hacer clic en el icono **Aplicaciones**, se abrirá la hoja **Aplicaciones** con una serie de recomendaciones en la sección de pasos de prevención. También mostrará el desglose de la aplicación por host o IP virtual, tal como aparece a continuación.

![Estado de la seguridad de las aplicaciones](./media/security-center-monitoring/security-center-monitoring-fig18-new.png)

Al igual que en las restantes recomendaciones, puede hacer clic en ella para ver más información acerca del problema y cómo corregirlo. El ejemplo que se muestra en la ilustración siguiente es una aplicación que se identificó como aplicación web no segura. Cuando selecciona la aplicación que se consideró como no segura, se abrirá otra hoja con la siguiente opción disponible:

![Aplicaciones](./media/security-center-monitoring/security-center-monitoring-fig19-new.png)

La hoja **Aplicaciones web no seguras** incluirá una lista de todas las máquinas virtuales que contienen aplicaciones que no se consideran seguras. La lista muestra el nombre de la VM, así como el estado actual y la gravedad del problema. Si hace clic en esta aplicación web, se abrirá la hoja **Agregar un firewall de aplicaciones web** con opciones para instalar un WAF (Firewall de aplicaciones web) de terceros, como se muestra a continuación.

![Adición de WAF](./media/security-center-monitoring/security-center-monitoring-fig20-new.png)

## Consulte también
En este documento, aprendió a usar las funcionalidades de supervisión en el Centro de seguridad de Azure. Para obtener más información sobre el Centro de seguridad de Azure, consulte los siguientes recursos:

- [Establecimiento de directivas de seguridad en el Centro de seguridad de Azure](security-center-policies.md): obtenga información sobre cómo configurar los ajustes de seguridad en el Centro de seguridad de Azure.
- [Administración y respuesta a las alertas de seguridad en el Centro de seguridad de Azure](security-center-managing-and-responding-alerts.md): obtenga información sobre cómo administrar y responder a alertas de seguridad.
- [Supervisión de las soluciones de asociados con Azure Security Center](security-center-partner-solutions.md): aprenda a supervisar el estado de mantenimiento de las soluciones de asociados.
- [Preguntas más frecuentes sobre el Centro de seguridad de Azure](security-center-faq.md): Encuentre las preguntas más frecuentes sobre el uso del servicio.
- [Blog de seguridad de Azure](http://blogs.msdn.com/b/azuresecurity/): encuentre entradas de blog sobre el cumplimiento y la seguridad de Azure.

<!---HONumber=AcomDC_0720_2016-->