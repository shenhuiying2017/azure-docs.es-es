---
title: "Obtención de información acerca de los últimos lanzamientos de SO invitado de Azure | Microsoft Docs"
description: "Noticias sobre los lanzamientos más recientes y compatibilidad con el SO invitado de Azure Cloud Services."
services: cloud-services
documentationcenter: na
author: raiye
manager: timlt
editor: 
ms.assetid: 6306cafe-1153-44c7-8554-623b03d59a34
ms.service: cloud-services
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: tbd
ms.date: 1/4/2018
ms.author: raiye
ms.openlocfilehash: e645d57dfff040eb05f4d395fac058b2b485933b
ms.sourcegitcommit: d6984ef8cc057423ff81efb4645af9d0b902f843
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/05/2018
---
# <a name="azure-guest-os-releases-and-sdk-compatibility-matrix"></a>Matriz de compatibilidad del SDK y versiones del SO invitado de Azure
Proporciona información actualizada sobre los lanzamientos del SO invitado de Azure más recientes para Cloud Services. Esta información le ayuda a planear la ruta de actualización antes de que se deshabilite un SO invitado. Si configura los roles para utilizar actualizaciones *automáticas* del SO invitado como se describe en la documentación sobre la [configuración de actualización del SO invitado de Azure][Azure Guest OS Update Settings], no es fundamental que lea esta página.

> [!IMPORTANT]
> Esta página se aplica a los roles web y de trabajo de Cloud Services, que se ejecutan en un sistema operativo invitado. **No se aplica** a las máquinas virtuales de IaaS.
>
>


> [!TIP]
>  Suscríbase a la [Fuente RSS de actualización del SO invitado] para recibir notificaciones puntuales sobre todos los cambios del sistema operativo invitado.
>
>

> [!IMPORTANT]
> A partir de la implementación de noviembre, solo las 2 últimas versiones del SO invitado serán compatibles y estarán disponibles en Azure Portal.
>
>

¿No sabe en qué consiste exactamente el SO invitado o cómo funcionan las versiones de este? Lea [esta](#how-it-works) sección.

## <a name="news-updates"></a>Actualizaciones de noticias
###### <a name="january-4-2018"></a>**4 de enero de 2018**
El sistema operativo invitado de enero se ha publicado para cuatro familias de SO (WA-GUEST-OS-4.50_201801-01) & 5 (WA-GUEST-OS-5.15_201801-01) y contiene revisiones de seguridad importantes.  

###### <a name="january-4-2018"></a>**4 de enero de 2018**
Se ha publicado el sistema operativo invitado de diciembre.

###### <a name="december-14-2017"></a>**14 de diciembre de 2017**
Se ha publicado el sistema operativo invitado de noviembre.

###### <a name="november-8-2017"></a>**8 de noviembre de 2017**
Se ha publicado el SO invitado de octubre.

###### <a name="october-6-2017"></a>**6 de octubre de 2017**
Se ha publicado el SO invitado de septiembre. Para la versión de septiembre de 2016 de Windows Server, netfx3 está habilitado de forma predeterminada. Los clientes deben agregar "dism /online /disable-feature /featurename:netfx3" en su OnStart si el flujo de trabajo les exige que ejecuten una aplicación .NET 2.x con un tiempo de ejecución de 4.x o si ejecutaron una aplicación .NET 2.x, controlaron el error y luego ejecutaron una aplicación .NET 4.x.

###### <a name="september-14-2017"></a>**14 de septiembre de 2017**
La implementación del SO invitado de septiembre comienza el 14 de septiembre y está previsto que se lance el 9 de octubre.

###### <a name="august-24-2017"></a>**24 de agosto de 2017**
Se ha publicado el SO invitado de agosto.

###### <a name="august-3-2017"></a>**3 de agosto de 2017**
Se ha publicado el SO invitado de julio.

###### <a name="july-19-2017"></a>**19 de julio de 2017**
La implementación del SO invitado del mes de julio comienza el 19 de julio y está previsto que se lance el 8 de agosto.

###### <a name="july-7-2017"></a>**7 de julio de 2017**
Se ha publicado el SO invitado de junio.

###### <a name="june-16-2017"></a>**16 de junio de 2017**
La implementación del SO invitado del mes de junio comienza el 16 de junio y está previsto que se lance el 11 de julio.


## <a name="releases"></a>Lanzamientos
## <a name="family-5-releases"></a>Lanzamientos de la familia 5
**Windows Server 2016**

Versión de .NET Framework instalada: 4.0, 4.5, 4.5.1, 4.5.2, 4.6, 4.6.1, 4.6.2

> [!NOTE]
> Las fechas con un asterisco (*) están sujetas a cambios.
>
> La contraseña de RDP para la Familia de SO 5 debe tener 10 caracteres como mínimo.
>

| Cadena de configuración | Fecha de lanzamiento | Fecha de deshabilitación | Fecha de expiración |
| --- | --- | --- | --- |
| WA-GUEST-OS-5.15_201801-01 |4 de enero de 2018 |Post 5.17 |TBD |
| WA-GUEST-OS-5.14_201712-01 |4 de enero de 2018 |Post 5.16 |TBD |
| WA-GUEST-OS-5.13_201711-01 |14 de diciembre de 2017 |Post 5.15 |TBD |
|~~WA-GUEST-OS-5.12_201710-02~~ |8 de noviembre de 2017 |4 de enero de 2018 |TBD |
|~~WA-GUEST-OS-5.11_201709-01~~ |6 de octubre de 2017 |14 de diciembre de 2017 |TBD |
|~~WA-GUEST-OS-5.10_201708-01~~ |24 de agosto de 2017 |14 de diciembre de 2017 |TBD |
|~~WA-GUEST-OS-5.9_201707-01~~ |3 de agosto de 2017 |8 de noviembre de 2017 |TBD |
|~~WA-GUEST-OS-5.8_201706-01~~ |7 de julio de 2017 |6 de octubre de 2017 |TBD |
|~~WA-GUEST-OS-5.7_201705-01~~ |5 de junio de 2017 |24 de agosto de 2017 |TBD |
|~~WA-GUEST-OS-5.6_201704-01~~ |9 de mayo de 2017 |3 de agosto de 2017 |TBD |
|~~WA-GUEST-OS-5.5_201703-01~~ |10 de abril de 2017 |7 de julio de 2017 |TBD |
|~~WA-GUEST-OS-5.4_201612-01~~ |10 de enero de 2017 |5 de junio de 2017|TBD |

## <a name="family-4-releases"></a>Lanzamientos de la familia 4
**Windows Server 2012 R2**

Versión de .NET Framework instalada: 4.0, 4.5, 4.5.1, 4.5.2

> [!NOTE]
> Las fechas con un asterisco (*) están sujetas a cambios.
>
>

| Cadena de configuración | Fecha de lanzamiento | Fecha de deshabilitación | Fecha de expiración |
| --- | --- | --- | --- |
| WA-GUEST-OS-4.50_201801-01 |4 de enero de 2018 |Post 4.52 |TBD |
| WA-GUEST-OS-4.49_201712-01 |4 de enero de 2018 |Post 4.51 |TBD |
| WA-GUEST-OS-4.48_201711-01 |14 de diciembre de 2017 |Post 4.50 |TBD |
|~~WA-GUEST-OS-4.47_201710-02~~ |8 de noviembre de 2017 |4 de enero de 2018 |TBD |
|~~WA-GUEST-OS-4.46_201709-01~~ |6 de octubre de 2017 |14 de diciembre de 2017 |TBD |
|~~WA-GUEST-OS-4.45_201708-01~~ |24 de agosto de 2017 |14 de diciembre de 2017 |TBD |
|~~WA-GUEST-OS-4.44_201707-01~~ |3 de agosto de 2017 |8 de noviembre de 2017 |TBD |
|~~WA-GUEST-OS-4.43_201706-01~~ |7 de julio de 2017 |6 de octubre de 2017 |TBD |
|~~WA-GUEST-OS-4.42_201705-01~~ |5 de junio de 2017 |24 de agosto de 2017 |TBD |
|~~WA-GUEST-OS-4.41_201704-01~~ |9 de mayo de 2017 |3 de agosto de 2017 |TBD |
|~~WA-GUEST-OS-4.40_201703-01~~ |10 de abril de 2017 |7 de julio de 2017 |TBD |
|~~WA-GUEST-OS-4.39_201612-01~~ |10 de enero de 2017 |5 de junio de 2017 |TBD |

## <a name="family-3-releases"></a>Lanzamientos de la familia 3
**Windows Server 2012**

Versión de .NET Framework instalada: 4.0, 4.5, 4.5.1, 4.5.2

> [!NOTE]
> Las fechas con un asterisco (*) están sujetas a cambios.
>
>

| Cadena de configuración | Fecha de lanzamiento | Fecha de deshabilitación | Fecha de expiración |
| --- | --- | --- | --- |
| WA-GUEST-OS-3.56_201712-01 |4 de enero de 2018 |Post 3.58 |TBD |
| WA-GUEST-OS-3.55_201711-01 |14 de diciembre de 2017 |Post 3.57 |TBD |
|~~WA-GUEST-OS-3.54_201710-02~~ |8 de noviembre de 2017 |4 de enero de 2018 |TBD |
|~~WA-GUEST-OS-3.53_201709-01~~ |6 de octubre de 2017 |14 de diciembre de 2017 |TBD |
|~~WA-GUEST-OS-3.52_201708-01~~ |24 de agosto de 2017 |14 de diciembre de 2017 |TBD |
|~~WA-GUEST-OS-3.51_201707-01~~ |3 de agosto de 2017 |8 de noviembre de 2017 |TBD |
|~~WA-GUEST-OS-3.50_201706-01~~ |7 de julio de 2017 |6 de octubre de 2017 |TBD |
|~~WA-GUEST-OS-3.49_201705-01~~ |5 de junio de 2017 |24 de agosto de 2017 |TBD |
|~~WA-GUEST-OS-3.48_201704-01~~ |9 de mayo de 2017 |3 de agosto de 2017 |TBD |
|~~WA-GUEST-OS-3.47_201703-01~~ |10 de abril de 2017 |7 de julio de 2017 |TBD |
|~~WA-GUEST-OS-3.46_201612-01~~ |10 de enero de 2017 |5 de junio de 2017 |TBD |

## <a name="family-2-releases"></a>Lanzamientos de la familia 2
**Windows Server 2008 R2 SP1**

Versión de .NET Framework instalada: 3.5, 4.0, 4.5, 4.5.1, 4.5.2

> [!NOTE]
> Las fechas con un asterisco (*) están sujetas a cambios.
>
>

| Cadena de configuración | Fecha de lanzamiento | Fecha de deshabilitación | Fecha de expiración |
| --- | --- | --- | --- |
| WA-GUEST-OS-2.69_201712-01 |4 de enero de 2018 |Post 2.71 |TBD |
| WA-GUEST-OS-2.68_201711-01 |14 de diciembre de 2017 |Post 2.70 |TBD |
|~~WA-GUEST-OS-2.67_201710-02~~ |8 de noviembre de 2017 |4 de enero de 2018 |TBD |
|~~WA-GUEST-OS-2.66_201709-01~~ |6 de octubre de 2017 |14 de diciembre de 2017 |TBD |
|~~WA-GUEST-OS-2.65_201708-01~~ |24 de agosto de 2017 |14 de diciembre de 2017 |TBD |
|~~WA-GUEST-OS-2.64_201707-01~~ |3 de agosto de 2017 |8 de noviembre de 2017 |TBD |
|~~WA-GUEST-OS-2.63_201706-01~~ |7 de julio de 2017 |6 de octubre de 2017 |TBD |
|~~WA-GUEST-OS-2.62_201705-01~~ |5 de junio de 2017 |24 de agosto de 2017 |TBD |
|~~WA-GUEST-OS-2.61_201704-01~~ |9 de mayo de 2017 |3 de agosto de 2017 |TBD |
|~~WA-GUEST-OS-2.60_201703-01~~ |10 de abril de 2017 |7 de julio de 2017 |TBD |
|~~WA-GUEST-OS-2.59_201701-01~~ |10 de enero de 2017 |5 de junio de 2017 |TBD |
|~~WA-GUEST-OS-2.58_201612-01~~ |10 de enero de 2017 |9 de mayo de 2017|TBD |


## <a name="msrc-patch-updates"></a>Actualizaciones de revisiones de MSRC
La lista de revisiones que se incluyen con cada lanzamiento mensual del SO invitado está disponible [aquí][patches].

## <a name="sdk-support"></a>Compatibilidad con SDK
Aunque la [directiva de retirada para Azure SDK][retire policy sdk] indica que solo se admiten las versiones posteriores a la 2.2, determinadas familias de SO invitado le permiten usar versiones anteriores. Siempre debe usar el SDK compatible más reciente.

| Familia del SO invitado | Versiones del SDK compatibles |
| --- | --- |
| 5 |Versión 2.9.5.1+ |
| 4 |Versión 2.1 o superior |
| 3 |Versión 1.8 o superior |
| 2 |Versión 1.3 o superior |
| 1 |Versión 1.0 o superior |

## <a name="guest-os-release-information"></a>Información de los lanzamientos del SO invitado
Hay tres fechas importantes para los lanzamientos de SO invitado: la de **lanzamiento**, la de **deshabilitación** y la de **expiración**. Se considera que un SO invitado se encuentra disponible cuando figura en el Portal y se puede seleccionar como el SO invitado de destino. Cuando un SO invitado llega a su fecha de **deshabilitación**, se quita de Azure, aunque todos los servicios en la nube que usen dicho SO invitado seguirán funcionando con normalidad.

El período entre las fechas de **deshabilitación** y de **expiración** permite llevar a cabo la transición de un SO invitado a uno más reciente con tranquilidad. Si usa la opción *automática* para el SO invitado, siempre tendrá la versión más reciente, por lo que no tendrá que preocuparse de que expire.

Una vez que pase la fecha de **expiración**, cualquier servicio en la nube que siga usando ese SO invitado se detendrá, eliminará o actualizará de forma obligada. Puede obtener más información sobre la directiva de retirada [aquí][retirepolicy].

## <a name="guest-os-family-version-explanation"></a>Diferencias entre la versión y la familia de los SO invitados
Las familias del SO invitado se basan en versiones lanzadas de Microsoft Windows Server. El SO invitado es el sistema operativo subyacente en el que se ejecuta Azure Cloud Services. Cada SO invitado tiene un número de familia, versión y lanzamiento.

* **Guest OS family**  
  Versión del sistema operativo Windows Server en la que se basa un SO invitado. Por ejemplo, la *familia 3* se basa en Windows Server 2012.
* **Versión del SO invitado**  
  Corresponde a la imagen de la familia del SO invitado, más las revisiones pertinentes del [Centro de respuestas de seguridad de Microsoft (MSRC)][msrc] disponibles en la fecha en que se genera la nueva versión del SO invitado. Es posible que no se incluyan todas las revisiones.

    Los números empiezan por 0 y se incrementan en 1 cada vez que se agrega un nuevo conjunto de actualizaciones. Solo se muestran los ceros finales si es importante. Es decir, la versión 2.10 es una versión mucho más posterior y diferente que la versión 2.1.
* **Lanzamiento del SO invitado**  
  Relanzamiento de una versión de SO invitado. Un relanzamiento se produce si Microsoft encuentra, durante las pruebas, problemas que requieran cambios. El lanzamiento más reciente siempre reemplaza a los lanzamientos anteriores, ya sean públicos o no. Azure Portal solo permitirá a los usuarios elegir el lanzamiento más reciente de una versión concreta. Las implementaciones que se ejecutan en un lanzamiento anterior no suelen ser una actualización forzada, según la gravedad del error.

En el ejemplo siguiente, 2 es la familia, 12 es la versión y "rel2" es el lanzamiento.

**Lanzamiento del SO invitado** : 2.12 rel2

**Cadena de configuración para este lanzamiento** - WA-GUEST-OS-2.12_201208-02

La cadena de configuración para un SO invitado incluye esta misma información incrustada, junto con una fecha que muestra qué revisiones de MSRC se tuvieron en cuenta para ese lanzamiento. En este ejemplo, las revisiones de MSRC generadas para Windows Server 2008 R2 hasta e incluido agosto de 2012 se tuvieron en cuenta para su inclusión. Solo se incluyen las revisiones que se aplican específicamente a esa versión de Windows Server. Por ejemplo, si una revisión de MSRC se aplica a Microsoft Office, no se incluirá porque este producto no forma parte de la imagen base de Windows Server.

## <a name="guest-os-system-update-process"></a>Proceso de actualización del SO invitado
Esta página incluye información sobre los próximos lanzamientos del SO invitado. Los clientes han indicado que desean saber cuándo se produce un lanzamiento porque sus roles de servicio en la nube se reiniciarán si están establecidos en actualización "Automática". Las versiones del SO invitado suelen producirse al menos cinco (5) días después de que tenga lugar la actualización de MSRC el segundo martes de cada mes. Los nuevos lanzamientos incluyen todas las revisiones de MSRC relevantes para cada familia del SO invitado.

Microsoft Azure publica actualizaciones constantemente. El SO invitado es solo una de estas actualizaciones en la canalización. Un lanzamiento puede verse afectado por una cantidad de factores tal que no se puede enumerar aquí. Además, Azure se ejecuta literalmente en cientos de miles de máquinas. Esto significa que es imposible especificar una fecha y hora exacta de reinicio de los roles. Estamos trabajando en un plan para limitar o acotar los reinicios.

Cuando se publica un nuevo lanzamiento del SO invitado, puede tardar tiempo en propagarse completamente en Azure. A medida que los servicios se actualizan al nuevo SO invitado, estos se reinician respetando los dominios de actualización. Los servicios configurados para usar actualizaciones "Automáticas" serán los primeros en obtener el lanzamiento. Después de la actualización, verá la nueva versión del SO invitado del servicio en Azure Portal. Durante ese período se pueden producir relanzamientos. Algunas versiones se pueden implementar durante largos períodos de tiempo y puede que no se produzcan actualizaciones reinicios de actualizaciones automáticas durante muchas semanas después de la fecha de lanzamiento oficial. Una vez que un SO invitado está disponible, puede elegir esa versión explícitamente desde el portal o en el archivo de configuración.

Para una gran cantidad de información valiosa sobre los reinicios e indicaciones para obtener más información técnica detallada de las actualizaciones de SO invitado y de SO host, consulte la entrada de blog de MSDN titulada [Role Instance Restarts Due to OS Upgrades][restarts] (Reinicios de instancias de rol debido a actualizaciones del SO).

Si actualiza manualmente el SO invitado, lea la [directiva de retirada del SO invitado][retirepolicy] para obtener más información.

## <a name="guest-os-supportability-and-retirement-policy"></a>Directiva de compatibilidad y retirada del SO invitado
La directiva de compatibilidad y retirada del SO invitado se explica [aquí][retirepolicy].

[Fuente RSS de actualización del SO invitado]: https://raw.githubusercontent.com/MicrosoftDocs/azure-cloud-services-files/master/GuestOS/GuestOSFeed.xml
[Install .NET on a Cloud Service Role]: https://azure.microsoft.com/en-us/documentation/articles/cloud-services-dotnet-install-dotnet/?WT.mc_id=azurebg_email_Trans_963_RevisedNET_Update
[Azure Guest OS Update Settings]: cloud-services-how-to-configure-portal.md
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
[fix]: https://technet.microsoft.com/en-us/library/security/ms17-010.aspx
