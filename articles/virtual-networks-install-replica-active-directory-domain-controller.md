<properties linkid="manage-services-networking-replica-domain-controller" urlDisplayName="Replica domain controller" pageTitle="Install a replica domain controller in Azure" metaKeywords="" description="A tutorial that teaches you how to install a domain controller from your Corp Active Directory forest on your Azure virtual machine." metaCanonical="" services="virtual-network" documentationCenter="" title="Install a Replica Active Directory Domain Controller in Azure Virtual Networks" authors="Justinha" solutions="" writer="Justinha" manager="TerryLan" editor="LisaToft" />

<tags ms.service="virtual-network" ms.workload="infrastructure-services" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="09/17/2014" ms.author="Justinha" />

# Instalación de un controlador de dominio réplica de Active Directory en Redes virtuales de Azure

Este tutorial le guiará a través de los pasos para instalar un controlador de dominio adicional desde su bosque de Active Directory corporativo en una máquina virtual en [Red virtual de Azure][Red virtual de Azure]. En este tutorial, la red virtual de la máquina virtual está conectada a la red de su empresa. Si desea obtener una guía conceptual sobre la instalación de Servicios de dominio de Active Directory (AD DS) en la Red virtual de Azure, consulte[Directrices para implementar Windows Server Active Directory en máquinas virtuales de Azure][Directrices para implementar Windows Server Active Directory en máquinas virtuales de Azure].

## Tabla de contenido

-   [Requisitos previos][Requisitos previos]
-   [Paso 1: Comprobación de dirección IP estática para YourPrimaryDC][Paso 1: Comprobación de dirección IP estática para YourPrimaryDC]
-   [Paso 2: Instalación del bosque corporativo][Paso 2: Instalación del bosque corporativo]
-   [Paso 3: Creación de subredes y sitios][Paso 3: Creación de subredes y sitios]
-   [Paso 4: Instalación de un controlador de dominio adicional en CloudSite][Paso 4: Instalación de un controlador de dominio adicional en CloudSite]
-   [Paso 5: Validación de la instalación][Paso 5: Validación de la instalación]
-   [Paso 6: Aprovisionamiento de una máquina virtual que se une mediante dominio en el inicio][Paso 6: Aprovisionamiento de una máquina virtual que se une mediante dominio en el inicio]
-   [Paso 7: Copia de seguridad del controlador de dominio][Paso 7: Copia de seguridad del controlador de dominio]
-   [Paso 8: Prueba de autenticación y autorización][Paso 8: Prueba de autenticación y autorización]

## <span id="Prerequisites"></span></a>Requisitos previos

-   [Configurar una VPN de sitio a sitio en el Portal de administración][Configurar una VPN de sitio a sitio en el Portal de administración] configurado entre la red virtual Azure y la red Corp.
-   Creación de un servicio en la nube en la red virtual.
-   Implementación de dos máquinas virtuales en el Servicio en la nube que sean parte de la red virtual (especifique la subred donde desea colocar la máquina virtual). Para obtener más información, consulte [Incorporación de una máquina virtual a una red virtual][Incorporación de una máquina virtual a una red virtual]. Una máquina virtual debe ser lo suficientemente grande como para acoplarle dos discos de datos. Estos discos de datos se necesitan para almacenar:

    -   La base de datos y los registros de Active Directory.
    -   Copias de seguridad del estado del sistema.
-   Una red corporativa con dos máquinas virtuales (YourPrimaryDC y FileServer).
-   Infraestructura del sistema de nombres de dominio (DNS) implementada si necesita que usuarios externos resuelvan nombres para cuentas en Active Directory. En este caso, debe crear una delegación de zonas DNS antes de instalar el servidor DNS en el controlador de dominio o permitir que el asistente para instalación de los Servicios de dominio de Active Directory cree la delegación. Para obtener más información acerca de la creación de una delegación de zonas DNS, consulte [Creación de una delegación de zona][Creación de una delegación de zona].
-   En el controlador de dominio que instala en una máquina virtual de Azure, configure el solucionador de cliente DNS de la siguiente manera:

    -   Servidor DNS preferido: el servidor DNS local
    -   Servidor DNS alternativo: dirección de bucle invertido o, si es posible, otro servidor DNS que se ejecute en un controlador de dominio en la misma red virtual.

<div class="dev-callout"> 
<b>Nota:</b>
<p>Debe proporcionar su propia infraestructura DNS para admitir AD DS en la Red virtual de Azure. La infraestructura DNS proporcionada por Azure para esta versi&oacute;n no es compatible con algunas de las caracter&iacute;sticas que AD DS requiere, como el registro de recursos SRV din&aacute;mico. </p>
</div>

<div class="dev-callout"> 
<b>Nota:</b>
<p>Si ya complet&oacute; los pasos de <a href="/es-es/manage/services/networking/active-directory-forest/">Instalaci&oacute;n de un nuevo bosque de Active Directory en Azure</a>, es posible que sea necesario quitar AD DS del controlador de dominio en la red virtual de Azure antes de comenzar este tutorial. Para ver m&aacute;s informaci&oacute;n sobre c&oacute;mo quitar AD DS, consulte <a href="http://technet.microsoft.com/es-es/library/cc771844(v=WS.10).aspx">Eliminaci&oacute;n de un controlador de dominio de un dominio</a>.</p>
</div>

## <span id="verifystaticip"></span></a>Paso 1: Comprobación de dirección IP estática para YourPrimaryDC

1.  Inicie sesión en YourPrimaryDC en la red corporativa.

2.  En el Administrador de servidores, haga clic en Ver conexiones de red.

3.  Haga clic con el botón secundario en la conexión de red de área local y haga clic en Propiedades.

4.  Haga clic en Protocolo de Internet versión 4 (TCP/IPv4) y haga clic en Propiedades.

5.  Compruebe que el servidor tiene asignada una dirección IP estática.

    ![VerifystaticIPaddressyourPrimaryDC1][VerifystaticIPaddressyourPrimaryDC1]

## <span id="installforest"></span></a>Paso 2: Instalación del bosque corporativo

1.  En la sesión de RDP para la máquina virtual, haga clic en **Inicio**, escriba **dcpromo** y pulse Entrar.

    ![InstallCorpForest1][InstallCorpForest1]

2.  En la página de bienvenida, haga clic en **Siguiente**.

    ![InstallCorpForest2][InstallCorpForest2]

3.  En la página de compatibilidad de sistema operativo, haga clic en **Siguiente**.

    ![InstallCorpForest3][InstallCorpForest3]

4.  En la página para elegir una configuración de implementación, haga clic en **Crear un dominio nuevo en un bosque nuevo** y, a continuación, en **Siguiente**.

    ![InstallCorpForest4][InstallCorpForest4]

5.  En la página para asignar un nombre al dominio raíz del bosque, escriba el nombre de dominio completo **corp.contoso.com** del dominio raíz del bosque y haga clic en **Siguiente**.

    ![InstallCorpForest5][InstallCorpForest5]

6.  En la página para establecer el nivel funcional del bosque, haga clic en **Windows Server 2008 R2** y, a continuación, en **Siguiente**.

    ![InstallCorpForest6][InstallCorpForest6]

7.  En la página de opciones adicionales del controlador de dominio, haga clic en **Servidor DNS** y, a continuación, en **Siguiente**.

    ![InstallCorpForest7][InstallCorpForest7]

8.  Si aparece la siguiente advertencia de delegación DNS, haga clic en **Sí**.

    ![InstallCorpForest8][InstallCorpForest8]

9.  En la página de ubicación de la base de datos, archivos de registro y SYSVOL de Active Directory, escriba o seleccione la ubicación de los archivos y haga clic en **Siguiente**.

    ![InstallCorpForest9][InstallCorpForest9]

10. En la página del administrador de restauración de los servicios de directorio, escriba y confirme la contraseña del DSRM y haga clic en **Siguiente**.

    ![InstallCorpForest10][InstallCorpForest10]

11. En la página de resumen, confirme sus selecciones y haga clic en **Siguiente**.

    ![InstallCorpForest11][InstallCorpForest11]

12. Una vez que ha finalizado el Asistente para instalación de Active Directory, haga clic en **Finalizar** y, a continuación, en **Reiniciar ahora** para completar la instalación.

    ![InstallCorpForest12][InstallCorpForest12]

## <span id="subnets"></span></a>Paso 3: Creación de subredes y sitios

1.  En YourPrimaryDC, haga clic en Inicio, Herramientas administrativas y, a continuación, en Sitios y servicios de Active Directory.
2.  Haga clic en **Sitios**, clic con el botón secundario en **Subredes** y, a continuación, haga clic en **Nueva subred**.

    ![CreateSubnetsandSites1][CreateSubnetsandSites1]

3.  En **Prefijo::**, escriba **10.1.0.0/24**, seleccione el objeto del sitio **Default-First-Site-Name** y haga clic en **Aceptar**.

    ![CreateSubnetsandSites2][CreateSubnetsandSites2]

4.  Haga clic con el botón secundario en **Sitios** y, a continuación, haga clic en **Sitio nuevo**.

    ![CreateSubnetsandSites3][CreateSubnetsandSites3]

5.  En Nombre, escriba **CloudSite**, seleccione **DEFAULTIPSITELINK** y haga clic en **Aceptar**.

    ![CreateSubnetsandSites4][CreateSubnetsandSites4]

6.  Haga clic en **Aceptar** para confirmar que se creó el sitio.

    ![CreateSubnetsandSites5][CreateSubnetsandSites5]

7.  Haga clic con el botón secundario en **Subredes** y, a continuación, haga clic en **Nueva subred**.

    ![CreateSubnetsandSites6][CreateSubnetsandSites6]

8.  En **Prefijo::**, escriba **10.4.2.0/24**, seleccione el objeto del sitio **CloudSite** y haga clic en **Aceptar**.

    ![CreateSubnetsandSites7][CreateSubnetsandSites7]

## <span id="cloudsite"></span></a>Paso 4: Instalación de un controlador de dominio adicional en CloudSite

1.  Inicie sesión en YourVMachine, haga clic en **Inicio**, escriba **dcpromo** y presione Entrar.

    ![AddDC1][AddDC1]

2.  En la página de bienvenida, haga clic en **Siguiente**.

    ![AddDC2][AddDC2]

3.  En la página de compatibilidad de sistema operativo, haga clic en **Siguiente**.

    ![AddDC3][AddDC3]

4.  En la página para elegir una configuración de implementación, haga clic en **Bosque existente**, luego en **Agregar un controlador de dominio a un dominio existente** y, a continuación, haga clic en **Siguiente**.

    ![AddDC4][AddDC4]

5.  En la página de credenciales de red, asegúrese de que está instalando el controlador de dominio en el dominio **corp.contoso.com** y escriba las credenciales de un miembro del grupo Administradores del dominio (o utilice credenciales corporativas o de administrador).

    ![AddDC5][AddDC5]

6.  En la página para seleccionar un dominio, haga clic en **Siguiente**.

    ![AddDC6][AddDC6]

7.  En la página para seleccionar un sitio, asegúrese de que CloudSite está seleccionado y haga clic en **Siguiente**.

    ![AddDC7][AddDC7]

8.  En la página de opciones adicionales del controlador de dominio, haga clic en **Siguiente**.

    ![AddDC8][AddDC8]

9.  En la advertencia de asignación de IP estática, haga clic en **Sí, el equipo usará una dirección IP asignada automáticamente por un servidor DHCP (no recomendado).**

    **Importante**

    A pesar de que la dirección IP en la Red virtual de Azure es dinámica, su concesión durará mientras dure la máquina virtual. Por lo tanto, no es necesario definir una dirección IP estática en el controlador de dominio que instale en la red virtual. Configurar una dirección IP estática en la máquina virtual generará errores de comunicación.

    ![AddDC9][AddDC9]

10. Cuando aparezca la advertencia de delegación DNS, haga clic en **Sí**.

    ![AddDC10][AddDC10]

11. En la página de ubicación de la base de datos, archivos de registro y SYSVOL de Active Directory, haga clic en Examinar y escriba o seleccione una ubicación en el disco de datos para los archivos de Active Directory y haga clic en **Siguiente**.

    ![AddDC11][AddDC11]

12. En la página del administrador de restauración de los servicios de directorio, escriba y confirme la contraseña del DSRM y haga clic en **Siguiente**.

    ![AddDC12][AddDC12]

13. En la página de resumen, haga clic en **Siguiente**.

    ![AddDC13][AddDC13]

14. Una vez que ha finalizado el Asistente para instalación de Active Directory, haga clic en **Finalizar** y, a continuación, en **Reiniciar ahora** para completar la instalación.

    ![AddDC14][AddDC14]

## <span id="validate"></span></a>Paso 5: Validación de la instalación

1.  Vuelva a conectarse a la máquina virtual.

2.  Haga clic en **Inicio**, haga clic con el botón secundario en **Símbolo del sistema** y, a continuación, haga clic en **Ejecutar como administrador**.

3.  Escriba el siguiente comando y pulse Entrar: 'Dcdiag /c /v'

4.  Compruebe que las pruebas se ejecutaron correctamente.

Una vez que se ha configurado el controlador de dominio, ejecute el siguiente cmdlet de Windows PowerShell para aprovisionar máquinas virtuales adicionales y hacer que se unan automáticamente al dominio cuando se aprovisionan. Cuando se aprovisionan las máquinas virtuales se debe configurar el solucionador de cliente DNS para las máquinas virtuales. Sustituya los nombres correctos para el dominio, nombre de máquina virtual, etc.

Para obtener más información acerca del uso de Windows PowerShell, consulte [Azure PowerShell][Azure PowerShell] y [Azure Management Cmdlets][Azure Management Cmdlets].

## <span id="provisionvm"></span></a>Paso 6: Aprovisionamiento de una máquina virtual que se une mediante dominio en el inicio

1.  Para crear una máquina virtual que se une mediante dominio en su primer inicio, abra Azure PowerShell ISE, pegue el siguiente script siguiente, reemplace los marcadores de posición por sus propios valores y ejecútelo.

    Para determinar la dirección IP interna del controlador de dominio, haga clic en el nombre de la red virtual en que se ejecuta.

    En el siguiente ejemplo, la dirección IP interna del controlador de dominio es 10.4.3.1. Add-AzureProvisioningConfig también toma un parámetro -MachineObjectOU que, si se especifica (requiere el nombre completo en Active Directory), permite configurar una directiva de grupo en todas las máquinas virtuales de ese contenedor.

    Una vez que se aprovisionan las máquinas virtuales, inicie sesión especificando una cuenta de dominio con el formato de nombre principal de usuario (UPN), como <administrator@corp.contoso.com>.

        #Deploy a new VM and join it to the domain
        #-------------------------------------------
        #Specify my DC's DNS IP (10.4.3.1)
        $myDNS = New-AzureDNS -Name 'ContosoDC13' -IPAddress '10.4.3.1'

        # OS Image to Use
        $image = 'MSFT__Sql-Server-11EVAL-11.0.2215.0-08022012-es-es-30GB.vhd'
        $service = 'myazuresvcindomainM1'
        $AG = 'YourAffinityGroup'
        $vnet = 'YourVirtualNetwork'
        $pwd = 'p@$$w0rd'
        $size = 'Small'

        #VM Configuration
        $vmname = 'MyTestVM1'
        $MyVM1 = New-AzureVMConfig -name $vmname -InstanceSize $size -ImageName $image |
            Add-AzureProvisioningConfig -WindowsDomain -Password $pwd -Domain 'corp' -DomainPassword 'p@$$w0rd' -DomainUserName 'Administrator' -JoinDomain 'corp.contoso.com'|
            Set-AzureSubnet -SubnetNames 'BackEnd'

        New-AzureVM -ServiceName $service -AffinityGroup $AG -VMs $MyVM1 -DnsSettings $myDNS -VNetName $vnet

## <span id="backup"></span></a>Paso 7: Copia de seguridad del controlador de dominio

1.  Conéctese a YourVMachine.

2.  Haga clic en **Inicio**, en **Administrador de servidores**, **Agregar características** y, a continuación, seleccione **Características de Copias de seguridad de Windows Server**. Siga las instrucciones para instalar Copias de seguridad de Windows Server.

3.  Haga clic en **Inicio**, en **Copias de seguridad de Windows Server** y, a continuación, en **Hacer copia de seguridad una vez**.

4.  Haga clic en **Opciones diferentes** y, a continuación, en **Siguiente**.

5.  Haga clic en **Servidor completo** y, a continuación, en **Siguiente**.

6.  Haga clic en **Unidades locales** y, a continuación, en **Siguiente**.

7.  Seleccione la unidad de destino que no hospeda los archivos de sistema operativo o la base de datos de Active Directory y, a continuación, haga clic en Siguiente.

    ![BackupDC][BackupDC]

8.  Confirme la configuración de copia de seguridad que seleccionó y haga clic en **Copia de seguridad**.

## <span id="test"></span></a>Paso 8: Prueba de autenticación y autorización

1.  Para probar la autenticación y autorización, cree una cuenta de usuario de dominio en Active Directory.
    Inicie sesión en la máquina virtual cliente en cada sitio y cree una carpeta compartida en la máquina virtual.

2.  Pruebe el acceso a la carpeta compartida con distintas cuentas, grupos y permisos.

## Otras referencias

-   [Red virtual][Red virtual de Azure]

-   [IaaS para profesionales de TI en Windows Azure: (01) Principios básicos sobre máquinas virtuales][IaaS para profesionales de TI en Windows Azure: (01) Principios básicos sobre máquinas virtuales]

-   [IaaS para profesionales de TI en Windows Azure: (05) Creación de redes virtuales y conectividad entre instalaciones][IaaS para profesionales de TI en Windows Azure: (05) Creación de redes virtuales y conectividad entre instalaciones]

-   [Azure PowerShell][Azure PowerShell]

-   [Azure Management Cmdlets][Azure Management Cmdlets]

  [Red virtual de Azure]: http://msdn.microsoft.com/es-es/library/windowsazure/jj156007.aspx
  [Directrices para implementar Windows Server Active Directory en máquinas virtuales de Azure]: http://msdn.microsoft.com/es-es/library/windowsazure/jj156090.aspx
  [Requisitos previos]: #Prerequisites
  [Paso 1: Comprobación de dirección IP estática para YourPrimaryDC]: #verifystaticip
  [Paso 2: Instalación del bosque corporativo]: #installforest
  [Paso 3: Creación de subredes y sitios]: #subnets
  [Paso 4: Instalación de un controlador de dominio adicional en CloudSite]: #cloudsite
  [Paso 5: Validación de la instalación]: #validate
  [Paso 6: Aprovisionamiento de una máquina virtual que se une mediante dominio en el inicio]: #provisionvm
  [Paso 7: Copia de seguridad del controlador de dominio]: #backup
  [Paso 8: Prueba de autenticación y autorización]: #test
  [Configurar una VPN de sitio a sitio en el Portal de administración]: http://msdn.microsoft.com/es-es/library/dn133795.aspx
  [Incorporación de una máquina virtual a una red virtual]: http://azure.microsoft.com/es-es/documentation/articles/virtual-networks-add-virtual-machine/
  [Creación de una delegación de zona]: http://technet.microsoft.com/library/cc753500.aspx
  [VerifystaticIPaddressyourPrimaryDC1]: ./media/virtual-networks-install-replica-active-directory-domain-controller/VerifystaticIP.png
  [InstallCorpForest1]: ./media/virtual-networks-install-replica-active-directory-domain-controller/InstallCorpForest1.png
  [InstallCorpForest2]: ./media/virtual-networks-install-replica-active-directory-domain-controller/InstallCorpForest2.png
  [InstallCorpForest3]: ./media/virtual-networks-install-replica-active-directory-domain-controller/InstallCorpForest3.png
  [InstallCorpForest4]: ./media/virtual-networks-install-replica-active-directory-domain-controller/InstallCorpForest4.png
  [InstallCorpForest5]: ./media/virtual-networks-install-replica-active-directory-domain-controller/InstallCorpForest5.png
  [InstallCorpForest6]: ./media/virtual-networks-install-replica-active-directory-domain-controller/InstallCorpForest6.png
  [InstallCorpForest7]: ./media/virtual-networks-install-replica-active-directory-domain-controller/InstallCorpForest7.png
  [InstallCorpForest8]: ./media/virtual-networks-install-replica-active-directory-domain-controller/InstallCorpForest8.png
  [InstallCorpForest9]: ./media/virtual-networks-install-replica-active-directory-domain-controller/InstallCorpForest9.png
  [InstallCorpForest10]: ./media/virtual-networks-install-replica-active-directory-domain-controller/InstallCorpForest10.png
  [InstallCorpForest11]: ./media/virtual-networks-install-replica-active-directory-domain-controller/InstallCorpForest11.png
  [InstallCorpForest12]: ./media/virtual-networks-install-replica-active-directory-domain-controller/InstallCorpForest12.png
  [CreateSubnetsandSites1]: ./media/virtual-networks-install-replica-active-directory-domain-controller/CreateSubnetsandSites1.png
  [CreateSubnetsandSites2]: ./media/virtual-networks-install-replica-active-directory-domain-controller/CreateSubnetsandSites2.png
  [CreateSubnetsandSites3]: ./media/virtual-networks-install-replica-active-directory-domain-controller/CreateSubnetsandSites3.png
  [CreateSubnetsandSites4]: ./media/virtual-networks-install-replica-active-directory-domain-controller/CreateSubnetsandSites4.png
  [CreateSubnetsandSites5]: ./media/virtual-networks-install-replica-active-directory-domain-controller/CreateSubnetsandSites5.png
  [CreateSubnetsandSites6]: ./media/virtual-networks-install-replica-active-directory-domain-controller/CreateSubnetsandSites6.png
  [CreateSubnetsandSites7]: ./media/virtual-networks-install-replica-active-directory-domain-controller/CreateSubnetsandSites7.png
  [AddDC1]: ./media/virtual-networks-install-replica-active-directory-domain-controller/AddDC1.png
  [AddDC2]: ./media/virtual-networks-install-replica-active-directory-domain-controller/AddDC2.png
  [AddDC3]: ./media/virtual-networks-install-replica-active-directory-domain-controller/AddDC3.png
  [AddDC4]: ./media/virtual-networks-install-replica-active-directory-domain-controller/AddDC4.png
  [AddDC5]: ./media/virtual-networks-install-replica-active-directory-domain-controller/AddDC5.png
  [AddDC6]: ./media/virtual-networks-install-replica-active-directory-domain-controller/AddDC6.png
  [AddDC7]: ./media/virtual-networks-install-replica-active-directory-domain-controller/AddDC7.png
  [AddDC8]: ./media/virtual-networks-install-replica-active-directory-domain-controller/AddDC8.png
  [AddDC9]: ./media/virtual-networks-install-replica-active-directory-domain-controller/AddDC9.png
  [AddDC10]: ./media/virtual-networks-install-replica-active-directory-domain-controller/AddDC10.png
  [AddDC11]: ./media/virtual-networks-install-replica-active-directory-domain-controller/AddDC11.png
  [AddDC12]: ./media/virtual-networks-install-replica-active-directory-domain-controller/AddDC12.png
  [AddDC13]: ./media/virtual-networks-install-replica-active-directory-domain-controller/AddDC13.png
  [AddDC14]: ./media/virtual-networks-install-replica-active-directory-domain-controller/AddDC14.png
  [Azure PowerShell]: http://msdn.microsoft.com/es-es/library/windowsazure/jj156055.aspx
  [Azure Management Cmdlets]: http://msdn.microsoft.com/es-es/library/windowsazure/jj152841
  [BackupDC]: ./media/virtual-networks-install-replica-active-directory-domain-controller/BackupDC.png
