---
title: "Creación y uso de un equilibrador de carga interno con una instancia de Azure App Service Environment"
description: "Obtener detalles sobre la creación y uso de una instancia de Azure App Service Environment aislado de Internet"
services: app-service
documentationcenter: na
author: ccompy
manager: stefsch
ms.assetid: 0f4c1fa4-e344-46e7-8d24-a25e247ae138
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/13/2017
ms.author: ccompy
ms.translationtype: Human Translation
ms.sourcegitcommit: cb4d075d283059d613e3e9d8f0a6f9448310d96b
ms.openlocfilehash: 8bc82a297ef8e3f1beac773a6a3b0c4a9b80334f
ms.contentlocale: es-es
ms.lasthandoff: 06/26/2017

---
# <a name="create-and-use-an-internal-load-balancer-with-an-app-service-environment"></a>Creación y uso de un equilibrador de carga interno con una instancia de App Service Environment #

Una instancia de Azure App Service Environment (ASE) es una implementación de Azure App Service en una subred de Azure Virtual Network (VNet). Hay dos modos de implementación de un ASE: 

- con una dirección IP virtual en una dirección IP externa, a menudo denominado un _ASE externo_.
- con la dirección VIP en una dirección IP interna, llamada a menudo un _ASE con un ILB_ porque el punto de conexión interno es un equilibrador de carga interno (ILB). 

Este artículo muestra cómo crear un _ASE con un ILB_.  Para obtener información general sobre los ASE, puede empezar con [Introducción a App Service Environment][Intro] y, si desea obtener información sobre cómo crear un ASE externo, comience con [Creación de un ASE externo][MakeExternalASE].

## <a name="overview"></a>Información general ##

Un ASE puede implementarse con un punto de conexión accesible por Internet o con una dirección IP en la red virtual. Para establecer la dirección IP en una dirección de red virtual, el ASE tiene que implementarse con un ILB. Al implementar el ASE con un ILB, debe proporcionar:

-   Un dominio propio con el que se crean las aplicaciones
-   El certificado usado para las conexiones HTTPS.
-   Administración de DNS para el dominio

A cambio, podrá realizar todo esto:

-   Hospedar aplicaciones de intranet de forma segura en la nube a las que se accede a través de una VPN sitio a sitio o ExpressRoute
-   Hospedar aplicaciones en la nube que no se muestran en los servidores DNS públicos
-   Crear aplicaciones de back-end aisladas de Internet con las que pueden integrarse de forma segura sus aplicaciones de front-end.

***Funcionalidad deshabilitada***

Hay algunas operaciones que no podrá realizar al utilizar un ASE con un ILB, que incluyen:

-   Uso de SSL basado en IP
-   Asignar direcciones IP a aplicaciones específicas.
-   Comprar y usar un certificado con una aplicación a través del Portal. Puede seguir obteniendo certificados directamente de una entidad de certificación y utilizarlos con sus aplicaciones, pero no mediante Azure Portal.

## <a name="create-an-ilb-ase"></a>Creación de un ASE con un ILB ##

Pasos para crear un ASE con un ILB:

1.  En Azure Portal, seleccione **Nuevo -&gt; Web y móvil -&gt; App Service Environment**.
2.  Seleccione su suscripción.
3.  Seleccione o cree un grupo de recursos.
4.  Seleccione o cree una red Virtual.
5.  Si ha seleccionado red virtual, cree una subred. Asegúrese de establecer un tamaño de subred suficientemente grande para alojar el crecimiento futuro de su ASE. El tamaño recomendado es un `/25`, que tiene 128 direcciones y puede controlar el máximo tamaño de ASE. No se puede utilizar un `/29` o menor tamaño de subred.  La infraestructura necesita usar al menos 5 direcciones.  Incluso con un `/28` tendría un escalado máximo de 11 instancias en una subred `/28`. Si cree que necesitará superar las 100 instancias máximas predeterminadas en los planes de App Service en un día futuro o que necesitará escalar cerca de 100 pero con un escalado de front-end más rápido, utilice /24 con 256 direcciones.
6.  Seleccione **Red virtual/Ubicación -&gt; Configuración de red virtual** y establezca el valor de **Tipo de dirección VIP** en **Interna**.
7.  Proporcione el nombre de dominio. Será el que utilice para las aplicaciones creadas en este ASE. Hay algunas restricciones. No puede ser:
    - net
    - azurewebsites.net
    - p.azurewebsites.net
    - &lt;nombre_del_ASE&gt;.p.azurewebsites.net

    También, si piensa usar un nombre de dominio personalizado para las aplicaciones que se hospedan en este ASE, no puede tener una superposición entre el nombre de dominio personalizado y el nombre de dominio usado por su ASE. Para un ASE con un ILB con el nombre de dominio _contoso.com_, no puede usar nombres de dominio personalizados para las aplicaciones con la forma:
    - www.contoso.com
    - abcd.def.contoso.com
    - abcd.contoso.com

    Si conoce los nombres de dominio personalizados que deseará usar con las aplicaciones que implemente en el ASE con un ILB, elija un dominio para el ASE con un ILB que no tenga conflicto en el momento de su creación. En este ejemplo, podría ser algo como _contoso-interno.com_.
8.  Seleccione **Aceptar** y, a continuación, **Crear**.

    ![][1]

En la hoja Red virtual, hay una opción denominada **Configuración de red virtual**. Aquí podrá seleccionar una dirección VIP externa o interna. El valor predeterminado es **Externa**. Si selecciona **Externa**, el ASE utilizará una dirección VIP accesible de Internet. Si selecciona **Interna**, el ASE se configurará con un ILB en una dirección IP dentro de la red virtual.

Después de seleccionar **Interna**, se quita la posibilidad para agregar más direcciones IP para el ASE y, en su lugar, debe proporcionar el dominio del ASE. En un ASE con una dirección VIP externa, se usa el nombre del ASE en el dominio para las aplicaciones creadas en dicho ASE.

Si establece el valor de **Tipo de dirección VIP** en **Interna**, el nombre del ASE no se usa en el dominio del ASE. El dominio se especifica explícitamente. Si el dominio fuera ***contoso.corp.net*** y creara una aplicación en dicho ASE denominada ***timereporting***, la dirección URL de dicha aplicación sería ***timereporting.contoso.corp.net***.

## <a name="apps-in-an-ilb-ase"></a>Aplicaciones en un ASE con un ILB ##

En un ASE con un ILB, las aplicaciones se crean de la misma forma que en un ASE sin equilibrador de carga.

1.  En Azure Portal, seleccione **Nuevo &gt; Web y móvil &gt; Web** o **Móvil** o **Aplicación de API**.
2.  Escriba el nombre de la aplicación.
3.  Seleccione la suscripción.
4.  Seleccione o cree el grupo de recursos.
5.  Seleccione o cree un plan de App Service. Si va a crear un nuevo plan de App Service, seleccione el ASE como la ubicación y elija el grupo de trabajo en el que quiera que se cree. Cuando cree el plan de App Service, seleccione el ASE como la ubicación y el grupo de trabajo. Al especificar el nombre de la aplicación, verá que el dominio que se encuentra bajo el nombre de la aplicación se reemplaza por el dominio del ASE.
6.  Seleccione **Crear**. Debe activar la casilla **Anclar a panel** si quiere que la aplicación se muestre en el panel.

    ![][2]

Debajo del nombre de la aplicación, el nombre del dominio se actualiza para reflejar el dominio del ASE.

## <a name="post-ilb-ase-creation-validation"></a>Validación posterior a la creación del ASE con un ILB ##

Un ASE con un ILB es ligeramente diferente a un ASE sin ILB. Como ya se ha indicado antes, tiene que administrar su propio DNS y proporcionar también su propio certificado para las conexiones HTTPS.

Cuando cree el ASE, observará que el nombre de dominio muestra el dominio que especificó y que hay un nuevo elemento en el menú **Configuración** llamado **Certificado de ILB**. El ASE se crea con un certificado que no especifica el dominio del ASE con un ILB. Si usa el ASE con ese certificado, el explorador le indicará que no es válido. Este certificado hace más fácil probar HTTPS, pero necesitará cargar su propio certificado asociado al dominio del ASE con un ILB, tanto si está autofirmado como si ha sido adquirido a una entidad de certificación (CA).

![][3]

Hay varias maneras de obtener un certificado SSL válido, entre las que se incluyen las CA internas, la adquisición de un certificado de un emisor externo y el uso de un certificado autofirmado. Independientemente del origen del certificado SSL, es preciso configurar correctamente los siguientes atributos del certificado:

-   _Subject_: este atributo se debe establecer en _\*.su-dominio-raíz_
-   _Subject Alternative Name_: este atributo debe incluir tanto _\*.su-dominio-raíz_ como _\*.scm.su-dominio-raíz_. El motivo de la segunda entrada es que las conexiones SSL con el sitio de SCM/Kudu asociadas a cada aplicación se realizarán mediante una dirección, cuyo formato será _su-nombre-de-aplicación.scm.su-dominio-raíz_.

Con un certificado SSL válido, se necesitan dos pasos preparatorios adicionales. El certificado SSL se debe guardar en formato .pfx, o bien convertirse a dicho formato. Recuerde que es preciso que el archivo .pfx incluya todos los certificados raíz e intermedios, y también debe protegerse con una contraseña.

Si desea crear su propio certificado con PowerShell puede usar los comandos que se muestra a continuación.  Asegúrese de usar el nombre de dominio del ASE con un ILB en lugar de *internal.contoso.com*. 

    $certificate = New-SelfSignedCertificate -certstorelocation cert:\localmachine\my -dnsname "\*.internal-contoso.com","\*.scm.internal-contoso.com"
    
    $certThumbprint = "cert:\localMachine\my\" +$certificate.Thumbprint
    $password = ConvertTo-SecureString -String "CHANGETHISPASSWORD" -Force -AsPlainText
    
    $fileName = "exportedcert.pfx" 
    Export-PfxCertificate -cert $certThumbprint -FilePath $fileName -Password $password

El certificado que generan estos comandos de PowerShell aún producirá avisos en los exploradores, porque no fue creado por una entidad de certificación que se encuentre en la cadena de confianza utilizada por el explorador. La única manera de obtener un certificado que no cause problemas con el navegador es obtenerlo de una entidad de certificación comercial incluida en la cadena de confianza del navegador.  

![][4]

Para cargar sus propios certificados y probar el acceso:

1.  Cuando cree el ASE, vaya a su interfaz de usuario: **ASE -> Configuración -> Certificados de ILB**.
2.  Establezca el certificado del ILB seleccionando el archivo de certificado .pfx y proporcione la contraseña. Este paso tarda unos minutos en completarse y se muestra un mensaje que indica que se está realizando una operación de carga.
3.  Obtenga la dirección del ILB del ASE (**ASE > Propiedades > Dirección IP virtual**).
4.  Cree una aplicación web en el ASE, después de crearlo.
5.  Cree una máquina virtual si no dispone de una en la red virtual.

    > [!NOTE] 
    > No cree esta máquina virtual en la misma subred que el ASE. En caso contrario, interrumpirá la configuración.
    >
    >

6.  Establezca el DNS para el dominio del ASE. Puede utilizar un carácter comodín con el dominio en el DNS o, si quiere realizar algunas pruebas sencillas, editar el archivo de hosts de la máquina virtual para establecer el nombre de la aplicación web en la dirección IP virtual. Si el ASE tiene el nombre de dominio _.ilbase.com_ y crea una aplicación web denominada _mytestapp_, su dirección será _mytestapp.ilbase.com_. A continuación, configure _mytestapp.ilbase.com_ para que resuelva a la dirección del ILB. (en Windows, el archivo hosts se encuentra en _C:\Windows\System32\drivers\etc\_.) Si desea probar la publicación de la implementación web o acceder a la consola avanzada, necesitará también crear un registro para _mytestapp.scm.ilbase.com_.
7.  Utilice un explorador en esa máquina virtual y vaya a ***http://mytestapp.ilbase.com*** (o la dirección que corresponda según el nombre de la aplicación web en el dominio).
8.  Utilice un explorador en esa máquina virtual y vaya a ***https://mytestapp.ilbase.com***. Debe aceptar el aviso de seguridad si utiliza un certificado autofirmado.

La dirección IP del ILB se lista en **Direcciones IP**. También incluye las direcciones IP utilizadas por la dirección VIP externa y para el tráfico de administración de entrada.

![][5]

### <a name="functions-and-the-ilb-ase"></a>Functions y el ASE con un ILB

Al usar Functions en un ASE con un ILB puede producirse un error que dice: *"No se han podido recuperar sus funciones ahora.  Inténtelo de nuevo más tarde."*  La razón de ello es que la interfaz de usuario de Functions accede al sitio del scm a través de HTTPS.  Si está utilizando un certificado HTTP para el ASE que no tiene un certificado raíz que se encuentre en el explorador puede producirse esta situación.  Además, los exploradores IE y Edge no comparten la opción *accept-invalid-cert* (aceptar certificado no válido) entre distintas pestañas. Por lo que puede hacer:

- agregar el certificado a su almacén de certificados de confianza 
- o puede usar Chrome, pero debe ir al sitio de scm en primer lugar, aceptar el certificado que no es de confianza y, a continuación, ir al portal

## <a name="dns-configuration"></a>Configuración de DNS ##

Cuando se utiliza una dirección VIP externa, Azure se encarga de administrar el DNS. Todas las aplicaciones que cree en el ASE se agregan automáticamente a DNS de Azure, que es un DNS público. En un ASE con un ILB tiene que administrar su propio DNS. Para un dominio concreto como _contoso.net_, debe crear registros A de DNS que apunten a la dirección del ILB para:

- *.contoso.net
- *.scm.contoso.net

Si el dominio del ASE con un ILB se usa para otros fines además del ASE, deberá administrar el DNS en función del nombre de aplicación. Esto es mucho más complejo ya que sería necesario agregar los nuevos nombres de aplicación en el DNS al crear dichas aplicaciones. Por este motivo, se recomienda el uso de un dominio dedicado.

## <a name="publishing-with-an-ilb-ase"></a>Publicación con un ASE con un ILB ##

Para cada aplicación que se cree, hay dos puntos de conexión. En un ASE con un ILB, existen *&lt;nombre_de_aplicación>.&lt;dominio_del_ASE_ILB>* y *&lt;nombre_de_aplicación>.scm.&lt;dominio_del_ASE_ILB>*. 

El nombre del sitio SCM le lleva a la consola de Kudu, denominada **Advanced Portal** dentro de Azure Portal. La consola de Kudu le permite hacer muchas cosas, incluida la visualización de las variables de entorno, explorar el disco, utilizar una consola y mucho más. Para más información, consulte [Consola de Kudu para Azure App Service][Kudu]. 

En el servicio App Service multiinquilino y en un ASE Externo, hay un inicio de sesión único entre Azure Portal y la consola de Kudu. Sin embargo, para el ASE con un ILB, debe usar las credenciales de publicación para iniciar sesión en la consola de Kudu.

Los sistemas de CI basados en Internet, como GitHub y VSTS, no funcionan con un ASE con un ILB ya que el punto de conexión de publicación no es accesible desde Internet. En lugar de eso, necesita usar un sistema de CI que use un modelo de extracción, como Dropbox.

Los puntos de conexión de publicación para las aplicaciones en un ASE con un ILB usan el dominio con el que se creó el ASE con un ILB. Esto puede verse en el perfil de publicación de la aplicación y en la hoja del portal de la aplicación (en **Información general** > **Información esencial** y también en **Propiedades**). Si tiene un ASE con un ILB con el subdominio *contoso.net* y una aplicación llamada *mytest*, podría conectarse por FTP a *mytest.contoso.net* y realizar la implementación web en *mytest.scm.contoso.net*.

## <a name="coupling-an-ilb-ase-with-a-waf-device"></a>Acoplamiento de un ASE con un ILB con un dispositivo WAF ##

Azure App Service proporciona una gran cantidad de medidas de seguridad para proteger el sistema y ayudar a determinar si una aplicación ha sido atacada. La mejor protección para una aplicación web es acoplar una plataforma de hospedaje, como Azure App Service, con un firewall de aplicaciones web (WAF). Dado que un ASE con un ILB tiene un punto de conexión de aplicación aislado de la red, es perfecto para este tipo de uso.  

Para más información acerca de cómo configurar el ASE con un ILB con un dispositivo WAF, consulte [Configurar un firewall de aplicaciones web con App Service Environment][ASEWAF]. Ese artículo muestra cómo usar un dispositivo virtual Barracuda con su ASE. Otra opción es utilizar Azure Application Gateway. App Gateway utiliza las reglas de núcleo de OWASP para proteger cualquier aplicación que se encuentre detrás de él. Para más información sobre Azure App Gateway, consulte [Introducción al firewall de aplicaciones web de Azure][AppGW].

### <a name="getting-started"></a>Introducción ###

Todos los artículos y procedimientos para App Service Environment están disponibles en el archivo [Léame de App Service Environment][ASEReadme].

Para empezar a trabajar con App Service Environment, consulte [Introducción a App Service Environment][Intro].

Para obtener más información acerca de la plataforma de Servicio de aplicaciones de Azure, consulte [Servicio de aplicaciones de Azure](http://azure.microsoft.com/documentation/articles/app-service-value-prop-what-is/).
 
<!--Image references-->
[1]: ./media/creating_and_using_an_internal_load_balancer_with_app_service_environment/createilbase-network.png
[2]: ./media/creating_and_using_an_internal_load_balancer_with_app_service_environment/createilbase-webapp.png
[3]: ./media/creating_and_using_an_internal_load_balancer_with_app_service_environment/createilbase-certificate.png
[4]: ./media/creating_and_using_an_internal_load_balancer_with_app_service_environment/createilbase-certificate2.png
[5]: ./media/creating_and_using_an_internal_load_balancer_with_app_service_environment/createilbase-ipaddresses.png

<!--Links-->
[Intro]: ./intro.md
[MakeExternalASE]: ./create-external-ase.md
[MakeASEfromTemplate]: ./create-from-template.md
[MakeILBASE]: ./create-ilb-ase.md
[ASENetwork]: ./network-info.md
[ASEReadme]: ./readme.md
[UsingASE]: ./using-an-ase.md
[UDRs]: ../../virtual-network/virtual-networks-udr-overview.md
[NSGs]: ../../virtual-network/virtual-networks-nsg.md
[ConfigureASEv1]: ../../app-service-web/app-service-web-configure-an-app-service-environment.md
[ASEv1Intro]: ../../app-service-web/app-service-app-service-environment-intro.md
[webapps]: ../../app-service-web/app-service-web-overview.md
[mobileapps]: ../../app-service-mobile/app-service-mobile-value-prop.md
[APIapps]: ../../app-service-api/app-service-api-apps-why-best-platform.md
[Functions]: ../../azure-functions/index.yml
[Pricing]: http://azure.microsoft.com/pricing/details/app-service/
[ARMOverview]: ../../azure-resource-manager/resource-group-overview.md
[ConfigureSSL]: ../../app-service-web/web-sites-purchase-ssl-web-site.md
[Kudu]: http://azure.microsoft.com/resources/videos/super-secret-kudu-debug-console-for-azure-web-sites/
[AppDeploy]: ../../app-service-web/web-sites-deploy.md
[ASEWAF]: ../../app-service-web/app-service-app-service-environment-web-application-firewall.md
[AppGW]: ../../application-gateway/application-gateway-web-application-firewall-overview.md

