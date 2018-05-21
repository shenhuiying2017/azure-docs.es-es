---
title: 'Personalización de la interfaz de usuario de la solución de supervisión remota: Azure | Microsoft Docs'
description: En este artículo se ofrece información sobre cómo se puede configurar el acceso al código fuente de la interfaz de usuario del acelerador de la solución de supervisión remota y realizar algunas personalizaciones.
services: iot-suite
suite: iot-suite
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-suite
ms.date: 01/17/2018
ms.topic: article
ms.devlang: NA
ms.tgt_pltfrm: NA
ms.workload: NA
ms.openlocfilehash: 94bd864215ad80b299db09b6237f2cebe63feb88
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/20/2018
---
# <a name="customize-the-remote-monitoring-solution-accelerator"></a>Personalización del acelerador de la solución de supervisión remota

En este artículo se proporciona información sobre cómo puede acceder al código fuente y personalizar la interfaz de usuario del acelerador de la solución de supervisión remota. El artículo describe:

## <a name="prepare-a-local-development-environment-for-the-ui"></a>Preparación de un entorno de desarrollo local para la interfaz de usuario

El código de la interfaz de usuario del acelerador de la solución de supervisión remota se implementa mediante el marco de trabajo React.js. Puede encontrar el código fuente en el repositorio de GitHub [azure-iot-pcs-remote-monitoring-webui](https://github.com/Azure/azure-iot-pcs-remote-monitoring-webui).

Para realizar cambios de la interfaz de usuario, puede realizar una copia de ella localmente. La copia local se conecta a una instancia implementada de la solución para realizar acciones como la recuperación de telemetría.

Los siguientes pasos describen el proceso para configurar un entorno local para el desarrollo de la interfaz de usuario:

1. Implemente una instancia **básica** del acelerador de la solución con la CLI **pcs**. Tome nota del nombre de la implementación y las credenciales proporcionadas para la máquina virtual. Para más información, vea [Implementación mediante la CLI](iot-accelerators-remote-monitoring-deploy-cli.md).

1. Use Azure Portal o la [CLI az](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) para habilitar el acceso SSH a la máquina virtual que hospeda los microservicios en la solución. Por ejemplo: 

    ```sh
    az network nsg rule update --name SSH --nsg-name {your solution name}-nsg --resource-group {your solution name} --access Allow
    ```

1. Use Azure Portal o la [CLI az](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) para encontrar el nombre y la dirección IP pública de la máquina virtual. Por ejemplo: 

    ```sh
    az resource list --resource-group {your solution name} -o table
    az vm list-ip-addresses --name {your vm name from previous command} --resource-group {your solution name} -o table
    ```

1. Use SSH para conectarse a la máquina virtual con la dirección IP del paso anterior y las credenciales proporcionadas cuando ejecutó **pcs** para implementar la solución.

1. Para permitir la conexión de la UX local, ejecute los siguientes comandos en el shell de bash en la máquina virtual:

    ```sh
    cd /app
    sudo ./start.sh --unsafe
    ```

1. Una vez completado el comando e iniciado el sitio web, puede desconectarse de la máquina virtual.

1. En la copia local del repositorio [azure-iot-pcs-remote-monitoring-webui](https://github.com/Azure/azure-iot-pcs-remote-monitoring-webui), edite el archivo **.env** para agregar la dirección URL de la solución implementada:

    ```config
    NODE_PATH = src/
    REACT_APP_BASE_SERVICE_URL=https://{your solution name}.azurewebsites.net/
    ```

1. En un símbolo del sistema de la copia local de la carpeta `azure-iot-pcs-remote-monitoring-webui`, ejecute los siguientes comandos para instalar las bibliotecas necesarias y ejecute la interfaz de usuario localmente:

    ```cmd/sh
    npm install
    npm start
    ```

1. El comando anterior ejecuta la interfaz de usuario localmente en http://localhost:3000/dashboard. Puede modificar el código mientras se ejecuta el sitio y ver su actualización de forma dinámica.

## <a name="customize-the-layout"></a>Personalización del diseño

Cada página de la solución de supervisión remota se compone de un conjunto de controles, que se conocen como *paneles* en el código fuente. Por ejemplo, la página **Dashboard** consta de cinco paneles: Overview (Introducción), Map (Mapa), Alarms (Alarmas), Telemetry (Telemetría) y KPIs (Indicadores clave de rendimiento). Puede encontrar el código fuente que define cada página y sus paneles en el repositorio de GitHub [pcs-remote-monitoring-webui](https://github.com/Azure/pcs-remote-monitoring-webui). Por ejemplo, el código que define la página **Dashboard**, su diseño y los paneles de la página se encuentra en la carpeta [src/components/pages/dashboard](https://github.com/Azure/pcs-remote-monitoring-webui/tree/master/src/components/pages/dashboard).

Dado que los paneles administran su propio diseño y tamaño, puede modificar con facilidad el diseño de una página. Por ejemplo, los siguientes cambios del elemento **PageContent** en el archivo `src/components/pages/dashboard/dashboard.js` intercambian las posiciones de los paneles de mapa y telemetría y cambian los anchos relativos de los paneles de mapa y KPI:

```nodejs
<PageContent className="dashboard-container" key="page-content">
  <Grid>
    <Cell className="col-1 devices-overview-cell">
      <OverviewPanel
        openWarningCount={openWarningCount}
        openCriticalCount={openCriticalCount}
        onlineDeviceCount={onlineDeviceCount}
        offlineDeviceCount={offlineDeviceCount}
        isPending={kpisIsPending || devicesIsPending}
        error={devicesError || kpisError}
        t={t} />
    </Cell>
    <Cell className="col-5">
      <TelemetryPanel
        telemetry={telemetry}
        isPending={telemetryIsPending}
        error={telemetryError}
        colors={chartColorObjects}
        t={t} />
    </Cell>
    <Cell className="col-3">
      <CustAlarmsPanel
        alarms={currentActiveAlarmsWithName}
        isPending={kpisIsPending || rulesIsPending}
        error={rulesError || kpisError}
        t={t} />
    </Cell>
    <Cell className="col-4">
    <PanelErrorBoundary msg={t('dashboard.panels.map.runtimeError')}>
        <MapPanel
          azureMapsKey={azureMapsKey}
          devices={devices}
          devicesInAlarm={devicesInAlarm}
          mapKeyIsPending={azureMapsKeyIsPending}
          isPending={devicesIsPending || kpisIsPending}
          error={azureMapsKeyError || devicesError || kpisError}
          t={t} />
      </PanelErrorBoundary>
    </Cell>
    <Cell className="col-6">
      <KpisPanel
        topAlarms={topAlarmsWithName}
        alarmsPerDeviceId={alarmsPerDeviceType}
        criticalAlarmsChange={criticalAlarmsChange}
        warningAlarmsChange={warningAlarmsChange}
        isPending={kpisIsPending || rulesIsPending || devicesIsPending}
        error={devicesError || rulesError || kpisError}
        colors={chartColorObjects}
        t={t} />
    </Cell>
  </Grid>
</PageContent>
```

![Cambio del diseño del panel](./media/iot-accelerators-remote-monitoring-customize/layout.png)

> [!NOTE]
> El mapa no está configurado en la implementación local.

También puede agregar varias instancias del mismo panel o varias versiones si [duplica y personaliza un panel](#duplicate-and-customize-an-existing-control). En el ejemplo siguiente se muestra cómo agregar dos instancias del panel de telemetría mediante la edición del archivo `src/components/pages/dashboard/dashboard.js`:

```nodejs
<PageContent className="dashboard-container" key="page-content">
  <Grid>
    <Cell className="col-1 devices-overview-cell">
      <OverviewPanel
        openWarningCount={openWarningCount}
        openCriticalCount={openCriticalCount}
        onlineDeviceCount={onlineDeviceCount}
        offlineDeviceCount={offlineDeviceCount}
        isPending={kpisIsPending || devicesIsPending}
        error={devicesError || kpisError}
        t={t} />
    </Cell>
    <Cell className="col-3">
      <TelemetryPanel
        telemetry={telemetry}
        isPending={telemetryIsPending}
        error={telemetryError}
        colors={chartColorObjects}
        t={t} />
    </Cell>
    <Cell className="col-3">
      <TelemetryPanel
        telemetry={telemetry}
        isPending={telemetryIsPending}
        error={telemetryError}
        colors={chartColorObjects}
        t={t} />
    </Cell>
    <Cell className="col-2">
      <CustAlarmsPanel
        alarms={currentActiveAlarmsWithName}
        isPending={kpisIsPending || rulesIsPending}
        error={rulesError || kpisError}
        t={t} />
    </Cell>
    <Cell className="col-4">
    <PanelErrorBoundary msg={t('dashboard.panels.map.runtimeError')}>
        <MapPanel
          azureMapsKey={azureMapsKey}
          devices={devices}
          devicesInAlarm={devicesInAlarm}
          mapKeyIsPending={azureMapsKeyIsPending}
          isPending={devicesIsPending || kpisIsPending}
          error={azureMapsKeyError || devicesError || kpisError}
          t={t} />
      </PanelErrorBoundary>
    </Cell>
    <Cell className="col-6">
      <KpisPanel
        topAlarms={topAlarmsWithName}
        alarmsPerDeviceId={alarmsPerDeviceType}
        criticalAlarmsChange={criticalAlarmsChange}
        warningAlarmsChange={warningAlarmsChange}
        isPending={kpisIsPending || rulesIsPending || devicesIsPending}
        error={devicesError || rulesError || kpisError}
        colors={chartColorObjects}
        t={t} />
    </Cell>
  </Grid>
</PageContent>
```

Puede ver distintas telemetrías en cada panel:

![Varios paneles de telemetría](./media/iot-accelerators-remote-monitoring-customize/multiple-telemetry.png)

> [!NOTE]
> El mapa no está configurado en la implementación local.

## <a name="duplicate-and-customize-an-existing-control"></a>Duplicación y personalización de un control existente

En los pasos siguientes se describe cómo usar el panel **alarms** (alarmas) como ejemplo de cómo duplicar una panel existente, modificarlo y usar la versión modificada:

1. En la copia local del repositorio, realice una copia de la carpeta **alarms** (alarmas) en la carpeta `src/components/pages/dashboard/panels`. Asigne a la nueva copia el nombre **cust_alarms**.

1. En el archivo **alarmsPanel.js** de la carpeta **cust_alarms**, edite el nombre de la clase como **CustAlarmsPanel**:

    ```nodejs
    export class CustAlarmsPanel extends Component {
    ```

1. Agregue la siguiente línea al archivo `src/components/pages/dashboard/panels/index.js` :

    ```nodejs
    export * from './cust_alarms';
    ```

1. Reemplace `AlarmsPanel` con `CustAlarmsPanel` en el archivo `src/components/pages/dashboard/dashboard.js`:

    ```nodejs
    import {
      OverviewPanel,
      CustAlarmsPanel,
      TelemetryPanel,
      KpisPanel,
      MapPanel,
      transformTelemetryResponse,
      chartColors
    } from './panels';

    ...

    <Cell className="col-3">
      <CustAlarmsPanel
        alarms={currentActiveAlarmsWithName}
        isPending={kpisIsPending || rulesIsPending}
        error={rulesError || kpisError}
        t={t} />
    </Cell>
    ```

Ya ha reemplazado el panel **Alarms** (Alarmas) original con una copia llamada **CustAlarms**. Esta copia es idéntica al original. Ahora puede modificar la copia. Por ejemplo, para cambiar el orden de las columnas en el panel **Alarms** (Alarmas):

1. Abra el archivo `src/components/pages/dashboard/panels/cust_alarms/alarmsPanel.js` .

1. Modifique las definiciones de columna tal y como se muestra en el siguiente fragmento de código:

    ```nodejs
    this.columnDefs = [
      rulesColumnDefs.severity,
      {
        headerName: 'rules.grid.count',
        field: 'count'
      },
      {
        ...rulesColumnDefs.ruleName,
        minWidth: 200
      },
      rulesColumnDefs.explore
    ];
    ```

En la siguiente captura de pantalla se muestra la nueva versión del panel **Alarms** (Alarmas):

![Panel de alarmas actualizado](./media/iot-accelerators-remote-monitoring-customize/reorder-columns.png)

## <a name="customize-the-telemetry-chart"></a>Personalización del gráfico de telemetría

El gráfico de telemetría de la página **Dashboard** (Panel) se define mediante los archivos de la carpeta `src/components/pages/dashboard/panels/telemtry`. La interfaz de usuario recupera la telemetría del back-end de la solución del archivo `src/services/telemetryService.js`. Los pasos siguientes describen cómo cambiar el período de tiempo mostrado en el gráfico de telemetría de 15 minutos a 5 minutos:

1. En el archivo `src/services/telemetryService.js`, localice la función llamada **getTelemetryByDeviceIdP15M**. Realice una copia de esta función y modifique la copia de la manera siguiente:

    ```nodejs
    static getTelemetryByDeviceIdP5M(devices = []) {
      return TelemetryService.getTelemetryByMessages({
        from: 'NOW-PT5M',
        to: 'NOW',
        order: 'desc',
        devices
      });
    }
    ```

1. Para usar esta nueva función para rellenar el gráfico de telemetría, abra el archivo `src/components/pages/dashboard/dashboard.js`. Busque la línea que inicializa el flujo de telemetría y modifíquela como se indica a continuación:

    ```node.js
    const getTelemetryStream = ({ deviceIds = [] }) => TelemetryService.getTelemetryByDeviceIdP5M(deviceIds)
    ```

El gráfico de telemetría ahora muestra los cinco minutos de datos de telemetría:

![Gráfico de telemetría que muestra un día](./media/iot-accelerators-remote-monitoring-customize/telemetry-period.png)

## <a name="add-a-new-kpi"></a>Adición de un nuevo KPI

En la página **Dashboard** (Panel) se muestran los KPI en el panel **System KPIs** (KPI del sistema). Estos KPI se calculan en el archivo `src/components/pages/dashboard/dashboard.js`. Los KPI se representan mediante el archivo `src/components/pages/dashboard/panels/kpis/kpisPanel.js`. Los siguientes pasos describen cómo calcular y representar un nuevo valor de KPI en la página **Dashboard** (Panel). El ejemplo mostrado refleja cómo agregar un nuevo cambio de porcentaje en el KPI de alarmas de advertencia:

1. Abra el archivo `src/components/pages/dashboard/dashboard.js` . Modifique el objeto **initialState** para incluir una propiedad **warningAlarmsChange** como sigue:

    ```nodejs
    const initialState = {
      ...

      // Kpis data
      currentActiveAlarms: [],
      topAlarms: [],
      alarmsPerDeviceId: {},
      criticalAlarmsChange: 0,
      warningAlarmsChange: 0,
      kpisIsPending: true,
      kpisError: null,

      ...
    };
    ```

1. Modifique el objeto **currentAlarmsStats** para incluir **totalWarningCount** como una propiedad:

    ```nodejs
    return {
      openWarningCount: (acc.openWarningCount || 0) + (isWarning && isOpen ? 1 : 0),
      openCriticalCount: (acc.openCriticalCount || 0) + (isCritical && isOpen ? 1 : 0),
      totalWarningCount: (acc.totalWarningCount || 0) + (isWarning ? 1 : 0),
      totalCriticalCount: (acc.totalCriticalCount || 0) + (isCritical ? 1 : 0),
      alarmsPerDeviceId: updatedAlarmsPerDeviceId
    };
    ```

1. Calcule el nuevo KPI. Busque el cálculo del número de alarmas críticas. Duplique el código y modifique la copia como sigue:

    ```nodejs
    // ================== Warning Alarms Count - START
    const currentWarningAlarms = currentAlarmsStats.totalWarningCount;
    const previousWarningAlarms = previousAlarms.reduce(
      (cnt, { severity }) => severity === 'warning' ? cnt + 1 : cnt,
      0
    );
    const warningAlarmsChange = ((currentWarningAlarms - previousWarningAlarms) / currentWarningAlarms * 100).toFixed(2);
    // ================== Warning Alarms Count - END
    ```

1. Incluya el nuevo KPI **warningAlarmsChange** en el flujo de KPI:

    ```nodejs
    return ({
      kpisIsPending: false,

      // Kpis data
      currentActiveAlarms,
      topAlarms,
      criticalAlarmsChange,
      warningAlarmsChange,
      alarmsPerDeviceId: currentAlarmsStats.alarmsPerDeviceId,

      ...
    });

1. Include the new **warningAlarmsChange** KPI in the state data used to render the UI:

    ```nodejs
    const {
      ...

      currentActiveAlarms,
      topAlarms,
      alarmsPerDeviceId,
      criticalAlarmsChange,
      warningAlarmsChange,
      kpisIsPending,
      kpisError,

      ...
    } = this.state;
    ```

1. Actualice los datos pasados al panel de KPI:

    ```node.js
    <KpisPanel
      topAlarms={topAlarmsWithName}
      alarmsPerDeviceId={alarmsPerDeviceType}
      criticalAlarmsChange={criticalAlarmsChange}
      warningAlarmsChange={warningAlarmsChange}
      isPending={kpisIsPending || rulesIsPending || devicesIsPending}
      error={devicesError || rulesError || kpisError}
      colors={chartColorObjects}
      t={t} />
    ```

Ya ha terminado de realizar los cambios en el archivo `src/components/pages/dashboard/dashboard.js`. Los siguientes pasos describen los cambios realizados en el archivo `src/components/pages/dashboard/panels/kpis/kpisPanel.js` para mostrar el nuevo KPI:

1. Modifique la siguiente línea de código para recuperar los nuevos valores de KPI como sigue:

    ```nodejs
    const { t, isPending, criticalAlarmsChange, warningAlarmsChange, error } = this.props;
    ```

1. Modifique el incremento para mostrar el nuevo valor de KPI como sigue:

    ```nodejs
    <div className="kpi-cell">
      <div className="kpi-header">{t('dashboard.panels.kpis.criticalAlarms')}</div>
      <div className="critical-alarms">
        {
          criticalAlarmsChange !== 0 &&
            <div className="kpi-percentage-container">
              <div className="kpi-value">{ criticalAlarmsChange }</div>
              <div className="kpi-percentage-sign">%</div>
            </div>
        }
      </div>
      <div className="kpi-header">{t('Warning alarms')}</div>
      <div className="critical-alarms">
        {
          warningAlarmsChange !== 0 &&
            <div className="kpi-percentage-container">
              <div className="kpi-value">{ warningAlarmsChange }</div>
              <div className="kpi-percentage-sign">%</div>
            </div>
        }
      </div>
    </div>
    ```

La página **Dashboard** (Panel) ahora muestra el nuevo valor de KPI:

![KPI de advertencia](./media/iot-accelerators-remote-monitoring-customize/new-kpi.png)

## <a name="customize-the-map"></a>Personalización del mapa

Consulte la página de [personalización del mapa](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/wiki/Developer-Reference-Guide#upgrade-map-key-to-see-devices-on-a-dynamic-map) en GitHub para detalles de los componentes del mapa en la solución.

<!--
### Connect an external visualization tool

See the [Connect an external visualization tool](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/) page in GitHub for details of how to connect an external visualization tool.

-->

## <a name="other-customization-options"></a>Otras opciones de personalización

Para modificar aún más la capa de presentación y visualizaciones en la solución de supervisión remota, puede editar el código. Los repositorios GitHub pertinentes son:

* [Microservicio de configuración de soluciones de Azure IoT (.NET)](https://github.com/Azure/pcs-ui-config-dotnet/)
* [Microservicio de configuración de soluciones de Azure IoT (Java)](https://github.com/Azure/pcs-ui-config-java/)
* [Interfaz de usuario web de supervisión remota de Azure IoT PCS](https://github.com/Azure/pcs-remote-monitoring-webui)

## <a name="next-steps"></a>Pasos siguientes

En este artículo, aprendió sobre los recursos que tiene a su disposición para ayudarlo a personalizar la interfaz de usuario web en el acelerador de la solución de supervisión remota.

Para información más conceptual sobre el acelerador de la solución de supervisión remota, vea [Arquitectura de supervisión remota](iot-accelerators-remote-monitoring-sample-walkthrough.md).

Para más información sobre cómo personalizar la solución de supervisión remota, consulte [Personalización y nueva implementación de un microservicio](iot-accelerators-microservices-example.md).
<!-- Next tutorials in the sequence -->