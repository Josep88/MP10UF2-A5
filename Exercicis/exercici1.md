### Esquema d'instal·lació d'un MySQL Cluster amb  4 màquines virtuals amb 0,5/1 GB de RAM i CentOS Desactiva el firewall o permet la connexió als ports 3306, 4444, 4567, 4568
foto
### IPs de cada Node (X són els 2 últim dígits del DNI d'un dels components del grup.):
&nbsp;&nbsp;Node 1: nodeid = 1, hostname=percona1, IP=192.168.14.71
&nbsp;&nbsp;Node 2: nodeid = 2, hostname=percona2, IP=192.168.14.72
&nbsp;&nbsp;Node 3: nodeid = 3, hostname=percona3, IP=192.168.14.73
&nbsp;&nbsp;Node 4: nodeid = 4, hostname=percona4, IP=192.168.14.74

__CONFIGURACIÓ A TOTS ELS EQUIPS__  

Modifiquem el nom de cada host:  
>  ![1](https://raw.githubusercontent.com/Josep88/MP10UF2-A5/master/img/exercici1/1.PNG)  
>  ![1](https://raw.githubusercontent.com/Josep88/MP10UF2-A5/master/img/exercici1/2.PNG)  
  
Reiniciem, i després posem el selinux en permissive:  
>  ![1](https://raw.githubusercontent.com/Josep88/MP10UF2-A5/master/img/exercici1/4.PNG)  
  
Desactivem el firewall:  
>  ![1](https://raw.githubusercontent.com/Josep88/MP10UF2-A5/master/img/exercici1/5.PNG)  

Instal·lem el repositori:  
>  ![1](https://raw.githubusercontent.com/Josep88/MP10UF2-A5/master/img/exercici1/8.PNG)  
  
I instal·lem el cluster:  
>  ![1](https://raw.githubusercontent.com/Josep88/MP10UF2-A5/master/img/exercici1/3.PNG)  
>  ![1](https://raw.githubusercontent.com/Josep88/MP10UF2-A5/master/img/exercici1/7.PNG)  

Iniciem el servei de MySQL i agafem la contrasenya temporal del root amb la comanda:  
>systemctl start mysql  
>sudo grep 'temporary password' /var/log/mysqld.log  

Ara entrem al MySQL amb l'usuari root i aquesta contrasenya i modifiquem la contrasenya per la que volem:  
>ALTER USER 'root'@'localhost' IDENTIFIED BY 'rootPass';  
  
I tornem a parar el servei del MySQL:  
>systemctl stop mysql  
  
__CONFIGURACIÓ AL PRIMER NODE (Bootsapping the first node)__  
  
Fitxer /etc/percona-xtradb-cluster.conf.d/wsrep.cnf:  
>  ![1](https://raw.githubusercontent.com/Josep88/MP10UF2-A5/master/img/exercici1/6.PNG)  
  
Iniciem el primer node:  
>systemctl start mysql@bootstrap.service  

Això crea la variable wsrep_cluster_address iniciada. si la maquina es reinicia no cal tornar-ho a iniciar aixi.  
I ho podem comprovar per veure que el cluster s'ha bootstrapetjat:  
>  ![1](https://raw.githubusercontent.com/Josep88/MP10UF2-A5/master/img/exercici1/9.PNG)  
  
Hi ha que crear l'usuari per fer les transferències del state snapshot:  
>  ![1](https://raw.githubusercontent.com/Josep88/MP10UF2-A5/master/img/exercici1/10.PNG)  
  
__CONFIGURACIÓ ALs ALTRES NODES__  

Fitxer de configuració /etc/percona-xtradb-cluster.conf.d/wsrep.cnf:  
>  ![1](https://raw.githubusercontent.com/Josep88/MP10UF2-A5/master/img/exercici1/11.PNG)  
  
Iniciem el MySQL:  
>systemctl start mysql  
  
I comprovem que esta funcionant:  
>show status like 'wsrep%';
  
### Explica El Bootstrapping the first node i el concepte split-brain  
  
__BOOTSTRAPPING THE FIRST NODE__


__SPLIT-BRAIN__

  
***
[Torna enrere](https://github.com/Josep88/MP10UF2-A5)
