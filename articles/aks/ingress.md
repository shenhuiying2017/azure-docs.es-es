---
title: "Configuración de la entrada con un clúster de Azure Container Service (AKS)"
description: "Instale y configure un controlador de entrada NGINX en un clúster de Azure Container Service (AKS)."
services: container-service
author: neilpeterson
manager: timlt
ms.service: container-service
ms.topic: article
ms.date: 2/21/2018
ms.author: nepeters
ms.custom: mvc
ms.openlocfilehash: c25a0171bd412050a7c94e9b077436cd1ebe893b
ms.sourcegitcommit: 782d5955e1bec50a17d9366a8e2bf583559dca9e
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 03/02/2018
---
# <a name="https-ingress-on-azure-container-service-aks"></a>Entrada HTTPS en Azure Container Service (AKS)

Un controlador de entrada es un software que proporciona el proxy inverso, el enrutamiento del tráfico configurable y la terminación de TLS para los servicios de Kubernetes. Los recursos de entrada de Kubernetes se usan para configurar las reglas de entrada y las rutas de los distintos servicios de Kubernetes. Mediante reglas de entrada y un controlador de entrada, se puede enrutar el tráfico a varios servicios de Kubernetes de un clúster de Kubernetes con una sola dirección externa.

Este documento le guiará con la implementación de ejemplo del [controlador de entrada NGINX][nginx-ingress] en un clúster de Azure Container Service (AKS). Además, con el proyecto [KUBE-LEGO][kube-lego] se generan y se configuran automáticamente certificados de [Let's Encrypt][lets-encrypt]. Por último, en el clúster de AKS se ejecutan varias aplicaciones, a las cuales se puede acceder con una sola dirección.

## <a name="install-an-ingress-controller"></a>Instalación de un controlador de entrada

Instale el controlador de entrada NGINX con Helm. Consulte la [documentación][nginx-ingress] del controlador de entrada NGINX para información de implementación detallada. 

```
helm install stable/nginx-ingress
```

Durante la instalación se crea una dirección IP pública de Azure para el controlador de entrada. Obtenga la dirección IP pública con el comando de servicio get kubectl. La asignación de la dirección IP al servicio puede tardar un tiempo.

```console
$ kubectl get service

NAME                                          TYPE           CLUSTER-IP     EXTERNAL-IP      PORT(S)                      AGE
kubernetes                                    ClusterIP      10.0.0.1       <none>           443/TCP                      3d
toned-spaniel-nginx-ingress-controller        LoadBalancer   10.0.236.223   52.224.125.195   80:30927/TCP,443:31144/TCP   18m
toned-spaniel-nginx-ingress-default-backend   ClusterIP      10.0.5.86      <none>           80/TCP                       18m
```

Dado que no se han creado reglas de entrada, si llega hasta la dirección IP pública, se le dirigirá a la página 404 predeterminada de los controladores de entrada NGINX.

![Back-end predeterminado de NGINX](media/ingress/default-back-end.png)

## <a name="configure-dns-name"></a>Configuración del nombre DNS

Dado que se usan certificados HTTPS, debe configurar un nombre de dominio completo para la dirección IP de los controladores de entrada. En este ejemplo, se crea un nombre de dominio completo de Azure con la CLI de Azure. Actualice el script con la dirección IP del controlador de entrada y el nombre de dominio completo que desee.

```
#!/bin/bash

# Public IP address
IP="52.224.125.195"

# Name to associate with public IP address
DNSNAME="demo-aks-ingress"

# Get resource group and public ip name
RESOURCEGROUP=$(az network public-ip list --query "[?ipAddress!=null]|[?contains(ipAddress, '$IP')].[resourceGroup]" --output tsv)
PIPNAME=$(az network public-ip list --query "[?ipAddress!=null]|[?contains(ipAddress, '$IP')].[name]" --output tsv)

# Update public ip address with dns name
az network public-ip update --resource-group $RESOURCEGROUP --name  $PIPNAME --dns-name $DNSNAME
```

Si es necesario, ejecute el siguiente comando para recuperar el nombre de dominio completo. Actualice el valor de dirección IP con el del controlador de entrada.

```azurecli
az network public-ip list --query "[?ipAddress!=null]|[?contains(ipAddress, '52.224.125.195')].[dnsSettings.fqdn]" --output tsv
```

El controlador de entrada ahora es accesible mediante el nombre de dominio completo.

## <a name="install-kube-lego"></a>Instalación de KUBE-LEGO

El controlador de entrada NGINX es compatible con la terminación de TLS. Aunque hay varias maneras de recuperar y configurar los certificados para HTTPS, en este documento se muestra el uso de [KUBE-LEGO][kube-lego], que proporciona una funcionalidad de generación y administración de certificados [Let's Encrypt][lets-encrypt] automática.

Para instalar el controlador de KUBE-LEGO, use el siguiente comando de instalación de Helm. Actualice la dirección de correo electrónico con una de su organización.

```
helm install stable/kube-lego \
  --set config.LEGO_EMAIL=user@contoso.com \
  --set config.LEGO_URL=https://acme-v01.api.letsencrypt.org/directory
```

Para más información sobre la configuración de KUBE-LEGO, consulte la [documentación de KUBE-LEGO][kube-lego].

## <a name="run-application"></a>Ejecución de la aplicación

Hasta este momento se han configurado un controlador de entrada y una solución de administración de certificados. Ahora ejecute algunas aplicaciones en el clúster de AKS.

En este ejemplo, se ejecutan varias instancias de una sencilla aplicación Hola mundo con Helm.

Antes de ejecutar la aplicación, agregue el repositorio de ejemplos de Helm de Azure al sistema de desarrollo.

```
helm repo add azure-samples https://azure-samples.github.io/helm-charts/
```

 Ejecute el gráfico de Hola mundo de AKS con el siguiente comando:

```
helm install azure-samples/aks-helloworld
```

A continuación, instale una segunda instancia de la aplicación Hola mundo.

Para esta segunda instancia, especifique un nuevo título de manera que las dos aplicaciones se distingan visualmente. También debe especificar un nombre de servicio único. Estas configuraciones se pueden ver en el siguiente comando.

```console
helm install azure-samples/aks-helloworld --set title="AKS Ingress Demo" --set serviceName="ingress-demo"
```

## <a name="create-ingress-route"></a>Creación de la ruta de entrada

Ambas aplicaciones se ejecutan ahora en el clúster de Kubernetes, sin embargo, se han configurado con un servicio de tipo `ClusterIP`. Por lo tanto, no se puede acceder a ellas desde internet. Para que estén disponibles, cree un recurso de entrada de Kubernetes. El recurso de entrada configura las reglas de enrutamiento del tráfico a una de las dos aplicaciones.

Cree un archivo denominado `hello-world-ingress.yaml` y cópielo en el siguiente código YAML.

Tenga en cuenta que el tráfico a la dirección `https://demo-aks-ingress.eastus.cloudapp.azure.com/` se enruta al servicio denominado `aks-helloworld`. El tráfico a la dirección `https://demo-aks-ingress.eastus.cloudapp.azure.com/hello-world-two` se enruta al servicio `ingress-demo`.

```
apiVersion: extensions/v1beta1
kind: Ingress
metadata:
  name: hello-world-ingress
  annotations:
    kubernetes.io/tls-acme: "true"
    ingress.kubernetes.io/rewrite-target: /
spec:
  tls:
  - hosts:
    - demo-aks-ingress.eastus.cloudapp.azure.com
    secretName: tls-secret
  rules:
  - host: demo-aks-ingress.eastus.cloudapp.azure.com
    http:
      paths:
      - path: /
        backend:
          serviceName: aks-helloworld
          servicePort: 80
      - path: /hello-world-two
        backend:
          serviceName: ingress-demo
          servicePort: 80
```

Cree el recurso de entrada con el comando `kubectl apply`.

```console
kubectl apply -f hello-world-ingress.yaml
```

## <a name="test-the-ingress-configuration"></a>Prueba de la configuración de entrada

Busque el nombre de dominio completo del controlador de entrada de Kubernetes para ver la aplicación Hola mundo.

![Primer ejemplo de aplicación](media/ingress/app-one.png)

Ahora busque el nombre de dominio completo del controlador de entrada con la ruta de acceso `/hello-world-two`, verá la aplicación Hola mundo con el título personalizado.

![Segundo ejemplo de aplicación](media/ingress/app-two.png)

Observe que la conexión está cifrada y que se utiliza un certificado emitido por Let's Encrypt.

![Certificado de Let's Encrypt](media/ingress/certificate.png)

## <a name="next-steps"></a>Pasos siguientes

Más información sobre el software que se muestra en este documento. 

- [Controlador de entrada NGINX][nginx-ingress]
- [KUBE-LEGO][kube-lego]

<!-- LINKS - external -->
[kube-lego]: https://github.com/jetstack/kube-lego
[lets-encrypt]: https://letsencrypt.org/
[nginx-ingress]: https://github.com/kubernetes/ingress-nginx
