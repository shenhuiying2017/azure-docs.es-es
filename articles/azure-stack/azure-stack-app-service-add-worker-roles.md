---
title: 'Escalado horizontal de roles de trabajo de App Services: Azure Stack | Microsoft Docs'
description: Instrucciones detalladas de escalado de App Services en Azure Stack
services: azure-stack
documentationcenter: 
author: kathm
manager: slinehan
editor: anwestg
ms.assetid: 3cbe87bd-8ae2-47dc-a367-51e67ed4b3c0
ms.service: azure-stack
ms.workload: app-service
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 4/6/2017
ms.author: kathm
ms.translationtype: HT
ms.sourcegitcommit: 988e7fe2ae9f837b661b0c11cf30a90644085e16
ms.openlocfilehash: f844658c6ad2529fd385476be63095bdae7c88e5
ms.contentlocale: es-es
ms.lasthandoff: 09/15/2017

---
# <a name="app-service-on-azure-stack-adding-more-worker-roles"></a>App Service en Azure Stack: incorporación de más roles de trabajo 

Este documento proporciona instrucciones acerca de cómo escalar App Service en roles de trabajo de Azure Stack. Contiene los pasos para crear roles de trabajo adicionales para admitir aplicaciones de cualquier tamaño.

> [!NOTE]
> Si su entorno de prueba de concepto de Azure Stack no tiene más de 96 GB de RAM, puede encontrar dificultades al agregar capacidad adicional.

App Service en Azure Stack, de forma predeterminada, es compatible con los niveles de trabajo gratuito y compartido. Para agregar otros niveles de trabajo, debe agregar más roles de trabajo.

Si no está seguro de lo que se implementó con la instalación predeterminada de App Service en Azure Stack, en [App Service on Azure Stack overview](azure-stack-app-service-overview.md) (Introducción a App Service en Azure Stack) encontrará más detalles.

Hay dos maneras de agregar capacidad para App Service en Azure Stack:
1.  [Incorporación de roles de trabajo adicionales directamente desde el administrador del proveedor de recursos de App Service](#Add-additional-workers-directly-from-within-the-App-Service-Resource-Provider-Admin).
2.  [Creación de máquinas virtuales manualmente y su incorporación al proveedor de recursos de App Service](#Create-additional-VMs-manually-and-add-them-to-the-App-Service-Resource-Provider).

## <a name="add-additional-workers-directly-within-the-app-service-resource-provider-admin"></a>Incorporación de roles de trabajo adicionales directamente desde el administrador del proveedor de recursos de App Service.

1.  Inicie sesión en Azure Stack Portal como administrador de servicios.
2.  Vaya a **Proveedores de recursos** y seleccione **App Service Resource Provider Admin** (Administración de proveedores de recursos de App Service). ![Proveedores de recursos de Azure Stack][1]
3.  Haga clic en **Roles**.  Aquí verá el desglose de los roles de App Service implementados.
4.  Haga clic en la opción **Nueva instancia de rol** ![Incorporación de una nueva instancia de rol][2]
5.  En la hoja **Nueva instancia de rol**:
    1. Elija las **instancias de rol** adicionales que quiera agregar.  En la versión preliminar hay un máximo de 10.
    2. Seleccione el **tipo de rol**.  En esta versión preliminar, esta opción se limita a rol de trabajo web.
    3. Seleccione el **nivel de trabajo** donde quiera implementar este rol de trabajo, las opciones predeterminadas son Pequeño, Medio, Grande o Compartido.  Si ha creado sus propios niveles de trabajo, también estarán disponibles para la selección.
    4. Haga clic en **Aceptar** para implementar los roles de trabajo adicionales.
6.  App Service en Azure Stack se Ahora incorporará las máquinas virtuales adicionales, las configurará, instalará el software necesario y las marcará como listas cuando finalice el proceso,  que puede tardar unos 80 minutos.
7.  Puede supervisar el progreso de la disponibilidad de los roles de trabajo nuevos desde la hoja **Roles**.

>[!NOTE]
>  En esta versión preliminar, el flujo de la nueva instancia de rol integrada se limita a los roles de trabajo e implementa únicamente máquinas virtuales de tamaño A1.  Esta funcionalidad se ampliará en una versión futura.

## <a name="manually-adding-additional-capacity-to-app-service-on-azure-stack"></a>Suma manual de capacidad para App Service en Azure Stack

Para agregar roles adicionales, se necesitan los pasos siguientes:

1. [Creación de una máquina virtual](#step-1-create-a-new-vm-to-support-the-new-instance-size)
2. [Configuración de la máquina virtual](#step-2-configure-the-virtual-machine)
3. [Configuración del rol de trabajo web en Azure Stack Portal](#step-3-configure-the-web-worker-role-in-the-azure-stack-portal)
4. [Configuración de los planes de App Service](#step-4-configure-app-service-plans)

## <a name="step-1-create-a-new-vm-to-support-the-new-instance-size"></a>Paso 1: Creación de una máquina virtual para admitir el nuevo tamaño de instancia
Cree una máquina virtual como se describe en [este artículo](azure-stack-provision-vm.md), asegurándose de que se realizan las selecciones siguientes:

* Nombre de usuario y contraseña: proporcione los mismos nombre de usuario y contraseña que especificó al instalar App Service en Azure Stack.
* Suscripción: use la suscripción de proveedor predeterminada.
* Grupo de recursos: elija **AppService-LOCAL**.

> [!NOTE]
> Almacene las máquinas virtuales de los roles de trabajo en el mismo grupo de recursos donde se haya implementado App Service en Azure Stack (recomendación para esta versión).
> 
> 

## <a name="step-2-configure-the-virtual-machine"></a>Paso 2: Configuración de la máquina virtual
Una vez completada la implementación, la configuración siguiente es necesaria para admitir el rol de trabajo web:

1. Vaya al grupo de recursos **AppService-LOCAL** del portal y seleccione la máquina que creó en el paso 1.
2. Haga clic en Conectar en la hoja de la máquina virtual para descargar el perfil de escritorio remoto.  Abra el perfil para abrir una sesión de escritorio remoto en la máquina virtual.
3. Inicie sesión en la máquina virtual con el nombre de usuario y la contraseña que especificó en el paso 1.
4. Haga clic en el botón **Iniciar** y escriba PowerShell para abrir PowerShell. Haga clic en **PowerShell.exe** y seleccione **Ejecutar como administrador** para abrir PowerShell en modo de administrador.
5. Copie y pegue los siguientes comandos (de uno en uno) en la ventana de PowerShell y presione Entrar:
   
   ```netsh advfirewall firewall set rule group="File and Printer Sharing" new enable=Yes```
   ```netsh advfirewall firewall set rule group="Windows Management Instrumentation (WMI)" new enable=yes```
   ```reg add HKLM\\SOFTWARE\\Microsoft\\Windows\\CurrentVersion\\Policies\\system /v LocalAccountTokenFilterPolicy /t REG\_DWORD /d 1 /f```
   
6. Cierre la sesión de escritorio remoto.
7. Reinicie la máquina virtual desde el portal.

> [!NOTE]
> Estos son los requisitos mínimos para App Service en Azure Stack. Son los valores predeterminados de la imagen de Windows 2012 R2 que se incluye en Azure Stack. Las instrucciones se han proporcionado como referencia y para los que usen una imagen diferente.
> 
> 

## <a name="step-3-configure-the-worker-role-in-the-azure-stack-portal"></a>Paso 3: Configuración del rol de trabajo en Azure Stack Portal
1. Abra el portal como administrador de servicios en la **máquina virtual de cliente**.
2. Vaya a **Resource Providers** &gt; **App Service Resource Provider Admin** (Proveedores de recursos > Administrador de proveedores de recursos de App Service).![Administrador de proveedores de recursos de App Service][3]
3. En la hoja de configuración, haga clic en **Roles**.![App Service Resource Provider Roles][4] (Roles > Roles de proveedor de recursos de App Service)
4. Haga clic en **Agregar instancia de rol**.
5. En el cuadro de texto **Nombre del servidor**, escriba la **dirección IP** del servidor que creó anteriormente (en la sección 1).
6. Seleccione el **Tipo de rol** que quiera agregar: Controlador, Servidor de administración, Front-end, Trabajo web, Publicador o Servidor de archivos.  En este caso, seleccione Trabajo web.
7. Haga clic en el **nivel** donde quiera implementar la nueva instancia (Pequeño, Medio, Grande o Compartido).  Si ha creado sus propios niveles de trabajo, también estarán disponibles para la selección.
8. Haga clic en **Aceptar**.
9. Vuelva a la vista **Roles**.
10. Haga clic en la fila correspondiente a la combinación de tipo de rol y nivel de trabajo que asignó a la máquina virtual.
11. Busque el nombre del servidor que acaba de agregar. Revise la columna Estado y espere para ir al paso siguiente hasta que el estado sea "Listo", esto puede tardar unos 80 minutos. ![Rol de proveedor de recursos de App Service listo][5]

## <a name="step-4-configure-app-service-plans"></a>Paso 4: Configuración de los planes de App Service

1. Inicie sesión en el portal en la máquina virtual de cliente.
2. Vaya a **Nuevo** &gt; **Web and Mobile** (Web y móvil).
3. Seleccione el tipo de aplicación que desea implementar.
4. Proporcione la información de la aplicación y seleccione el **plan y la ubicación de App Service**.
    1. Haga clic en **Create New**.
    2. Cree el plan; para ello, seleccione el nivel de precios correspondiente.

> [!NOTE]
> Puede crear varios planes en esta hoja. Sin embargo, antes de la implementación, asegúrese de que ha seleccionado el plan adecuado.
> 
> 

A continuación se muestra un ejemplo de los distintos niveles de precios predeterminados disponibles.  Observe que si no hay ningún rol de trabajo disponible para un nivel de trabajo concreto, la opción de elegir el nivel de precios correspondiente tampoco lo estará.![Planes de tarifa predeterminados para App Service en Azure Stack][6]

<!--Image references-->
[1]: ./media/azure-stack-app-service-add-worker-roles/azure-stack-resource-providers.png
[2]: ./media/azure-stack-app-service-add-worker-roles/app-service-new-role-instance.png
[3]: ./media/azure-stack-app-service-add-worker-roles/app-service-resource-provider-admin.png
[4]: ./media/azure-stack-app-service-add-worker-roles/app-service-resource-provider-roles.png
[5]: ./media/azure-stack-app-service-add-worker-roles/app-service-resource-provider-role-ready.png
[6]: ./media/azure-stack-app-service-add-worker-roles/app-service-resource-provider-pricing-tier.png

