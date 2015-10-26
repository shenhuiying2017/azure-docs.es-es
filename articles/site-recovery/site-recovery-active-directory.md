<properties
	pageTitle="Guía de ASR para Active Directory | Microsoft Azure" 
	description="En este artículo se explica en detalle cómo puede crear una solución de recuperación ante desastres para AD con Azure Site Recovery, realizar conmutaciones por error planeadas, no planeadas o de prueba con el plan de recuperación de un solo clic, las configuraciones admitidas y los requisitos previos." 
	services="site-recovery" 
	documentationCenter="" 
	authors="prateek9us" 
	manager="abhiag" 
	editor=""/>

<tags 
	ms.service="site-recovery" 
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="na"
	ms.workload="storage-backup-recovery" 
	ms.date="10/12/2015" 
	ms.author="pratshar"/>

#Solución automatizada de recuperación ante desastres para Active Directory y DNS con Azure Site Recovery


Todas las aplicaciones empresariales, como SharePoint, Dynamics AX y SAP, dependen de la infraestructura de DNS y AD para funcionar correctamente. Al crear una solución de recuperación ante desastres para cualquier aplicación de este tipo, es importante proteger y recuperar AD antes de que aparezcan los demás componentes de la aplicación en caso de una interrupción.

Azure Site Recovery (ASR) es un servicio basado en Azure que proporciona capacidades de recuperación ante desastres mediante la organización de la replicación, la conmutación por error y la recuperación de máquinas virtuales. Azure Site Recovery es compatible con un número de tecnologías de replicación para replicar, proteger y conmutar por error de forma coherente y sin problemas máquinas virtuales y aplicaciones en nubes públicas o privadas o de proveedor de servicios de hosting.

Con Azure Site Recovery, puede crear un plan de recuperación ante desastres automatizada completa para su AD. Puede iniciar la conmutación por error en segundos desde cualquier lugar en el caso de una interrupción y hacer que Active Directory vuelva a funcionar en unos minutos. Si tiene un AD para varias aplicaciones, como SharePoint y SAP, en el sitio principal y decide realizar una conmutación por error del sitio completo, puede realizar una conmutación por error de AD primero mediante Azure Site Recovery (ASR) y, a continuación, de las otras aplicaciones mediante planes de recuperación específicos de la aplicación.

En este artículo se explica en detalle cómo puede crear una solución de recuperación ante desastres para AD, realizar conmutaciones por error planeadas, no planeadas o de prueba con el plan de recuperación de un solo clic, las configuraciones admitidas y los requisitos previos. Se espera que el lector esté familiarizado con AD y Azure Site Recovery. Hay dos opciones recomendadas según la complejidad del entorno de local del cliente para proteger AD.

####Opción 1

Si hay un número pequeño de aplicaciones y un solo controlador de dominio en el entorno y se conmuta por error todo el sitio junto, se recomienda usar la replicación de ASR para replicar la máquina del controlador de dominio en el sitio secundario (aplicable tanto para sitio a sitio como para sitio a Azure). La misma máquina virtual replicada puede usarse también para probar la conmutación por error.

####Opción 2
Si hay un gran número de aplicaciones y hay más de un controlador de dominio en el entorno o si planea realizar una conmutación por error de pocas aplicaciones de cada vez, se recomienda que además de habilitar la replicación de ASR en la máquina virtual del controlador de dominio (que se usará para probar la conmutación por error), también configure un controlador de dominio adicional en el sitio de recuperación ante desastres (sitio secundario local o en Azure).

>[AZURE.NOTE]Incluso si escoge la Opción 2, para realizar una conmutación por error de prueba, seguirá siendo necesario que configure la replicación ASR del controlador de dominio. Examine la sección [Consideraciones sobre la conmutación por error de prueba](#considerations-for-test-failover) para obtener más información.


Las siguientes secciones explican cómo habilitar la protección en el controlador de dominio de ASR y cómo configurar un controlador de dominio en Azure.


##Requisitos previos

La implementación de la recuperación ante desastres para AD mediante Azure Site Recovery requiere que se cumplan los requisitos previos siguientes.

- Una implementación local del servidor de Active Directory y DNS
- Creación de un almacén de los servicios de Azure Site Recovery en la suscripción de Microsoft Azure 
- Si Azure es su sitio de recuperación, ejecute la herramienta de evaluación de preparación de máquinas virtuales de Azure en las máquinas virtuales para asegurarse de que son compatibles con las máquinas virtuales de Azure y los servicios de Azure Site Recovery


##Habilitación de la protección de los controladores de dominio o DNS con ASR


###Proteger la máquina virtual
Habilite la protección de los controladores de dominio o la máquina virtual DNS con ASR Realice la configuración de Azure Site Recovery relevante en función de si la máquina virtual está implementada en Hyper-V o en VMware. Se recomienda configurar la frecuencia de la coherencia frente a bloqueos en 15 minutos.

###Configurar los valores de red de máquina virtual
- Para el controlador de dominio o la máquina virtual DNS, configure los valores de red en ASR para que la máquina virtual se conecte a la red correcta después de una conmutación por error. 
- Por ejemplo al usar Azure como el sitio de recuperación ante desastres puede seleccionar la máquina virtual en "la nube de VMM" o "Grupo de protección" para configurar los valores de red, como se muestra en la siguiente instantánea.
- 
![Configuración de red de máquina virtual](./media/site-recovery-active-directory/VM-Network-Settings.png)

##Habilitación de la protección para AD a través de la replicación de AD.
###Escenario de sitio a sitio

Cree un controlador de dominio en el sitio secundario (DR) y mientras promueve al servidor a la función de controlador de dominio, asigne el nombre del mismo dominio que se usa en el sitio principal. Puede usar los sitios de Active Directory y el complemento Servicios para configurar los valores en el objeto de vínculo de sitio al que se agregan los sitios. Al configurar los valores en un vínculo de sitio, puede controlar cuándo se produce la replicación entre dos o más sitios y con qué frecuencia se produce. Puede consultar [Programación de la replicación entre sitios](https://technet.microsoft.com/es-ES/library/cc731862.aspx) para obtener más detalles.

###Escenario de sitio a Azure
Siga estas instrucciones para crear un [controlador de dominio en una red virtual de Azure](../virtual-network/virtual-networks-install-replica-active-directory-domain-controller.md). Mientras promueve al servidor a la función de controlador de dominio asigne el nombre del mismo dominio que se usa en el sitio principal.

Una vez hecho esto, debe [volver a configurar el servidor DNS para la red virtual](../virtual-network/virtual-networks-install-replica-active-directory-domain-controller.md#reconfigure-dns-server-for-the-virtual-network) para usar el servidor DNS de Azure
  
![Red de Azure](./media/site-recovery-active-directory/azure-network.png)

##Consideraciones sobre la conmutación por error de prueba
La conmutación por error de prueba se realiza en una red que está aislada de la red de producción para que no se produzca impacto alguno en la carga de trabajo de producción. La mayoría de las aplicaciones también requieren la presencia de un controlador de dominio y un servidor DNS para funcionar. Por lo tanto, antes de conmutar por error una aplicación, tiene que crearse un controlador de dominio en la red aislada que se usará para probar la conmutación por error. La manera más fácil de hacerlo es habilitar primero la protección en el controlador de dominio o la máquina virtual DNS mediante ASR y antes de activar la conmutación por error de prueba del plan de recuperación de la aplicación, desencadenar la conmutación por error de prueba de la máquina virtual de controlador de dominio. A continuación encontrará las instrucciones paso a paso para probar la conmutación por error:

1. Habilite la protección en el controlador de dominio o la máquina virtual DNS como lo haría para cualquier otra máquina virtual.
2. Cree una red aislada. Cualquier red virtual que se cree en Azure de forma predeterminada está aislada de otras redes. Se recomienda que el intervalo de IP para esta red sea el mismo que el de la red de producción. No habilite la conectividad de sitio a sitio en esta red.
3. Proporcione IP de DNS en la red creada en el paso anterior como la dirección IP que se espera que la máquina virtual DNS obtenga. Si usa Azure como sitio de recuperación ante desastres puede proporcionar la dirección IP para la máquina virtual que se usará en la conmutación por error en el valor de configuración de "Dirección IP de destino" en las propiedades de la máquina virtual. Si su sitio de recuperación ante desastres es local y usa DHCP, siga las instrucciones que aquí se proporcionan para [preparar DNS y DHCP para probar la conmutación por error](site-recovery-failover.md#prepare-dhcp) 

>[AZURE.NOTE]La dirección IP que se asigna a una máquina virtual en una conmutación por error de prueba es el misma que la dirección IP que obtendría al realizar una conmutación por error planeada o no planeada, dado que esta dirección IP está disponible en la red de conmutación por error de prueba. Si la misma IP no está disponible en la red de conmutación por error de prueba, la máquina virtual obtendrá otra IP que sí esté disponible en la red de conmutación por error de prueba.

4. Vaya a la máquina virtual de controlador de dominio y pruebe la conmutación por error del mismo en la red aislada. 
5. Pruebe la conmutación por error del plan de recuperación de la aplicación.
6. Una vez finalizada la prueba, marque el trabajo de la conmutación por error de prueba de la máquina virtual del controlador de dominio y del plan de recuperación como "Completo" en la pestaña trabajos de ASR. 

###DNS y controlador de dominio en máquinas virtuales diferentes: 
Si se da el caso de que el DNS no está en la misma máquina virtual que el controlador de dominio, tendrá que crear un DNS para probar la conmutación por error. Si se encuentren en la misma máquina virtual, puede omitir esta sección. Puede utilizar un servidor DNS nuevo y crear todas las zonas necesarias. Por ejemplo, si el dominio de Active Directory es contoso.com, puede crear una zona con el nombre contoso.com. Las entradas correspondientes a Active Directory se deben actualizar en DNS. Para ello, realice lo siguiente:

- Asegúrese de que está establecida la siguiente configuración antes de que se incluya cualquier otra máquina virtual en el plan de recuperación:
	- El nombre de la zona depende del nombre de raíz del bosque.
	- La zona debe estar respaldada por archivos.
	- La zona debe habilitarse para actualizaciones seguras y no seguras.
	- La resolución de la máquina virtual del controlador de dominio debe apuntar a la dirección IP de la máquina virtual DNS.
- Ejecute el siguiente comando en la máquina virtual del controlador de dominio Directorio: nltest /dsregdns

- **Agregar zona**: use el siguiente script de muestra para agregar una zona en el servidor DNS, permitir actualizaciones no seguras y agregar una entrada para sí mismo en DNS:

	    dnscmd /zoneadd contoso.com  /Primary 
	    dnscmd /recordadd contoso.com  contoso.com. SOA %computername%.contoso.com. hostmaster. 1 15 10 1 1 
	    dnscmd /recordadd contoso.com %computername%  A <IP_OF_DNS_VM> 
	    dnscmd /config contoso.com /allowupdate 1


##Resumen
Con Azure Site Recovery, puede crear un plan de recuperación ante desastres automatizada completa para su AD. Puede iniciar la conmutación por error en segundos desde cualquier lugar en el caso de una interrupción y hacer que Active Directory vuelva a funcionar en unos minutos. Si tiene un AD para varias aplicaciones, como SharePoint y SAP, en el sitio principal y decide realizar una conmutación por error del sitio completo, puede realizar una conmutación por error de AD primero mediante Azure Site Recovery (ASR) y, a continuación, de las otras aplicaciones mediante planes de recuperación específicos de la aplicación.


Consulte [Guías de cargas de trabajo de Site Recovery](../site-recovery/site-recovery-workload.md) para obtener más detalles sobre la protección de varias cargas de trabajo mediante ASR.

<!---HONumber=Oct15_HO3-->