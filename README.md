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
    - 4.3 [Configuración de Certificado SSL](#configuración-de-certificado-ssl)
    - 4.6 [Despliegue de la Aplicación de Gestión de Usuarios](#despliegue-de-la-aplicación-de-gestión-de-usuarios)
    - 4.7 [Opcional: Despliegue de CMS](#opcional-despliegue-de-cms)
5. [Conclusión](#Conclusión)

## Introducción

El propósito de esta práctica es configurar una infraestructura en AWS que incluya un servidor balanceador para distribuir peticiones a los servidores web en el back-end y a un servidor de datos que albergará un servidor MySQL. La aplicación web se implementará en los servidores back-end utilizando Apache y PHP, conectándose al servidor MySQL que ejecuta MariaDB.

## Objetivo

La infraestructura mejorará la seguridad al permitir que solo el balanceador tenga acceso a Internet.

## Infraestructura

La infraestructura de este proyecto se trata de un balanceador en capa 1 expuesta a red pública con dos servidor web en Backend y capa 2 (red privada) ademas de un servidor de BBDD en capa 3 (red privada).

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

Activamos el proxy que hemos realizado y reiniciamos el servidor apache.

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


#### Capa 3: Servidor de Base de Datos

En la capa 3 necesitaremos una instancia

![image](https://github.com/ArturoLucero28/BalanceadorDeCargaConApache/assets/146435794/7c2277e6-b65c-4442-89e2-3f725e0c7071)


### Configuración de Certificado SSL

...

## Conclusión



