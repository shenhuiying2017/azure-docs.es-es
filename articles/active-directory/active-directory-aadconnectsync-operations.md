---
title: "Sincronización de Azure AD Connect: tareas y consideraciones operativas | Microsoft Docs"
description: "En este tema se describen las tareas operativas de la sincronización de Azure AD Connect y cómo prepararse para utilizar este componente."
services: active-directory
documentationcenter: 
author: AndKjell
manager: femila
editor: 
ms.assetid: b29c1790-37a3-470f-ab69-3cee824d220d
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/01/2016
ms.author: billmath
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: 293c84d0be4434a49aa67a66a6b109fcede9df6f


---
# <a name="azure-ad-connect-sync-operational-tasks-and-consideration"></a>Sincronización de Azure AD Connect: tareas y consideraciones operativas
El objetivo de este tema es describir las tareas operativas de la sincronización de Azure AD Connect.

## <a name="staging-mode"></a>Modo provisional
Se puede usar el modo provisional para distintos escenarios como:

* Alta disponibilidad.
* Prueba e implementación de nuevos cambios de configuración.
* Introducción de un nuevo servidor y baja del antiguo.

Con un servidor en modo provisional puede realizar cambios en la configuración y obtener una vista previa de los cambios antes de activar el servidor. También permite ejecutar una importación y sincronización completas para comprobar que se esperan todos los cambios antes de realizarlos en el entorno de producción.

Durante la instalación puede seleccionar que el servidor esté en **modo provisional**. Esta acción activará el servidor para la importación y sincronización, pero no realizará ninguna exportación. Un servidor en modo provisional no ejecutará la sincronización de contraseñas ni habilitará la escritura diferida de contraseñas, aunque se seleccionen estas características durante la instalación. Cuando se deshabilita el modo provisional, el servidor iniciará la exportación, y habilitará la sincronización de contraseñas y la escritura diferida de contraseñas.

Todavía puede forzar una exportación mediante el administrador del servicio de sincronización.

Un servidor en modo provisional seguirá recibiendo cambios de Active Directory y Azure AD. Siempre tendrá una copia de los cambios más recientes y podrá asumir muy rápidamente las responsabilidades de otro servidor. Si realiza cambios de configuración en el servidor principal, es su responsabilidad realizar los mismos cambios en el servidor o servidores en modo provisional.

Para aquellos con conocimientos de tecnologías de sincronización anteriores, el modo provisional es diferente, ya que el servidor tiene su propia base de datos SQL. Esta arquitectura permite que el servidor en modo provisional se encuentre en otro centro de datos.

### <a name="verify-the-configuration-of-a-server"></a>Comprobación de la configuración de un servidor
Para aplicar este método, siga estos pasos:

1. [Preparación](#prepare)
2. [Importación y sincronización](#import-and-synchronize)
3. [Verify](#verify)
4. [Cambio de servidor activo](#switch-active-server)

#### <a name="prepare"></a>Preparación
1. Instale Azure AD Connect, seleccione el **modo provisional** y anule la selección de **Iniciar la sincronización** en la última página del Asistente para instalación. Esto permitirá ejecutar el motor de sincronización de forma manual.
   ![ReadyToConfigure](./media/active-directory-aadconnectsync-operations/readytoconfigure.png)
2. Cierre o inicie la sesión y, en el menú de inicio, seleccione **Servicio de sincronización**.

#### <a name="import-and-synchronize"></a>Importación y sincronización
1. Seleccione **Conectores** y elija el primer conector de tipo **Active Directory Domain Services**. Haga clic en **Ejecutar** y seleccione **Importación completa** y **Aceptar**. Realice estos pasos para todos los conectores de este tipo.
2. Seleccione el conector de tipo **Azure Active Directory (Microsoft)**. Haga clic en **Ejecutar** y seleccione **Importación completa** y **Aceptar**.
3. Asegúrese de que sigue seleccionada la pestaña Conectores. Para cada conector de tipo **Active Directory Domain Services**, haga clic en **Ejecutar**, y seleccione **Sincronización diferencial** y **Aceptar**.
4. Seleccione el conector de tipo **Azure Active Directory (Microsoft)**. Haga clic en **Ejecutar**, seleccione **Sincronización diferencial** y, después, **Aceptar**.

Ahora ha almacenado provisionalmente los cambios de exportación en Azure AD y AD local (si está usando la implementación híbrida de Exchange). Los siguientes pasos le permiten inspeccionar lo que está a punto de cambiar antes de que comience realmente la exportación a los directorios.

#### <a name="verify"></a>Verify
1. Inicie un símbolo del sistema y vaya a `%ProgramFiles%\Microsoft Azure AD Sync\bin`
2. Ejecute: `csexport "Name of Connector" %temp%\export.xml /f:x`  
    El nombre del conector puede encontrarse en el Servicio de sincronización. Tendrá un nombre similar a contoso.com – AAD en Azure AD.
3. Ejecute: `CSExportAnalyzer %temp%\export.xml > %temp%\export.csv`
4. Ahora tiene un archivo en %temp% denominado "export.csv" que se puede examinar en Microsoft Excel. Este archivo contiene todos los cambios que se van a exportar.
5. Realice los cambios necesarios en los datos o la configuración y ejecute estos pasos de nuevo (Importación y sincronización y Comprobación) hasta que se esperen los cambios que se van a exportar.

**Descripción del archivo export.csv**

La mayor parte del archivo se explica por sí solo. Algunas abreviaturas para comprender el contenido:

* OMODT: tipo de modificación de objeto. Indica si la operación en un nivel de objeto es Agregar, Actualizar o Eliminar.
* AMODT: tipo de modificación de atributo. Indica si la operación en un nivel de atributo es Agregar, Actualizar o Eliminar.

Si el atributo tiene varios valores, no se mostrarán todos los cambios. Solo será visible el número de valores agregados y quitados.

#### <a name="switch-active-server"></a>Cambio de servidor activo
1. En el servidor actualmente activo, desactive el servidor (DirSync/FIM/Azure AD Sync) para que no exporte a Azure AD o establézcalo en modo provisional (Azure AD Connect).
2. Ejecute el Asistente para instalación en el servidor en **modo provisional** y deshabilite el **modo provisional**.
   ![ReadyToConfigure](./media/active-directory-aadconnectsync-operations/additionaltasks.png)

## <a name="disaster-recovery"></a>Recuperación ante desastres
Parte del diseño de implementación es planear qué hacer en caso de que se produzca un desastre en el que se pierda el servidor de sincronización. Hay diferentes modelos de uso y cuál es el que se debe usar dependerá de varios factores como los siguientes:

* ¿Cuál es su grado de tolerancia por no ser capaz de realizar cambios en objetos en Azure AD durante el tiempo de inactividad?
* Si usa la sincronización de contraseñas, ¿aceptarán los usuarios que tienen que usar la antigua contraseña en Azure AD en caso de que la cambien de forma local?
* ¿Tiene una dependencia en operaciones en tiempo real, como la escritura diferida de contraseñas?

En función de las respuestas a estas preguntas y de la directiva de su organización, puede implementar una de las estrategias siguientes:

* Recompilación si es necesario.
* Servidor en espera de reserva, conocido como **modo provisional**.
* Uso de máquinas virtuales.

Si no utiliza la base de datos de SQL Express integrada, también debe revisar la sección sobre [alta disponibilidad de SQL](#sql-high-availability) .

### <a name="rebuild-when-needed"></a>Recompilación si es necesario
Una estrategia viable es planear una recompilación del servidor si es necesario. Normalmente, la instalación del motor de sincronización y la realización de la importación y sincronización iniciales se pueden completar en unas pocas horas. Si no hay un servidor de reserva disponible, es posible usar temporalmente un controlador de dominio para hospedar el motor de sincronización.

El servidor del motor de sincronización no almacena ningún estado acerca de los objetos, por lo que se puede recompilar la base de datos a partir de los datos de Active Directory y Azure AD. El atributo **sourceAnchor** se usa para unir los objetos de local y de la nube. Si recompila el servidor con objetos existentes locales y en la nube, el motor de sincronización los hará coincidir de nuevo en la reinstalación. Las acciones que necesita documentar y guardar son los cambios de configuración realizados en el servidor, como reglas de filtrado y de sincronización. Estas configuraciones personalizadas deben volver a aplicarse antes de iniciar la sincronización.

### <a name="have-a-spare-standby-server---staging-mode"></a>Servidor en espera de reserva - modo provisional
Si tiene un entorno más complejo, se recomienda tener uno o más servidores en espera. Durante la instalación puede habilitar un servidor que esté en **modo provisional**.

Para información adicional, vea [Modo provisional](#staging-mode).

### <a name="use-virtual-machines"></a>Uso de máquinas virtuales
Un método común y admitido es ejecutar el motor de sincronización en una máquina virtual. En caso de que el host tenga un problema, la imagen con el servidor del motor de sincronización se puede migrar a otro servidor.

### <a name="sql-high-availability"></a>alta disponibilidad de SQL
Si no utiliza la versión de SQL Server Express que se incluye con Azure AD Connect, también debe tener en cuenta la alta disponibilidad para SQL Server. La única solución de alta disponibilidad admitida son clústeres de SQL. Entre las soluciones no admitidas se incluyen creación de reflejos y siempre visible.

## <a name="next-steps"></a>Pasos siguientes
**Temas de introducción**  

* [Sincronización de Azure AD Connect: comprender y personalizar la sincronización](active-directory-aadconnectsync-whatis.md)  
* [Integración de las identidades locales con Azure Active Directory](active-directory-aadconnect.md)  




<!--HONumber=Nov16_HO3-->


