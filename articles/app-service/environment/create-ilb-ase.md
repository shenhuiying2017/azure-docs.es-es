---
title: "Creación y uso de un equilibrador de carga interno con una instancia de Azure App Service Environment"
description: "Obtener detalles sobre cómo crear y usar una instancia de Azure App Service Environment aislado de Internet"
services: app-service
documentationcenter: na
author: ccompy
manager: stefsch
ms.assetid: 0f4c1fa4-e344-46e7-8d24-a25e247ae138
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: quickstart
ms.date: 06/13/2017
ms.author: ccompy
ms.custom: mvc
ms.openlocfilehash: 9f7343102cf7af6d7f2ba6b4b2f08b7b855da6f8
ms.sourcegitcommit: b854df4fc66c73ba1dd141740a2b348de3e1e028
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/04/2017
---
# <a name="create-and-use-an-internal-load-balancer-with-an-app-service-environment"></a>Creación y uso de un equilibrador de carga interno con una instancia de App Service Environment #

 Azure App Service Environment es una implementación de Azure App Service en una subred de Azure Virtual Network (VNet). Hay dos maneras de implementar una instancia de App Service Environment (ASE): 

- Con una dirección VIP en una dirección IP externa, a la que se suele hacer referencia como instancia externa de ASE.
- Con una dirección VIP en una dirección IP interna, llamada a menudo un ASE con un ILB porque el punto de conexión interno es un equilibrador de carga interno (ILB). 

Este artículo muestra cómo crear un ASE con un ILB. Para obtener información general sobre el ASE, consulte [Introducción a App Service Environment][Intro]. Para obtener información sobre cómo crear un ASE externo, consulte [Creación de un ASE externo][MakeExternalASE].

## <a name="overview"></a>Información general ##

Puede implementar un ASE con un punto de conexión accesible por Internet o con una dirección IP en la red virtual. Para establecer la dirección IP en una dirección de red virtual, el ASE debe implementarse con un ILB. Al implementar el ASE con un ILB, debe proporcionar:

-   Su propio dominio, el cual usa al crear sus aplicaciones.
-   El certificado usado para las conexiones HTTPS.
-   Administración de DNS para el dominio.

A cambio, podrá realizar todo esto:

-   Hospedar aplicaciones de intranet de forma segura en la nube a las que se tiene acceso a través de una VPN sitio a sitio o Azure ExpressRoute.
-   Hospedar aplicaciones en la nube que no se muestran en los servidores DNS públicos.
-   Crear aplicaciones de back-end aisladas de Internet con las que pueden integrarse de forma segura sus aplicaciones de front-end.

### <a name="disabled-functionality"></a>Funcionalidad deshabilitada ###

Sin embargo, cuando utilice un ASE con un ILB, no podrá realizar algunas operaciones:

-   Use SSL basada en IP.
-   Asigne direcciones IP a aplicaciones específicas.
-   Compre y use un certificado con una aplicación a través de Azure Portal. Puede obtener certificados directamente en una entidad de certificación y usarlos con sus aplicaciones. No puede obtenerlas a través de Azure Portal.

## <a name="create-an-ilb-ase"></a>Creación de un ASE con un ILB ##

Pasos para crear un ASE con un ILB:

1. En Azure Portal, seleccione **Nuevo** > **Web y móvil** > **App Service Environment**.

2. Seleccione su suscripción.

3. Seleccione o cree un grupo de recursos.

4. Cree una red virtual o seleccione una.

5. Si selecciona una red virtual existente, debe crear una subred para almacenar el ASE. Asegúrese de establecer un tamaño de subred suficientemente grande para alojar el crecimiento futuro de su ASE. Recomendamos un tamaño de `/25`, que tiene 128 direcciones y puede controlar los ASE de tamaño máximo. El tamaño mínimo que puede seleccionar es `/28`. Tras las necesidades de infraestructura, este tamaño se puede escalar hasta un máximo de 11 instancias.

    * Vaya más allá del máximo predeterminado de 100 instancias en los planes de App Service.

    * Escale cerca de 100, pero con un escalado de front-end más rápido.

6. Seleccione **Virtual Network/Ubicación** > **Configuración de Virtual Network**. Establezca el valor de **Tipo de dirección VIP** en **Interna**.

7. Escriba un valor en nombre de dominio. Este dominio es el que utiliza para las aplicaciones creadas en este ASE. Hay algunas restricciones. No puede ser:

    * net   

    * azurewebsites.net

    * p.azurewebsites.net

    * &lt;nombre_del_ASE&gt;.p.azurewebsites.net

   El nombre de dominio personalizado usado para las aplicaciones y el nombre de dominio que utiliza su ASE no se pueden superponer. Para un ASE con un ILB con el nombre de dominio _contoso.com_, no puede usar nombres de dominio personalizados para aplicaciones como:

    * www.contoso.com

    * abcd.def.contoso.com

    * abcd.contoso.com

   Si conoce los nombres de dominio personalizados para sus aplicaciones, elija un dominio para el ASE con un ILB que no tenga ningún conflicto con esos nombres de dominio personalizados. En este ejemplo, puede usar algo como *contoso-internal.com* para el dominio de su ASE, ya que de este modo no habrá ningún conflicto con los nombres de dominio personalizados que terminen en *.contoso.com*.

8. Seleccione **Aceptar** y después **Crear**.

    ![Creación de ASE][1]

En la hoja **Virtual Network**, hay una opción denominada **Configuración de Virtual Network**. Puede usarla para seleccionar una VIP externa o interna. El valor predeterminado es **Externa**. Si selecciona **Externa**, el ASE utilizará una dirección VIP accesible de Internet. Si selecciona **Interna**, el ASE se configurará con un ILB en una dirección IP de su red virtual.

Tras seleccionar **Interna**, se quita la capacidad de agregar más direcciones IP a su ASE. En su lugar, debe proporcionar el dominio del ASE. En un ASE con una dirección VIP externa, se usa el nombre del ASE en el dominio para las aplicaciones creadas en dicho ASE.

Si establece el valor de **Tipo de dirección VIP** en **Interna**, el nombre del ASE no se usa en el dominio del ASE. El dominio se especifica explícitamente. Si el dominio fuera *contoso.corp.net* y creara una aplicación en dicho ASE denominada *timereporting*, la dirección URL de dicha aplicación sería timereporting.contoso.corp.net.


## <a name="create-an-app-in-an-ilb-ase"></a>Creación de una aplicación en un ASE con un ILB ##

Crea una aplicación en un ASE con un ILB del mismo modo que crea una aplicación en un ASE normalmente.

1. En Azure Portal, seleccione **Nuevo** > **Web y móvil** > **Web** o **Móvil** o **Aplicación de API**.

2. Escriba el nombre de la aplicación.

3. Seleccione la suscripción.

4. Seleccione o cree un grupo de recursos.

5. Seleccione o cree un plan de App Service. Si desea crear un nuevo plan de App Service, seleccione su ASE como ubicación. Seleccione el grupo de trabajo donde desea que se cree el plan de App Service. Cuando cree el plan de App Service, seleccione el ASE como ubicación y grupo de trabajo. Al especificar el nombre de la aplicación, el dominio que se encuentra bajo el nombre de la aplicación se reemplaza por el dominio del ASE.

6. Seleccione **Crear**. Si quiere que la aplicación aparezca en el panel, active la casilla **Anclar a panel**.

    ![Creación del plan de App Service][2]

    Debajo del **nombre de la aplicación**, el nombre del dominio se actualiza para reflejar el dominio del ASE.

## <a name="post-ilb-ase-creation-validation"></a>Validación posterior a la creación del ASE con un ILB ##

Un ASE con un ILB es ligeramente diferente a un ASE sin ILB. Como ya se ha indicado, debe administrar su propio DNS. También debe proporcionar su propio certificado para las conexiones HTTPS.

Después de crear su ASE, el nombre de dominio muestra el dominio especificado. Aparece un nuevo elemento en el menú **Configuración** denominado **Certificado de ILB**. El ASE se crea con un certificado que no especifica el dominio del ASE con un ILB. Si usa el ASE con ese certificado, el explorador le indicará que no es válido. Este certificado facilita la tarea de probar HTTPS, pero debe cargar su propio certificado, que está asociado al dominio del ASE con un ILB. Este paso es necesario independientemente de si el certificado es autofirmado o se adquiere de una entidad de certificación.

![Nombre de dominio del ASE con un ILB][3]

El ASE con un ILB necesita un certificado SSL válido. Use entidades de certificación internas, compre un certificado a un emisor externo o utilice un certificado autofirmado. Independientemente del origen del certificado SSL, es preciso configurar correctamente los siguientes atributos del certificado:

* **Firmante**: este atributo se debe establecer en *.your-root-domain-here.
* **Nombre alternativo del firmante**: este atributo debe incluir tanto **.your-root-domain-here* como **.scm.your-root-domain-here*. Las conexiones SSL con el sitio de SCM/Kudu asociadas a cada aplicación usan una dirección cuyo formato será *your-app-name.scm.your-root-domain-here*.

Convierta/guarde el certificado SSL como un archivo .pfx. El archivo .pfx debe incluir todos los certificados intermedios y raíz. Protéjalo con una contraseña.

Si desea crear un certificado autofirmado, puede usar los comandos de PowerShell aquí. Asegúrese de usar el nombre de dominio del ASE con un ILB en lugar de *internal.contoso.com*: 

    $certificate = New-SelfSignedCertificate -certstorelocation cert:\localmachine\my -dnsname "\*.internal-contoso.com","\*.scm.internal-contoso.com"
    
    $certThumbprint = "cert:\localMachine\my\" +$certificate.Thumbprint
    $password = ConvertTo-SecureString -String "CHANGETHISPASSWORD" -Force -AsPlainText
    
    $fileName = "exportedcert.pfx" 
    Export-PfxCertificate -cert $certThumbprint -FilePath $fileName -Password $password

El certificado que generan estos comandos de PowerShell produce avisos en los exploradores porque no fue creado por una entidad de certificación que se encuentre en la cadena de confianza del explorador. Para obtener un certificado en el que confíe su explorador, proporciónelo a partir de una entidad de certificación comercial de la cadena de confianza del explorador. 

![Establecimiento de certificado de ILB][4]

Para cargar sus propios certificados y probar el acceso:

1. Una vez creado el ASE, vaya a la interfaz de usuario de ASE. Seleccione **ASE** > **Configuración** > **Certificado de ILB**.

2. Para establecer el certificado de ILB, seleccione el archivo de certificado .pfx y escriba la contraseña. Este paso tarda algún tiempo en procesarse. Aparece un mensaje que indica que una operación de carga está en curso.

3. Obtenga la dirección de ILB para su ASE. Seleccione **ASE** > **Propiedades** > **Dirección IP virtual**.

4. Cree una aplicación web en el ASE, después de crearlo.

5. Cree una máquina virtual si no dispone de una en la red virtual.

    > [!NOTE] 
    > No intente crear esta máquina virtual en la misma subred que el ASE porque se producirá un error o causará problemas.
    >
    >

6. Establezca el DNS para el dominio del ASE. Puede usar un comodín con el dominio en el DNS. Para realizar algunas pruebas sencillas, edite el archivo de hosts en su máquina virtual para establecer el nombre de la aplicación web en la dirección IP de IP virtual:

    a. Si el ASE tiene el nombre de dominio _.ilbase.com_ y crea una aplicación web denominada _mytestapp_, su dirección será _mytestapp.ilbase.com_. A continuación, establezca _mytestapp.ilbase.com_ para que resuelva a la dirección del ILB. (en Windows, el archivo hosts se encuentra en _C:\Windows\System32\drivers\etc\_.)

    b. Para probar la publicación de la implementación web o tener acceso a la consola avanzada, cree un registro para _mytestapp.scm.ilbase.com_.

7. Utilice un explorador en esa máquina virtual y vaya a http://mytestapp.ilbase.com (o vaya allí donde el nombre de la aplicación web esté con su dominio).

8. Utilice un explorador en esa máquina virtual y vaya a https://mytestapp.ilbase.com. Si utiliza un certificado autofirmado, acepte el aviso de seguridad.

    La dirección IP del ILB se incluye en **Direcciones IP**. Esta lista también incluye las direcciones IP utilizadas por la dirección VIP externa y para el tráfico de administración de entrada.

    ![Dirección IP del ILB][5]

## <a name="web-jobs-functions-and-the-ilb-ase"></a>Trabajos web, Functions y ASE de ILB ##

Functions y los trabajos web se admiten en un ASE de ILB, pero para que el portal funcione con ellos, debe tener acceso a la red para el sitio de SCM.  Esto significa que el explorador debe estar en un host que se encuentre en la red virtual o conectado a ella.  

Al usar Azure Functions en un ASE con un ILB, puede recibir un mensaje de error que dice "No se han podido recuperar sus funciones ahora. Inténtelo de nuevo más tarde." Este error se produce porque la interfaz de usuario de Functions aprovecha el sitio de SCM a través de HTTPS y el certificado raíz no está en la cadena de confianza del explorador. Los trabajos web tienen un problema similar. Para evitar este problema puede hacer cualquiera de las siguientes acciones:

- Agregue el certificado a su almacén de certificados de confianza. Esto desbloquea Edge e Internet Explorer.
- Use Chrome y primero vaya al sitio de SCM, acepte el certificado que no es de confianza y luego vaya al portal.
- Use un certificado comercial que se encuentre en la cadena de confianza del explorador.  Esta es la mejor opción.  

## <a name="dns-configuration"></a>Configuración de DNS ##

Cuando se utiliza una dirección VIP externa, Azure se encarga de administrar el DNS. Todas las aplicaciones que cree en el ASE se agregan automáticamente a DNS de Azure, que es un DNS público. En un ASE con un ILB tiene que administrar su propio DNS. Para un dominio concreto como _contoso.net_, debe crear registros A de DNS que apunten a la dirección del ILB para:

- *.contoso.net
- *.scm.contoso.net

Si el dominio del ASE con un ILB se usa para otros fines además del ASE, deberá administrar el DNS en función del nombre de aplicación. Este método es complejo ya que es necesario agregar los nuevos nombres de aplicación en el DNS al crear dichas aplicaciones. Por ello, recomendamos que use un dominio dedicado.

## <a name="publish-with-an-ilb-ase"></a>Publicación con un ASE con un ILB ##

Para cada aplicación que se cree, hay dos puntos de conexión. En un ASE con un ILB, existen *&lt;app name>.&lt;ILB ASE Domain>* y *&lt;app name>.scm.&lt;ILB ASE Domain>*. 

El nombre del sitio SCM le lleva a la consola de Kudu, denominada **Advanced Portal** dentro de Azure Portal. La consola de Kudu le permite visualizar las variables de entorno, explorar el disco, utilizar una consola y mucho más. Para más información, consulte [Consola de Kudu para Azure App Service][Kudu]. 

En el servicio App Service multiinquilino y en un ASE Externo, hay un inicio de sesión único entre Azure Portal y la consola de Kudu. Sin embargo, para el ASE con un ILB, debe usar las credenciales de publicación para iniciar sesión en la consola de Kudu.

Los sistemas de CI basados en Internet, como GitHub y Visual Studio Team Services, no funcionan con un ASE con un ILB ya que el punto de conexión de publicación no es accesible desde Internet. En lugar de eso, necesita usar un sistema de CI que use un modelo de extracción, como Dropbox.

Los puntos de conexión de publicación para las aplicaciones en un ASE con un ILB usan el dominio con el que se creó el ASE con un ILB. Este dominio aparece en el perfil de publicación de la aplicación y en la hoja del portal de la aplicación (en **Información general** > **Información esencial** y también en **Propiedades**). Si tiene un ASE con un ILB con el subdominio *contoso.net* y una aplicación llamada *mytest*, use *mytest.contoso.net* para FTP y *mytest.scm.contoso.net* para la implementación web.

## <a name="couple-an-ilb-ase-with-a-waf-device"></a>Acoplamiento de un ASE con un ILB con un dispositivo WAF ##

Azure App Service proporciona muchas medidas de seguridad que protegen el sistema. También ayudan a determinar si una aplicación ha sido víctima del ataque de un hacker. La mejor protección para una aplicación web es acoplar una plataforma de hospedaje, como Azure App Service, con un firewall de aplicaciones web (WAF). Dado que un ASE con un ILB tiene un punto de conexión de la aplicación aislado de la red, es adecuado para este tipo de uso.

Para obtener más información sobre cómo configurar el ASE con un ILB con un dispositivo WAF, consulte [Configurar un firewall de aplicaciones web con App Service Environment][ASEWAF]. En este artículo se muestra cómo usar una aplicación virtual Barracuda con su ASE. Otra opción es utilizar Azure Application Gateway. Application Gateway utiliza las reglas de núcleo de OWASP para proteger cualquier aplicación que se encuentre detrás de él. Para obtener más información sobre Application Gateway, consulte [Introducción al firewall de aplicaciones web de Azure][AppGW].

## <a name="get-started"></a>Introducción ##

* Para empezar a trabajar con las instancias de App Service Environment, consulte [Introducción a App Service Environment][Intro].
 
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
[UsingASE]: ./using-an-ase.md
[UDRs]: ../../virtual-network/virtual-networks-udr-overview.md
[NSGs]: ../../virtual-network/virtual-networks-nsg.md
[ConfigureASEv1]: app-service-web-configure-an-app-service-environment.md
[ASEv1Intro]: app-service-app-service-environment-intro.md
[webapps]: ../app-service-web-overview.md
[mobileapps]: ../../app-service-mobile/app-service-mobile-value-prop.md
[Functions]: ../../azure-functions/index.yml
[Pricing]: http://azure.microsoft.com/pricing/details/app-service/
[ARMOverview]: ../../azure-resource-manager/resource-group-overview.md
[ConfigureSSL]: ../web-sites-purchase-ssl-web-site.md
[Kudu]: http://azure.microsoft.com/resources/videos/super-secret-kudu-debug-console-for-azure-web-sites/
[ASEWAF]: app-service-app-service-environment-web-application-firewall.md
[AppGW]: ../../application-gateway/application-gateway-web-application-firewall-overview.md
