---
title: "Envío de eventos a Azure Event Hubs mediante C | Microsoft Docs"
description: "Envío de eventos a Azure Event Hubs mediante C"
services: event-hubs
documentationcenter: 
author: jtaubensee
manager: timlt
editor: 
ms.assetid: 
ms.service: event-hubs
ms.workload: na
ms.tgt_pltfrm: c
ms.devlang: csharp
ms.topic: article
ms.date: 01/30/2017
ms.author: jotaub;sethm
translationtype: Human Translation
ms.sourcegitcommit: f92909e0098a543f99baf3df3197a799bc9f1edc
ms.openlocfilehash: f62c0ca57bfd15a9ad1f767fa8fd59cc73b71c43
ms.lasthandoff: 03/01/2017

---

# <a name="send-events-to-azure-event-hubs-using-c"></a>Envío de eventos a Azure Event Hubs mediante C

## <a name="introduction"></a>Introducción
Centros de eventos es un sistema de recopilación de alta escalabilidad que puede recibir millones de eventos por segundo, habilitando una aplicación para procesar y analizar las grandes cantidades de datos generados por las aplicaciones y los dispositivos conectados. Una vez recopilados en los Centros de eventos, puede transformar y almacenar los datos usando cualquier proveedor de análisis en tiempo real o clúster de almacenamiento.

Para obtener más información, consulte [Información general de los Centros de eventos de Azure][Event Hubs overview].

En este tutorial, aprenderá a enviar eventos a un centro de eventos mediante una aplicación de consola en C. Para recibir eventos, haga clic en el idioma de recepción adecuado en la tabla de contenido izquierda.

Para completar este tutorial, necesitará lo siguiente:

* Un entorno de desarrollo de C. Para este tutorial, consideraremos la pila de gcc en una VM Linux de Azure con Ubuntu 14.04.
* Microsoft Visual Studio o Visual Studio Community Edition
* Una cuenta de Azure activa. En caso de no tener ninguna, puede crear una cuenta de evaluación gratuita en tan solo unos minutos. Para obtener más información, consulte [Evaluación gratuita de Azure](https://azure.microsoft.com/pricing/free-trial/).

## <a name="send-messages-to-event-hubs"></a>Envío de mensajes a Centros de eventos
En esta sección se escribirá una aplicación C para enviar eventos al Centro de eventos. Usaremos la biblioteca Proton AMQP del [proyecto Apache Qpid](http://qpid.apache.org/). Esto es parecido a usar temas y colas de Bus de servicio con AMQP a través de C como se muestra [aquí](https://code.msdn.microsoft.com/Using-Apache-Qpid-Proton-C-afd76504). Para más información, vea la [documentación de Qpid Proton](http://qpid.apache.org/proton/index.html).

1. En la página [Qpid AMQP Messenger](http://qpid.apache.org/components/messenger/index.html), haga clic en el vínculo **Instalación de Qpid Proton** y siga las instrucciones dependiendo de su entorno.
2. Para compilar la biblioteca Proton, instale los paquetes siguientes:
   
    ```shell
    sudo apt-get install build-essential cmake uuid-dev openssl libssl-dev
    ```
3. Descargue la [biblioteca de Qpid Proton](http://qpid.apache.org/proton/index.html) y extráigala; por ejemplo:
   
    ```shell
    wget http://archive.apache.org/dist/qpid/proton/0.7/qpid-proton-0.7.tar.gz
    tar xvfz qpid-proton-0.7.tar.gz
    ```
4. Cree un directorio de compilación, compile y realice la instalación:
   
    ```shell
    cd qpid-proton-0.7
    mkdir build
    cd build
    cmake -DCMAKE_INSTALL_PREFIX=/usr ..
    sudo make install
    ```
5. En su directorio de trabajo, cree un nuevo archivo denominado **sender.c** con el siguiente contenido. No olvide sustituir el valor para el nombre del Centro de eventos y el espacio de nombres (este último suele ser `{event hub name}-ns`). También debe sustituir una versión con codificación URL de la clave para la regla **SendRule** creada anteriormente. Puede codificar con URL [aquí](http://www.w3schools.com/tags/ref_urlencode.asp).
   
    ```c
    #include "proton/message.h"
    #include "proton/messenger.h"
   
    #include <getopt.h>
    #include <proton/util.h>
    #include <sys/time.h>
    #include <stddef.h>
    #include <stdio.h>
    #include <string.h>
    #include <unistd.h>
    #include <stdlib.h>
   
    #define check(messenger)                                                     
      {                                                                          
        if(pn_messenger_errno(messenger))                                        
        {                                                                        
          printf("check\n");                                                     
          die(__FILE__, __LINE__, pn_error_text(pn_messenger_error(messenger))); 
        }                                                                        
      }  
   
    pn_timestamp_t time_now(void)
    {
      struct timeval now;
      if (gettimeofday(&now, NULL)) pn_fatal("gettimeofday failed\n");
      return ((pn_timestamp_t)now.tv_sec) * 1000 + (now.tv_usec / 1000);
    }  
   
    void die(const char *file, int line, const char *message)
    {
      printf("Dead\n");
      fprintf(stderr, "%s:%i: %s\n", file, line, message);
      exit(1);
    }
   
    int sendMessage(pn_messenger_t * messenger) {
        char * address = (char *) "amqps://SendRule:{Send Rule key}@{namespace name}.servicebus.windows.net/{event hub name}";
        char * msgtext = (char *) "Hello from C!";
   
        pn_message_t * message;
        pn_data_t * body;
        message = pn_message();
   
        pn_message_set_address(message, address);
        pn_message_set_content_type(message, (char*) "application/octect-stream");
        pn_message_set_inferred(message, true);
   
        body = pn_message_body(message);
        pn_data_put_binary(body, pn_bytes(strlen(msgtext), msgtext));
   
        pn_messenger_put(messenger, message);
        check(messenger);
        pn_messenger_send(messenger, 1);
        check(messenger);
   
        pn_message_free(message);
    }
   
    int main(int argc, char** argv) {
        printf("Press Ctrl-C to stop the sender process\n");
   
        pn_messenger_t *messenger = pn_messenger(NULL);
        pn_messenger_set_outgoing_window(messenger, 1);
        pn_messenger_start(messenger);
   
        while(true) {
            sendMessage(messenger);
            printf("Sent message\n");
            sleep(1);
        }
   
        // release messenger resources
        pn_messenger_stop(messenger);
        pn_messenger_free(messenger);
   
        return 0;
    }
    ```
6. Compile el archivo, suponiendo que **gcc**:
   
    ```
    gcc sender.c -o sender -lqpid-proton
    ```

    > [!NOTE]
    > En este código, usamos una ventana de salida de 1 para forzar que los mensajes salgan tan pronto como sea posible. En general, la aplicación debe probar con los mensajes por lotes para aumentar el rendimiento. Vea la [página Qpid AMQP Messenger](http://qpid.apache.org/components/messenger/index.html) para más información sobre cómo usar la biblioteca de Qpid Proton en este y otros entornos y desde las plataformas para las que se proporcionan enlaces (actualmente, Perl, PHP, Python y Ruby).


## <a name="next-steps"></a>Pasos siguientes
Para más información acerca de Event Hubs, visite los vínculos siguientes:

* [Información general de Event Hubs](event-hubs-what-is-event-hubs.md)
* [Creación de un centro de eventos](event-hubs-create.md)
* [Preguntas más frecuentes sobre Event Hubs](event-hubs-faq.md)

<!-- Images. -->
[21]: ./media/event-hubs-c-ephcs-getstarted/run-csharp-ephcs1.png
[24]: ./media/event-hubs-c-ephcs-getstarted/receive-eph-c.png

<!-- Links -->
[Azure classic portal]: https://manage.windowsazure.com/
[Event Processor Host]: https://www.nuget.org/packages/Microsoft.Azure.ServiceBus.EventProcessorHost
[Event Hubs overview]: event-hubs-overview.md
[sample application that uses Event Hubs]: https://code.msdn.microsoft.com/Service-Bus-Event-Hub-286fd097
[Scale out Event Processing with Event Hubs]: https://code.msdn.microsoft.com/Service-Bus-Event-Hub-45f43fc3

