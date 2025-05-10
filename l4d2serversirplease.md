# Guía de instalación de un servidor dedicado para L4D2

### **IMPORTANTE: NO IGNORE ESTO**
<------------------ SOLO PARA LINUX ------------------>
**Esto significa que Windows no está soportado, así que no lo preguntes. 😄**

---

### **Objetivo del documento:**
El propósito de este documento es hacer que sea fácil configurar servidores optimizados para quienes desean alojar su propio servidor de L4D2. La mayoría de los proveedores de servidores no ayudarán con la instalación de software de terceros, así que este documento está diseñado para ayudarte a hacer todo por ti mismo, desde el principio hasta el final.

---

### **Recomendaciones de especificaciones del servidor:**

- **Sistema operativo**: Ubuntu
- **Espacio dedicado en el servidor**: No uses recursos compartidos. Un VDS adecuado funcionará bien.
- **Recursos por servidor**: 1 núcleo y 1GB de memoria.
- **CPU moderna**: Si planeas aumentar la tasa de ticks, lo ideal es una CPU sólida de al menos 3 GHz.
- **Protección DDoS**: Para absorber ataques volumétricos y filtrar tráfico malicioso.

**Hosts recomendados** (según mi experiencia):
- NFOServers (para EE. UU.)
- OVH (para la UE)

---

### **Iniciar instalación del servidor dedicado:**

#### **Instalación y conexión inicial del servidor:**
Conéctate a tu panel de administración del proveedor de hosting y asegúrate de usar Ubuntu 20.04 o una versión más nueva. Si no tienes esta opción, la mayoría de los proveedores tienen un panel intuitivo para hacer este cambio.

**Para la siguiente parte, necesitas un cliente SSH como Putty.**

- Abre Putty e ingresa la IP de tu servidor en el campo "Hostname".
- El puerto debe ser el 22 por defecto y el tipo de conexión "SSH" (esto es correcto).
- Una vez ingresada la IP, haz clic en **Abrir**.
- Si aparece una advertencia de seguridad, selecciona "Sí".

Una vez que aparezca la pantalla negra que dice "login as:", ingresa el nombre de usuario proporcionado por tu proveedor de hosting (probablemente sea "root"). Luego, ingresa la contraseña.

---

### **Requisitos previos para L4D2:**
Antes de poder instalar L4D2, debes instalar algunos paquetes. Simplemente copia y pega cada uno de los siguientes comandos en la terminal de Putty.

```bash
dpkg --add-architecture i386  # Habilitar multi-arquitectura
apt-get update && apt-get upgrade  # Actualizar el sistema
apt-get install libc6:i386  # Instalar bibliotecas base de 32 bits
apt-get install lib32z1  # Instalar librerías adicionales
apt-get install screen  # Instalar Screen (para administrar el servidor)

Crear un usuario para ejecutar los servidores:

No quieres ejecutar estos servicios como root, ¿verdad? Vamos a crear un usuario llamado steam para ejecutar el servidor, de modo que no tengas que iniciar sesión como root cada vez.

adduser steam  # Crear el usuario 'steam'
adduser steam sudo  # Agregar permisos de sudo
login  # Iniciar sesión como el usuario 'steam'

Instalar Steam y archivos de L4D2:

Ya no estamos logueados como root, ahora estamos en el usuario "Steam". A continuación, instala los archivos necesarios para el servidor de L4D2 en la carpeta /home/steam/Steam/steamapps/common/l4d2.

Ejecuta los siguientes comandos uno por uno:

wget http://media.steampowered.com/installer/steamcmd_linux.tar.gz  # Descargar SteamCMD
tar -xvzf steamcmd_linux.tar.gz  # Descomprimir SteamCMD
./steamcmd.sh +force_install_dir ./Steam/steamapps/common/l4d2 +login anonymous +@sSteamCmdForcePlatformType windows +app_update 222860 validate +quit && ./steamcmd.sh +force_install_dir ./Steam/steamapps/common/l4d2 +login anonymous +@sSteamCmdForcePlatformType linux +app_update 222860 validate +quit  # Descargar L4D2

Configurar los archivos de inicio/reinicio/parada del servidor:

A continuación, necesitarás descargar el archivo srcds1 de esta guía. Puedes hacer clic en el enlace, seleccionar "raw", hacer clic derecho y guardar. Es recomendable usar Notepad++ o cualquier editor de texto.

    Abre el archivo srcds1, edítalo y cambia la IP de ejemplo 1.3.3.7 por la IP real de tu servidor.

    Guarda el archivo como srcds1 (sin la extensión .txt).

Luego, mueve este archivo a la carpeta /etc/init.d:

    Usando FileZilla:

        Conéctate a tu servidor usando SFTP con el mismo usuario y contraseña que usaste en Putty.

        Navega a la carpeta /etc/init.d.

        Arrastra y suelta el archivo srcds1 en esta carpeta.

Instalar archivos de configuración del servidor:

Antes de iniciar el servidor, instala los archivos de configuración y asegúrate de que estén configurados correctamente. Para ello, descarga los archivos desde el repositorio de Competitive Rework en GitHub.

    Descargar y configurar los archivos:

        Descarga el archivo ZIP desde GitHub.

        Extrae los archivos y abre la carpeta.

        Edita myhost.txt y mymotd.txt para personalizar el mensaje de bienvenida del servidor.

        Renombra server.cfg a server1.cfg y configura las opciones relevantes (como el nombre del servidor, la contraseña y el grupo de Steam).

        Sube estos archivos editados a la carpeta del servidor en /home/steam/Steam/steamapps/common/l4d2/left4dead2/.

Iniciar, reiniciar o detener los servidores:

Primero, debemos darle permisos al sistema para ejecutar los archivos. Para esto, ejecuta el siguiente comando en la terminal:

sudo chmod +x /etc/init.d/srcds1

Ya puedes iniciar, reiniciar o detener los servidores con los siguientes comandos:

/etc/init.d/srcds1 start  # Iniciar el servidor
/etc/init.d/srcds1 restart  # Reiniciar el servidor
/etc/init.d/srcds1 stop  # Detener el servidor

Si ves un error como -bash: /etc/init.d/srcds1: /bin/sh^M: bad interpreter, significa que el archivo tiene saltos de línea de DOS. Puedes corregirlo con el comando dos2unix.
Preguntas frecuentes (F.A.Q.):

    ¿Puedo ejecutar múltiples servidores en mi VDS con varios núcleos?
    Sí, puedes ejecutar varios servidores siempre y cuando sigas estos pasos.

    ¿Cómo obtengo el plugin de mix de SirPlease?
    El plugin no es público, pero puedes usar el creado por LuckyLock, que tiene una función similar. Instálalo en la carpeta addons/sourcemod/plugins/optional/ y agrega la línea en el archivo de configuración correspondiente.

    ¿El servidor ya se actualizó, pero no puedo conectarme?
    Simplemente actualiza el servidor con los siguientes comandos:

    ./steamcmd.sh
    force_install_dir ./Steam/steamapps/common/l4d2
    login anonymous
    app_update 222860 validate
    quit

    ¿El Tickrate no se muestra correctamente?
    Es un límite visual del cliente. Aunque el net_graph puede mostrar un valor de 100, los valores intermedios muestran lo que realmente está recibiendo el cliente del servidor.

¡Con esto deberías estar listo para correr tu servidor de L4D2 en Linux! 🎮


Este archivo está listo para ser subido a GitHub como un archivo `.md`. Puedes seguir los mismos pasos mencionados anteriormente para crear y subir el archivo.

¡Espero que te sirva y que te diviertas con tu servidor de L4D2! 😄