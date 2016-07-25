<properties 
   pageTitle="Obtenga información acerca de los últimos lanzamientos de SO invitado de Azure | Microsoft Azure" 
   description="Noticias sobre los lanzamientos más recientes y compatibilidad con el SO invitado de Servicios en la nube de Azure." 
   services="cloud-services" 
   documentationCenter="na" 
   authors="yuemlu" 
   manager="timlt" 
   editor=""/>

<tags
   ms.service="cloud-services"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="tbd" 
   ms.date="07/13/2016"
   ms.author="yuemlu"/>

# Matriz de compatibilidad del SDK y versiones del SO invitado de Azure
Proporciona información actualizada sobre los lanzamientos del SO invitado de Azure más recientes para Servicios en la nube. Esta información le ayudará a planear su ruta de actualización antes de que se deshabilite un SO invitado. Si configura los roles para utilizar actualizaciones *automáticas* del SO invitado como se describe en la documentación sobre la [configuración de actualización del SO invitado de Azure][], no es fundamental que lea esta página.

> [AZURE.IMPORTANT] Esta página se aplica a los roles web y de trabajo de Servicios en la nube, que se ejecutan en un sistema operativo invitado. **No se aplica** a las máquinas virtuales de IaaS.

<!-- -->

> [AZURE.TIP] Suscríbase a la [Fuente RSS de actualización del SO invitado][rss] para recibir la notificación más puntual en todos los cambios del sistema operativo invitado.

¿No sabe en qué consiste exactamente el SO invitado o cómo funcionan las versiones de este? Lea [esta](#how-it-works) sección.

## Actualizaciones de noticias
###### **13 de julio de 2016**
La implementación del SO invitado de julio comienza el 13 de julio de 2016, y está previsto que se lance el 12 de agosto de 2016.

###### **15 de junio de 2016**
La implementación del SO invitado de junio comienza el 15 de junio de 2016, y está previsto que se lance el 14 de julio de 2016.

###### **17 de mayo de 2016**
La implementación del SO invitado de mayo comienza el 17 de mayo de 2016 y está previsto que se publique el 10 de junio de 2016.

###### **18 de abril de 2016**
La implementación del SO invitado de abril comienza el 18 de abril de 2016 y está previsto que se publique el 12 de mayo de 2016.

###### **14 de marzo de 2016**
La implementación del SO invitado de marzo comienza el 14 de marzo de 2016 y está previsto que se publique el 8 de abril de 2016.

###### **22 de febrero de 2016**
La implementación del SO invitado de febrero comienza el 22 de febrero de 2016 y está previsto que se publique el 9 de marzo de 2016.

###### **18 de enero de 2016**
La implementación del SO invitado de enero comienza el 18 de enero de 2016 y está previsto que se publique el 12 de febrero de 2016.

###### **4 de enero de 2016**
El SO invitado 201511-02 de noviembre se publicó el 4 de enero de 2016 para la implementación. Esta versión del SO no está establecida como el SO predeterminado para actualización automática, por lo que el tiempo de aprovisionamiento de la implementación del SO invitado a la versión de SO 201511-02 de noviembre sería un poco más largo.

## Lanzamientos

## Lanzamientos de la familia 4
**Windows Server 2012 R2**

Admite .NET 4.0, 4.5, 4.5.1 y 4.5.2

>[AZURE.NOTE] Las fechas con un asterisco (*) están sujetas a cambios.

| Cadena de configuración | Fecha de lanzamiento | Fecha de deshabilitación | Fecha de expiración |
| ------------------------------ | --------------- | ------------- | ---- |
| WA-GUEST-OS-4.34\_201607-01 | 12 de agosto de 2016* | Publicación 4.36 | TBD |
| WA-GUEST-OS-4.33\_201606-01 | 13 de julio de 2016 | Después de la versión 4.35 | TBD |
| WA-GUEST-OS-4.32\_201605-01 | 10 de junio de 2016 | Post 4.34 | TBD |
| WA-GUEST-OS-4.31\_201604-01 | 2 de mayo de 2016 | 13 de agosto de 2016 | TBD |
| WA-GUEST-OS-4.30\_201603-01 | 7 de abril de 2016 | 10 de julio de 2016 | TBD |
| WA-GUEST-OS-4.29\_201602-02 | 12 de marzo de 2016 | 2 de junio de 2016 | TBD |
| WA-GUEST-OS-4.28\_201601-01 | 12 de febrero de 2016 | 7 de mayo de 2016 | TBD | 
| WA-GUEST-OS-4.27\_201512-01 | 12 de enero de 2016 | 12 de abril de 2016 | TBD |
| ~~WA-GUEST-OS-4.26\_201511-02~~ | 4 de enero de 2016 | 12 de marzo de 2016 | TBD |
| ~~WA-GUEST-OS-4.26\_201511-01~~ | 10 de diciembre de 2015 | 12 de marzo de 2016 | TBD |
| ~~WA-GUEST-OS-4.25\_201510-01~~ | 6 de noviembre de 2015 | 12 de febrero de 2016 | TBD |
| ~~WA-GUEST-OS-4.24\_201509-01~~ | 1 de octubre de 2015 | 10 de enero de 2016 | TBD |
| ~~WA-GUEST-OS-4.23\_201508-02~~ | 9 de septiembre de 2015 | 6 de diciembre de 2015 | TBD |
| ~~WA-GUEST-OS-4.22\_201507-02~~ | 7 de agosto de 2015 | 1 de noviembre de 2015 | TBD |
| ~~WA-GUEST-OS-4.21\_201506-01~~ | 9 de julio de 2015 | 9 de octubre de 2015 | TBD |
| ~~WA-GUEST-OS-4.20\_201505-02~~ | 12 de junio de 2015 | 7 de septiembre de 2015 | TBD |
| ~~WA-GUEST-OS-4.19\_201504-01~~ | 17 de abril de 2015 | 9 de agosto de 2015 | TBD |

## Lanzamientos de la familia 3

**Windows Server 2012**

Admite .NET 4.0, 4.5, 4.5.1 y 4.5.2

>[AZURE.NOTE] Las fechas con un asterisco (*) están sujetas a cambios.

| Cadena de configuración | Fecha de lanzamiento | Fecha de deshabilitación | Fecha de expiración |
| ------------------------------ | -------------- | ------------- | --- |
| WA-GUEST-OS-3.41\_201607-01 | 12 de agosto de 2016* | Publicación 3.43 | TBD |
| WA-GUEST-OS-3.40\_201606-01 | 13 de julio de 2016 | Después de la versión 3.42 | TBD |
| WA-GUEST-OS-3.39\_201605-01 | 10 de junio de 2016 | Post 3.41 | TBD |
| WA-GUEST-OS-3.38\_201604-01 | 2 de mayo de 2016 | 13 de agosto de 2016 | TBD |
| WA-GUEST-OS-3.37\_201603-01 | 7 de abril de 2016 | 10 de julio de 2016 | TBD |
| WA-GUEST-OS-3.36\_201602-02 | 12 de marzo de 2016 | 2 de junio de 2016 | TBD |
| WA-GUEST-OS-3.35\_201601-01 | 12 de febrero de 2016 | 7 de mayo de 2016 | TBD |
| WA-GUEST-OS-3.34\_201512-01 | 12 de enero de 2016 | 12 de abril de 2016 | TBD |
| ~~WA-GUEST-OS-3.33\_201511-02~~ | 4 de enero de 2016 | 12 de marzo de 2016 | TBD |
| ~~WA-GUEST-OS-3.33\_201511-01~~ | 10 de diciembre de 2015 | 12 de marzo de 2016 | TBD |
| ~~WA-GUEST-OS-3.32\_201510-01~~ | 6 de noviembre de 2015 | 12 de febrero de 2016 | TBD |
| ~~WA-GUEST-OS-3.31\_201509-01~~ | 1 de octubre de 2015 | 10 de enero de 2016 | TBD |
| ~~WA-GUEST-OS-3.30\_201508-02~~ | 9 de septiembre de 2015 | 6 de diciembre de 2015 | TBD |
| ~~WA-GUEST-OS-3.29\_201507-02~~ | 7 de agosto de 2015 | 1 de noviembre de 2015 | TBD |
| ~~WA-GUEST-OS-3.28\_201506-01~~ | 9 de julio de 2015 | 9 de octubre de 2015 | TBD |
| ~~WA-GUEST-OS-3.27\_201505-02~~ | 12 de junio de 2015 | 7 de septiembre de 2015 | TBD |
| ~~WA-GUEST-OS-3.26\_201504-01~~ | 17 de abril de 2015 | 9 de agosto de 2015 | TBD |


## Lanzamientos de la familia 2

**Windows Server 2008 R2 SP1**

Admite .NET 3.5, 4.0, 4.5, 4.5.1, 4.5.2

>[AZURE.NOTE] Las fechas con un asterisco (*) están sujetas a cambios.

| Cadena de configuración | Fecha de lanzamiento | Fecha de deshabilitación | Fecha de expiración |
| ------------------------------ | ------------- | ------------  | --- |
| WA-GUEST-OS-2.53\_201607-01 | 12 de agosto de 2016* | Publicación 2.55 | TBD |
| WA-GUEST-OS-2.52\_201606-01 | 13 de julio de 2016 | Después de la versión 2.54 | TBD |
| WA-GUEST-OS-2.51\_201605-01 | 10 de junio de 2016 | Post 2.53 | TBD |
| WA-GUEST-OS-2.50\_201604-01 | 2 de mayo de 2016 | 13 de agosto de 2016 | TBD |
| WA-GUEST-OS-2.49\_201603-01 | 7 de abril de 2016 | 10 de julio de 2016 | TBD |
| WA-GUEST-OS-2.48\_201602-02 | 12 de marzo de 2016 | 2 de junio de 2016 | TBD |
| WA-GUEST-OS-2.47\_201601-01 | 12 de febrero de 2016 | 7 de mayo de 2016 | TBD |
| WA-GUEST-OS-2.46\_201512-01 | 12 de enero de 2016 | 12 de abril de 2016 | TBD |
| ~~WA-GUEST-OS-2.45\_201511-02~~ | 4 de enero de 2016 | 12 de marzo de 2016 | TBD |
| ~~WA-GUEST-OS-2.45\_201511-01~~ | 10 de diciembre de 2015 | 12 de marzo de 2016 | TBD |
| ~~WA-GUEST-OS-2.44\_201510-01~~ | 6 de noviembre de 2015 | 12 de febrero de 2016 | TBD |
| ~~WA-GUEST-OS-2.43\_201509-01~~ | 1 de octubre de 2015 | 10 de enero de 2016 | TBD |
| ~~WA-GUEST-OS-2.42\_201508-02~~ | 9 de septiembre de 2015 | 6 de diciembre de 2015 | TBD |
| ~~WA-GUEST-OS-2.41\_201507-02~~ | 7 de agosto de 2015 | 1 de noviembre de 2015 | TBD |
| ~~WA-GUEST-OS-2.40\_201506-01~~ | 9 de julio de 2015 | 9 de octubre de 2015 | TBD |
| ~~WA-GUEST-OS-2.39\_201505-02~~ | 12 de junio de 2015 | 7 de septiembre de 2015 | TBD |
| ~~WA-GUEST-OS-2.38\_201504-01~~ | 17 de abril de 2015 | 9 de agosto de 2015 | TBD |

## Actualizaciones de revisiones de MSRC
La lista de revisiones que se incluyen con cada lanzamiento del SO invitado mensual está disponible [aquí][patches].

## Compatibilidad con SDK

Aunque la [directiva de retirada para el SDK de Azure][retire policy sdk] indica que solo se admiten las versiones posteriores a la 2.2, determinadas familias de SO invitado permiten usar versiones anteriores. Siempre debe usar el SDK compatible más reciente.

| Familia del SO invitado | Versiones del SDK compatibles |
| --------------- | ----------------------- |
| 4 | Versión 2.1 o superior |
| 3 | Versión 1.8 o superior |
| 2 | Versión 1.3 o superior |
| 1 | Versión 1.0 o superior |

## Información de los lanzamientos del SO invitado
Hay tres fechas importantes para los lanzamientos de SO invitado: la de **lanzamiento**, la de **deshabilitación** y la de **expiración**. Se considera que un SO invitado se encuentra disponible cuando figura en el Portal y se puede seleccionar como el SO invitado de destino. Cuando un SO invitado llega a su fecha de **deshabilitación**, se quita de Azure. Sin embargo, cualquier servicio en la nube que utilice dicho SO invitado seguirá funcionando con normalidad.

El período entre las fechas de **deshabilitación** y de **expiración** permite realizar la transición de un SO invitado a uno más reciente con tranquilidad. Si usa la opción *automática* para el SO invitado, siempre tendrá la versión más reciente, por lo que no tendrá que preocuparse de que expire.

Una vez que transcurra la fecha de **expiración**, cualquier servicio en la nube que siga usando ese SO invitado se detendrá, eliminará o actualizará de forma obligada. Puede leer más sobre la directiva de retirada [aquí][retirepolicy].

## Diferencias entre la versión y la familia de los SO invitados
Las familias del SO invitado se basan en versiones lanzadas de Microsoft Windows Server. El SO invitado es el sistema operativo subyacente en el que se ejecutan Servicios en la nube de Azure. Cada SO invitado tiene un número de familia, versión y lanzamiento.

- **Familia del SO invitado** Versión del sistema operativo Windows Server en la que se basa un SO invitado. Por ejemplo, la *familia 3* se basa en Windows Server 2012.

- **Versión del SO invitado** Corresponde a la imagen de familia del SO invitado, más las revisiones relevantes del [Centro de respuestas de seguridad de Microsoft (MSRC)][msrc] disponibles en la fecha en que se genera la nueva versión del SO invitado. Es posible que no se incluyan todas las revisiones.

    Los números empiezan por 0 y se incrementan en 1 cada vez que se agrega un nuevo conjunto de actualizaciones. Solo se muestran los ceros finales si es importante. Es decir, la versión 2.10 es una versión mucho más posterior y diferente que la versión 2.1.

- **Lanzamiento del SO invitado** Relanzamiento de una versión de SO invitado. Un relanzamiento se produce si Microsoft encuentra, durante las pruebas, problemas que requieran cambios. El lanzamiento más reciente siempre reemplaza a los lanzamientos anteriores, ya sean públicos o no. El portal de Azure clásico solo permitirá a los usuarios elegir el lanzamiento más reciente de una versión concreta. Las implementaciones que se ejecutan en un lanzamiento anterior no suelen ser una actualización forzada, según la gravedad del error.

En el ejemplo siguiente, 2 es la familia, 12 es la versión y "rel2" es el lanzamiento.

**Lanzamiento del SO invitado**: 2.12 rel2

**Cadena de configuración para este lanzamiento** -WA-GUEST-OS-2. 12\_201208-02

La cadena de configuración para un SO invitado incluye esta misma información incrustada, junto con una fecha que muestra qué revisiones de MSRC se tuvieron en cuenta para ese lanzamiento. En este ejemplo, las revisiones de MSRC generadas para Windows Server 2008 R2 hasta e incluido agosto de 2012 se tuvieron en cuenta para su inclusión. Solo se incluyen las revisiones que se aplican específicamente a esa versión de Windows Server. Por ejemplo, si una revisión de MSRC se aplica a Microsoft Office, no se incluirá porque este producto no forma parte de la imagen base de Windows Server.

## Proceso de actualización del SO invitado
Esta página incluye información sobre los próximos lanzamientos del SO invitado. Los clientes han indicado que desean saber cuándo se produce un lanzamiento porque sus roles de servicio en la nube se reiniciarán si están establecidos en actualización "Automática". Las versiones del SO invitado suelen producirse al menos 5 días después de que tenga lugar la actualización de MSRC el segundo martes de cada mes. Los nuevos lanzamientos incluyen todas las revisiones de MSRC relevantes para cada familia del SO invitado.

Microsoft Azure publica actualizaciones constantemente. El SO invitado es solo una de estas actualizaciones en la canalización. Un lanzamiento puede verse afectado por una cantidad de factores tal que no se puede enumerar aquí. Además, Azure se ejecuta literalmente en cientos de miles de máquinas. Esto significa que es imposible especificar una fecha y hora exacta de reinicio de los roles. Actualizaremos la [fuente RSS del SO invitado][rss] con la información más reciente que tengamos, pero se debe considera la fecha como un período aproximado. Somos conscientes de que esto es problemático para los clientes y estamos trabajando en un plan limitar o regular los reinicios.

Cuando se publica un nuevo lanzamiento del SO invitado, puede tardar tiempo en propagarse completamente en Azure. A medida que los servicios se actualizan al nuevo SO invitado, estos se reinician respetando los dominios de actualización. Los servicios configurados para usar actualizaciones "Automáticas" serán los primeros en obtener el lanzamiento. Después de la actualización, verá la nueva versión del SO invitado para el servicio en el Portal de Azure clásico. Durante ese período se pueden producir relanzamientos. Algunas versiones se pueden implementar durante largos períodos de tiempo y puede que no se produzcan actualizaciones reinicios de actualizaciones automáticas durante muchas semanas después de la fecha de lanzamiento oficial. Una vez que un SO invitado está disponible, puede elegir esa versión explícitamente desde el portal o en el archivo de configuración.

Para una gran cantidad de información valiosa sobre los reinicios y punteros para obtener más información técnica detallada de las actualizaciones de SO invitado y de SO host, consulte la entrada de blog de MSDN titulada [Reinicios de instancias de rol debido a actualizaciones del SO][restarts].

Si actualiza manualmente el SO invitado, lea la [Directiva de retirada del SO invitado][retirepolicy].


## Directiva de compatibilidad y retirada del SO invitado
La directiva de compatibilidad y retirada del SO invitado se explica [aquí][retirepolicy].

[Install .NET on a Cloud Service Role]: https://azure.microsoft.com/es-ES/documentation/articles/cloud-services-dotnet-install-dotnet/?WT.mc_id=azurebg_email_Trans_963_RevisedNET_Update
[configuración de actualización del SO invitado de Azure]: cloud-services-how-to-configure.md
[rss]: http://sxp.microsoft.com/feeds/3.0/msdntn/WindowsAzureOSUpdates
[ssl3 announcement]: http://azure.microsoft.com/blog/2014/12/09/azure-security-ssl-3-0-update/
[Microsoft Security Advisory 3009008]: https://technet.microsoft.com/library/security/3009008.aspx
[ssl3-fixit]: http://go.microsoft.com/?linkid=9863266
[MS14-066]: https://technet.microsoft.com/library/security/ms14-066.aspx
[MS14-046]: https://technet.microsoft.com/library/security/ms14-046.aspx
[retire policy sdk]: https://msdn.microsoft.com/library/dn479282.aspx
[server and gos]: https://msdn.microsoft.com/library/dn775043.aspx
[azuresupport]: http://azure.microsoft.com/support/options/
[net install pkg]: http://www.microsoft.com/download/details.aspx?id=42643
[msrc]: http://www.microsoft.com/security/msrc/default.aspx
[update guest os portal]: https://msdn.microsoft.com/library/gg433101.aspx
[update guest os svc]: https://msdn.microsoft.com/library/gg456324.aspx
[restarts]: http://blogs.msdn.com/b/kwill/archive/2012/09/19/role-instance-restarts-due-to-os-upgrades.aspx
[patches]: cloud-services-guestos-msrc-releases.md
[retirepolicy]: cloud-services-guestos-retirement-policy.md
[fam1retire]: cloud-services-guestos-family1-retirement.md
 

<!---HONumber=AcomDC_0713_2016-->