---
title: "Creación de Azure BizTalk Services en Azure Portal | Microsoft Docs"
description: "Obtenga información acerca de cómo aprovisionar o crear BizTalk Services en Azure Portal; MABS, WABS"
services: biztalk-services
documentationcenter: 
author: MandiOhlinger
manager: anneta
editor: 
ms.assetid: 3ad18876-a649-40d6-9aa0-1509c1d62c43
ms.service: biztalk-services
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: hero-article
ms.date: 11/07/2016
ms.author: mandia
ms.openlocfilehash: 61776b19ba0ee273b78e3b0a6f610e5701251dd0
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/11/2017
---
# <a name="create-biztalk-services-using-the-azure-portal"></a>Creación de BizTalk Services mediante Azure Portal

> [!INCLUDE [BizTalk Services is being retired, and replaced with Azure Logic Apps](../../includes/biztalk-services-retirement.md)]

> [!INCLUDE [Use APIs to manage MABS](../../includes/biztalk-services-retirement-azure-classic-portal.md)]

> [!TIP]
> Para iniciar sesión en el Portal de Azure, se necesita una suscripción a Azure y una cuenta de Azure. En caso de no tener ninguna, puede crear una cuenta de evaluación gratuita en tan solo unos minutos. Consulte [Evaluación gratuita de Azure](http://go.microsoft.com/fwlink/p/?LinkID=239738).


## <a name="CreateService"></a>Creación de un servicio de BizTalk

> [!INCLUDE [Use APIs to manage MABS](../../includes/biztalk-services-retirement-azure-classic-portal.md)]

Según el estado del servicio de BizTalk, hay algunas operaciones que no se pueden completar. Para ver una lista de estas operaciones, vaya al [gráfico del estado de BizTalk Services](biztalk-service-state-chart.md).

## <a name="post-provisioning-steps"></a>Pasos posteriores al aprovisionamiento
* [Instalación del certificado en un equipo local](#InstallCert)
* [Incorporación de un certificado listo para producción](#AddCert)
* [Obtención del espacio de nombres de control de acceso](#ACS)

#### <a name="InstallCert"></a>Instalación del certificado en un equipo local

> [!INCLUDE [Use APIs to manage MABS](../../includes/biztalk-services-retirement-azure-classic-portal.md)]

#### <a name="AddCert"></a>Incorporación de un certificado listo para producción

> [!INCLUDE [Use APIs to manage MABS](../../includes/biztalk-services-retirement-azure-classic-portal.md)]

#### <a name="ACS"></a>Obtención del espacio de nombres de control de acceso

> [!INCLUDE [Use APIs to manage MABS](../../includes/biztalk-services-retirement-azure-classic-portal.md)]

Cuando implementa un proyecto de servicio de BizTalk desde Visual Studio, debe escribir este espacio de nombres del servicio de control de acceso. El espacio de nombres Control de acceso se crea automáticamente para el Servicio de BizTalk.

Los valores de control de acceso se pueden utilizar con cualquier aplicación. Una vez que se crean Azure BizTalk Services, el espacio de nombres del servicio de control de acceso controla la autenticación con su implementación del servicio de BizTalk. Si desea cambiar la suscripción o administrar el espacio de nombres, seleccione **ACTIVE DIRECTORY** en el panel de navegación que se encuentra a la izquierda y, luego, seleccione el espacio de nombres. La barra de tareas muestra sus opciones.

Haga clic en **Administrar** para abrir el Portal de administración del sistema de control de acceso. En el Portal de administración de Access Control, el servicio de BizTalk usa **Identidades de servicio**:  
![Identidades de servicio de ACS en el Portal de administración del sistema de control de acceso][ACSServiceIdentities]

La identidad de servicio del control de acceso es un conjunto de credenciales que permiten que las aplicaciones o los clientes se autentiquen directamente con el control de acceso.

> [!IMPORTANT]
> El servicio de BizTalk usa el valor **Owner** para la identidad de servicio predeterminada y el valor **Password**. Si usa el valor de Symmetric Key, en lugar del valor de Password, puede aparecer el siguiente error:<br/><br/>*Could not connect to the Access Control Management Service account with the specified credentials*
> 
> 

[Administración del espacio de nombres ACS](https://msdn.microsoft.com/library/azure/hh674478.aspx) muestra algunas directrices y recomendaciones.

## <a name="requirements-explained"></a>Requisitos explicados
Estos requisitos no se aplican a la versión gratuita.

<table border="1">
<tr bgcolor="FAF9F9">
        <td><strong>Lo que necesita</strong></td>
        <td><strong>Por qué es necesario</strong></td>
</tr>
<tr>
<td>Suscripción de Azure</td>
<td>La suscripción determina quién puede iniciar sesión en Azure. El titular de la cuenta crea la suscripción en <a HREF="https://account.windowsazure.com/Subscriptions"> Suscripciones de Azure</a>.
<br/><br/>
La cuenta de Azure puede tener varias suscripciones y la puede administrar alguien que tenga permisos. Por ejemplo, el titular de su cuenta de Azure crea una suscripción llamada <em>BizTalkServiceSubscription</em> y concede acceso a la misma a los administradores de BizTalk de la empresa (por ejemplo, ContosoBTSAdmins@live.com). En este escenario, los administradores de BizTalk inician sesión en Azure y tienen permisos totales de administrador en todos los servicios hospedados en la suscripción, incluidos Azure BizTalk Services. Los administradores de BizTalk no son los titulares de la cuenta de Azure y, por lo tanto, no tienen acceso a información de facturación alguna.
<br/><br/>
<a HREF="http://go.microsoft.com/fwlink/p/?LinkID=267577">Administración de suscripciones y cuentas de almacenamiento en Azure</a> proporciona más información.
</td>
</tr>
<tr>
<td>Azure SQL Database</td>
<td>Almacena las tablas, vistas y procedimientos almacenados que utilizan los Servicios de BizTalk de Azure, incluidos los datos de seguimiento.
<br/><br/>
Cuando cree un servicio de BizTalk, puede usar un servidor de Azure SQL Server existente, una instancia de Azure SQL Database existente, o bien puede crear automáticamente un servidor o una base de datos nuevos.
<br/><br/>
La escala de SQL Database se configura automáticamente. Normalmente, la escala predeterminada es suficiente para un Servicio de BizTalk. El cambio de la escala puede afectar al precio. Consulte <a HREF="http://go.microsoft.com/fwlink/p/?LinkID=234930">Cuentas y facturación en Azure SQL Database</a>
<br/><br/>
<strong>Notas</strong>
<br/>
<ul>
<li> Cuando crea una nueva Base de datos SQL o SQL Server de Azure, Servicios de Azure se habilita automáticamente. El Servicio de BizTalk requiere que los servicios de Azure estén habilitados.</li>
<li>Si crea una instancia de Azure SQL Database nueva en un SQL Server de Azure existente, no se cambian las reglas de firewall del servidor. Como resultado, es posible que otros servicios de Azure no puedan tener acceso a las bases de datos del servidor.</li>
</ul>
</td>
</tr>
<tr>
<td>Espacio de nombres dAzure Access Control Service</td>
<td>Autentica con Azure BizTalk Services. Cuando implementa un proyecto de servicio de BizTalk desde Visual Studio, debe escribir este espacio de nombres del servicio de control de acceso. Al crear un Servicio de BizTalk, se crea automáticamente el espacio de nombres Control de acceso. </td>
</tr>

<tr>
<td>Cuenta de Azure Storage</td>
<td>Proporciona acceso a tablas, blobs y colas usados por su servicio de BizTalk para guardar lo siguiente:

<ul>
<li>Archivos de registro que supervisan el Servicio de BizTalk. </li>
<li>Cuando se crea un contrato X12 o AS2 entre asociados, puede habilitar la característica de archivado para almacenar propiedades de mensaje. Estos datos se guardan en la cuenta de almacenamiento.</li>
</ul>
<br/>
Cuando crea un servicio de BizTalk, puede usar una cuenta de almacenamiento existente o puede crear automáticamente una nueva.
<br/><br/>
La configuración de almacenamiento predeterminada es suficiente para un Servicio de BizTalk.
<br/><br/>
Cuando crea una cuenta de almacenamiento, se crean automáticamente una clave primaria y una clave secundaria. Estas claves controlan el acceso a su cuenta de almacenamiento. El servicio de BizTalk utiliza automáticamente la clave principal.
<br/><br/>
Consulte <a HREF="http://go.microsoft.com/fwlink/p/?LinkID=285671">Almacenamiento</a> para más información.
</td>
</tr>

<tr>
<td>Certificado privado de SSL</td>
<td>
Cuando se crea un servicio de Azure BizTalk, también se crea una URL HTTPS que incluye su nombre de Servicio de BizTalk. Esta URL se configura automáticamente para usar un certificado solo de desarrollo autofirmado. Para producción, necesita un certificado SSL privado.
<br/><br/>
<strong>Información importante sobre el certificado SSL</strong>

<ul>
<li>La fecha de caducidad del certificado debe ser menor a cinco años.</li>
<li>Todos los certificados privados requieren una contraseña. Apréndase esta contraseña y, como procedimiento recomendado, comparta esta contraseña con sus administradores.</li>
<li>Los certificados autofirmados se utilizan en un entorno de prueba o de desarrollo. Cuando utilice certificados autofirmados, importe el certificado a su almacén personal de certificados y al almacén de certificados de las entidades de certificación raíz de confianza.</li>
</ul>
<br/>Cuando envíe la solicitud de certificado de producción a su entidad de certificación, proporcione las siguientes propiedades del certificado:
<br/>

<ul>
<li><strong>Uso mejorado de clave</strong>: como mínimo, Azure BizTalk Services requiere la autenticación de servidor.</li>
<li><strong>Nombre común</strong>: escriba el nombre de dominio completo (FQDN) de la dirección URL de Azure BizTalk Services. Consulte <a HREF="#CreateService">Crear un servicio de BizTalk</a> en este artículo.</li>
</ul>
<br/>
Es posible agregar un certificado nuevo o distinto después de crear el servicio de BizTalk.
</td>
</tr>
</table>
<!---Loc Comment: Please, check link [Create a BizTalk Service] since it is not redirecting to any location.--->



## <a name="hybrid-connections"></a>conexiones híbridas
Cuando cree un Servicio de BizTalk de Azure, la pestaña **Conexiones híbridas** estará disponible:

![Pestaña Conexiones híbridas][HybridConnectionTab]

Las conexiones híbridas se usan para conectar un sitio web de Azure o un servicio móvil de Azure a cualquier recurso local que usa un puerto TCP estático, como SQL Server, MySQL, API web HTTP, Mobile Services y la mayoría de los servicios web personalizados.  Las conexiones híbridas y el servicio de adaptador de BizTalk son diferentes. El Servicio de adaptador de BizTalk se usa para conectar Azure BizTalk Services a un sistema de línea de negocio (LOB) local.

 Vea [Conexiones híbridas](integration-hybrid-connection-overview.md) para obtener más información, incluida la creación y la administración de conexiones híbridas.

## <a name="next-steps"></a>Pasos siguientes
Ahora que se crea un servicio de BizTalk, familiarícese con [BizTalk Services: pestañas Panel, Monitor y Escala](biztalk-dashboard-monitor-scale-tabs.md). Su servicio de BizTalk está listo para las aplicaciones. Para comenzar a crear aplicaciones, vaya a [Azure BizTalk Services](http://go.microsoft.com/fwlink/p/?LinkID=235197).

## <a name="see-also"></a>Consulte también
* [BizTalk Services: Gráfico de ediciones](biztalk-editions-feature-chart.md)<br/>
* [BizTalk Services: gráfico de estado](biztalk-service-state-chart.md)<br/>
* [BizTalk Services: copias de seguridad y restauración](biztalk-backup-restore.md)<br/>
* [BizTalk Services: limitaciones](biztalk-throttling-thresholds.md)<br/>
* [BizTalk Services: nombre del emisor y clave del emisor](biztalk-issuer-name-issuer-key.md)<br/>
* [¿Cómo puedo comenzar a utilizar el SDK de Azure BizTalk Services?](http://go.microsoft.com/fwlink/p/?LinkID=302335)<br/>
* [Conexiones híbridas](integration-hybrid-connection-overview.md)

[NewBizTalkService]: ./media/biztalk-provision-services/WABS_NewBizTalkService.png
[NEWButton]: ./media/biztalk-provision-services/WABS_New.png
[ProgressComplete]: ./media/biztalk-provision-services/WABS_ProgressComplete.png
[ACSConnectInfo]: ./media/biztalk-provision-services/WABS_ACSConnectInformation.png
[QuickGlance]: ./media/biztalk-provision-services/WABS_QuickGlance.png
[ACSServiceIdentities]: ./media/biztalk-provision-services/WABS_ACSServiceIdentities.png
[HybridConnectionTab]: ./media/biztalk-provision-services/WABS_HybridConnectionTab.png
