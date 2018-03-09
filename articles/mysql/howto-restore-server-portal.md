---
title: "Restauración de un servidor en Azure Database for MySQL"
description: "En este artículo se describe cómo restaurar un servidor en Azure Database for MySQL mediante Azure Portal."
services: mysql
author: ajlam
ms.author: andrela
manager: kfile
editor: jasonwhowell
ms.service: mysql-database
ms.topic: article
ms.date: 02/28/2018
ms.openlocfilehash: 5bef3f11d0b546fbd6b1161b20d7dfb81e975f99
ms.sourcegitcommit: 782d5955e1bec50a17d9366a8e2bf583559dca9e
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 03/02/2018
---
# <a name="how-to-back-up-and-restore-a-server-in-azure-database-for-mysql-using-the-azure-portal"></a>Copia de seguridad y restauración de un servidor en Azure Database for MySQL mediante Azure Portal

## <a name="backup-happens-automatically"></a>Las copias de seguridad se realizan automáticamente
Periódicamente, se realizan copias de seguridad de los servidores de Azure Database for MySQL para habilitar las características de restauración. Con esta característica, puede restaurar el servidor y todas sus bases de datos en un servidor nuevo a un momento dado anterior.

## <a name="prerequisites"></a>requisitos previos
Para completar esta guía, necesita:
- Un [servidor de Azure Database for MySQL y una base de datos](quickstart-create-mysql-server-database-using-azure-portal.md)

## <a name="set-backup-configuration"></a>Configuración de copia de seguridad

Elija la configuración del servidor para copias de seguridad con redundancia local o con redundancia geográfica en el momento de crear el servidor, en la ventana **Plan de tarifa**.

> [!NOTE]
> Después de crear un servidor, no se puede cambiar el tipo de redundancia elegido, redundancia geográfica o redundancia local.
>

Al crear un servidor mediante Azure Portal, en la ventana **Plan de tarifa** se selecciona **Redundancia local** o **Redundancia geográfica** para las copias de seguridad del servidor. En esta ventana se selecciona también el **Período de retención de copia de seguridad**, es decir, durante cuánto tiempo se almacenarán las copias de seguridad del servidor.

   ![Plan de tarifa: elija la redundancia de las copias de seguridad](./media/howto-restore-server-portal/pricing-tier.png)

Para más información acerca de cómo establecer estos valores durante la creación, consulte la [guía de inicio rápido del servidor de Azure Database for MySQL](quickstart-create-mysql-server-database-using-azure-portal.md).

Para cambiar el período de retención de copia de seguridad en un servidor, siga estos pasos:
1. Inicie sesión en el [Portal de Azure](https://portal.azure.com/).
2. Seleccione un servidor de Azure Database for MySQL. Esta acción abre la página **Información general**.
3. Seleccione **Plan de tarifa** en el menú, en **Configuración**. Con el control deslizante puede cambiar el **Período de retención de copia de seguridad** entre 7 y 35 días.
En la captura de pantalla siguiente, se ha aumentado a 34 días.
![Aumento del período de retención de copia de seguridad](./media/howto-restore-server-portal/3-increase-backup-days.png)

4. Haga clic en **Aceptar** para confirmar el cambio.

El período de retención de copia de seguridad rige durante cuánto tiempo se puede realizar una restauración a un momento dado, porque se basa en las copias de seguridad disponibles. La restauración a un momento dado se describe con más detalle en la sección siguiente. 

## <a name="point-in-time-restore-in-the-azure-portal"></a>Restauración a un momento dado en Azure Portal
Azure Database for MySQL permite restaurar el servidor a un momento dado y en una nueva copia del servidor. Puede usar este nuevo servidor para recuperar los datos o hacer que las aplicaciones cliente apunten a este nuevo servidor.

Por ejemplo, si una tabla se quitó accidentalmente a mediodía de hoy, podría restaurar al momento justo antes de mediodía y recuperar la tabla y los datos que faltan desde esa copia nueva del servidor. La restauración a un momento dado se realiza en el nivel del servidor, no en el nivel de base de datos.

Los siguientes pasos restauran el servidor de ejemplo a un momento dado:
1. En Azure Portal, seleccione el servidor de Azure Database for MySQL. 

2. En la barra de herramientas de la página **Información general** del servidor, seleccione **Restaurar**.

   ![Azure Database for MySQL - Información general - Botón Restaurar](./media/howto-restore-server-portal/2-server.png)

3. Rellene el formulario Restaurar con la información necesaria:

   ![Azure Database for MySQL - Información sobre restauración ](./media/howto-restore-server-portal/3-restore.png)
  - **Punto de restauración**: seleccione el momento que desea restaurar.
  - **Servidor de destino**: especifique el nombre del nuevo servidor.
  - **Ubicación**: no se puede seleccionar la región. De manera predeterminada, es el mismo que el del servidor de origen.
  - **Plan de tarifa**: estos parámetros no se pueden cambiar al realizar una restauración a un momento dado. Es el mismo que el del servidor de origen. 

4. Haga clic en **Aceptar** para restaurar el servidor a un momento dado. 

5. Una vez finalizada la restauración, busque el nuevo servidor que se crea para comprobar que los datos se restauraron del modo esperado.

>[!Note]
>Tenga en cuenta que el nuevo servidor creado mediante restauración a un momento dado tiene el mismo nombre de inicio de sesión y contraseña de administrador del servidor que el servidor existente tenía en ese momento dado. Puede cambiar la contraseña en la página **Información general** del nuevo servidor.

## <a name="next-steps"></a>Pasos siguientes
- Más información acerca de las [copias de seguridad](concepts-backup.md) del servicio.
- Más información sobre las opciones de [continuidad del negocio](concepts-business-continuity.md).
