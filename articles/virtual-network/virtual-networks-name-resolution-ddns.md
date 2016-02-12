<properties 
   pageTitle="Uso de DNS dinámico para registrar nombres de host"
   description="En esta página se proporcionan algunos detalles sobre cómo configurar DNS dinámico para registrar los nombres de host en sus propios servidores DNS."
   services="virtual-network"
   documentationCenter="na"
   authors="GarethBradshawMSFT"
   manager="carmonm"
   editor="tysonn" />
<tags 
   ms.service="virtual-network"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="01/21/2016"
   ms.author="joaoma" />

# Uso de DNS dinámico para registrar nombres de host
[Azure ofrece resolución de nombres](virtual-networks-name-resolution-for-vms-and-role-instances.md) para las máquinas virtuales e instancias de roles. Cuando la resolución de nombres tiene que ir más allá de lo que Azure ofrece, puede proporcionar sus propios servidores DNS. Esto le ofrece la capacidad de personalizar su solución DNS para satisfacer sus propias necesidades específicas. Por ejemplo, mediante el acceso al controlador de dominio de AD local.

Azure no tiene la capacidad (por ejemplo, credenciales) para realizar directamente los registros en los servidores DNS, por lo que a menudo se necesitan medidas alternativas. A continuación se proporcionan algunos detalles de alto nivel de algunos de los escenarios más comunes.

## Clientes Windows ##
Los clientes Windows no unidos a dominio intentan realizar actualizaciones de DDNS no seguras cuando arrancan o cuando su dirección IP cambia. El nombre DNS es el nombre de host más el sufijo DNS primario. Azure deja en blanco el sufijo DNS primario, pero esto se puede invalidar en la máquina virtual, a través de la [UI](https://technet.microsoft.com/library/cc794784.aspx) o [con automatización](https://social.technet.microsoft.com/forums/windowsserver/3720415a-6a9a-4bca-aa2a-6df58a1a47d7/change-primary-dns-suffix).

Los clientes Windows unidos a dominio registran sus direcciones IP con el controlador de dominio con DDNS seguros. El proceso de unión a dominio establece el sufijo DNS primario en el cliente y administra la relación de confianza.

## Clientes Linux ##
Los clientes Linux, por lo general, no se registran con el servidor DNS al iniciarse; asumen que esto lo hace el servidor DHCP. El paquete Bind viene con una herramienta denominada *nsupdate* que puede utilizarse para enviar actualizaciones de DDNS. Como el protocolo DDNS está normalizado, *nsupdate* puede utilizarse incluso si no se usa Bind en el servidor DNS.

El cliente DHCP de Linux proporciona enlaces que permiten ejecutar scripts cuando se asigna o cambia una dirección IP. Por ejemplo, en */etc/dhcp/dhclient-exit-hooks.d/*. Esto se puede usar para registrar el nombre de host en DNS. Por ejemplo:
    
    	#!/bin/sh
    	requireddomain=mydomain.local
    
    	# only execute on the primary nic
    	if [ "$interface" != "eth0" ]
    	then
    		return
    	fi
    
		# when we have a new IP, perform nsupdate
		if [ "$reason" = BOUND ] || [ "$reason" = RENEW ] || 
		   [ "$reason" = REBIND ] || [ "$reason" = REBOOT ]
		then
    		host=`hostname`
	    	nsupdatecmds=/var/tmp/nsupdatecmds
  			echo "update delete $host.$requireddomain a" > $nsupdatecmds
  			echo "update add $host.$requireddomain 3600 a $new_ip_address" >> $nsupdatecmds
  			echo "send" >> $nsupdatecmds

  			nsupdate $nsupdatecmds
		fi

		#done
		exit 0;

El comando *nsupdate* también puede realizar actualizaciones de DDNS seguras. Por ejemplo, al usar un servidor Bind DNS, se [genera](http://linux.yyz.us/nsupdate/) un par de claves pública-privada, y el servidor DNS se [configura](http://linux.yyz.us/dns/ddns-server.html) con la parte pública de la clave, para que pueda verificar la firma de la solicitud. Para firmar la solicitud de actualización de DDNS, se debe proporcionar el par de claves para *nsupdate* con su opción *-k*.

Cuando se utiliza un servidor DNS de Windows, puede usar la autenticación Kerberos con el conmutador *-g* de nsupdate (no está disponible en la versión de Windows de *nsupdate*). Para ello, utilice *kinit* para cargar las credenciales (por ejemplo, desde un [archivo keytab](http://www.itadmintools.com/2011/07/creating-kerberos-keytab-files.html)), y luego *nsupdate -g* seleccionará las credenciales de la memoria caché.

Si es necesario, un sufijo de búsqueda DNS puede agregarse a las máquinas virtuales. El sufijo DNS se especifica en el archivo */etc/resolv.conf*. La mayoría de las distribuciones de Linux administran automáticamente el contenido de este archivo, por lo que normalmente no puede editarse. No obstante, el sufijo puede invalidarse con el comando *supersede* del cliente DHCP; en */etc/dhcp/dhclient.conf*, agregue:

		supersede domain-name <required-dns-suffix>;

<!---HONumber=AcomDC_0204_2016-->