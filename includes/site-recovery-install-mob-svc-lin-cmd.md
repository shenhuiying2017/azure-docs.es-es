1. Copie el instalador en una carpeta local (por ejemplo, /tmp) del servidor que desea proteger. En un terminal, ejecute los siguientes comandos:
  ```
  cd /tmp
  tar -xvzf Microsoft-ASR_UA*release.tar.gz
  ```
2. Para instalar Mobility Service, ejecute el siguiente comando:

  ```
  sudo ./install -t both -a host -R Agent -d /usr/local/ASR -i <IP address> -p <port> -s y -c https -P MobSvc.passphrase
  ```

#### <a name="mobility-service-installer-command-line-arguments"></a>Argumentos de la línea de comandos del instalador de Mobility Service

|Parámetro|Tipo|Descripción|Valores posibles|
|-|-|-|-|
|-t |Obligatorio|Tipo de agente<br>(en desuso en una próxima versión)|*ambos*|
|-a |Obligatorio|Configuración de agente<br>(en desuso en una próxima versión) |*host*|
|-R |Opcional|Rol del agente|Agente<br>MasterTarget|
|-d |Opcional|Ubicación en que se instalará Mobility Service|/usr/local/ASR|
|-i |Obligatorio|Dirección IP del servidor de configuración|Cualquier dirección IP válida|
|-p |Obligatorio|Puerto en el que el servidor de configuración escucha las conexiones entrantes|443|
|-s |Obligatorio|Inicia el servicio después de una instalación correcta<br>(en desuso en una próxima versión)|*y*|
|-c |Obligatorio|Modo de comunicación entre el agente y el servidor de procesos<br>(en desuso en una próxima versión) |*https*|
|-P |Obligatorio|Frase de contraseña del servidor de configuración|Cualquier ruta de acceso local o UNC válida|


#### <a name="example"></a>Ejemplo
```
sudo ./install -t both -a host -R Agent -d /usr/local/ASR -i 192.168.2.53 -p 443 -s y -c https -P /tmp/MobSvc.passphrase
```
