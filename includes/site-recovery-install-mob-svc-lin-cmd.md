1. Copie el instalador en una carpeta local (es decir, /tmp) en el servidor que desea proteger y ejecute los siguientes comandos desde una ventana de terminal.
  ```
  cd /tmp
  tar -xvzf Microsoft-ASR_UA*release.tar.gz
  ```
2. Ahora se puede instalar Mobility Service mediante la siguiente línea de comandos

  ```
  sudo ./install -t both -a host -R Agent -d /usr/local/ASR -i <IP address> -p <port> -s y -c https -P MobSvc.passphrase
  ```

#### <a name="mobility-service-installer-command-line-arguments"></a>Argumentos de la línea de comandos del instalador de Mobility Service

|Parámetro|Tipo|Descripción|Valores posibles|
|-|-|-|-|
|-t |Obligatorio|Tipo de agente<br>(Dejará de utilizarse en la próxima versión)|*ambos*|
|-a |Obligatorio|Configuración de agente<br>(Dejará de utilizarse en la próxima versión) |*host*|
|-R |Opcional|Rol del agente|Agente<br>MasterTarget|
|-d |Opcional|Ubicación donde se instalará Mobility Service|/usr/local/ASR|
|-i |Obligatorio|Dirección IP del servidor de configuración.|Cualquier dirección IP válida|
|-p |Obligatorio|Puerto en el que el servidor de configuración escucha las conexiones entrantes|443|
|-s |Obligatorio|Inicio del servicio después de una instalación correcta<br>(Dejará de utilizarse en la próxima versión)|*y*|
|-c |Obligatorio|Modo de comunicación entre el agente y el servidor de procesos<br>(Dejará de utilizarse en la próxima versión) |*https*|
|-P |Obligatorio|Frase de contraseña del servidor de configuración|Cualquier ruta de acceso local o UNC válida|


#### <a name="sample-usage"></a>Ejemplo de uso
```
sudo ./install -t both -a host -R Agent -d /usr/local/ASR -i 192.168.2.53 -p 443 -s y -c https -P /tmp/MobSvc.passphrase
```


<!--HONumber=Jan17_HO3-->


