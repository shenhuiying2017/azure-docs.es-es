---
title: "Características de seguridad para proteger copias de seguridad híbridas mediante Azure Backup | Microsoft Docs"
description: "Aprenda a usar las características de seguridad de Azure Backup para que las copias de seguridad sean más seguras"
services: backup
documentationcenter: 
author: JPallavi
manager: vijayts
editor: 
ms.assetid: 47bc8423-0a08-4191-826d-3f52de0b4cb8
ms.service: backup
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/24/2016
ms.author: pajosh
translationtype: Human Translation
ms.sourcegitcommit: 9b3686a5dfb4fa2cc1f5711830f90fc44b036673
ms.openlocfilehash: bd8e7ea5663df4c75e8c1a549b5b8d0207770c93


---
# <a name="security-features-for-protecting-hybrid-backups-using-azure-backup"></a>Características de seguridad para proteger copias de seguridad híbridas mediante Azure Backup
Cada vez más clientes tienen problemas de seguridad como malware, ransomware, intrusión etc. Estos problemas de seguridad provocan la pérdida de datos y el costo por infracción de seguridad no para de aumentar. Para protegerse contra dichos ataques, Azure Backup proporciona características de seguridad que protegen las copias de seguridad híbridas. Este artículo trata sobre cómo habilitar y sacar provecho de estas características mediante el agente de Microsoft Azure Recovery Services y el servidor de copia de seguridad de Microsoft Azure. Estas características se han basado en tres pilares de seguridad:

1. **Prevención**: se agrega una capa adicional de autenticación cada vez que se realiza una operación crítica, como cambiar la frase de contraseña. Esta validación se realiza para asegurarse de que dichas operaciones solo pueden realizarlas usuarios que tengan credenciales de Azure válidas. 
2. **Alerta**: se envía una notificación por correo electrónico al administrador de suscripciones cada vez que se realiza una operación crítica, como eliminar datos de copia de seguridad. Este correo electrónico garantiza que el usuario recibe una notificación oportunamente acerca de dichas acciones.
3. **Recuperación**: los datos de copia de seguridad eliminados se conservan durante 14 días a partir de la fecha de eliminación. Esto garantiza la capacidad de recuperación de los datos en un período dado, con el fin de que no haya pérdida de datos, aunque se produzca un ataque. Además, se mantiene el mayor número de puntos de recuperación mínimos para protegerse contra datos dañados. 

> [!NOTE]
> Las características de seguridad solo se deben habilitar si se usa: <br/>
> * **Agente de MAB**: la versión mínima del agente es la 2.0.9052. Una vez que estas características estén habilitadas, debe realizar la actualización a esta versión del agente para realizar operaciones críticas, como cambiar la frase de contraseña i detener la copia de seguridad con eliminación de datos <br/>
> * **Servidor de copia de seguridad de Azure**: la versión mínima del agente de MAB es la 2.0.9052 con la actualización 1 del servidor de copia de seguridad de Azure <br/>
> * **DPM**: no habilite estas características para DPM. Estas características se incorporarán en futuros paquetes acumulativos de actualizaciones, por lo que su habilitación actual no afectará a la funcionalidad existente. <br/>
> * **Copia de seguridad de VM de IaaS**: no habilite estas características en la copia de seguridad de VM de IaaS. Estas características no están aún disponibles para la copia de seguridad de VM de IaaS, por lo que su habilitación no afectará a la copia de seguridad de VM de IaaS.
> * Una vez habilitadas, obtendrá las características de seguridad de todos los equipos del agente de Azure Recovery Services (MARS) y servidores de copia de seguridad de Azure registrados en el almacén. <br/>
> * La habilitación a esta configuración es una acción única, por lo que una vez que se habiliten estas características no será posible deshabilitarlas. <br/>
> * Esta característica solo está disponible para el almacén de Recovery Services.
> 
> 

## <a name="enabling-security-features"></a>Habilitación de características de seguridad
Los usuarios que crean un almacén de Recovery Services podrán hacer uso de todas las características de seguridad. En el caso de un almacén de Recovery Services existente, se deben usar los pasos siguientes para habilitar estas características:

1. Inicie sesión en Azure Portal con las credenciales de Azure
2. Escriba Recovery Services en el menú del concentrador para navegar a la lista de Recovery Services. 
   
    ![Creación del almacén de Servicios de recuperación, paso 1](./media/backup-azure-security-feature/browse-to-rs-vaults.png) <br/>
   
    Aparece la lista de almacenes de Servicios de recuperación. Seleccione un almacén en ella. 
   
    Se abre el panel del almacén seleccionado.
3. En la lista de elementos que aparece en el almacén, haga clic en **Propiedades** en **Configuración**.
   
    ![Abrir las propiedades del almacén](./media/backup-azure-security-feature/vault-list-properties.png)
4. Haga clic en **Actualizar** en **Configuración de seguridad**.
   
    ![Abrir configuración de seguridad](./media/backup-azure-security-feature/security-settings-update.png)
   
    El vínculo de actualización abre la hoja Configuración de seguridad, donde puede habilitar estas características y ver el resumen de la característica.
5. Seleccione un valor en el menú desplegable **Have you configured Azure Multi-Factor Authentication?** (¿Ha configurado Azure Multi-Factor Authentication?) para confirmar si ha habilitado [Azure Multi-Factor Authentication](../multi-factor-authentication/multi-factor-authentication.md). Si está habilitado, se le pedirá que realice la autenticación desde otro dispositivo (por ejemplo, un teléfono móvil) al iniciar sesión en Azure Portal. 
   
   Como parte de las características de seguridad, cuando se realizan operaciones críticas en Azure Backup, es preciso escribir el PIN de seguridad que se encuentra en Azure Portal. La habilitación de Azure Multi-Factor Authentication agrega una capa de seguridad, lo que garantiza que solo los usuarios autorizados con credenciales válidas de Azure y autenticados desde un segundo dispositivos pueden acceder a Azure Portal y realizar dichas operaciones críticas.
6. Utilice el botón de alternancia para realizar la **habilitación** y haga clic en el botón **Guardar** de la parte superior para guardar la configuración de seguridad, como se muestra en la ilustración. **Habilitar** solo se puede seleccionar después de seleccionar un valor en la lista desplegable "Have you configured Azure Multi-Factor Authentication?" (¿Ha configurado Azure Multi-Factor Authentication?).
   
    ![Habilitación de configuración de seguridad](./media/backup-azure-security-feature/enable-security-settings.png)

## <a name="recovering-deleted-backup-data"></a>Recuperación de datos de copia de seguridad eliminados
Como medida de seguridad, Azure Backup conserva los datos de copia de seguridad eliminados durante 14 días adicionales y no los elimina inmediatamente si se realiza la operación de detener copia de seguridad con la eliminación de datos de copia de seguridad. Para restaurar estos datos en el período de 14 días, siga estos pasos:

Para los usuarios del **agente de Microsoft Recovery Services (MARS)**:

1. Si la máquina en la que se realizaron las copias de seguridad aún está disponible, use [Recover data to the same machine](backup-azure-restore-windows-server.md#recover-data-to-the-same-machine) (Recuperar los datos en el mismo equipo) en MARS para realizar la recuperación desde todos los puntos de recuperación antiguos.
2. Si el equipo que se ha mencionado anteriormente no está disponible, utilice [Recover data to the same machine](backup-azure-restore-windows-server.md#recover-to-an-alternate-machine) (Recuperar los datos en otro equipo) para usar otro equipo MARS para obtener estos datos.

Para los usuarios del **servidor de copia de seguridad de Azure**:

1. Si el servidor en que se realizan las copias de seguridad está aún disponible, vuelva a proteger los orígenes de datos eliminados y use la característica Recuperar datos para realizar la recuperación desde todos los puntos de recuperación antiguos.
2. Si el equipo que se ha mencionado anteriormente no está disponible, utilice [Recover data from another Azure Backup Server](backup-azure-alternate-dpm-server.md#recover-data-from-another-azure-backup-server) (Recuperar datos de otro servidor de copia de seguridad de Azure) para usar otro servidor de copia de seguridad de Azure para obtener estos datos.

## <a name="preventing-attacks"></a>Prevención de ataques
Como parte de esta característica, se han agregado comprobaciones para asegurarse de que los usuarios válidos son los únicos que pueden realizar varias operaciones.

### <a name="authentication-to-perform-critical-operations"></a>Autenticación para realizar operaciones críticas
Como parte de la adición de una capa adicional de autenticación para las operaciones críticas, se le solicita que escriba el PIN de seguridad al realizar las operaciones Detener la protección con eliminación de datos y Cambio de la frase de contraseña. 

Para recibir el PIN de seguridad, siga estos pasos:

1. Inicie sesión en Azure Portal.
2. Navegue hasta el almacén del servicio de recuperación > Configuración > Propiedades.
3. Haga clic en **Generar** en PIN de seguridad. La generación del vínculo abre una hoja, que contiene el PIN de seguridad que se va a escribir en la interfaz de usuario del agente de Azure Recovery Services. 
    Este PIN solo es válido durante 5 minutos y se genera automáticamente después de ese período.

### <a name="maintaining-minimum-retention-range"></a>Mantenimiento del intervalo de retención mínimo
Para asegurarse de que siempre hay un número válido de puntos de recuperación disponibles, se han agregado las siguientes comprobaciones:

1. Para la retención diaria, se deben realizar un mínimo de **siete** días de retención
2. Para la retención semanal, se deben realizar un mínimo de **cuatro** semanas de retención
3. Para la retención mensual, se deben realizar un mínimo de **tres** meses de retención
4. Para la retención anual, se debe realizar un mínimo de **un** año de retención

## <a name="notifications-for-critical-operations"></a>Notificaciones de operaciones críticas
Cada vez que se realizan algunas operaciones críticas, se envía una notificación por correo electrónico al administrador de suscripciones con detalles sobre la operación. Si desea configurar los identificadores de correo electrónico adicionales para recibir notificaciones por correo electrónico, puede usar Azure Portal para configurarlos.

Las características de seguridad que se mencionan en este artículo proporcionan mecanismos de defensa contra ataques dirigidos impide que los atacantes toquen las copias de seguridad. Y lo que es más importante, estas características proporcionan la capacidad de recuperar datos si se produce un ataque.

## <a name="next-steps"></a>Pasos siguientes
* [Comience a usar el almacén de Azure Recovery Services](backup-azure-vms-first-look-arm.md) para habilitar estas características
* [Descargue la versión más reciente del agente de Azure Recovery Services](http://aka.ms/azurebackup_agent) para proteger los equipos con Windows y los datos de copia de seguridad frente a ataques
* [Descargue la versión más reciente del servidor de copia de seguridad de Azure](https://aka.ms/latest_azurebackupserver) para proteger las cargas de trabajo y los datos de copia de seguridad frente a ataques



<!--HONumber=Nov16_HO4-->


