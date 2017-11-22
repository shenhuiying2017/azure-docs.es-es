---
title: "Tareas posteriores a la implementación de OpenShift en Azure | Documentos de Microsoft"
description: "Tareas adicionales posteriores a la implementación de un clúster de OpenShift."
services: virtual-machines-linux
documentationcenter: virtual-machines
author: haroldw
manager: najoshi
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 
ms.author: haroldw
ms.openlocfilehash: 77c4719b5cee7f5736d73ee10cf6abf12229ea11
ms.sourcegitcommit: 6a22af82b88674cd029387f6cedf0fb9f8830afd
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/11/2017
---
# <a name="post-deployment-tasks"></a>Tareas posteriores a la implementación

Después de implementar un clúster de OpenShift, puede configurar elementos adicionales. En este artículo se trata lo siguiente:

- Cómo configurar el inicio de sesión único con Azure Active Directory (Azure AD)
- Cómo configurar Operations Management Suite para supervisar OpenShift
- Cómo configurar las métricas y el registro

## <a name="configure-single-sign-on-by-using-azure-active-directory"></a>Configurar el inicio de sesión único con Azure Active Directory

Para usar Azure Active Directory para la autenticación, primero debe crear un registro de la aplicación de Azure AD. Este proceso implica dos pasos: crear el registro de la aplicación y configurar los permisos.

### <a name="create-an-app-registration"></a>Crear un registro de aplicación

Estos pasos usan la CLI de Azure para crear el registro de aplicación y la GUI (portal) para definir los permisos. Para crear el registro de aplicación, se necesitan los cinco datos siguientes:

- Nombre para mostrar: nombre del registro de aplicación (p. ej.: OCPAzureAD)
- Página principal: dirección URL de la consola OpenShift (p. ej.: https://masterdns343khhde.westus.cloudapp.azure.com:8443/console)
- URI del identificador: dirección URL de la consola OpenShift (p. ej.: https://masterdns343khhde.westus.cloudapp.azure.com:8443/console)
- URL de respuesta: dirección URL pública maestra y nombre del registro de aplicación (p. ej: https://masterdns343khhde.westus.cloudapp.azure.com:8443/oauth2callback/OCPAzureAD)
- Contraseña: contraseña segura (use una contraseña segura)

En el ejemplo siguiente, se creará un registro de aplicación con la información anterior:

```azurecli
az ad app create --display-name OCPAzureAD --homepage https://masterdns343khhde.westus.cloudapp.azure.com:8443/console --reply-urls https://masterdns343khhde.westus.cloudapp.azure.com:8443/oauth2callback/hwocpadint --identifier-uris https://masterdns343khhde.westus.cloudapp.azure.com:8443/console --password {Strong Password}
```

Si el comando se ejecuta correctamente, recibirá una salida JSON similar a la siguiente:

```json
{
  "appId": "12345678-ca3c-427b-9a04-ab12345cd678",
  "appPermissions": null,
  "availableToOtherTenants": false,
  "displayName": "OCPAzureAD",
  "homepage": "https://masterdns343khhde.westus.cloudapp.azure.com:8443/console",
  "identifierUris": [
    "https://masterdns343khhde.westus.cloudapp.azure.com:8443/console"
  ],
  "objectId": "62cd74c9-42bb-4b9f-b2b5-b6ee88991c80",
  "objectType": "Application",
  "replyUrls": [
    "https://masterdns343khhde.westus.cloudapp.azure.com:8443/oauth2callback/OCPAzureAD"
  ]
}
```

Tome nota de la propiedad appId que ha devuelto el comando para otro paso.

En Azure Portal:

1.  Seleccione **Azure Active Directory** > **Registro de aplicaciones**.
2.  Busque el registro de su aplicación (p. ej.: OCPAzureAD).
3.  En los resultados, haga clic en el registro de aplicación.
4.  En **Configuración**, seleccione **Permisos necesarios**.
5.  En **Permisos necesarios**, seleccione **Agregar**.

  ![Registro de aplicaciones](media/openshift-post-deployment/app-registration.png)

6.  Haga clic en el paso 1 (Seleccionar API) y, después, en **Windows Azure Active Directory (Microsoft.Azure.ActiveDirectory)**. Haga clic en **Seleccionar** en la parte inferior.

  ![Selección de API del registro de aplicaciones](media/openshift-post-deployment/app-registration-select-api.png)

7.  En el paso 2: Seleccionar permisos, seleccione **Iniciar sesión y leer el perfil del usuario**, en **Permisos delegados**, y haga clic en **Seleccionar**.

  ![Acceso al registro de aplicaciones](media/openshift-post-deployment/app-registration-access.png)

8.  Seleccione **Listo**.

### <a name="configure-openshift-for-azure-ad-authentication"></a>Configuración de OpenShift para la autenticación de Azure AD

Para configurar OpenShift para usar Azure AD como proveedor de autenticación, el archivo /etc/origin/master/master-config.yaml se debe editar en todos los nodos maestros.

Busque el identificador de inquilino mediante el siguiente comando de CLI:

```azurecli
az account show
```

En el archivo yaml, busque las siguientes líneas:

```yaml
oauthConfig:
  assetPublicURL: https://masterdns343khhde.westus.cloudapp.azure.com:8443/console/
  grantConfig:
    method: auto
  identityProviders:
  - challenge: true
    login: true
    mappingMethod: claim
    name: htpasswd_auth
    provider:
      apiVersion: v1
      file: /etc/origin/master/htpasswd
      kind: HTPasswdPasswordIdentityProvider
```

Inserte las líneas siguientes inmediatamente después de las líneas anteriores:

```yaml
  - name: <App Registration Name>
    challenge: false
    login: true
    mappingMethod: claim
    provider:
      apiVersion: v1
      kind: OpenIDIdentityProvider
      clientID: <appId>
      clientSecret: <Strong Password>
      claims:
        id:
        - sub
        preferredUsername:
        - unique_name
        name:
        - name
        email:
        - email
      urls:
        authorize: https://login.microsoftonline.com/<tenant Id>/oauth2/authorize
        token: https://login.microsoftonline.com/<tenant Id>/oauth2/token
```

Busque el identificador de inquilino mediante el siguiente comando de CLI: ```az account show```.

Reinicie los servicios maestros de OpenShift en todos los nodos maestros:

**OpenShift Origin**

```bash
sudo systemctl restart origin-master-api
sudo systemctl restart origin-master-controllers
```

**OpenShift Container Platform (OCP) con varios maestros**

```bash
sudo systemctl restart atomic-openshift-master-api
sudo systemctl restart atomic-openshift-master-controllers
```

**OpenShift Container Platform con un solo maestro**

```bash
sudo systemctl restart atomic-openshift-master
```

En la consola de OpenShift, ahora verá dos opciones para la autenticación: htpasswd_auth y [App Registration].

## <a name="monitor-openshift-with-operations-management-suite"></a>Supervisión de OpenShift con Operations Management Suite

Existen dos opciones para supervisar OpenShift con Operations Management Suite: instalación de Agente de OMS en host de VM o contenedor de OMS. En este artículo se proporcionan instrucciones para implementar el contenedor de OMS.

## <a name="create-an-openshift-project-for-operations-management-suite-and-set-user-access"></a>Crear un proyecto de OpenShift para Operations Management Suite y definir el acceso de usuario

```bash
oadm new-project omslogging --node-selector='zone=default'
oc project omslogging
oc create serviceaccount omsagent
oadm policy add-cluster-role-to-user cluster-reader system:serviceaccount:omslogging:omsagent
oadm policy add-scc-to-user privileged system:serviceaccount:omslogging:omsagent
```

## <a name="create-a-daemon-set-yaml-file"></a>Crear un archivo yaml de conjunto de demonios

Cree un archivo denominado ocp-omsagent.yml:

```yaml
apiVersion: extensions/v1beta1
kind: DaemonSet
metadata:
  name: oms
spec:
  selector:
    matchLabels:
      name: omsagent
  template:
    metadata:
      labels:
        name: omsagent
        agentVersion: 1.4.0-45
        dockerProviderVersion: 10.0.0-25
    spec:
      nodeSelector:
        zone: default
      serviceAccount: omsagent
      containers:
      - image: "microsoft/oms"
        imagePullPolicy: Always
        name: omsagent
        securityContext:
          privileged: true
        ports:
        - containerPort: 25225
          protocol: TCP
        - containerPort: 25224
          protocol: UDP
        volumeMounts:
        - mountPath: /var/run/docker.sock
          name: docker-sock
        - mountPath: /etc/omsagent-secret
          name: omsagent-secret
          readOnly: true
        livenessProbe:
          exec:
            command:
              - /bin/bash
              - -c
              - ps -ef | grep omsagent | grep -v "grep"
          initialDelaySeconds: 60
          periodSeconds: 60
      volumes:
      - name: docker-sock
        hostPath:
          path: /var/run/docker.sock
      - name: omsagent-secret
        secret:
         secretName: omsagent-secret
````

## <a name="create-a-secret-yaml-file"></a>Crear un archivo yaml secreto

Para crear el archivo yaml secreto, se necesitan dos datos: el identificador del área de trabajo de OMS y la clave compartida del área de trabajo de OMS. 

A continuación se incluye un archivo ocp-secret.yml de ejemplo: 

```yaml
apiVersion: v1
kind: Secret
metadata:
  name: omsagent-secret
data:
  WSID: wsid_data
  KEY: key_data
```

Reemplace wsid_data con el identificador de área de trabajo de OMS codificado en Base64. Después, reemplace key_data con la clave compartida del área de trabajo de OMS codificada en Base64.

```bash
wsid_data='11111111-abcd-1111-abcd-111111111111'
key_data='My Strong Password'
echo $wsid_data | base64 | tr -d '\n'
echo $key_data | base64 | tr -d '\n'
```

## <a name="create-the-secret-and-daemon-set"></a>Crear el secreto y conjunto de demonios

Implementar el archivo secreto:

```bash
oc create -f ocp-secret.yml
```

Implementar el conjunto de demonios del Agente de OMS:

```bash
oc create -f ocp-omsagent.yml
```

## <a name="configure-metrics-and-logging"></a>Configurar las métricas y el registro

La plantilla de Azure Resource Manager para OpenShift Container Platform proporciona parámetros de entrada para habilitar las métricas y el registro. La oferta de Marketplace de OpenShift Container Platform y la plantilla de Resource Manager de OpenShift Origin no lo hacen.

Si se usó la plantilla de Resource Manager de OCP, y las métricas y el registro no estaban habilitados durante la instalación; o si se usó la oferta de Marketplace de OCP, se pueden habilitar fácilmente en otro momento. Si se usa la plantilla de Resource Manager de OpenShift Origin, se necesita cierto trabajo previo.

### <a name="openshift-origin-template-pre-work"></a>Trabajo previo de la plantilla de OpenShift Origin

1. SSH para el primer nodo maestro que usa el puerto 2200.

   Ejemplo:

   ```bash
   ssh -p 2200 clusteradmin@masterdnsixpdkehd3h.eastus.cloudapp.azure.com 
   ```

2. Edite el archivo /etc/ansible/hosts y agregue las líneas siguientes después de la sección de proveedor de identidad (# Enable HTPasswdPasswordIdentityProvider):

   ```yaml
   # Setup metrics
   openshift_hosted_metrics_deploy=false
   openshift_metrics_cassandra_storage_type=dynamic
   openshift_metrics_start_cluster=true
   openshift_metrics_hawkular_nodeselector={"type":"infra"}
   openshift_metrics_cassandra_nodeselector={"type":"infra"}
   openshift_metrics_heapster_nodeselector={"type":"infra"}
   openshift_hosted_metrics_public_url=https://metrics.$ROUTING/hawkular/metrics

   # Setup logging
   openshift_hosted_logging_deploy=false
   openshift_hosted_logging_storage_kind=dynamic
   openshift_logging_fluentd_nodeselector={"logging":"true"}
   openshift_logging_es_nodeselector={"type":"infra"}
   openshift_logging_kibana_nodeselector={"type":"infra"}
   openshift_logging_curator_nodeselector={"type":"infra"}
   openshift_master_logging_public_url=https://kibana.$ROUTING
   ```

3. Reemplace $ROUTING con la cadena usada para la opción openshift_master_default_subdomain en el mismo archivo /etc/ansible/hosts.

### <a name="azure-cloud-provider-in-use"></a>Proveedor de soluciones en la nube de Azure en uso

En el primer nodo maestro (Origin) o nodo de bastión (OCP), use SSH con las credenciales proporcionadas durante la implementación. Ejecute el siguiente comando:

```bash
ansible-playbook /usr/share/ansible/openshift-ansible/playbooks/byo/openshift-cluster/openshift-metrics.yml \
-e openshift_metrics_install_metrics=True \
-e openshift_metrics_cassandra_storage_type=dynamic

ansible-playbook /usr/share/ansible/openshift-ansible/playbooks/byo/openshift-cluster/openshift-logging.yml \
-e openshift_logging_install_logging=True \
-e openshift_hosted_logging_storage_kind=dynamic
```

### <a name="azure-cloud-provider-not-in-use"></a>Proveedor de soluciones en la nube de Azure que no está en uso

En el primer nodo maestro (Origin) o nodo de bastión (OCP), use SSH con las credenciales proporcionadas durante la implementación. Ejecute el siguiente comando:

```bash
ansible-playbook /usr/share/ansible/openshift-ansible/playbooks/byo/openshift-cluster/openshift-metrics.yml \
-e openshift_metrics_install_metrics=True 

ansible-playbook /usr/share/ansible/openshift-ansible/playbooks/byo/openshift-cluster/openshift-logging.yml \
-e openshift_logging_install_logging=True 
```

## <a name="next-steps"></a>Pasos siguientes

- [Introducción a OpenShift Container Platform](https://docs.openshift.com/container-platform/3.6/getting_started/index.html)
- [Introducción a OpenShift Origin](https://docs.openshift.org/latest/getting_started/index.html)
