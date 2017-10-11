---
title: "Guía de solución de problemas de Azure DNS | Microsoft Docs"
description: "Cómo solucionar problemas comunes con DNS de Azure"
services: dns
documentationcenter: na
author: jtuliani
manager: carmonm
editor: 
ms.assetid: 95b01dc3-ee69-4575-a259-4227131e4f9c
ms.service: dns
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/20/2017
ms.author: jonatul
ms.openlocfilehash: 1d9bb681a864bdc3e5a2f9c9a531d9566b16ada4
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/11/2017
---
# <a name="azure-dns-troubleshooting-guide"></a>Guía de solución de problemas de Azure DNS

Esta página proporciona información para solucionar problemas para preguntas que se plantean con frecuencia sobre Azure DNS.

Si estos pasos no resuelven el problema, también puede buscar o publicar su problema en nuestro [foro de soporte técnico de la comunidad en MSDN](https://social.msdn.microsoft.com/Forums/en-US/home?forum=WAVirtualMachinesVirtualNetwork). Como alternativa, abra una solicitud de soporte técnico de Azure.


## <a name="i-cant-create-a-dns-zone"></a>No puedo crear una zona DNS

Para resolver problemas habituales, pruebe uno o varios de los pasos siguientes:

1.  Revise los registros de auditoría de Azure DNS para determinar el motivo del error.
2.  Cada nombre de zona DNS debe ser único dentro de su grupo de recursos. Es decir, dos zonas DNS con el mismo nombre no pueden compartir un grupo de recursos. Intente usar otro nombre de zona u otro grupo de recursos.
3.  Es posible que vea el mensaje "Se alcanzó o superó el número máximo de zonas en la suscripción {ID de la suscripción}". Use otra suscripción de Azure, elimine algunas zonas o póngase en contacto con el soporte técnico de Azure para aumentar el límite de su suscripción.
4.  Es posible que vea el mensaje "La zona '{nombre de la zona}' no está disponible". Este error significa que Azure DNS no pudo asignar servidores de nombres para esta zona DNS. Pruebe a usar otro nombre de zona. O bien, si es el propietario del nombre de dominio, póngase en contacto con el soporte técnico de Azure, que puede asignarle servidores de nombres.


### <a name="recommended-documents"></a>**Documentos recomendados**

[DNS zones and records](dns-zones-records.md)
 (Registros y zonas DNS)<br>
[Creación de una zona DNS](dns-getstarted-create-dnszone-portal.md)

## <a name="i-cant-create-a-dns-record"></a>No puedo crear un registro de DNS

Para resolver problemas habituales, pruebe uno o varios de los pasos siguientes:

1.  Revise los registros de auditoría de Azure DNS para determinar el motivo del error.
2.  ¿El conjunto de registros ya existe?  El DNS de Azure administra los registros mediante los *conjuntos* de registros, que son una colección de registros con el mismo nombre y el mismo tipo. Si ya existe un registro con el mismo nombre y tipo, para agregar otro registro de dicho tipo debe editar el conjunto de registros existente.
3.  ¿Está intentando crear un registro en el vértice de la zona DNS (la "raíz" de la zona)? Si es así, la convención de DNS es usar el carácter ‘@’ como nombre del registro. Tenga en cuenta también que los estándares de DNS no permiten registros CNAME en el vértice de la zona.
4.  ¿Tiene un conflicto de CNAME?  Los estándares de DNS no permiten un registro CNAME con el mismo nombre que un registro de cualquier otro tipo. Si tiene un CNAME existente, la creación de un registro con el mismo nombre, pero de un tipo diferente genera un error.  Del mismo modo, la creación de un CNAME genera un error si el nombre coincide con un registro existente de otro tipo. Elimine el conflicto quitando el otro registro o eligiendo otro nombre de registro.
5.  ¿Ha alcanzado el límite en el número de conjuntos de registros permitido en una zona DNS? En Azure Portal se muestra el número actual y el número máximo de conjuntos de registros, en las propiedades de la zona. Si ha alcanzado este límite, elimine algunos conjuntos de registros o póngase en contacto con el soporte técnico de Azure para aumentar el límite de conjuntos de registros para esta zona y vuelva a intentarlo. 


### <a name="recommended-documents"></a>**Documentos recomendados**

[DNS zones and records](dns-zones-records.md)
 (Registros y zonas DNS)<br>
[Creación de una zona DNS](dns-getstarted-create-dnszone-portal.md)



## <a name="i-cant-resolve-my-dns-record"></a>No puedo resolver mi registro de DNS

La resolución de nombres de DNS es un proceso de varios pasos que puede generar un error por diversos motivos. Los pasos siguientes ayudan a investigar por qué se producen errores en la resolución de DNS para un registro de DNS en una zona hospedada en Azure DNS.

1.  Confirme que los registros de DNS se hayan configurado correctamente en el DNS de Azure. Revise los registros de DNS en Azure Portal y compruebe que el nombre de la zona, el nombre del registro y el tipo de registro son correctos.
2.  Confirme que los registros de DNS se resuelven correctamente en los servidores de nombres de DNS de Azure.
    - Si realiza consultas de DNS del equipo local, puede ver los resultados almacenados en caché que no reflejen el estado actual de los servidores de nombres.  Además, las redes corporativas suelen usan servidores proxy de DNS que impiden que las consultas de DNS se dirijan a servidores de nombres específicos.  Para evitar estos problemas, utilice un servicio de resolución de nombres basado en web, como [digwebinterface](http://digwebinterface.com).
    - Asegúrese de especificar los servidores de nombres correctos para la zona DNS, que se muestran en Azure Portal.
    - Compruebe que el nombre de DNS (tiene que especificar el nombre completo, incluido el nombre de zona) y el tipo de registro son correctos
3.  Confirme que el nombre de dominio del DNS se ha [delegado correctamente a los servidores de nombres de DNS de Azure](dns-domain-delegation.md). Hay un [muchos sitios web de terceros que ofrecen la validación de la delegación de DNS](https://www.bing.com/search?q=dns+check+tool). Se trata de una prueba de delegación de *zona*, por lo que solo se debe escribir el nombre de la zona DNS, no el nombre de registro completo.
4.  Una vez completado lo anterior, el registro de DNS ahora debe resolverse correctamente. Para comprobarlo, puede volver a usar [digwebinterface](http://digwebinterface.com), pero esta vez con la configuración predeterminada del servidor de nombres.


### <a name="recommended-documents"></a>**Documentos recomendados**

[Delegación de un dominio en DNS de Azure](dns-domain-delegation.md)



## <a name="how-do-i-specify-the-service-and-protocol-for-an-srv-record"></a>¿Cómo puedo especificar el "servicio" y el "protocolo" para un registro SRV?

El DNS de Azure administra los registros de DNS como conjuntos de registros: la colección de registros con el mismo nombre y el mismo tipo. Para un conjunto de registros SRV, el "servicio" y el "protocolo" deben especificarse como parte del nombre del conjunto de registros. Los otros parámetros SRV (la "prioridad", el "peso", el "puerto" y el "destino") se especifican por separado para cada registro del conjunto.

Ejemplo de nombres de registros SRV (nombre de servicio "sip", protocolo "tcp"):

- \_sip.\_tcp (crea un conjunto de registros en el vértice de la zona)
- \_sip.\_tcp.sipservice (crea un conjunto de registros con el nombre "sipservice")

### <a name="recommended-documents"></a>**Documentos recomendados**

[DNS zones and records](dns-zones-records.md)
 (Registros y zonas DNS)<br>
[Creación de registros y conjuntos de registros de DNS mediante Azure Portal](dns-getstarted-create-recordset-portal.md)
<br>
[Tipo de registro SRV (Wikipedia)](https://en.wikipedia.org/wiki/SRV_record)


## <a name="next-steps"></a>Pasos siguientes

* Más información sobre [Registros y zonas DNS](dns-zones-records.md)
* Para empezar a usar DNS de Azure, vea cómo [crear una zona DNS](dns-getstarted-create-dnszone-portal.md) y [crear registros DNS](dns-getstarted-create-recordset-portal.md).
* Para migrar una zona DNS existente, vea cómo [importar y exportar un archivo de zona DNS](dns-import-export.md).

