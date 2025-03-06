# Una opera de miedo #
 
 Bienvenidos a Jidoor para una NOCHE MÁGICA

The Impresario os ha contratado para la preparación del retorno triunfal al mundo de la opera de Maria en la obra María y Draco justo cuando os avisa de que tiene un gran problema. Han informatizado toda la orquesta y el público está listo para su regreso, pero está cundiendo el caos absoluto entre todos los artistas: se han perdido instrumentos, encargados de seguridad han desaparecido, los músicos están desconcertados y la artista principal esta bajo la amenaza de que puede ser secuestrada. La actuación es esta misma noche, asi que el tiempo apremia.

La situación es la siguiente, tenemos una serie de instrumentos (usuarios) y una serie de carpetas en los atriles (/src/jidoor/opera) que contienen las partituras que tienen que tocar. Pero por desgracia unos músicos son muy torpes con las partituras y están entrando cuando no les toca o están tocando lo que no es, ¡TODO ES EL CAOS!

- Lo primero que tenemos que crear es la ruta (/src/jidoor/opera) con el siguiente comando:

    ````
    mkdir /src/jidoor/opera

    ````

# Usuarios #

Los usuarios que se han de crear o verificar en los sistemas son los siguientes:

- Oboe
- Clarinete
- Flauta
- Guitarra
- Corno
- Cello
- Contrabajo
- Sistro
- Timbal
- Conductor

PARA CREAR LOS USUARIOS DEBEMOS CREAR UN TXT:

````
nano usuarios.txt
````
EL TXT DEBERÁ CONTENER LOS SIGUIENTES USUARIOS:

````
- Oboe
- Clarinete
- Flauta
- Guitarra
- Corno
- Cello
- Contrabajo
- Sistro
- Timbal
- Conductor
- Maria
- Draco
- Ralse
- Narrador
````

PARA CREAR LOS USUARIOS DEBEREMOS UTILIZAR UN SCRIPT COMO ESTE:

````
nano crearusuarios.sh
````

DENTRO DEL NANO DEBEREMOS PONER ESTE SCRIPT:

````
#!/bin/bash

echo -n "Nombre del fichero de usuarios: "
read lista

if [ ! -f "$lista" ];then
	echo "No existe el fichero"
	exit 1
fi

while IFS= read -r usuario
do 
	echo "Creando el usuario $usuario"
	
	#Crear usuario
	useradd -m -p "fp2023" "$usuario"
done < $lista
````

# Grupos #
Parecido a la práctica anterior lo que tendremos que poner en el txt son todos los grupos que queramos poner

````
nano grupos.txt
````

Dentro del nano pondremos los siguientes grupos;

- Cuerda
- Viento_metal
- Viento_madera
- Percusión
- Conductor
- Orquesta
- Artistas

Ahora para crear el script de crear grupos crearemos el script creargrupo.sh

````
nano creargrupo.sh
````
Dentro del script deberemos añadir esto:

````
#!/bin/bash

echo -n "Nombre del fichero de grupos: "
read lista

if [ ! -f "$lista" ];then
	echo "No existe el fichero"
	exit 1
fi

while IFS= read -r grupo
do 
	echo "Creando el usuario $grupo"
	
	#Crear grupo
	groupadd "$grupo"
done < $lista
````
Ahora crearemos otro script para añadir los usuarios al grupo que queramos

````
nano añadiragrupo.sh
````

En este nano añadiremos:
````
#!/bin/bash

read -p "Indica a que grupo va a pertenecer el usuario: " grupo
read -p "Indica el usuario que deseas meter al grupo: " usuario

usermod -a -G $grupo $usuario
````

E iremos indicando el grupo y el usuario que queremos intrudicir en x grupo

Una vez creados comprobamos que estan creados y dentro de su grupo

# Tarea 1: Vista de los espectadores #

Primero tendremos que crear el usuario espectador y modificar la contraseña
````
sudo useradd -m -s /bin/bash espectador
sudo passwd fp.2023
````
Instalaremos el SSH en caso de que no este instalado y lo configuraremos

````
sudo apt-get update
sudo apt-get install openssh-server
````
Una vez lo tenemos instalado procederemos a configurarlo:
````
sudo nano /etc/ssh/sshd_config
````
Una vez hayamos entrado al config del ssh modificaremos la siguiente linea añadiendo esto:

````
Banner /etc/banner.txt
````

Creamos el archivo Banner.txt
````
sudo nano /etc/banner.txt
````
Dentro del nano pondremos el texto que queremos que salga de banner, en este caso sera este:
- Bienvenido a la ópera

Una vez ya tenemos todo reiniciamos el ssh para aplicar los cambios:
````
sudo systemctl restart ssh
````
Editaremos el archivo del usuario espectador para limitar sus acciones:
````
sudo nano /home/espectador/.bashrc
````
Editamos y ponemos esto:
````
echo "Contenido de la carpeta /src/jidoor/opera:"
cat /src/jidoor/opera/*

echo "Lista de trabajos a interpretar:"
ls /src/jidoor/opera

echo "Gracias por asistir, será desconectado"

exit
````
# Acto 1 Obertura #

Para comenzar, añadimos a el usuario Director al archivo /etc/sudoers

crearemos el script obertura.sh:

````
nano obertura.sh
````
En el script deberemos poner:
````
#!/bin/bash

rutas=("/src/jidoor/Il_barbiere_di_Siviglia/orquesta" "/src/jidoor/Il_barbiere_di_Siviglia/artistas")

if [ -d  "${rutas[0]}" ];then
	echo "Las carpetas ${rutas[0]} y ${rutas[1]} existen ya en el sistema, todo correcto"
else
	echo "La carpeta ${rutas[0]} no existe en el sistema, procedemos a la creacion"
	mkdir -p "${rutas[0]}"
	echo "${rutas[0]} ha sido creada"
fi
if [ -d "${rutas[1]}" ];then
	echo "La carpeta ${rutas[1]} ya existe en el sistema, todo correcto"
else
	echo "La carpeta ${rutas[1]} no existe en el sistema, procedemos a la creacion"
	mkdir -p "${rutas[1]}"
	echo "${rutas[1]} ha sido creada"
fi

echo -n "Nombre del fichero de usuarios: "
read lista

if [ ! -f "$lista" ];then
	echo "No existe el fichero"
	exit 1
fi

while IFS= read -r usuario
do 	
	echo "$usuario" > ${rutas[0]}/$usuario.txt
	echo "$usuario" > ${rutas[1]}/$usuario.txt
	Sgrupo=$(groups $usuario | awk '{print $4}')
	echo "Texto del usuario $usuario escrito correctamente"
	chown $usuario:$grupo ${rutas[0]}/$usuario.txt
	chmod 640 ${rutas[0]}/$usuario.txt
	chmod 200 ${rutas[1]}/$usuario.txt
	echo "Permisos del usuario $usuario y su archivo cambiados correctamente"
done < $lista
````

# Tarea 2 #

Haz un script llamado act01.sh con las ordenes necesarias. Usar variables para almacenar los paths e implementar bucles es buena idea.

Lo que haremos es crear el script ```` act01.sh````


Comenzamos definiendo donde se encuentra el banner, definimos idespectador como un comando que vamos a usar, mandamos a 2>/dev/null para que no salgan errores

Si el usuario no esta creado lo creamos con useradd, y si está ya creado avisamos con un echo
Cambiamos la contraseña del usuario espectador a “a” con el comando chpasswd y avisamos de que la hemos cambiado
Cambiamos el dueño de la carpeta sor a el usuario espectador grupo espectador, y finalmente realizamos un ssh con el usuario espectador, la ip de mi máquina y el final es para que se meta en la carpeta /usr/sor usando bash

En el script pondremos todo esto para que funcione:

````
#!/bin/bash

rutabanner="/etc/banner.txt"
idespectador=$(id espectador 2>/dev/null)
if [ "$idespectador" ==  "" ];then
	echo "El usuario espectador no existe en el sistema, procedemos a la creacion"
	useradd espectador
	echo "El usuario espectador ha sido creado"
else
	echo "EL usuario espectador esta en el sitema, siguiente paso"
fi 

echo "espectador:a" | sudo chpasswd
echo "Contraseña cambiada para el usuario espectador"
chown -R espectador:espectador /usr/sor
echo "Cambiado el dueño de la carpeta sor a espectador grupo espectador"
echo "Vamos a conectarnos a el usuario espectador"
ssh espectador@10.1.16.66 -t "cd /src/jidoor; bash --login"
````

# Acto 2 Aria di Mezzo Carattere #

El primer acto ha conseguido salir adelante, pero hay un problema mayor, alguien ha tirado todas las carpetas al suelo y han quedado mezcladas las partituras y documentos de la obra, ademas The Impresario ha decidido que es peligroso que María actúe, menos mal que Celes Chere ha decidido sustituir a María durante su acto. Como Draco ha terminado la Obertura únicamente habría que gestionar las partituras de Aria di Mezzo Carattere

El director de orquesta te encarga que rápidamente ordenes todos los documentos en su lugar, pero para hacerlo correctamente pueden darse cuatro casos que se han de solucionar.

La carpeta de un determinado instrumento no tiene partituras: Entonces se deberá formar la siguiente estructura en el directorio HOME del usuario.

Para este script reutilizaremos el codigo

Tendremos que hacer esto:

````
nano acto2.sh
````
En el nano tendremos que introducir todo este codigo:

````
#!/bin/bash
usuario=$1
if [ "$2" == "restablecer" ];then
    Sgrupo=$(groups $usuario | awk '{print $4}')
    echo "------------- $usuario ---------------------" 
    idusuario=$(id $usuario 2>/dev/null)
    if [ "$idusuario" == "" ];then
        echo "El usuario $usuario indicado no es valido o no existe"
    else
        echo "El usuario $usuario esta en el sitema, siguiente paso"
    fi 
    if [ ! -f /home/$usuario/$usuario.txt ];then
        touch /home/$usuario/$usuario.txt
        chown $usuario:$grupo /home/$usuario/$usuario.txt
        chmod 640 /home/$usuario/$usuario.txt
    else
        echo "Error"
    fi
    if [ ! -d /home/$usuario/Acto_I ];then
        mkdir /home/$usuario/Acto_I
        chown $usuario:$grupo /home/$usuario/Acto_I
        chmod 640 /home/$usuario/Acto_I
    else
        echo "Error"
    fi
    if [ ! -f /home/$usuario/Acto_I/$usuario"_1.txt" ];then
        touch /home/$usuario/Acto_I/$usuario"_1.txt"
        chown $usuario:$grupo /home/$usuario/Acto_I/$usuario"_1.txt"
        chmod 640 /home/$usuario/Acto_I/$usuario"_1.txt"
    else
        echo "Error"
    fi
    if [ ! -d /home/$usuario/Acto_II ];then
        mkdir /home/$usuario/Acto_II
        chown $usuario:$grupo /home/$usuario/Acto_II
        chmod 640 /home/$usuario/Acto_II
    else
        echo "Error"
    fi
    if [ ! -f /home/$usuario/Acto_II/$usuario"_2.txt" ];then
        touch /home/$usuario/Acto_II/$usuario"_2.txt"
        chown $usuario:$grupo /home/$usuario/Acto_II/$usuario"_2.txt"
        chmod 640 /home/$usuario/Acto_II/$usuario"_2.txt"
    else
        echo "Error"
    fi
    if [ ! -d /home/$usuario/Acto_III ];then
        mkdir /home/$usuario/Acto_III
        chown $usuario:$grupo /home/$usuario/Acto_III
        chmod 640 /home/$usuario/Acto_III
    else
        echo "Error"
    fi
    if [ ! -f /home/$usuario/Acto_III/$usuario"_3.txt" ];then
        touch /home/$usuario/Acto_III/$usuario"_3.txt"
        chown $usuario:$grupo /home/$usuario/Acto_III/$usuario"_3.txt"
        chmod 640 /home/$usuario/Acto_III/$usuario"_3.txt"
    else
        echo "Error"
    fi
    if [ ! -d /home/$usuario/Acto_IV ];then
        mkdir /home/$usuario/Acto_IV
        chown $usuario:$grupo /home/$usuario/Acto_IV
        chmod 640 /home/$usuario/Acto_IV
    else
        echo "Error"
    fi
    if [ ! -f /home/$usuario/Acto_IV/$usuario"_4.txt" ];then
        touch /home/$usuario/Acto_IV/$usuario"_4.txt"
        chown $usuario:$grupo /home/$usuario/Acto_IV/$usuario"_4.txt"
        chmod 640 /home/$usuario/Acto_IV/$usuario"_4.txt"
    else
        echo "Error"
    fi
    tree /home/$usuario
    echo "Todo se ha ajustado con exito"
    
    # Añadir el usuario Celes  sus permisos
    sudo useradd -m Celes
    sudo usermod -a -G $(groups Maria | awk '{print $3}') Celes

    # Crear un directorio que sea una copia del de Maria
    cp -r /home/Maria /home/Celes

    # Cambiar el nombre del fichero Maria.txt a Celes.txt manteniendo el contenido
    mv /home/Celes/Maria.txt /home/Celes/Celes.txt

    # Ajustar los permisos del fichero Celes.txt
    chown Celes:$(groups Maria | awk '{print $3}') /home/Celes/Celes.txt
    chmod 640 /home/Celes/Celes.txt

    echo "Usuario Celes añadido y configurado correctamente"
fi

if [ "$2" == "probar" ];then
    #propietario=$(stat -c %U $usuario.txt)
    #gpropietario=$(stat -c %G grupos.txt)
    #permisos1=$(stat --format='%a' $usuario.txt | cut -c 1)
    #permisos2=$(stat --format='%a' $usuario.txt | cut -c 2)
    #permisos3=$(stat --format='%a' $usuario.txt | cut -c 3)
    echo "------------- $usuario ---------------------" 
    idusuario=$(id $usuario 2>/dev/null)
    if [ "$idusuario" == "" ];then
        echo "TEST1: el usuario $usuario no existe"
    else
        echo "TEST1: el usuario $usuario existe"
    fi 
    if [ -f /home/$usuario/$usuario.txt ];then
        echo "TEST2: el fichero $usuario".txt" existe"
    else
        echo "TEST2: el fichero $usuario".txt" no existe"
    fi
    if [ -r /home/$usuario/$usuario.txt ];then
        echo "TEST3: el usuario puede leer el fichero"
    else
        echo "TEST3: el usuario no puede leer el fichero"
    fi
    if [ -f /home/$usuario/Acto_I/$usuario"_1.txt" ];then
        echo "TEST4: el fichero $usuario"_1.txt" del Acto_I existe"
    else
        echo "TEST4: el fichero $usuario"_1.txt" del Acto_I no existe"
    fi
    if [ -r /home/$usuario/Acto_I/$usuario"_1.txt" ];then
        echo "TEST5: el usuario $usuario puede leer el fichero $usuario"_1.txt""
    else
        echo "TEST5: el usuario $usuario no puede leer el fichero $usuario"_1.txt""
    fi
    if [ -f /home/$usuario/Acto_II/$usuario"_2.txt" ];then
        echo "TEST6: el fichero $usuario"_2.txt" del Acto_II existe"
    else
        echo "TEST6: el fichero $usuario"_2.txt" del Acto_II no existe"
    fi
    if [ -r /home/$usuario/Acto_II/$usuario"_2.txt" ];then
        echo "TEST7: el usuario $usuario puede leer el fichero $usuario"_2.txt""
    else
        echo "TEST7: el usuario $usuario no puede leer el fichero $usuario"_2.txt""
    fi
    if [ -f /home/$usuario/Acto_III/$usuario"_3.txt" ];then
        echo "TEST8: el fichero $usuario"_3.txt" del Acto_ III existe"
    else
        echo "TEST8: el fichero $usuario"_3.txt" del Acto_ III no existe"
    fi
    if [ -r /home/$usuario/Acto_III/$usuario"_3.txt" ];then
        echo "TEST9: el usuario $usuario puede leer el fichero $usuario"_3.txt""
    else
        echo "TEST9: el usuario $usuario no puede leer el fichero $usuario"_3.txt""
    fi
    if [ -f /home/$usuario/Acto_IV/$usuario"_4.txt" ];then
        echo "TEST10: el fichero $usuario"_4.txt" del Acto_IV existe"
    else
        echo "TEST10: el fichero $usuario"_4.txt" del Acto_IV no existe"
    fi
    if [ -r /home/$usuario/Acto_IV/$usuario"_4.txt" ];then
        echo "TEST11: el usuario $usuario puede leer el fichero $usuario"_4.txt""
    else
        echo "TEST11: el usuario $usuario no puede leer el fichero $usuario"_4.txt""
    fi
    echo "Se ha probado todo con exito"
fi
if [ "$2" == "limpiar" ];then
    echo "------------- $usuario ---------------------" 
    idusuario=$(id $usuario 2>/dev/null)
    if [ "$idusuario" == "" ];then
        echo "No eres un usario valido"
        break
    else
        echo "El usuario $usuario esta en el sitema, siguiente paso"
    fi 
    rm -r /home/$usuario/*
    echo "Se ha limpiado la carpeta /home del usuario $usuario"
fi 
````

````
nano celes.sh

#!/bin/bash

eliminar_usuario_maria() {
    if id "Maria" &>/dev/null; then
        sudo userdel -r Maria
        echo "Usuario María eliminado."
    else
        echo "El usuario María no existe."
    fi
}

crear_usuario() {
    local usuario=$1
    sudo useradd -m $usuario
    echo "Usuario $usuario creado."
    
    # Crear archivo de partitura
    local archivo_partitura="/home/$usuario/partitura.txt"
    echo "Preparando archivo de partitura para $usuario..."
    touch $archivo_partitura
    chown $usuario:$usuario $archivo_partitura
    chmod 640 $archivo_partitura
    echo "Archivo de partitura para $usuario preparado."
}

mostrar_guion() {
    local guion="/src/script.txt"
    if [ -f $guion ]; then
        while IFS= read -r linea
        do
            echo "$linea"
            sleep 2
        done < "$guion"
    else
        echo "El archivo de guión no existe en /src/script.txt."
    fi
}

while [[ "$#" -gt 0 ]]; do
    case $1 in
        -g|--guion) mostrar_guion; exit 0 ;;
        *) echo "Opción no válida: $1"; exit 1 ;;
    esac
    shift
done

eliminar_usuario_maria
crear_usuario "Celes"
crear_usuario "Locke"
````

He reutilizado el código anterior, así que declaro la variable usuario como el primer argumento. En primer lugar, verificamos si el usuario existe en el sistema. Si no existe, lo creamos y continuamos con el siguiente paso. Luego, comprobamos si el archivo [nombreusuario].txt existe; si no, lo creamos. Hacemos lo mismo con la carpeta Acto_I y, si no existe, la creamos. Repetimos este proceso para los archivos y carpetas hasta Acto_IV. Al final, ejecutamos un comando tree para visualizar la estructura y poder identificar posibles errores.

# Acto 3 #
Afortunadamente la actuación de Celes ha salido a la perfección gracias a tu colaboración, aunque misteriosamente están desapareciendo las partituras de todos los actos.

Tarea 4
Haz un script llamado act03.sh que cree a un usuario Locke si no existe y que acepte un argumento que puede ser:

Todo (--all)
Acto 1 (--01)
Acto 2 (--02)
Acto 3 (--03)
Acto 4 (--04)
El script deberá borrar todos los ficheros y carpetas del acto indicado o todo. Si se le pasa un argumento deberá de dar un mensaje de aviso indicando que no se le ha pasado un argumento válido.

act03.sh deberá ejecutarse cada 10 minutos con un argumento aleatorio.

Adicionalmente crea un script act03_locke.sh que se ejecute cada 15 minutos y que ejecute act02.sh con Probar, Limpiar y Restablecer con todos los usuarios.


Crearemos el script act03.sh
````
#!/bin/bash

usuario=$1

idusuario=$(id $usuario 2>/dev/null)
if [ "$idusuario" == "" ]; then
    echo "El usuario $usuario indicado no es valido o no existe"
else
    echo "El usuario $usuario está en el sistema"
    case "$2" in
        "--all")
            rm -r /src/jidoor/opera/$usuario/*
            echo "Se ha borrado todo con éxito"
            ;;
        "--01")
            rm -r /src/jidoor/opera/$usuario/Acto_I
            echo "Se ha borrado el Acto I con éxito"
            ;;
        "--02")
            rm -r /src/jidoor/opera/$usuario/Acto_II
            echo "Se ha borrado el Acto II con éxito"
            ;;
        "--03")
            rm -r /src/jidoor/opera/$usuario/Acto_III
            echo "Se ha borrado el Acto III con éxito"
            ;;
        "--04")
            rm -r /src/jidoor/opera/$usuario/Acto_IV
            echo "Se ha borrado el Acto IV con éxito"
            ;;
        *)
            echo "No ha indicado un argumento válido"
            ;;
    esac
fi 

(crontab -l 2>/dev/null; echo "*/10 * * * * /src/jidoor/opera/act03.sh usuario$(shuf -i 1-100 -n 1) --$(shuf -e all 01 02 03 04 -n 1)") | crontab -
````
Ahora crearemos el script act03_locke.sh

Adicionalmente crea un script act03_locke.sh que se ejecute cada 15 minutos y que ejecute act02.sh con Probar, Limpiar y Restablecer con todos los usuarios.

````
nano act03_locke.sh
````
````
usuarios=("usuario1" "usuario2" "usuario3")

opciones=("Probar" "Limpiar" "Restablecer")

for usuario in "${usuarios[@]}"; do
    for opcion in "${opciones[@]}"; do
        /src/jidoor/opera/act02.sh $usuario $opcion
    done
done

(crontab -l 2>/dev/null; echo "*/15 * * * * /src/jidoor/opera/act03_locke.sh") | crontab -
````
# Acto IV: Finale
Locke ha descubierto que Ultros ha estado intentando sabotear la Opera. Durante el final ha robado varios instrumentos o ha descoordinado los distintos grupos de la orquesta.
# Tarea 5

````
#!/bin/bash
if [ "$2" == "-i" ] || [ "$2" == "--instruments" ];then
    usuario=$1
    idusuario=$(id $usuario 2>/dev/null)
    if [ "$idusuario" ==  "" ];then
        echo "El usuario $usuario indicado no es valido o no existe"
    else
        echo "El usuario $usuario esta en el sistema"
        rm -r /home/$usuario
        echo "Directorio /home/$usuario borrada con exito"
        ruta="/etc/passwd"
        rutatemp="/etc/ficherotemp"
        while IFS= read -r fila
        do 
            test1=$(echo $fila | tr ":" " " | awk '{print $1}')
            if [ "$1" != "$test1" ];then
                echo $fila >> $rutatemp
            fi      
        done < $ruta
        rm $ruta
        echo "Fichero original borrado"
        mv $rutatemp $ruta
        echo "Ahora todo esta correcto"
        ruta="/etc/shadow"
        rutatemp="/etc/ficherotemp"
        touch $rutatemp
        while IFS= read -r fila
        do 
            test1=$(echo $fila | tr ":" " " | awk '{print $1}')
            #ESCRIBIMOS TODAS LAS LINEAS MENOS LAS QUE SEAN DEL INSTRUMENTO
            if [ "$1" != "$test1" ];then
                echo $fila >> $rutatemp
            fi      
        done < $ruta
        rm $ruta
        echo "Fichero original borrado"
        mv $rutatemp $ruta
        echo "EL instrumento $usuario se ha borrado con exito"
        fi 
fi
if [ "$2" == "-g" ] || [ "$2" == "--groups" ];then
    grupo=$1
    ruta="/etc/group"
    rutatemp="/etc/grouptemp"
    verificar=$(cat $ruta | tr ":" " " | awk '{print $1}' | grep -w $grupo)
    if [ "$verificar" != "" ];then
        touch $rutatemp
        while IFS= read -r fila
        do 
            test1=$(echo $fila | tr ":" " " | awk '{print $1}')
            if [ "$1" != "$test1" ];then
                echo $fila >> $rutatemp
            fi      
        done < $ruta
        rm $ruta
        echo "Fichero original borrado"
        mv $rutatemp $ruta
        echo "Grupo $grupo borrado con exito"
    else
    echo "El grupo no existe"
    fi
fi
if [ "$2" != "-i" ] && [ "$2" != "--instrument" ] && [ "$2" != "-g" ] && [ "$2" != "--groups" ];then
    echo "No ha introducido ningun argumento valido"
fi

usuario_default="Ultros"
usuario_secundario="Setzer"
usuarios_borrar=("Celes" "Locke" "Ultros")

while true; do
  if [ -z "$(getent passwd | grep -E '\-i|\-\-instruments|\-g|\-\-groups')" ]; then
    useradd $usuario_secundario
    echo "Usuario $usuario_secundario creado"
    for usuario in "${usuarios_borrar[@]}"; do
      userdel $usuario
      echo "Usuario $usuario borrado"
    done
    echo "María ha sido secuestrada!"
  else
    useradd $usuario_default
    sleep 60
  fi
done
````