---
title: juego de Rodar la bola de Unity
description: "Pasos para crear el juego clásico de Rodar una bola, que es un requisito previo de todos los tutoriales de Unity para Mobile Engagement."
services: mobile-engagement
documentationcenter: mobile
author: piyushjo
manager: erikre
editor: 
ms.assetid: 0afd0eca-f74a-43aa-bba8-436a0265c312
ms.service: mobile-engagement
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 08/19/2016
ms.author: piyushjo
ms.openlocfilehash: 6392d1f780b1bc2348fee5947550b05e86ea4de2
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/11/2017
---
# <a id="unity-roll-a-ball"></a>Creación de un juego de Rodar la bola de Unity
Este tutorial le lleva por los pasos principales de un tutorial ligeramente modificado del [juego de Rodar la bola de Unity](http://unity3d.com/learn/tutorials/projects/roll-ball-tutorial). Este juego de ejemplo consta de un objeto esférico, el "jugador", que controla el usuario de la aplicación, y el objetivo del juego consiste en ir recopilando los objetos recopilables al chocar con ellos. Se supone que está familiarizado con los aspectos básicos del entorno del Editor de Unity. Si se tropieza con algún problema, consulte el tutorial completo. 

### <a name="setting-up-the-game"></a>Configuración del juego
Los pasos siguientes son del [tutorial de Unity](https://unity3d.com/learn/tutorials/projects/roll-a-ball/set-up?playlist=17141)

1. Abra el **Editor de Unity** y haga clic en **New** (Nuevo). 
   
    ![][51] 
2. Proporcione datos para **Project name** (Nombre del proyecto) y  & **Location** (Ubicación) seleccione **3D** y haga clic en **Create project** (Crear proyecto).
   
    ![][52]
3. Guarde la escena predeterminada que acaba de crear como parte del nuevo proyecto con el nombre **MiniGame** (MiniJuego) en una nueva carpeta **\_Scenes** (Escenas) en la carpeta **Assets** (Activos):
   
    ![][53]
4. En **3D Object (Objeto 3D) -> Plane (Plano)**, cree un objeto 3D plano como campo de juego y llame a este objeto plano **Ground** (Terreno).
   
    ![][1]
5. Restablezca el componente de transformación de este objeto **Ground** (Terreno) de modo que esté en el origen. 
   
    ![][3]
6. Desactive **Show Grid** (Mostrar cuadrícula) en **Gizmos menu** (Menú Gizmos) para el objeto **Ground** (Terreno).
   
    ![][4]
7. Actualice el componente **Scale** (Escala) del objeto **Ground** (Terreno) a [X = 2,Y = 1, Z = 2]. 
   
    ![][5]
8. En **3D Object (Objeto 3D) -> Sphere (Esfera)**, agregue un nuevo objeto 3D esférico y llame a este objeto esférico **Player** (Jugador). 
   
    ![][6]
9. Seleccione el objeto **Player** (Jugador) y haga clic en **Reset Transform** (Restablecer transformación) igual que hizo con el objeto plano. 
10. Actualice el componente **Transform (Transformación) -> Position (Posición) -> Y Coordinate (Coordenada Y)** para Player Y (Jugador Y) a 0.5.  
    
    ![][7]
11. Cree una nueva carpeta llamada **Materials** (Materiales) en el proyecto donde vamos a crear el material para colorear el jugador. 
12. Cree un nuevo **material** llamado **Background** (Fondo) en esta carpeta. 
    
    ![][8]
13. Actualice el color del material actualizando su propiedad **Albedo** . Puede seleccionar los valores RGB de [0,32,64]. 
    
    ![][9]
14. Arrastre este material a la vista de escena para aplicar el color al objeto **Ground** (Terreno). 
    
    ![][10]
15. Finalmente, actualice los valores de **Transform (Transformación)-> Rotation (Rotación) -> Y** a 60 en el objeto Directional Light (Luz direccional) para definir la claridad. 
    
    ![][12]

### <a name="moving-the-player"></a>Movimiento del jugador
Los pasos siguientes son del [tutorial de Unity](https://unity3d.com/learn/tutorials/projects/roll-a-ball/moving-the-player?playlist=17141)

1. Agregue un componente **RigidBody** al objeto **Player** (Jugador). 
   
    ![][13]
2. Cree una carpeta nueva llamada **Scripts** en el proyecto. 
3. Haga clic en **Add Component (Agregar componente)-> New Script (Nuevo script) -> C# Script (Script de C#)**. Asígnele el nombre **PlayerController** y haga clic en **Create and Add** (Crear y agregar). De esta forma se creará y adjuntará un script al objeto Player (Jugador).  
   
    ![][14]
4. Mueva este script a la carpeta **Scripts** del proyecto. 
5. Abra el script para editarlo en su editor de scripts preferido, actualice el código del script con el siguiente código y guárdelo. 
   
        using UnityEngine;
        using System.Collections;
   
        public class PlayerController : MonoBehaviour 
        {
            public float speed;
            private Rigidbody rb;
            void Start ()
            {
                rb = GetComponent<Rigidbody>();
            }
            void FixedUpdate ()
            {
                float moveHorizontal = Input.GetAxis ("Horizontal");
                float moveVertical = Input.GetAxis ("Vertical");
                Vector3 movement = new Vector3 (moveHorizontal, 0.0f, moveVertical);
                rb.AddForce (movement * speed);
            }
        }
6. Tenga en cuenta que el script anterior usa una propiedad **Speed** . En el Editor de Unity, actualice esta propiedad a 10.  
   
    ![][15]
7. Presione **Play** (Reproducir) en el Editor de Unity. Ahora la bola debe poder controlarse mediante el teclado y debe girar y moverse. 

### <a name="moving-the-camera"></a>Movimiento de la cámara
Los pasos siguientes son del [tutorial de Unity](https://unity3d.com/learn/tutorials/projects/roll-a-ball/moving-the-camera?playlist=17141) y en ellos se asociará el objeto **Main Camera** (Cámara principal) al objeto **Player** (Jugador). 

1. Actualice **Transform.Position** a X = 0, Y = 10.5, Z=-10.  
2. Actualice **Transform.Rotation** a X = 45, Y = 0, Z = 0.  
   
    ![][16]
3. Agregue un nuevo script llamado **CameraController** a **MainCamera** y muévalo a la carpeta Scripts. 
   
    ![][17]
4. Abra el script para editarlo y agréguele el siguiente código:
   
        using UnityEngine;
        using System.Collections;
   
        public class CameraController : MonoBehaviour {
   
            public GameObject player;
   
            private Vector3 offset;
   
            void Start ()
            {
                offset = transform.position - player.transform.position;
            }
   
            void LateUpdate ()
            {
                transform.position = player.transform.position + offset;
            }
        }
5. En el entorno de Unity, arrastre la variable Player a la ranura Player del objeto Main Camera (Cámara principal) de modo que los dos queden asociados. 
   
    ![][18]
6. Ahora, si hace clic en Play (Reproducir) en el Editor de Unity y gira el objeto Player Ball (Bola jugador), verá cómo la cámara sigue su movimiento.  

### <a name="setting-up-the-play-area"></a>Configuración del área de juego
Los pasos siguientes son del [tutorial de Unity](https://unity3d.com/learn/tutorials/projects/roll-a-ball/setting-up-the-play-area?playlist=17141). Vamos a crear las paredes que rodean el terreno para que el objeto Player Ball (Bola jugador) no se salga del área de juego en su movimiento. 

1. Haga clic en **Create (Crear) -> Create Empty (Crear vacío) -> Game Object (Objeto de juego)** y asigne el nombre **Walls** (Paredes).
   
    ![][19]
2. En este objeto Walls (Paredes), cree un nuevo objeto 3D cúbico en **3D Object (Objeto 3D) -> Cube (Cubo)** y llámelo "West Wall" (Pared oeste). 
   
    ![][20]
3. Actualice los datos de **Transform (Transformación) -> Position (Posición)** y **Transform (Transformación) -> Scale (Escala)** de este objeto. 
   
    ![][21]
4. Duplique el objeto West Wall (Pared oeste) para crear un objeto **East Wall** (Pared este) con la posición y la escala de transformación actualizadas. 
   
    ![][22]
5. Duplique el objeto East Wall (Pared este) para crear un objeto **North Wall** (Pared norte) con la posición y la escala de transformación actualizadas. 
   
    ![][23]
6. Duplique el objeto North Wall (Pared norte) y cree un objeto **South Wall** (Pared sur) con la posición y la escala de transformación actualizadas. 
   
    ![][24]

### <a name="creating-collectible-objects"></a>Creación de objetos recopilables
Los pasos siguientes son del [tutorial de Unity](https://unity3d.com/learn/tutorials/projects/roll-a-ball/creating-collectables?playlist=17141). Crearemos algunos objetos atractivos que constituirán el conjunto de objetos recopilables que el objeto Player Ball (Bola jugador) debe recopilar chocando con ellos. 

1. Cree un nuevo **objeto cubo 3D** y llámelo Pickup (Recogida). 
2. Ajuste los valores de **Transform (Transformación) -> Rotation (Rotación)** &  y **Transform (Transformación) -> Scale (Escala)** del objeto Pickup (Recogida). 
   
    ![][25]
3. Cree y asocie un **nuevo script de C#** denominado **Rotator** al objeto Pickup (Recogida). Asegúrese de colocar el script en la carpeta Scripts. 
   
    ![][26]
4. Abra este script para editarlo y actualizarlo a lo siguiente: 
   
        using UnityEngine;
        using System.Collections;
   
        public class Rotator : MonoBehaviour {
   
            void Update () 
            {
                transform.Rotate (new Vector3 (15, 30, 45) * Time.deltaTime);
            }
        }
5. Ahora, presione el modo Play (Reproducir) del Editor de Unity y el objeto Pickup (Recogida) se mostrará girando sobre su eje.
6. Cree una nueva carpeta llamada **Prefabs** 
   
    ![][27]
7. Arrastre el objeto **Pickup** (Recogida) y colóquelo en la carpeta Prefabs (Prefabricados).
   
    ![][28]
8. Cree un nuevo **objeto de juego vacío** llamado **Pickups** (Recogidas). Restablezca su posición al origen y arrastre el objeto Pickup (Recogida) a este objeto de juego.  
   
    ![][29]
9. Duplique el objeto **Pickup** (Recogida) y distribúyalo por el objeto **Ground** (Terreno) alrededor del objeto **Player** (Jugador) actualizando los valores X y Z de **Transform.Position** de forma adecuada. 
   
    ![][30]
10. Cree un **nuevo material** llamado **Pickup** (Recogida) y actualícelo para que sea de color rojo; para ello, actualice la **propiedad Albedo** de manera parecida a como lo hicimos para actualizar el objeto Ground (Terreno). 
    
    ![][31]
11. Aplique el material a los 4 objetos de recogida.
    
    ![][32]

### <a name="collecting-the-pickup-objects"></a>Recopilación de objetos de recogida
Los pasos siguientes son del [tutorial de Unity](https://unity3d.com/learn/tutorials/projects/roll-a-ball/collecting-pick-up-objects?playlist=17141). Actualizaremos el objeto Player (Jugador) para que pueda recopilar los objetos Pickup (Recogida) al chocar con ellos. 

1. Abra el script **Playercontroller** asociado al objeto Player (Jugador) para editarlo y actualizarlo a lo siguiente:  
   
        using UnityEngine;
        using System.Collections;
   
        public class PlayerController : MonoBehaviour {
   
            public float speed;
   
            private Rigidbody rb;
   
            void Start ()
            {
                rb = GetComponent<Rigidbody>();
            }
   
            void FixedUpdate ()
            {
                float moveHorizontal = Input.GetAxis ("Horizontal");
                float moveVertical = Input.GetAxis ("Vertical");
   
                Vector3 movement = new Vector3 (moveHorizontal, 0.0f, moveVertical);
   
                rb.AddForce (movement * speed);
            }
   
            void OnTriggerEnter(Collider other) 
            {
                if (other.gameObject.CompareTag ("Pick Up"))
                {
                    other.gameObject.SetActive (false);
                }
            }
        }
2. Cree una nueva **etiqueta** llamada **Pick up** (Recogida) (debe coincidir con lo que está en el script).  
   
    ![][33]
   
    ![][34]
3. Aplique esta **etiqueta** al objeto Prefab Pickup (Recogida prefabricados). 
   
    ![][35]
4. Active la casilla **IsTrigger** para el objeto Prefab (Prefabricado).
   
    ![][36]
5. Agregue un componente Rigid Body al objeto Pickup Prefab (Recogida prefabricados). Para optimizar el rendimiento, actualizaremos el colisionador estático que hemos usado a colisionador dinámico. 
   
    ![][37]
6. Finalmente, marque la propiedad **IsKinematic** del objeto Prefab (Prefabricado). 
   
    ![][38]
7. Presione **Play** (Reproducir) en el Editor de Unity y podrá jugar a este juego de **Rodar la bola** moviendo el objeto Player (Jugador) con las teclas del teclado para indicar la dirección. 

### <a name="updating-the-game-for-mobile-play"></a>Actualización del juego para dispositivos móviles
En las secciones anteriores hemos concluido el tutorial básico de Unity. Ahora modificaremos el juego para que pueda usarse en dispositivos móviles. Tenga en cuenta que hasta ahora hemos usado la entrada del teclado para probar el juego. Ahora vamos a cambiar eso de modo que podamos controlar el jugador con el movimiento del teléfono, por ejemplo, usando el acelerómetro como entrada. 

Abra el script **PlayerController** para editarlo y actualice el método **FixedUpdate** para usar el movimiento del acelerómetro para mover el objeto Player (Jugador). 

        void FixedUpdate()
        {
            //float moveHorizontal = Input.GetAxis("Horizontal");
            //float moveVertical = Input.GetAxis("Vertical");
            //Vector3 movement = new Vector3(moveHorizontal, 0.0f, moveVertical);
            rb.AddForce(Input.acceleration.x * Speed, 0, -Input.acceleration.z * Speed);
        }

Con este tutorial concluye la creación de un juego básico con Unity, que puede implementar para jugar en el dispositivo de su elección. 

<!-- Images -->
[1]: ./media/mobile-engagement-unity-roll-a-ball/1.png    
[2]: ./media/mobile-engagement-unity-roll-a-ball/2.png
[3]: ./media/mobile-engagement-unity-roll-a-ball/3.png
[4]: ./media/mobile-engagement-unity-roll-a-ball/4.png
[5]: ./media/mobile-engagement-unity-roll-a-ball/5.png
[6]: ./media/mobile-engagement-unity-roll-a-ball/6.png
[7]: ./media/mobile-engagement-unity-roll-a-ball/7.png
[8]: ./media/mobile-engagement-unity-roll-a-ball/8.png
[9]: ./media/mobile-engagement-unity-roll-a-ball/9.png    
[10]: ./media/mobile-engagement-unity-roll-a-ball/10.png    
[11]: ./media/mobile-engagement-unity-roll-a-ball/11.png    
[12]: ./media/mobile-engagement-unity-roll-a-ball/12.png
[13]: ./media/mobile-engagement-unity-roll-a-ball/13.png
[14]: ./media/mobile-engagement-unity-roll-a-ball/14.png
[15]: ./media/mobile-engagement-unity-roll-a-ball/15.png
[16]: ./media/mobile-engagement-unity-roll-a-ball/16.png
[17]: ./media/mobile-engagement-unity-roll-a-ball/17.png
[18]: ./media/mobile-engagement-unity-roll-a-ball/18.png
[19]: ./media/mobile-engagement-unity-roll-a-ball/19.png    
[20]: ./media/mobile-engagement-unity-roll-a-ball/20.png    
[21]: ./media/mobile-engagement-unity-roll-a-ball/21.png    
[22]: ./media/mobile-engagement-unity-roll-a-ball/22.png    
[23]: ./media/mobile-engagement-unity-roll-a-ball/23.png    
[24]: ./media/mobile-engagement-unity-roll-a-ball/24.png    
[25]: ./media/mobile-engagement-unity-roll-a-ball/25.png    
[26]: ./media/mobile-engagement-unity-roll-a-ball/26.png    
[27]: ./media/mobile-engagement-unity-roll-a-ball/27.png    
[28]: ./media/mobile-engagement-unity-roll-a-ball/28.png    
[29]: ./media/mobile-engagement-unity-roll-a-ball/29.png    
[30]: ./media/mobile-engagement-unity-roll-a-ball/30.png    
[31]: ./media/mobile-engagement-unity-roll-a-ball/31.png    
[32]: ./media/mobile-engagement-unity-roll-a-ball/32.png    
[33]: ./media/mobile-engagement-unity-roll-a-ball/33.png    
[34]: ./media/mobile-engagement-unity-roll-a-ball/34.png    
[35]: ./media/mobile-engagement-unity-roll-a-ball/35.png    
[36]: ./media/mobile-engagement-unity-roll-a-ball/36.png    
[37]: ./media/mobile-engagement-unity-roll-a-ball/37.png    
[38]: ./media/mobile-engagement-unity-roll-a-ball/38.png    
[51]: ./media/mobile-engagement-unity-roll-a-ball/new-project.png
[52]: ./media/mobile-engagement-unity-roll-a-ball/new-project-properties.png
[53]: ./media/mobile-engagement-unity-roll-a-ball/save-scene.png













