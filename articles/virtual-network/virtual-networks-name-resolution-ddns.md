---
title: "Uso de DNS dinámico para registrar nombres de host"
description: "En esta página se proporcionan detalles sobre cómo configurar DNS dinámico para registrar los nombres de host en sus propios servidores DNS."
services: dns
documentationcenter: na
author: GarethBradshawMSFT
manager: timlt
editor: 
ms.assetid: c315961a-fa33-45cf-82b9-4551e70d32dd
ms.service: dns
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/23/2017
ms.author: garbrad
ms.openlocfilehash: 440a062e5fff73526b2d77d7d0a7c52ca72a66f1
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="using-dynamic-dns-to-register-hostnames-in-your-own-dns-server"></a>Uso de DNS dinámico para registrar nombres de host en su propio servidor DNS
[Azure ofrece resolución de nombres](virtual-networks-name-resolution-for-vms-and-role-instances.md) para las máquinas virtuales y las instancias de rol. Sin embargo, cuando la resolución de nombres tiene que ir más allá de lo que Azure ofrece, puede proporcionar sus propios servidores DNS. Esto le ofrece la capacidad de personalizar su solución DNS para satisfacer sus propias necesidades específicas. Por ejemplo, puede que necesite acceder a los recursos locales a través del controlador de dominio de Active Directory.

Si los servidores DNS personalizados se hospedan como máquinas virtuales de Azure, puede reenviar consultas de nombre de host para la misma red virtual a Azure para resolver los nombres de host. Si no quiere usar esta ruta, puede registrar los nombres de host de las máquinas virtuales en el servidor DNS usando para ello DNS dinámico.  Azure no tiene la capacidad (por ejemplo, credenciales) para crear directamente los registros en los servidores DNS, por lo que a menudo se necesitan medidas alternativas. Aquí presentamos algunos escenarios comunes con alternativas.

## <a name="windows-clients"></a>Clientes Windows
Los clientes Windows no unidos a dominio intenta realizar actualizaciones de DNS dinámico (DDNS) no seguro cuando arrancan o cuando su dirección IP cambia. El nombre DNS es el nombre de host más el sufijo DNS primario. Azure deja en blanco el sufijo DNS principal, pero puede establecerlo en la VM con la [interfaz de usuario](https://technet.microsoft.com/library/cc794784.aspx) o [la automatización](https://social.technet.microsoft.com/forums/windowsserver/3720415a-6a9a-4bca-aa2a-6df58a1a47d7/change-primary-dns-suffix).

Los clientes Windows unidos a dominio registran sus direcciones IP con el controlador de dominio mediante DNS dinámico seguro. El proceso de unión a dominio establece el sufijo DNS primario en el cliente y crea y mantiene la relación de confianza.

## <a name="linux-clients"></a>Clientes Linux
Por lo general, los clientes Linux no se registran con el servidor DNS al iniciarse, dado que asumen que esto lo hace el servidor DHCP. Los servidores DHCP de Azure no tienen la capacidad ni las credenciales para realizar registros en el servidor DNS.  Puede usar una herramienta denominada *nsupdate*, que se incluye en el paquete Bind, para enviar las actualizaciones de DNS dinámico. Dado que el protocolo DNS dinámico está estandarizado, puede usar *nsupdate* incluso si no usa Bind en el servidor DNS.

Puede usar los enlaces que proporciona el cliente DHCP para crear y mantener la entrada del nombre de host en el servidor DNS. Durante el ciclo de DHCP, el cliente ejecuta los scripts que aparecen en */etc/dhcp/dhclient-exit-hooks.d/*. Esto se puede usar para registrar la nueva dirección IP mediante el uso de *nsupdate*. Por ejemplo:

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

        
        

También puede usar el comando *nsupdate* para realizar actualizaciones de DNS dinámico seguras. Por ejemplo, cuando usa un servidor Bind DNS, se [genera](http://linux.yyz.us/nsupdate/)un par de claves pública-privada.  El servidor DNS está [configurado](http://linux.yyz.us/dns/ddns-server.html) con la parte pública de la clave de manera que se puede comprobar la firma de la solicitud. Necesita usar la opción *-k* para proporcionar el par de claves a *nsupdate* con el fin de que se firme la solicitud de actualización de DNS dinámico.

Cuando usa un servidor DNS de Windows, puede usar la autenticación Kerberos con el parámetro *-g* en *nsupdate* (no está disponible en la versión de *nsupdate* para Windows). Para hacerlo, use *kinit* para cargar las credenciales (por ejemplo, desde un [archivo keytab](http://www.itadmintools.com/2011/07/creating-kerberos-keytab-files.html)). A continuación, *nsupdate -g* recogerá las credenciales desde la memoria caché.

En caso de ser necesario, puede agregar un sufijo de búsqueda DNS a las máquinas virtuales. El sufijo DNS se especifica en el archivo */etc/resolv.conf* . La mayoría de las distribuciones de Linux administran automáticamente el contenido de este archivo, por lo que normalmente no se puede editar. Sin embargo, puede reemplazar el sufijo si usa el comando *supersede* del cliente DHCP. Para hacerlo, en */etc/dhcp/dhclient.conf*, debe agregar lo siguiente:

        supersede domain-name <required-dns-suffix>;

