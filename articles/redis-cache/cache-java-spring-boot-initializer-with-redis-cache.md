---
title: "Configuración de una aplicación Spring Boot Initializer para usar Redis Cache"
description: "Aprenda a configurar una aplicación creada con Spring Boot Initializer para usar Azure Redis Cache."
services: redis-cache
documentationcenter: java
author: rmcmurray
manager: cfowler
editor: 
keywords: Spring, Spring Boot Starter, Redis Cache
ms.assetid: 
ms.service: cache
ms.workload: tbd
ms.tgt_pltfrm: cache-redis
ms.devlang: java
ms.topic: article
ms.date: 7/21/2017
ms.author: robmcm;zhijzhao;yidon
ms.translationtype: HT
ms.sourcegitcommit: 137671152878e6e1ee5ba398dd5267feefc435b7
ms.openlocfilehash: ea85a9cfe7079ade33a437987798a165a056dc02
ms.contentlocale: es-es
ms.lasthandoff: 07/28/2017

---

# <a name="how-to-configure-a-spring-boot-initializer-app-to-use-redis-cache"></a>Configuración de una aplicación Spring Boot Initializer para usar Redis Cache

## <a name="overview"></a>Información general

**[Spring Framework]** es una solución de código abierto que ayuda a los desarrolladores de Java a crear aplicaciones de nivel empresarial. Uno de los proyectos más populares que se basa en dicha plataforma es [Spring Boot], que proporciona un enfoque simplificado para crear aplicaciones de Java independientes. Para ayudar a los desarrolladores a empezar con Spring Boot, puede encontrar varios paquetes de ejemplo de Spring Boot en <https://github.com/spring-guides/>. Además de elegir de la lista de proyectos básicos de Spring Boot, el **[Spring Initializr]** ayuda a los desarrolladores en los primeros pasos para crear aplicaciones de Spring Boot personalizadas.

Este artículo le ayudará a crear una memoria caché en Redis mediante Azure Portal, después a utilizar **Spring Initializr** para crear una aplicación personalizada y, por último, a crear una aplicación web de Java que almacena y recupera datos mediante la memoria caché de Redis.

## <a name="prerequisites"></a>Requisitos previos

Los siguientes requisitos previos son necesarios para seguir los pasos descritos en este artículo:

* Una suscripción de Azure. Si todavía no la tiene, puede activar sus [ventajas como suscriptor de MSDN] o registrarse para obtener una [cuenta de Azure gratuita].

* Un [kit de desarrollo de Java (JDK)](http://www.oracle.com/technetwork/java/javase/downloads/), versión 1.7 o posterior.

* [Apache Maven](http://maven.apache.org/), versión 3.0 o posterior.

## <a name="create-a-redis-cache-on-azure"></a>Crear una caché de Redis en Azure

1. Vaya a Azure Portal en <https://portal.azure.com/> y haga clic en el elemento **+Nuevo**.

   ![Portal de Azure][AZ01]

1. Haga clic en **Base de datos** y luego en **Redis Cache**.

   ![Portal de Azure][AZ02]

1. En la hoja **Nueva caché en Redis**, escriba un nombre para la memoria caché en **Nombre DNS** y luego especifique la información correspondiente en **Suscripción**, **Grupo de recursos**, **Ubicación** y **Plan de tarifa**. Cuando haya especificado estas opciones, haga clic en **Crear** para crear la memoria caché.

   ![Portal de Azure][AZ03]

1. Una vez completada la memoria caché, verá que aparece en su **Panel** de Azure, así como en las hojas **Todos los recursos** y **Cachés en Redis**. Puede hacer clic en la memoria caché en cualquiera de esas ubicaciones para abrir la hoja de propiedades de dicha memoria caché.

   ![Portal de Azure][AZ04]

1. Cuando se muestre la hoja que contiene la lista de propiedades de la memoria caché, haga clic en **Claves de acceso** y copie las claves de acceso de la memoria caché.

   ![Portal de Azure][AZ05]

## <a name="create-a-custom-application-using-the-spring-initializr"></a>Creación de una aplicación personalizada mediante Spring Initializr

1. Vaya a <https://start.spring.io/>.

1. Especifique que quiere generar un proyecto de **Maven** con **Java**, escriba los nombres de **Group** (Grupo) y **Artifact** (Artefacto) de su aplicación y luego haga clic en el vínculo **Switch to the full version** (Cambiar a la versión completa) de Spring Initializr.

   ![Opciones básicas de Spring Initializr][SI01]

   > [!NOTE]
   >
   > Spring Initializr usará los nombres de **Group** (Grupo) y **Artifact** (Artefacto) para crear el nombre del paquete, por ejemplo: *com.contoso.myazuredemo*.
   >

1. Desplácese hacia abajo hasta la sección **Web** y active la casilla **Web**, luego desplácese hacia abajo hasta la sección **NoSQL** y active la casilla **Redis**, después desplácese hasta la parte inferior de la página y haga clic en el botón **Generate Project** (Generar proyecto).

   ![Opciones completas de Spring Initializr][SI02]

1. Cuando se le solicite, descargue el proyecto en una ruta de acceso del equipo local.

   ![Descarga del proyecto personalizado de Spring Boot][SI03]

1. Después de extraer los archivos en el sistema local, la aplicación personalizada de Spring Boot estará lista para editarla.

   ![Archivos de proyecto personalizados de Spring Boot][SI04]

## <a name="configure-your-custom-spring-boot-to-use-your-redis-cache"></a>Configuración de su Spring Boot personalizada para usar Redis Cache

1. Busque el archivo *application.properties* en el directorio *resources* de la aplicación, o cree el archivo si todavía no existe.

   ![Localización del archivo application.properties][RE01]

1. Abra el archivo *application.properties* en un editor de texto y agréguele las siguientes líneas; luego, sustituya los valores de ejemplo por las propiedades adecuadas de la memoria caché:

   ```yaml
   # Specify the DNS URI of your Redis cache.
   spring.redisHost=myspringbootcache.redis.cache.windows.net

   # Specify the access key for your Redis cache.
   spring.redisPassword=447564652c20426f6220526f636b7321
   ```

   ![Edición del archivo application.properties][RE02]

1. Guarde y cierre el archivo *application.properties*.

1. Cree una carpeta denominada *controller* en la carpeta de origen para el paquete; por ejemplo:

   `C:\SpringBoot\myazuredemo\src\main\java\com\contoso\myazuredemo\controller`

   O bien

   `/users/example/home/myazuredemo/src/main/java/com/contoso/myazuredemo/controller`

1. Cree un archivo denominado *HelloController.java* en la carpeta *controller* que acaba de crear y agregue el siguiente código en él:

   ```java
   package com.contoso.myazuredemo;

   import org.springframework.web.bind.annotation.RequestMapping;
   import org.springframework.web.bind.annotation.RestController;
   import org.springframework.beans.factory.annotation.Value;
   import redis.clients.jedis.Jedis;
   import redis.clients.jedis.JedisShardInfo;

   @RestController
   public class HelloController {
   
      // Retrieve the DNS name for your cache.
      @Value("${spring.redisHost}")
      private String redisHost;

      // Retrieve the access key for your cache.
      @Value("${spring.redisPassword}")
      private String redisPassword;

      @RequestMapping("/")
      // Define the Hello World controller.
      public String hello() {
      
         // Create a JedisShardInfo object to connect to your Redis cache.
         JedisShardInfo jedisShardInfo = new JedisShardInfo(redisHost, 6380, true);
         // Specify your access key.
         jedisShardInfo.setPassword(redisPassword);
         // Create a Jedis object to store/retrieve information from your cache.
         Jedis jedis = new Jedis(jedisShardInfo);

         // Add a Hello World string to your cache.
         jedis.set("greeting", "Hello World!");

         // Return the string from your cache.
         return jedis.get("greeting");
      }
   }
   ```
   
   Necesitará reemplazar `com.contoso.myazuredemo` con el nombre del paquete para el proyecto.

1. Guarde y cierre el archivo *HelloController.java*.

1. Compile la aplicación de Spring Boot con Maven y ejecútela; por ejemplo:

   ```shell
   mvn package
   java -jar target/myazuredemo-0.0.1-SNAPSHOT.jar
   ```

1. Compruebe la aplicación web. Para ello, vaya a http://localhost:8080 con un explorador web, o bien utilice una sintaxis como la del siguiente ejemplo si tiene cURL disponible:

   ```shell
   curl http://localhost:8080
   ```

   Debe ver el mensaje "Hola mundo" del controlador de ejemplo mostrado, que se está recuperando dinámicamente de la memoria caché en Redis.

## <a name="next-steps"></a>Pasos siguientes

Para obtener más información sobre el uso de aplicaciones de Spring Boot en Azure, consulte los siguientes artículos:

* [Implementación de una aplicación de Spring Boot en Azure App Service](../app-service/app-service-deploy-spring-boot-web-app-on-azure.md)

* [Ejecución de una aplicación de Spring Boot en un clúster de Kubernetes en Azure Container Service](../container-service/container-service-deploy-spring-boot-app-on-kubernetes.md)

Para obtener más información sobre el uso de Azure con Java, vea el [Centro para desarrolladores de Java de Azure] y [Java Tools for Visual Studio Team Services] (Herramientas de Java para Visual Studio Team Services).

Para más información sobre la introducción al uso de Redis Cache con Java en Azure, consulte [Uso de Azure Redis Cache con Java][Redis Cache with Java].

<!-- URL List -->

[Centro para desarrolladores de Java de Azure]: https://azure.microsoft.com/develop/java/
[cuenta de Azure gratuita]: https://azure.microsoft.com/pricing/free-trial/
[Java Tools for Visual Studio Team Services]: https://java.visualstudio.com/
[ventajas como suscriptor de MSDN]: https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/
[Spring Boot]: http://projects.spring.io/spring-boot/
[Spring Initializr]: https://start.spring.io/
[Spring Framework]: https://spring.io/
[Redis Cache with Java]: cache-java-get-started.md

<!-- IMG List -->

[AZ01]: ./media/cache-java-spring-boot-initializer-with-redis-cache/AZ01.png
[AZ02]: ./media/cache-java-spring-boot-initializer-with-redis-cache/AZ02.png
[AZ03]: ./media/cache-java-spring-boot-initializer-with-redis-cache/AZ03.png
[AZ04]: ./media/cache-java-spring-boot-initializer-with-redis-cache/AZ04.png
[AZ05]: ./media/cache-java-spring-boot-initializer-with-redis-cache/AZ05.png

[SI01]: ./media/cache-java-spring-boot-initializer-with-redis-cache/SI01.png
[SI02]: ./media/cache-java-spring-boot-initializer-with-redis-cache/SI02.png
[SI03]: ./media/cache-java-spring-boot-initializer-with-redis-cache/SI03.png
[SI04]: ./media/cache-java-spring-boot-initializer-with-redis-cache/SI04.png

[RE01]: ./media/cache-java-spring-boot-initializer-with-redis-cache/RE01.png
[RE02]: ./media/cache-java-spring-boot-initializer-with-redis-cache/RE02.png

