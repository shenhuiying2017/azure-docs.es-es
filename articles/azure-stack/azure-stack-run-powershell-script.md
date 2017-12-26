---
title: "Implementación de Azure Stack Development Kit | Microsoft Docs"
description: Aprenda a preparar Azure Stack Development Kit y a ejecutar el script de PowerShell para implementarlo.
services: azure-stack
documentationcenter: 
author: jeffgilb
manager: femila
editor: 
ms.assetid: 0ad02941-ed14-4888-8695-b82ad6e43c66
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 12/15/2017
ms.author: jeffgilb
ms.reviewer: wfayed
ms.openlocfilehash: 470a45aea253e1e238983527427b600117e413fe
ms.sourcegitcommit: 821b6306aab244d2feacbd722f60d99881e9d2a4
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/16/2017
---
# <a name="deploy-the-azure-stack-development-kit"></a>Implementación de Azure Stack Development Kit

*Se aplica a: Azure Stack Development Kit*

Para implementar [Azure Stack Development Kit](azure-stack-poc.md), debe completar los pasos siguientes:

1. [Descargue el paquete de implementación](https://azure.microsoft.com/overview/azure-stack/try/?v=try) para obtener el archivo Cloudbuilder.vhdx.
2. Prepare el archivo cloudbuilder.vhdx para configurar el equipo (el host) en el que desea instalar el kit de desarrollo. Después de este paso, el host del kit de desarrollo arrancará con el archivo Cloudbuilder.vhdx.
3. Implemente Azure Stack Development Kit en el host del kit de desarrollo.

> [!NOTE]
> Para obtener mejores resultados, incluso si desea usar un entorno desconectado de Azure Stack, es mejor implementar mientras está conectado a Internet. De este modo, se puede activar la versión de evaluación de Windows Server 2016, incluida en la instalación del kit de desarrollo, durante la implementación.

## <a name="download-and-extract-the-development-kit"></a>Descargue y extraiga el kit de desarrollo
1. Antes de iniciar la descarga, asegúrese de que el equipo cumple con los requisitos previos siguientes:

  - El equipo debe tener al menos 60 GB de espacio en disco disponible en cuatro unidades de disco duro idénticas e independientes además del disco de sistema operativo.
  - [.NET framework 4.6 (o una versión posterior)](https://aka.ms/r6mkiy) debe estar instalado.

2. [Vaya a la página de introducción](https://azure.microsoft.com/overview/azure-stack/try/?v=try) donde puede descargar Azure Stack Development Kit, proporcione sus datos y, a continuación, haga clic en **Enviar**.
3. Descargue y ejecute el [comprobador de implementaciones de Azure Stack Development Kit](https://go.microsoft.com/fwlink/?LinkId=828735&clcid=0x409) que es el script de comprobación que constituye un requisito previo. Este script independiente revisa las comprobaciones previas realizadas por la configuración de Azure Stack Development Kit. Ofrece una manera de confirmar que se están cumpliendo los requisitos de hardware y software, antes de descargar el paquete más grande de Azure Stack Development Kit.
4. En **Descargar el software**, haga clic en **Azure Stack Development Kit**.

  > [!NOTE]
  > Solo la descarga de ASDK (AzureStackDevelopmentKit.exe) es de 10 GB aproximadamente. Si también desea descargar el archivo ISO de la versión de evaluación de Windows Server 2016, el tamaño de la descarga puede alcanzar unos 17 GB. Puede utilizar ese archivo ISO para crear y agregar una imagen de máquina virtual de Windows Server 2016 a Azure Stack Marketplace una vez que se complete la instalación de ASDK. Tenga en cuenta que esta imagen de evaluación de Windows Server 2016 solo se puede usar con ASDK y está sujeto a los términos de licencia de este.

5. Una vez finalizada la descarga, haga clic en **Ejecutar** para iniciar el autoextractor de ASDK (AzureStackDevelopmentKit.exe).
6. Revise y acepte el contrato de licencia que aparece en la página **Contrato de licencia** del asistente del autoextractor y, a continuación, haga clic en **Siguiente**.
7. Revise la información sobre la declaración de privacidad que aparece en la página **Aviso importante** del asistente del autoextractor y, a continuación, haga clic en **Siguiente**.
8. Seleccione la ubicación de los archivos de instalación de Azure Stack que se van a extraer en la página **Seleccionar ubicación de destino** del asistente del autoextractor y, a continuación, haga clic en **Siguiente**. La ubicación predeterminada es *carpeta actual*\Azure Stack Development Kit. 
9. Revise el resumen de información de la ubicación de destino en la página **Ready to Extract** (Listo para extraer) del asistente del autoextractor y, a continuación, haga clic en **Extraer** para extraer los archivos CloudBuilder.vhdx (de unos 25 GB) y ThirdPartyLicenses.rtf. Este proceso puede tardar un tiempo en finalizar.
10. Copie o mueva el archivo CloudBuilder.vhdx a la raíz de la unidad C:\ (C:\CloudBuilder.vhdx) del equipo host de ASDK.

> [!NOTE]
> Después de extraer los archivos, puede eliminar los archivos EXE y .BIN para recuperar espacio en el disco duro. O bien, puede hacer una copia de seguridad de estos archivos para que si necesita volver a implementar ASDK, no tenga que descargarlos de nuevo.

## <a name="deploy-the-asdk-using-a-guided-experience"></a>Implementación de ASDK con una experiencia guiada
ASDK se puede implementar mediante una interfaz gráfica de usuario (GUI) que se proporciona al descargar y ejecutar el script de PowerShell asdk-installer.ps1.

> [!NOTE]
> La interfaz de usuario del instalador de Azure Stack Development Kit es un script de código abierto basado en WCF y PowerShell.

### <a name="prepare-the-development-kit-host-using-a-guided-user-experience"></a>Preparación del host del kit de desarrollo mediante una experiencia guiada de usuario
Para poder instalar ASDK en el equipo host, se debe preparar el entorno de este.
1. Inicie sesión como administrador local en el equipo host de ASDK.
2. Asegúrese de que el archivo CloudBuilder.vhdx se ha movido a la raíz de la unidad C:\ (C:\CloudBuilder.vhdx).
3. Ejecute el script siguiente para descargar el archivo instalador del kit de desarrollo (asdk-installer.ps1) del [repositorios de herramientas de GitHub para Azure Stack](https://github.com/Azure/AzureStack-Tools) en la carpeta **C:\AzureStack_Installer** del equipo host del kit de desarrollo:

  ```powershell
  # Variables
  $Uri = 'https://raw.githubusercontent.com/Azure/AzureStack-Tools/master/Deployment/asdk-installer.ps1'
  $LocalPath = 'C:\AzureStack_Installer'
  # Create folder
  New-Item $LocalPath -Type directory
  # Download file
  Invoke-WebRequest $uri -OutFile ($LocalPath + '\' + 'asdk-installer.ps1')
  ```

4. En una consola de PowerShell con privilegios elevados, inicie el script **C:\AzureStack_Installer\asdk-installer.ps1** y, a continuación, haga clic en **Preparar entorno**.
5. En la página **Select Cloudbuilder vhdx** (Seleccionar vhdx de Cloudbuilder) del instalador, busque y seleccione el archivo **cloudbuilder.vhdx** que ha descargado y extraído en los pasos anteriores. En esta página puede también, opcionalmente, activar la casilla **Agregar controladores** si necesita agregar controladores adicionales al equipo host del kit de desarrollo.  
6. En la página **Configuración opcional**, proporcione la cuenta de administrador local para el equipo host del kit de desarrollo. 

  > [!IMPORTANT]
  > Si no proporciona estas credenciales, necesitará acceso directo o KVM al host después de reiniciar el equipo como parte de la configuración del kit de desarrollo.

7. También puede proporcionar estos valores opcionales en la página **Configuración opcional**:
    - **Nombre del equipo**: esta opción permite establecer el nombre para el host del kit de desarrollo. El nombre debe cumplir los requisitos de FQDN y debe tener una longitud máxima de 15 caracteres. El valor predeterminado es un nombre de equipo aleatorio generado por Windows.
    - **Zona horaria**: permite establecer la zona horaria para el host del kit de desarrollo. El valor predeterminado es la hora del Pacífico (EE.UU. y Canadá) (UTC-8:00).
    - **Configuración de IP estática**: hace que la implementación use una dirección IP estática. En caso contrario, cuando se reinicia el instalador en cloudbuilder.vhx, las interfaces de red se configuran con DHCP.
11. Haga clic en **Siguiente**.
12. Si ha elegido una configuración de IP estática en el paso anterior, ahora debe:
    - Seleccionar un adaptador de red. Asegúrese de que puede conectarse al adaptador antes de hacer clic en **Siguiente**.
    - Comprobar que los valores de **dirección IP**, **puerta de enlace**, y **DNS** valores son correctos y, a continuación, hacer clic en **Siguiente**.
13. Haga clic en **Siguiente** para iniciar el proceso de preparación.
14. Cuando la preparación indique **Completado**, haga clic en **Siguiente**.
15. Haga clic en **Reiniciar ahora** para arrancar en cloudbuilder.vhdx y continúe el proceso de implementación.


### <a name="deploy-the-development-kit-using-a-guided-experience"></a>Implementación del kit de desarrollo mediante una experiencia guiada
Después de preparar el equipo host de ASDK, este se puede implementar en la imagen de CloudBuilder.vhdx mediante los siguientes pasos. 
1. Una vez que el equipo host arranque correctamente en la imagen de CloudBuilder.vhdx, inicie sesión mediante las credenciales de administrador especificadas en los pasos anteriores. 
2. Abra una consola de PowerShell con privilegios elevados y ejecute el script **\AzureStack_Installer\asdk-installer.ps1** (que puede estar en una unidad diferente en la imagen de Cloudbuilder.vhdx). Haga clic en **Instalar**.
3. En el cuadro desplegable **Tipo**, seleccione **Azure Cloud** o **ADFS**.
    - **Azure Cloud**: permite configurar Azure Active Directory (Azure AD) como proveedor de identidades. Para usar esta opción, se necesitan una conexión a Internet, el nombre completo de un inquilino del directorio de Azure AD con la forma *nombreDeDominio*.onmicrosoft.com, o un nombre de dominio personalizado comprobado, y credenciales de administrador global para el directorio especificado. 
    - **AD FS**: el servicio de directorio con la marca predeterminada se usará como proveedor de identidades. La cuenta predeterminada con la que iniciar sesión es azurestackadmin@azurestack.local y la contraseña que debe utilizar es la que proporcionó como parte de la configuración.
4. En **Contraseña del administrador local**, en el cuadro **Contraseña**, escriba la contraseña del administrador local (que debe coincidir con la configurada en la actualidad) y, a continuación, haga clic en **Siguiente**.
5. Seleccione un adaptador de red para usarlo para el kit de desarrollo y, a continuación, haga clic en **Siguiente**.
6. Seleccione DHCP o la configuración de red estática para la máquina virtual BGPNAT01.
    - **DHCP** (valor predeterminado): la máquina virtual obtiene la configuración de la red IP del servidor DHCP.
    - **Estático**: use esta opción solo si el DHCP no puede asignar una dirección IP válida de Azure Stack para acceder a Internet. Se debe especificar una dirección IP estática con la longitud de la máscara de subred en formato CIDR (por ejemplo, 10.0.0.5/24).
7. Opcionalmente, establezca los valores siguientes:
    - **Id. de VLAN**: permite establecer el identificador de VLAN. Solo puede utilizar esta opción si el host y AzS-BGPNAT01 deben configurar el identificador de VLAN para tener acceso a la red física (e Internet). 
    - **Reenviador DNS**: se crea un servidor DNS como parte de la implementación de Azure Stack. Para permitir que los equipos dentro de la solución resuelvan nombres fuera de la marca, proporcione el servidor DNS de infraestructura existente. El servidor DNS en la marca reenvía solicitudes de resolución de nombres desconocidos a este servidor.
    - **Servidor horario**: este campo obligatorio establece el servidor horario que va a usar el kit de desarrollo. Este parámetro se debe proporcionar como una dirección IP válida del servidor horario. No se admiten nombres del servidor.
  
  > [!TIP]
  > Para buscar una dirección IP de servidor horario, visite [pool.ntp.org](http:\\pool.ntp.org) o haga ping a time.windows.com. 
  
8. Haga clic en **Siguiente**. 
9. En la página **Verifying network interface card properties** (Comprobación de las propiedades de la tarjeta de interfaz de red), verá una barra de progreso. 
    - Si dice **An update cannot be downloaded** (No se puede descargar una actualización), siga las instrucciones que aparecen en la página.
    - Cuando diga **Completado**, haga clic en **Siguiente**.
10. En la página de **resumen**, haga clic en **Implementar**. Aquí también puede copiar los comandos de instalación de PowerShell que se usarán para instalar el kit de desarrollo.
11. Si va a usar una implementación de Azure AD, se le solicitará que escriba sus credenciales de la cuenta de administrador global de Azure AD unos minutos después de iniciar la instalación.
12. El proceso de implementación puede tardar unas horas, durante las cuales el sistema se reiniciará automáticamente una vez. Si la implementación se realiza correctamente, en la consola de PowerShell aparecerá: **COMPLETE: Action ‘Deployment’** (COMPLETADA: Acción "Implementación"). Si se produce un error en la implementación, puede [volver a implementar](azure-stack-redeploy.md) desde el principio o usar los siguientes comandos de PowerShell, desde la misma ventana de PowerShell con privilegios elevados, para reiniciar la implementación desde el último paso correcto:

  ```powershell
  cd C:\CloudDeployment\Setup
  .\InstallAzureStackPOC.ps1 -Rerun
  ```

   > [!IMPORTANT]
   > Si desea supervisar el progreso de la implementación, inicie sesión como azurestack\AzureStackAdmin cuando se reinicie el host del kit de desarrollo durante la instalación. Si inicia sesión como administrador local después de que la máquina se una al dominio, no podrá ver el progreso de la implementación. No vuelva a ejecutar la implementación, en su lugar, inicie sesión como azurestack\AzureStackAdmin para validar que se está ejecutando.
   

## <a name="deploy-the-asdk-using-powershell"></a>Implementación de ASDK mediante PowerShell
Los pasos anteriores explicaban la implementación de ASDK con una experiencia de usuario guiada. Otra alternativa es usar PowerShell para implementar ASDK en el host del kit de desarrollo siguiendo los pasos descritos en esta sección.

### <a name="configure-the-asdk-host-computer-to-boot-from-cloudbuildervhdx"></a>Configuración del equipo host de ASDK para arrancar desde CloudBuilder.vhdx
Estos comandos van a configurar el equipo host de ASDK para que arranque desde el disco duro virtual que ha descargado y extraído (CloudBuilder.vhdx). Después de completar estos pasos, reinicie el equipo host de ASDK.

1. Inicie un símbolo del sistema como administrador.
2. Ejecute `bcdedit /copy {current} /d "Azure Stack"`
3. Copie (CTRL + C) el valor CLSID devuelto, incluidos los {} obligatorios. Este valor se conoce como {CLSID} y deberá pegarlo (CTRL+V o mediante clic con el botón derecho) en los pasos restantes.
4. Ejecute `bcdedit /set {CLSID} device vhd=[C:]\CloudBuilder.vhdx` 
5. Ejecute `bcdedit /set {CLSID} osdevice vhd=[C:]\CloudBuilder.vhdx` 
6. Ejecute `bcdedit /set {CLSID} detecthal on` 
7. Ejecute `bcdedit /default {CLSID}`
8. Para comprobar la configuración de arranque, ejecute `bcdedit`. 
9. Asegúrese de que el archivo CloudBuilder.vhdx se ha movido a la raíz de la unidad C:\ (C:\CloudBuilder.vhdx) y reinicie el equipo host del kit de desarrollo. Cuando se haya reiniciado el host de ASDK, debe arrancar ya de forma predeterminada desde la máquina virtual CloudBuilder.vhdx. 

### <a name="prepare-the-development-kit-host-using-powershell"></a>Preparación del host del kit de desarrollo con PowerShell 
Una vez que el equipo host del kit de desarrollo arranca correctamente en la imagen de CloudBuilder.vhdx, puede abrir una consola de PowerShell con privilegios elevados y ejecutar los comandos de esta sección para implementar ASDK en el host del kit de desarrollo.

> [!IMPORTANT] 
> La instalación de ASDK admite exactamente una tarjeta de interfaz de red (NIC) para la conexión con redes. Si tiene varias NIC, asegúrese de que solo una está habilitada (y todas las demás deshabilitadas) antes de ejecutar el script de implementación.

Puede implementar Azure Stack con Azure AD o AD FS como proveedor de identidades. Azure Stack, los proveedores de recursos y otras aplicaciones funcionan igual con ambos. Para más información sobre lo que es compatible con AD FS en Azure Stack, consulte el artículo sobre [características y conceptos clave](.\azure-stack-key-features.md).

> [!TIP]
> Si no proporciona ningún parámetro de instalación (consulte los parámetros opcionales y ejemplos de InstallAzureStackPOC.ps1 que aparecen a continuación), se le pedirán los parámetros obligatorios.

### <a name="deploy-azure-stack-using-azure-ad"></a>Implementación de Azure Stack con Azure AD 
Para implementar Azure Stack **empleando Azure AD como proveedor de identidades**, debe tener conectividad a Internet directamente o a través de un proxy transparente. Ejecute los siguientes comandos de PowerShell para implementar el kit de desarrollo con Azure AD:

  ```powershell
  cd C:\CloudDeployment\Setup     
  $adminpass = Get-Credential Administrator     
  .\InstallAzureStackPOC.ps1 -AdminPassword $adminpass.Password
  ```

  Unos minutos después de empezar la instalación de ASDK se le pedirán las credenciales de Azure AD. Debe proporcionar credenciales de administrador global para el inquilino de Azure AD. 

### <a name="deploy-azure-stack-using-ad-fs"></a>Implementación de Azure Stack con AD FS 
Para implementar el kit de desarrollo **mediante AD FS como proveedor de identidades**, ejecute los siguientes comandos de PowerShell (solo necesita agregar el parámetro -UseADFS): 

  ```powershell
  cd C:\CloudDeployment\Setup     
  $adminpass = Get-Credential Administrator 
  .\InstallAzureStackPOC.ps1 -AdminPassword $adminpass.Password -UseADFS
  ```

En las implementaciones con AD FS el servicio de directorio con la marca predeterminada se usará como proveedor de identidades. La cuenta predeterminada con la que iniciar sesión es azurestackadmin@azurestack.local y la contraseña que debe utilizar es la que proporcionó como parte de los comandos de configuración de PowerShell.

El proceso de implementación puede tardar unas horas, durante las cuales el sistema se reiniciará automáticamente una vez. Si la implementación se realiza correctamente, en la consola de PowerShell aparecerá: **COMPLETE: Action ‘Deployment’** (COMPLETADA: Acción "Implementación"). Si se produce un error en la implementación, puede intentar ejecutar el script de nuevo mediante el parámetro -rerun. O bien, puede [volver a implementar ASDK](.\azure-stack-redeploy.md) desde el principio.
> [!IMPORTANT]
> Si desea supervisar el progreso de la implementación una vez que se reinicie el host de ASDK, deberá iniciar sesión como AzureStack\AzureStackAdmin. Si inicia sesión como administrador local después de que el equipo host se reinicie (y se una al dominio azurestack.local), no podrá ver el progreso de la implementación. No vuelva a ejecutar la implementación, en su lugar, inicie sesión como azurestack para validar que se está ejecutando.
>

#### <a name="azure-ad-deployment-script-examples"></a>Ejemplos de scripts de implementación de Azure AD
Puede crear scripts para toda la implementación de Azure AD. Estos son algunos ejemplos comentados que incluyen algunos parámetros opcionales.

Si la identidad de Azure AD está asociada solo con **un** directorio de Azure AD:
```powershell
cd C:\CloudDeployment\Setup 
$adminpass = Get-Credential Administrator 
$aadcred = Get-Credential "<Azure AD global administrator account name>" 
.\InstallAzureStackPOC.ps1 -AdminPassword $adminpass.Password -InfraAzureDirectoryTenantAdminCredential $aadcred -TimeServer 52.168.138.145 #Example time server IP address.
```

Si la identidad de Azure AD está asociada con **más de un** directorio de Azure AD:
```powershell
cd C:\CloudDeployment\Setup 
$adminpass = Get-Credential Administrator 
$aadcred = Get-Credential "<Azure AD global administrator account name>" #Example: user@AADDirName.onmicrosoft.com 
.\InstallAzureStackPOC.ps1 -AdminPassword $adminpass.Password -InfraAzureDirectoryTenantAdminCredential $aadcred -InfraAzureDirectoryTenantName "<Azure AD directory in the form of domainname.onmicrosoft.com or an Azure AD verified custom domain name>" -TimeServer 52.168.138.145 #Example time server IP address.
```

Si su entorno **no** tiene DHCP habilitado, debe incluir los siguientes parámetros adicionales a una de las opciones anteriores (se proporciona un ejemplo de uso): 

```powershell
.\InstallAzureStackPOC.ps1 -AdminPassword $adminpass.Password -InfraAzureDirectoryTenantAdminCredential $aadcred -NatIPv4Subnet 10.10.10.0/24 -NatIPv4Address 10.10.10.3 -NatIPv4DefaultGateway 10.10.10.1 -TimeServer 10.222.112.26
```

### <a name="asdk-installazurestackpocps1-optional-parameters"></a>Parámetros opcionales InstallAzureStackPOC.ps1 de ASDK
|Parámetro|Obligatorio/opcional|Descripción|
|-----|-----|-----|
|AdminPassword|Obligatorio|Establece la cuenta de administrador local y el resto de cuentas de usuario en todas las máquinas virtuales que se crean como parte de la implementación del kit de desarrollo. Esta contraseña debe coincidir con la contraseña de administrador local actual del host.|
|InfraAzureDirectoryTenantName|Obligatorio|Establece el directorio del inquilino. Utilice este parámetro para especificar un directorio concreto en el que la cuenta de AAD tenga permisos para administrar varios directorios. Nombre completo de un inquilino de Azure Active Directory con el formato .onmicrosoft.com, o bien un nombre de dominio personalizado de Azure AD.|
|TimeServer|Obligatorio|Utilice este parámetro para especificar un servidor horario específico. Este parámetro se debe proporcionar como una dirección IP válida del servidor horario. No se admiten nombres del servidor.|
|InfraAzureDirectoryTenantAdminCredential|Opcional|Permite establecer el nombre de usuario y la contraseña de Azure Active Directory. Estas credenciales de Azure deben ser un identificador de organización.|
|InfraAzureEnvironment|Opcional|Seleccione el entorno de Azure con el que desea registrar esta implementación de Azure Stack. Las opciones incluyen Azure público, Azure China, Azure Gobierno de EE.UU.|
|DNSForwarder|Opcional|Se crea un servidor DNS como parte de la implementación de Azure Stack. Para permitir que los equipos dentro de la solución resuelvan nombres fuera de la marca, proporcione el servidor DNS de infraestructura existente. El servidor DNS en la marca reenvía solicitudes de resolución de nombres desconocidos a este servidor.|
|NatIPv4Address|Necesario para la compatibilidad con DHCP NAT|Establece una dirección IP estática para MAS-BGPNAT01. Solo puede utilizar este parámetro si el DHCP no puede asignar una dirección IP válida para tener acceso a Internet.|
|NatIPv4Subnet|Necesario para la compatibilidad con DHCP NAT|Prefijo de subred IP utilizado para la compatibilidad con DHCP NAT. Solo puede utilizar este parámetro si el DHCP no puede asignar una dirección IP válida para tener acceso a Internet.|
|PublicVlanId|Opcional|Permite establecer el identificador de VLAN. Solo puede utilizar este parámetro si el host y MAS-BGPNAT01 deben configurar el identificador de VLAN para tener acceso a la red física (e Internet). Por ejemplo, .\InstallAzureStackPOC.ps1 -Verbose -PublicVLan 305|
|Rerun|Opcional|Use esta marca para volver a ejecutar la implementación. Se utilizarán todas las entradas anteriores. No se admite la reescritura de datos proporcionados anteriormente ya que se generan varios valores únicos que se emplean en la implementación.|

## <a name="activate-the-administrator-and-tenant-portals"></a>Activación de los portales de administrador y de inquilino
Después de las implementaciones que usa Azure AD, debe activar los portales de administrador y de inquilino de Azure Stack. Esta activación concede los permisos correctos al portal de Azure Stack y al de Azure Resource Manager (permisos que se muestran en la página de consentimiento) para todos los usuarios del directorio.

- Para el portal de administrador, vaya a https://adminportal.local.azurestack.external/guest/signup, lea la información y, a continuación, haga clic en **Aceptar**. Después de aceptar, puede agregar los administradores de servicios que no sean también administradores de inquilinos de directorio.

- Para el portal de inquilino, vaya a https://portal.local.azurestack.external/guest/signup, lea la información y, a continuación, haga clic en **Aceptar**. Después de aceptar, los usuarios del directorio pueden iniciar sesión en el portal de inquilino. 

> [!NOTE] 
> Si no se activan los portales, solo el administrador del directorio puede iniciar sesión y usar los portales. Si cualquier otro usuario inicia sesión, verá un error que indica que el administrador no ha concedido permisos a otros usuarios. Cuando el administrador no pertenece de forma nativa al directorio en el que está registrado Azure Stack, el directorio de este se debe anexar a la dirección URL de activación. Por ejemplo, si Azure Stack está registrado con fabrikam.onmicrosoft.com y el usuario administrador es admin@contoso.com, vaya a https://portal.local.azurestack.external/guest/signup/fabrikam.onmicrosoft.com para activar el portal. 

## <a name="reset-the-password-expiration-policy"></a>Restablecimiento de la directiva de expiración de contraseñas 
Para asegurarse de que la contraseña del host del kit de desarrollo no expire antes de que termine el período de evaluación, siga estos pasos después de implementar ASDK.

### <a name="to-change-the-password-expiration-policy-from-powershell"></a>Para cambiar la directiva de caducidad de contraseña desde Powershell:
En una consola de Powershell con privilegios elevados, ejecute el comando:

```powershell
Set-ADDefaultDomainPasswordPolicy -MaxPasswordAge 180.00:00:00 -Identity azurestack.local
```

### <a name="to-change-the-password-expiration-policy-manually"></a>Para cambiar la directiva de caducidad de contraseña manualmente:
1. En el host del kit de desarrollo, abra **Administración de directivas de grupo** y vaya a **Administración de directivas de grupo** – **Bosque: azurestack.local** – **Dominios**: **azurestack.local**.
2. Haga clic con el botón derecho en **Directiva predeterminada de dominio**  y, a continuación, haga clic en **Editar**.
3. En el Editor de administración de directivas de grupo, vaya a **Configuración del equipo** – **Directivas** – **Configuración de Windows** – **Configuración de seguridad** – **Directivas de cuenta** – **Directiva de contraseñas**.
4. En el panel derecho, haga doble clic en **Vigencia máxima de la contraseña**.
5. En el cuadro de diálogo **Maximum password age Properties** (Propiedades de Vigencia máxima de la contraseña), cambie el valor **Password will expire in** (La contraseña expirará en) a 180 y, después, haga clic en **Aceptar**.


## <a name="next-steps"></a>Pasos siguientes

[Conexión a Azure Stack](azure-stack-connect-azure-stack.md)

[Configuración de PowerShell para entornos de Azure Stack](azure-stack-powershell-configure-quickstart.md)

[Registro de Azure Stack con una suscripción de Azure](azure-stack-register.md)



