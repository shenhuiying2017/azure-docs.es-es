---
title: "Implementación de Azure Stack Development Kit | Microsoft Docs"
description: Aprenda a preparar Azure Stack Development Kit y a ejecutar el script de PowerShell para implementarlo.
services: azure-stack
documentationcenter: 
author: ErikjeMS
manager: byronr
editor: 
ms.assetid: 0ad02941-ed14-4888-8695-b82ad6e43c66
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 7/17/2017
ms.author: erikje
ms.openlocfilehash: c3ffc819fcc442f87fb4985ecb08be8ec02ba957
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="deploy-the-azure-stack-development-kit"></a>Implementación de Azure Stack Development Kit

*Se aplica a: Azure Stack Development Kit*

Para implementar [Azure Stack Development Kit](azure-stack-poc.md), debe completar los pasos siguientes:

1. [Descargue el paquete de implementación](https://azure.microsoft.com/overview/azure-stack/try/?v=try) para obtener el archivo Cloudbuilder.vhdx.
2. [Prepare el archivo cloudbuilder.vhdx](#prepare-the-development-kit-host) mediante la ejecución del script asdk-installer.ps1 para configurar el equipo (el host) en el que desea instalar el kit de desarrollo. Después de este paso, el host del kit de desarrollo arrancará con el archivo Cloudbuilder.vhdx.
3. [Implemente el kit de desarrollo](#deploy-the-development-kit) en el host de este.

> [!NOTE]
> Para obtener mejores resultados, incluso si desea usar un entorno desconectado de Azure Stack, es mejor implementar mientras está conectado a Internet. De este modo, se puede activar la versión de evaluación de Windows Server 2016 durante la implementación. Si no se activa la versión de evaluación de Windows Server 2016 en un plazo de 10 días, se cerrará.
> 
> 

## <a name="download-and-extract-the-development-kit"></a>Descargue y extraiga el kit de desarrollo
1. Antes de iniciar la descarga, asegúrese de que el equipo cumple con los requisitos previos siguientes:

   * El equipo debe tener al menos 60 GB de espacio libre en el disco.
   * [.NET framework 4.6 (o una versión posterior)](https://aka.ms/r6mkiy) debe estar instalado.

2. [Vaya a la página de Introducción](https://azure.microsoft.com/overview/azure-stack/try/?v=try), proporcione sus detalles y haga clic en **Enviar**.
3. En **Descargar el software**, haga clic en **Azure Stack Development Kit**.
4. Ejecute el archivo AzureStackDownloader.exe descargado.
5. En la ventana del **descargador de Azure Stack Development Kit**, siga los pasos 1 a 5.
6. Una vez finalizada la descarga, haga clic en **Ejecutar** para iniciar el archivo MicrosoftAzureStackPOC.exe.
7. Revise la pantalla del contrato de licencia y la información del asistente de extracción automática y, a continuación, haga clic en **Siguiente**.
8. Revise la pantalla Declaración de privacidad y la información del asistente de extracción automática y, a continuación, haga clic en **Siguiente**.
9. Seleccione el destino de los archivos que va a extraer y haga clic en **Siguiente**.
   * El valor predeterminado es: <drive letter>:\<carpeta actual > \Microsoft Azure Stack
10. Revise la pantalla de la ubicación de destino y la información del asistente de extracción automática y, después, haga clic en **Extraer** para extraer los archivos CloudBuilder.vhdx (~25 GB) y ThirdPartyLicenses.rtf files. Este proceso puede tardar un tiempo en finalizar.

> [!NOTE]
> Después de extraer los archivos, puede eliminar los archivos exe y bin para recuperar espacio en la máquina. O bien, puede mover estos archivos a otra ubicación para que si necesita volver a implementarlos, no tenga que descargarlos de nuevo.
> 
> 

## <a name="prepare-the-development-kit-host"></a>Preparación del host del kit de desarrollo
1. Asegúrese de que puede conectarse físicamente al host del kit de desarrollo, o que tiene acceso a la consola física (por ejemplo, KVM). Debe tener ese acceso después de reiniciar el host del kit de desarrollo en el paso 13 que aparece a continuación.
2. Asegúrese de que el host del kit de desarrollo cumple los [requisitos mínimos](azure-stack-deploy.md). Puede usar el [comprobador de implementaciones de Azure Stack](https://gallery.technet.microsoft.com/Deployment-Checker-for-50e0f51b) para confirmar sus requisitos.
3. Inicie sesión como administrador local en el host del kit de desarrollo.
4. Copie o mueva el archivo CloudBuilder.vhdx a la raíz de la unidad C:\ (C:\CloudBuilder.vhdx).
5. Ejecute el script siguiente para descargar el archivo instalador del kit de desarrollo (asdk-installer.ps1) en la carpeta c:\AzureStack_Installer en el host del kit de desarrollo.
    ```powershell
    # Variables
    $Uri = 'https://raw.githubusercontent.com/Azure/AzureStack-Tools/master/Deployment/asdk-installer.ps1'
    $LocalPath = 'c:\AzureStack_Installer'

    # Create folder
    New-Item $LocalPath -Type directory

    # Download file
    Invoke-WebRequest $uri -OutFile ($LocalPath + '\' + 'asdk-installer.ps1')
    ```
6. Abra una consola de PowerShell con privilegios elevados > ejecute el script C:\AzureStack_Installer\asdk-installer.ps1 > haga clic en **Preparar entorno**.
7. En la página **Select Cloudbuilder vhdx** (Seleccionar vhdx de Cloudbuilder) del instalador, busque y seleccione el archivo cloudbuilder.vhdx que ha descargado en los pasos anteriores.
8. Opcional: active la casilla **Agregar controladores** para especificar una carpeta que contenga los controladores adicionales que desea en el host.
9. En la página **Configuración opcional**, proporcione la cuenta de administrador local para el host del kit de desarrollo. Si no proporciona estas credenciales, necesitará acceso KVM al host durante el proceso de instalación que aparece a continuación.
10. También en la página **Configuración opcional**, puede configurar las siguientes opciones:
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

## <a name="deploy-the-development-kit"></a>Implementación del kit de desarrollo
1. Inicie sesión como administrador local en el host del kit de desarrollo. Use las credenciales especificadas en los pasos anteriores.

    > [!IMPORTANT]
    > Para las implementaciones de Azure Active Directory, Azure Stack necesita acceder a Internet, ya sea directamente o a través de un proxy transparente. La implementación admite exactamente una NIC para redes. Si tiene varias NIC, asegúrese de que solo una está habilitada (y todas las demás deshabilitadas) antes de ejecutar el script de implementación en la sección siguiente.
    
2. Abra una consola de PowerShell con privilegios elevados > ejecute el script \AzureStack_Installer\asdk-installer.ps1 (que puede estar en una unidad diferente en Cloudbuilder.vhdx) > haga clic en **Instalar**.
3. En el cuadro de diálogo **Tipo**, seleccione **Azure Cloud** o **ADFS**.
    - **Azure Cloud**: Azure Active Directory es el proveedor de identidades. Utilice este parámetro para especificar un directorio concreto en el que la cuenta de AAD tiene permisos de administración globales. Nombre completo de un inquilino de Azure Active Directory con el formato .onmicrosoft.com. 
    - **ADFS**: el servicio de directorio con la marca predeterminada es el proveedor de identidades, la cuenta predeterminada con la que iniciar sesión es azurestackadmin@azurestack.local y la contraseña que debe utilizar es la que proporcionó como parte de la configuración.
4. En **Contraseña del administrador local**, en el cuadro **Contraseña**, escriba la contraseña del administrador local (que debe coincidir con la configurada en la actualidad) y, a continuación, haga clic en **Siguiente**.
5. Seleccione un adaptador de red para usarlo para el kit de desarrollo y, a continuación, haga clic en **Siguiente**.
6. Seleccione DHCP o la configuración de red estática para la máquina virtual BGPNAT01.
    - **DHCP** (valor predeterminado): la máquina virtual obtiene la configuración de la red IP del servidor DHCP.
    - **Estático**: use esta opción solo si el DHCP no puede asignar una dirección IP válida de Azure Stack para acceder a Internet. Se debe especificar una dirección IP estática con la longitud de la máscara de subred (por ejemplo, 10.0.0.5/24).
7. Opcionalmente, establezca los valores siguientes:
    - **Id. de VLAN**: permite establecer el identificador de VLAN. Solo puede utilizar esta opción si el host y AzS-BGPNAT01 deben configurar el identificador de VLAN para tener acceso a la red física (e Internet). 
    - **Reenviador DNS**: se crea un servidor DNS como parte de la implementación de Azure Stack. Para permitir que los equipos dentro de la solución resuelvan nombres fuera de la marca, proporcione el servidor DNS de infraestructura existente. El servidor DNS en la marca reenvía solicitudes de resolución de nombres desconocidos a este servidor.
    - **Servidor horario**: permite establecer un servidor horario específico. 
8. Haga clic en **Siguiente**. 
9. En la página **Verifying network interface card properties** (Comprobación de las propiedades de la tarjeta de interfaz de red), verá una barra de progreso. 
    - Si dice **An update cannot be downloaded** (No se puede descargar una actualización), siga las instrucciones que aparecen en la página.
    - Cuando diga **Completado**, haga clic en **Siguiente**.
10. En la página de **resumen**, haga clic en **Implementar**.
11. Si va a usar una implementación de Azure Active Directory, se le pedirá que escriba sus credenciales de la cuenta de administrador global de Azure Active Directory.
12. El proceso de implementación puede tardar unas horas, durante las cuales el sistema se reiniciará automáticamente una vez.
   
   > [!IMPORTANT]
   > Si desea supervisar el progreso de la implementación, inicie sesión como azurestack\AzureStackAdmin. Si inicia sesión como administrador local después de que la máquina se una al dominio, no podrá ver el progreso de la implementación. No vuelva a ejecutar la implementación, en su lugar, inicie sesión como azurestack\AzureStackAdmin para validar que se está ejecutando.
   > 
   > 
   
    Si la implementación se realiza correctamente, en la consola de PowerShell aparecerá: **COMPLETE: Action ‘Deployment’** (COMPLETADA: Acción "Implementación").
   
Si se produce un error en la implementación, puede usar el siguiente script de repetición de la ejecución de PowerShell en la misma ventana de PowerShell con privilegios elevados:

```powershell
cd c:\CloudDeployment\Setup
.\InstallAzureStackPOC.ps1 -Rerun
```

Este script reiniciará la implementación desde el último paso que se realizó correctamente.

O bien, puede [volver a implementar](azure-stack-redeploy.md) desde el principio.


## <a name="reset-the-password-expiration-to-180-days"></a>Restablecimiento de la expiración de la contraseña en 180 días

Para asegurarse de que la contraseña del host del kit de desarrollo no expire demasiado pronto, siga estos pasos después de realizar la implementación:

1. En el host del kit de desarrollo, abra **Administración de directivas de grupo** y vaya a **Administración de directivas de grupo** – **Bosque: azurestack.local** – **Dominios**: **azurestack.local**.
2. Haga clic con el botón derecho en **Directiva predeterminada de dominio**  y, a continuación, haga clic en **Editar**.
3. En el Editor de administración de directivas de grupo, vaya a **Configuración del equipo** – **Directivas** – **Configuración de Windows** – **Configuración de seguridad** – **Directivas de cuenta** – **Directiva de contraseñas**.
4. En el panel derecho, haga doble clic en **Vigencia máxima de la contraseña**.
5. En el cuadro de diálogo **Maximum password age Properties** (Propiedades de Vigencia máxima de la contraseña), cambie el valor **Password will expire in** (La contraseña expirará en) a 180 y, después, haga clic en **Aceptar**.


## <a name="next-steps"></a>Pasos siguientes

[Instalación de PowerShell](azure-stack-powershell-configure-quickstart.md)

[Registro de Azure Stack con una suscripción de Azure](azure-stack-register.md)

[Conexión a Azure Stack](azure-stack-connect-azure-stack.md)

