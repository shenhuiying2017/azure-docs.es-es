<properties 
	pageTitle="Cómo configurar la compatibilidad de red virtual para una Caché en Redis de Azure Premium" 
	description="Aprenda a crear y a administrar la compatibilidad con la red Virtual para las instancias de la Caché en Redis de Azure de nivel Premium" 
	services="redis-cache" 
	documentationCenter="" 
	authors="steved0x" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="cache" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="cache-redis" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="12/03/2015" 
	ms.author="sdanie"/>

# Cómo configurar la compatibilidad de red virtual para una Caché en Redis de Azure Premium
Caché en Redis de Azure tiene diferentes ofertas de caché que proporcionan flexibilidad en la elección del tamaño y las características de la caché, incluido el nuevo nivel Premium.

El nivel premium de Caché en Redis de Azure incluye la agrupación en clústeres, la persistencia y la compatibilidad de red virtual (VNET). Una red virtual es una representación de su propia red en la nube. Cuando una instancia de Caché en Redis de Azure se configure con una red virtual, no será accesible públicamente y solo se podrá tener acceso a ella desde clientes dentro de la red virtual. En este artículo se describe cómo configurar la compatibilidad de red virtual para una instancia de Caché en Redis de Azure premium.

Para obtener información sobre otras características de caché premium, vea [Cómo configurar la persistencia para una Caché en Redis de Azure Premium](cache-how-to-premium-persistence.md) y [Cómo configurar la agrupación en clústeres para Caché en Redis de Azure Premium](cache-how-to-premium-clustering.md).

## ¿Por qué la red virtual?
La implementación de la [Red virtual de Azure](https://azure.microsoft.com/services/virtual-network/) ofrece seguridad mejorada y aislamiento para su Caché en Redis de Azure, así como subredes, directivas de control de acceso y otras características para restringir aún más el acceso a Caché en Redis de Azure.

## Compatibilidad con redes virtuales
La compatibilidad de la red virtual está configurada en la hoja **Nueva caché en Redis** durante la creación de la memoria caché. Para crear una caché, inicie sesión en el [Portal de Azure](https://portal.azure.com) y haga clic en **Nuevo**->**Datos y almacenamiento**>**Caché en Redis**.

![Creación de una caché en Redis][redis-cache-new-cache-menu]

Para configurar la compatibilidad de redes virtuales, seleccione primero una de las caché **Premium** en la hoja **Elija su plan de tarifa**.

![Elegir su plan de tarifa][redis-cache-premium-pricing-tier]

La integración de la red virtual de memoria de Caché en Redis de Azure se configura en la hoja **Red virtual**. Desde aquí puede seleccionar una red virtual clásica existente. Para usar una red virtual nueva, siga los pasos descritos en [Creación de una red virtual (clásica) usando el Portal de Azure](../virtual-network/virtual-networks-create-vnet-classic-pportal.md) y luego regrese a la hoja de **Red virtual de Caché en Redis** para seleccionarla.

>[AZURE.NOTE]Caché en Redis de Azure funciona con redes virtuales clásicas. Para información sobre la creación de una red virtual clásica, consulte [Creación de una red virtual (clásica) usando el Portal de Azure](../virtual-network/virtual-networks-create-vnet-classic-pportal.md). Para obtener información sobre cómo conectar redes virtuales clásicas a redes virtuales ARM, consulte [Conexión de redes virtuales clásicas a redes virtuales nuevas](../virtual-network/virtual-networks-arm-asm-s2s.md).

![Red virtual][redis-cache-vnet]

Haga clic en **Red virtual** en la hoja **Red virtual** para seleccionar y configurar su red virtual.

![Red virtual][redis-cache-vnet-select]

Haga clic en la red virtual que desee para seleccionarla.

![Red virtual][redis-cache-vnet-subnet]

Haga clic en Subred para seleccionar la subred que desea.

![Red virtual][redis-cache-vnet-ip]

Escriba la **Dirección IP estática** deseada y haga clic en **Aceptar** para guardar la configuración de la red virtual. Si la dirección IP estática seleccionada ya está en uso, se mostrará un mensaje de error.

Cuando se cree la memoria caché, se puede tener acceso a ella únicamente por los clientes dentro de la misma red virtual.

>[AZURE.IMPORTANT]Para obtener acceso a su instancia de Caché en Redis de Azure al usar una red virtual, pase la dirección IP estática de la memoria caché de la red virtual como el primer parámetro y pase un parámetro `sslhost` con el punto de conexión de la memoria caché. En el ejemplo siguiente, la dirección IP estática es `10.10.1.5` y el punto de conexión de la memoria caché es `contoso5.redis.cache.windows.net`.

	private static Lazy<ConnectionMultiplexer> lazyConnection = new Lazy<ConnectionMultiplexer>(() =>
	{
	    return ConnectionMultiplexer.Connect("10.10.1.5,sslhost=contoso5.redis.cache.windows.net,abortConnect=false,ssl=true,password=password");
	});
	
	public static ConnectionMultiplexer Connection
	{
	    get
	    {
	        return lazyConnection.Value;
	    }
	}

## P+F de red virtual de Caché en Redis de Azure

La lista siguiente contiene las respuestas a las preguntas más frecuentes sobre el escalado de Caché en Redis de Azure.

## ¿Cuáles son algunos de los problemas comunes de configuración incorrecta con Caché en Redis de Azure y las redes virtuales?

En la lista siguiente se incluyen algunos errores de configuración comunes que pueden impedir que Caché en Redis de Azure funcione correctamente.

-	Falta de acceso a DNS. Las instancias de Caché en Redis de Azure de una red virtual requieren acceso a DNS para elementos del sistema de supervisión y tiempo de ejecución de la memoria caché. Si la instancia de la memoria caché no tiene acceso a DNS, no funcionará la supervisión y la memoria caché no se ejecutará correctamente.
-	Puertos TCP bloqueados que los clientes usan para conectarse a Redis, es decir, 6379 o 6380.
-	Tráfico HTTPS saliente bloqueado o interceptado desde la red virtual. Caché en Redis de Azure usa el tráfico HTTPS saliente para servicios de Azure, especialmente el almacenamiento.
-	Máquinas virtuales de instancia de rol de Redis bloqueadas frente a la comunicación entre sí dentro de la red virtual. Se debe permitir a las instancias de rol de Redis comunicarse entre sí mediante TCP en cualquiera de los puertos usados, lo cual puede estar sujeto a cambios, pero como mínimo se puede suponer que son todos los puertos usados en el archivo CSDEF de Redis.
-	El equilibrador de carga de Azure bloqueado frente a la conexión a las máquinas virtuales de Redis en el puerto TCP/HTTP 16001. Caché en Redis de Azure depende del sondeo de equilibrador de carga de Azure predeterminado para determinar qué instancias de rol están activas. El sondeo del equilibrador de carga predeterminado funciona de sondeo de equilibrador al hacer ping en el agente de invitado de Azure en el puerto 16001. Solo las instancias de rol que responden al ping se colocarán en rotación para recibir el tráfico reenviado por el ILB. Cuando no haya ninguna instancia en rotación porque los pings generen error al estar los puertos bloqueados, el ILB no aceptará conexiones TCP entrantes.
-	Tráfico web de la aplicación de cliente bloqueada usado para la validación de clave pública de SSL. Los clientes de Redis (dentro de la red virtual) deben poder hacer el tráfico HTTP a la Internet pública para descargar certificados de entidades de certificados y listas de revocación de certificados para realizar la validación de certificados SSL al usar el puerto 6380 para conectarse a Redis y realizar la autenticación del servidor SSL.
-	Equilibrador de carga de Azure bloqueado de la conexión a máquinas virtuales de Redis en un clúster mediante TCP en el puerto 1300x (13000, 13001, etc.) o 1500x (15000, 15001, etc.). Las redes virtuales se configuran en el archivo csdef con un sondeo del equilibrador de carga para abrir estos puertos. El equilibrador de carga de Azure debe permitirse por NSG, los NSG predeterminados hacen esto mediante la etiqueta AZURE\_LOADBALANCER. El equilibrador de carga de Azure tiene una sola dirección IP estática de 168.63.126.16. Para obtener más información, consulte [¿Qué es un grupo de seguridad de red?](../virtual-network/virtual-networks-nsg.md).

## ¿Puedo usar las redes virtuales con una memoria caché básica o estándar?

Las redes virtuales solo se pueden usar con memoria caché premium.

## Pasos siguientes
Obtenga información acerca de cómo usar más características de la memoria caché del nivel Premium.

-	[Cómo configurar la persistencia para una memoria Caché en Redis de Azure Premium](cache-how-to-premium-persistence.md)
-	[Cómo configurar la agrupación en clústeres para una memoria Caché en Redis de Azure Premium](cache-how-to-premium-clustering.md)





  
<!-- IMAGES -->

[redis-cache-new-cache-menu]: ./media/cache-how-to-premium-vnet/redis-cache-new-cache-menu.png

[redis-cache-premium-pricing-tier]: ./media/cache-how-to-premium-vnet/redis-cache-premium-pricing-tier.png

[redis-cache-vnet]: ./media/cache-how-to-premium-vnet/redis-cache-vnet.png

[redis-cache-vnet-select]: ./media/cache-how-to-premium-vnet/redis-cache-vnet-select.png

[redis-cache-vnet-ip]: ./media/cache-how-to-premium-vnet/redis-cache-vnet-ip.png

[redis-cache-vnet-subnet]: ./media/cache-how-to-premium-vnet/redis-cache-vnet-subnet.png

<!---HONumber=AcomDC_1210_2015-->