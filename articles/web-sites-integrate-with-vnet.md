<properties title="Integrate your Azure Website with an Azure Virtual Network" pageTitle="Integración de un sitio web de Azure con una red virtual de Azure" description="Muestra cómo conectar un sitio web de Azure a una red virtual de Azure nueva o existente" metaKeywords="" services="web-sites,virtual-network" solutions="web,integration,infrastructure" documentationCenter="" authors="cephalin" videoId="" scriptId="" manager="wpickett" />

<tags ms.service="web-sites" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="09/24/2014" ms.author="cephalin" />

# Integración de su sitio web de Azure con una red virtual de Azure #
Este documento describe la característica en vista previa de integración de redes virtuales y muestra cómo configurarla con su sitio web de Azure.  Si no conoce las redes virtuales de Azure, sepa que esta es una capacidad que le permite compilar soluciones híbridas con sus recursos locales y de Azure.  

Esta integración le da a su sitio web acceso a los recursos de su red virtual, pero no concede acceso a su sitio web desde la red virtual.  Algunos escenarios estándar se dan cuando el sitio web necesita obtener acceso a una base de datos o a un servicio web que se ejecutan en máquinas virtual de su VNET o en su propio centro de datos.  No le permite montar una unidad de disco.  Tampoco permite habilitar la integración con sistemas de autenticación en su vnet.  La característica está en vista previa todavía y seguiremos mejorándola antes de ponerla a disposición de los usuarios.

Si quiere más detalles sobre las redes virtuales de Azure, vea la información general sobre redes virtuales referente a los casos de uso y las ventajas de tener una red virtual de Azure.

## Introducción ##
Le recordamos algunas cosas que debe tener en cuenta antes de conectar su sitio a una red virtual.

1.	Los sitios solo se pueden conectar a una red virtual si se están ejecutando en un plan de hospedaje web que esté en el nivel de precios "Estándar".  Los sitios gratuitos, compartidos y básicos no se pueden conectar a una red virtual.
2.	Si su red virtual de destino ya existe, debe tener habilitada la característica punto a sitio con una puerta de enlace de enrutamiento dinámica para que se pueda conectar a un sitio web. No puede habilitar una VPN de punto a sitio si la puerta de enlace está configurada con enrutamiento estático.
3.	Solo puede tener 5 redes configuradas como mucho en su plan de hospedaje web.  Un sitio web solo se puede conectar a una red cada vez.  Esas 5 redes las pueden usar todos los sitios web que quiera dentro del mismo plan de hospedaje web.  

Puede conectarse a una red virtual nueva o existente.  Si crea una red nueva, se le preconfigura una puerta de enlace.  Crear y configurar una red virtual lleva unos minutos.  

Si su sitio web no está en un nivel estándar, la interfaz de usuario le informa y le da acceso a los niveles de precios al hacer clic en ella.

![](./media/web-sites-integrate-with-vnet/upgrade-to-standard.png) 

## Funcionamiento del sistema ##
Esta característica usa la tecnología VPN de punto a sitio para conectar el sitio web de Azure con la red virtual.  La arquitectura de sistema de Sitios web Azure es multiempresa por naturaleza, lo que excluye el aprovisionamiento de sitios web directamente en una VNET (como se hace con las máquinas virtuales).  Al basarse en tecnología de punto a sitio, limitamos el acceso de red a la máquina virtual que hospeda el sitio web.  El acceso a la red se restringe en esos hosts de sitio web para que sus sitios web solo puedan obtener acceso a las redes que configure para el acceso.  

El trabajo requerido para proteger sus redes y permitir el acceso solo a los sitios web que lo necesitan impide crear conexiones SMB.  Aunque puede obtener acceso a recursos remotos, esto no incluye poder montar una unidad de disco remota.

![](./media/web-sites-integrate-with-vnet/how-it-works.png)
 
Si no ha configurado un servidor DNS con su red virtual, necesitará usar direcciones IP.  Asegúrese de exponer los puertos de los extremos que quiera usar a través del firewall.  Cuando haya que probar su conexión, el único método actualmente disponible consiste en usar un sitio web o un trabajo web que haga una llamada al extremo en cuestión.  Actualmente, herramientas como hacer ping o nslookup no funcionan en la consola Kudu.  Próximamente, esta área se mejorará.  

## Conexión a una red preexistente ##
Si quiere conectarse a una red virtual para ir a la hoja de su sitio web, haga clic en la ventana de la red virtual en la sección Redes y seleccione una de sus redes preexistentes.

![](./media/web-sites-integrate-with-vnet/connect-to-existing-vnet.png)
 
El sistema creará un certificado para autenticarse con su red virtual si este es el primer sitio web de su suscripción que establece una conexión con esa red.  Para ver el certificado vaya al portal actual, navegue a Redes virtuales, seleccione la red y elija la pestaña Certificados.  

En la imagen superior se ve una red llamada cantConnectVnet que está atenuada en gris y no se puede seleccionar.  Esto solo puede pasar por dos motivos.  Significa que no ha habilitado una VPN punto a sitio en su red o que no ha aprovisionado una puerta de enlace de enrutamiento dinámica en su VNET.  Cuando ambas condiciones se cumplan, podrá seleccionar la red virtual para la integración con su sitio web.

## Creación de una red virtual nueva y conexión a ella ##
Además, para conectarse a una red virtual preexistente, también puede crear una nueva red virtual desde la interfaz de usuario del sitio web y conectarse a ella automáticamente.  Para hacer esto, siga la misma ruta de acceso para conectarse a la interfaz de usuario de la red virtual y seleccione Crear nueva red virtual.  La interfaz de usuario que se abre le permite ponerle un nombre a la red, especificar el espacio de direcciones y establecer las direcciones de los servidores DNS que usará la red virtual.

![](./media/web-sites-integrate-with-vnet/create-new-vnet.png)
 
La creación de una nueva red virtual con puertas de enlace configuradas puede tardar hasta 30 minutos.  Durante este tiempo, la interfaz de usuario le avisará de que sigue el proceso de creación y mostrará el mensaje siguiente.

![](./media/web-sites-integrate-with-vnet/new-vnet-progress.png)

Cuando la red se haya unido al sitio web, este tendrá acceso a los recursos que haya en esa red virtual sobre TCP o UDP.  Si quiere acceder a los recursos de su sistema local que están disponibles a través de la VPN de sitio a sitio en su red virtual, tendrá que agregar rutas en su propia red corporativa para permitir que el tráfico vaya desde la red hasta las direcciones punto a sitio configuradas en su red virtual.

Cuando complete correctamente la integración, el portal mostrará información básica sobre la conexión y le ofrecerá una forma de desconectar el sitio web de la red y una forma de sincronizar los certificados usados para autenticar la conexión.  La sincronización puede ser necesaria si un certificado ha expirado o se ha revocado.  

![](./media/web-sites-integrate-with-vnet/vnet-status-portal.png)

Administración de la conexión de red virtual
Puede ver la lista de todas las redes virtuales actualmente asociadas a sitios dentro de un plan de hospedaje web visitando la hoja del plan de hospedaje web.  Puede haber, como mucho, cinco redes asociadas a un plan de hospedaje web estándar.

Si el plan de hospedaje web se escala a un plan inferior, como son el gratuito, el compartido o el básico, las conexiones de red virtual que usan los sitios web del plan se deshabilitarán.  Si el plan se vuelve a escalar a un plan estándar, esas conexiones de red se restablecen.

En este momento, no se puede mover una máquina virtual a una red virtual en Azure.  La máquina virtual necesita aprovisionarse en esa red virtual durante la creación.  

## Obtención de acceso a recursos locales ##
Al trabajar con una red virtual que se ha configurado con VPN sitio a sitio, hay que realizar un paso más para ofrecer acceso a sus recursos locales desde su sitio web de Azure.  Hay que agregar rutas a su red local para permitir que el tráfico vaya desde su red hasta las direcciones punto a punto configuradas en su VNET.  Para ver el intervalo IP de su conectividad punto a sitio, vaya al área Red en el portal actual, tal como se muestra aquí.

![](./media/web-sites-integrate-with-vnet/vpn-to-onpremise.png)

## Certificados ##
Para establecer una conexión segura con su red virtual, hay que hacer un intercambio de certificados.  Abajo puede ver la huella digital del certificado público que Sitios web Azure genera desde el portal actual de red.  

![](./media/web-sites-integrate-with-vnet/vpn-to-onpremise-certificate.png)

Si los certificados dejan de estar sincronizados por cualquier motivo, como una eliminación accidental del portal de red, la conectividad se interrumpe.  Para arreglar esto, existe la acción Sincronizar conexión en la interfaz de usuario de la red virtual de sitios web que restablecerá la conectividad.

Esta acción también debe usarse si agrega un DNS a la red virtual o si agrega VPN de sitio a sitio a la red.  

![](./media/web-sites-integrate-with-vnet/vnet-sync-connection.png)

## Comparación y contraste con las conexiones híbridas ##
Sitios web Azure ofrece otra característica llamada Conexiones híbridas que se parece un poco a la integración de las redes virtuales. Aunque algunos casos de uso coinciden, ninguna característica puede reemplazar a la otra.  Con Conexiones híbridas, puede establecer conexiones con varios extremos de aplicación en un conjunto de redes.  La característica de redes virtuales conecta su sitio web a una VNET que se puede conectar a su red local.  Esto funciona muy bien si sus recursos están todos en el ámbito de esa red.  

Otra diferencia es que hay que instalar un agente de retransmisión para que Conexiones híbridas funcione.  Este agente tiene que ejecutarse en una instancia de Windows Server.  Con la característica de redes virtuales, no hay que instalar nada y se puede obtener acceso a los recursos remotos sea cual sea el sistema operativo de hospedaje.  

Actualmente, también hay diferencias en los niveles de precios entre las dos funciones.  Esto se debe a que en los niveles más baratos, la característica Conexiones híbridas es muy útil para escenarios de desarrollo y pruebas, y solo da acceso a un número reducido de extremos.  La característica de redes virtuales le da acceso a todo lo que haya en la VNET o esté conectado a ella.  
