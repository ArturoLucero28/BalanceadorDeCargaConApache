# Balanceador De Carga Con Apache

## Índice

1. [Introducción](#introducción)
2. [Objetivo](#objetivo)
3. [Infraestructura](#infraestructura)
4. [Configuración Paso a Paso](#configuración-paso-a-paso)
 - 4.1 [Pasos previos a la creacion de instancias](#Pasos-previos-a-la-creacion-de-instancias)
 - 
   -4.1.1 [Crear una VPC](#Crear-una-VPC)
        -4.1.2 [Configurar grupos de seguridad](#Configurar-grupos-de-seguridad)
        -4.1.3 [Configurar ACL](#Configurar-ACL) 
 - 4.2 [Configuración de la Infraestructura en AWS](#configuración-de-la-infraestructura-en-aws)
 - 
     - 4.2.1 [Capa 1: Balanceador de Carga](#capa-1-balanceador-de-carga)
     - 4.2.2 [Capa 2: Servidores Web](#capa-2-servidores-web)
     - 4.2.3 [Capa 3: Servidor de Base de Datos](#capa-3-servidor-de-base-de-datos)
5. [Resultado](#Resultado)
6. [Conclusión](#Conclusión)

## Introducción

El propósito de esta práctica es configurar una infraestructura en AWS que incluya un servidor balanceador para distribuir peticiones a los servidores web en el back-end y a un servidor de datos que albergará un servidor MySQL. La aplicación web se implementará en los servidores back-end utilizando Apache y PHP, conectándose al servidor MySQL que ejecuta MariaDB.

## Objetivo

La infraestructura mejorará la seguridad al permitir que solo el balanceador tenga acceso a Internet.

## Infraestructura

La infraestructura de este proyecto consiste en un balanceador en la capa 1 expuesta a la red pública con dos servidores web en el back-end en la capa 2 (red privada), además de un servidor de base de datos en la capa 3 (red privada).

## Configuración Paso a Paso

### Pasos previos a la creacion de instancias

### Crear una VPC

En la consola de AWS buscamos VPC y creamos una VPC nueva.

![image](https://github.com/ArturoLucero28/BalanceadorDeCargaConApache/assets/146435794/2370997f-f926-4b77-9e58-e9de429004a8)

Elegimos VPC y MÁS por el hecho que nos facilita la creacion de subredes públicas y privadas.

### Configurar grupos de seguridad

Despues de crear la VPC creamos un grupo de seguridad dirigiéndonos al panel de búsqueda poniendo grupos de seguridad, allí crearemos 3 grupos de seguridad correspondientes a las 3 capas.

Colocamos el nombre y una descripcion, importante elegir la VPC que hemos creado previamente.
En la capa 1 nos interesa en la regla de entrada habilitar HTTP (Puerto 80) y HTTPS (Puerto 443):

![image](https://github.com/ArturoLucero28/BalanceadorDeCargaConApache/assets/146435794/456b3e12-578d-4a29-b32c-aa40fa8f1ef7)

(hay que colocar sus respectivas reglas de salida)

En la capa 2  colocamos las mismas reglas.

En la capa 3 nos interesa colocar en los puertas de entrada MYSQL (Puerto 3306).

![image](https://github.com/ArturoLucero28/BalanceadorDeCargaConApache/assets/146435794/61cc1f78-f947-44b9-961c-7dbc737773f2)

### Configurar ACL

Podemos ver como antes al darle a VPC y MÁS están ya creadas, podemos configurarlas a nuestro gusto para que sea mas eficiente colocando sus respectivas reglas de entrada y salida correspondiente.
### Configuración de la Infraestructura en AWS

![image](https://github.com/ArturoLucero28/BalanceadorDeCargaConApache/assets/146435794/2b65da82-4775-4d6d-92bf-68f6b9a35bc8)

-Crear las maquinas virtuales en "EC2".

#### Capa 1: Balanceador de Carga

En la capa 1 necesitaremos 1 instancia

![image](https://github.com/ArturoLucero28/BalanceadorDeCargaConApache/assets/146435794/eba22e65-1cb7-4426-b3de-a5bb03dd6128)

Nos conectamos desde el CMD por SSH a nuestra máquina.

![image](https://github.com/ArturoLucero28/BalanceadorDeCargaConApache/assets/146435794/cf567f8f-6c6b-4b10-bd9d-5a3de63576dd)


En primer lugar ponemos la siguiente secuencia de comandos para actualizar el sistema e instalar apache:

![image](https://github.com/ArturoLucero28/BalanceadorDeCargaConApache/assets/146435794/1ea42336-bbd4-468e-b9ae-71771e1a49e6)

Creamos en la siguiente dirección "/etc/apache2/sites-available/balanceador.conf" un archivo en el que pondremos lo siguiente:

![image](https://github.com/ArturoLucero28/BalanceadorDeCargaConApache/assets/146435794/f81b78ef-e2e7-412b-92c8-365e89162b09)

*(Te facilito el codigo para que puedas copiarlo en tu CMD:*

    <VirtualHost *:80>
    ServerAdmin webmaster@localhost
    DocumentRoot /var/www/html
    
    ErrorLog ${APACHE_LOG_DIR}/error.log
    CustomLog ${APACHE_LOG_DIR}/access.log combined

    ProxyRequests Off
    ProxyPreserveHost On

    <Proxy balancer://mycluster>
        BalancerMember http://Aquí colocad vuestra ip del servidor 1/
        BalancerMember http://Aquí colocad vuestra ip del servidor 2/
    </Proxy>

    ProxyPass / balancer://mycluster/
    ProxyPassReverse / balancer://mycluster/
    </VirtualHost>
)

Activamos lo que acabamos de configurar y reiniciamos el servidor apache.

![image](https://github.com/ArturoLucero28/BalanceadorDeCargaConApache/assets/146435794/14a42d0b-10f4-4b06-8e04-8edc9a852090)

*Posible error*

El proxy en mi caso estaba descativado y al reinciar el apache daba error, la solución sería activarlo manualmente con los siguientes comandos

sudo a2enmod proxy

sudo a2enmod proxy_http

Tambien añadimos para asegurarnos más adelante una ausencia de errores:

![image](https://github.com/ArturoLucero28/BalanceadorDeCargaConApache/assets/146435794/18fdc0cc-fda3-4638-a2be-05e15c618514)


Antes de seguir al siguiente paso, necesitas tener un host creado en My No-Ip y vamos a crear el host con la ip elástica que esta asociada previamente con la instancia.

![image](https://github.com/ArturoLucero28/BalanceadorDeCargaConApache/assets/146435794/7ecfb966-1a27-49c9-952a-5d6f05d937c2)

![image](https://github.com/ArturoLucero28/BalanceadorDeCargaConApache/assets/146435794/554bf1d6-7cc6-438c-a7cf-0e9291281b84)

A continuacion configuramos:

![image](https://github.com/ArturoLucero28/BalanceadorDeCargaConApache/assets/146435794/32e4c856-303f-469f-a418-88e611154035)


#### Capa 2: Servidores Web

En la capa 2 necesitaremos 2 instancias

![image](https://github.com/ArturoLucero28/BalanceadorDeCargaConApache/assets/146435794/b6ba9f56-6b91-49ec-addb-cb7b238f70f7)

Realiazamos la siguiente secuencia de comando(Actualizar el sistema e instalar git y apache):

![image](https://github.com/ArturoLucero28/BalanceadorDeCargaConApache/assets/146435794/a6da385f-3ea5-416a-a325-e7035e0531f8)

Clonamos el directorio github que necesitamos en el directoro "/var/www/html/" y clonamos este directorio:

sudo git clone https://github.com/josejuansanchez/iaw-practica-lamp

Luego le cambiamos el nombre a usuarios para que sea mas fácil trabajar con él.

![image](https://github.com/ArturoLucero28/BalanceadorDeCargaConApache/assets/146435794/c122146c-2098-4c5f-ad56-96c71cc8a368)

Tenemos que crear un archivo  en "sudo nano /etc/apache2/sites-available/apache1.conf" y copiar lo siguiente:

![image](https://github.com/ArturoLucero28/BalanceadorDeCargaConApache/assets/146435794/409ffa78-f5c2-44c3-9c70-264718fdb806)

Te lo facilito para que puedas copiarlo:

    <VirtualHost *:443>
    ServerAdmin webmaster@localhost
    DocumentRoot /var/www/html/usuarios/src
    ErrorLog ${APACHE_LOG_DIR}/error.log
    CustomLog ${APACHE_LOG_DIR}/access.log combined
    </VirtualHost>

Activamos la SSL y reiniciamos apache

![image](https://github.com/ArturoLucero28/BalanceadorDeCargaConApache/assets/146435794/ceb8bee5-853b-4a43-9896-82c3e6061e28)

Seguimos activando y volvemos a reinciar para instaurar los cambios:

![image](https://github.com/ArturoLucero28/BalanceadorDeCargaConApache/assets/146435794/8059f7ac-6a65-4af7-9672-fb1e6bd9e200)

*PARA LA SEGUNDA INSTANCIA REALIZAREMOS LOS MISMOS PASOS QUE EN LA PRIMERA*

(Vuelve aqui para empezar)

 [Capa 2: Servidores Web](#capa-2-servidores-web)

#### Capa 3: Servidor de Base de Datos

En la capa 3 necesitaremos una instancia

![image](https://github.com/ArturoLucero28/BalanceadorDeCargaConApache/assets/146435794/7c2277e6-b65c-4442-89e2-3f725e0c7071)

Instalamos maria db servidor y actualizamos previamente el sistema

![image](https://github.com/ArturoLucero28/BalanceadorDeCargaConApache/assets/146435794/b8e06199-0256-436f-94b1-9b838fe3abfc)

colocamos nuestra ip en el archivo sudo nano /etc/mysql/mariadb.conf.d/50-server.cnf

![image](https://github.com/ArturoLucero28/BalanceadorDeCargaConApache/assets/146435794/a5639669-41be-421c-bf38-740883fb04d2)

Pasamos la base de datos de las apache a nuestra maquina sql

![image](https://github.com/ArturoLucero28/BalanceadorDeCargaConApache/assets/146435794/6cadf34d-7306-4b47-b878-2aff9bcaa76b)

Un ejemplo de los comandos

![image](https://github.com/ArturoLucero28/BalanceadorDeCargaConApache/assets/146435794/b187a04b-17b1-419b-a82a-f62b1f1fc2cc)

![image](https://github.com/ArturoLucero28/BalanceadorDeCargaConApache/assets/146435794/155d87ba-f13a-422f-b1af-70bf4614633c)

![image](https://github.com/ArturoLucero28/BalanceadorDeCargaConApache/assets/146435794/db65bcc4-2c92-4f5b-8715-129975168977)

![image](https://github.com/ArturoLucero28/BalanceadorDeCargaConApache/assets/146435794/ab8072be-fb04-4836-bd67-ad075e1e99ed)



## Resultado
## Conclusión



