### Escull un dels més coneguts (ProxySQL, HAProxy, MySQL Router,...), proposa una arquitectura i documenta la seva instal·lació. Apropita't dels nodes creats en aquesta activitat de clustering.
  
Poso la maquina del balancejador a la xarxa dels nodes del clúster.  
IP 192.168.14.75  
  
Instal·lem proxysql:  
>![1](https://raw.githubusercontent.com/Josep88/MP10UF2-A5/master/img/exercici2/1.PNG)   
  
Instal·lem repositori del percona-xtradb-cluster:  
>![1](https://raw.githubusercontent.com/Josep88/MP10UF2-A5/master/img/exercici2/2.PNG)   
  
Instal·lem percona-xtradb-cluster:  
>![1](https://raw.githubusercontent.com/Josep88/MP10UF2-A5/master/img/exercici2/3.PNG)   
>![1](https://raw.githubusercontent.com/Josep88/MP10UF2-A5/master/img/exercici2/4.PNG)   
  
Iniciem mysql i proxysql:  
> systemctl start mysql  
> systemctl start proxysql  
  
Fitxer de configuració /etc/proxysql-admin.cnf:  
>![1](https://raw.githubusercontent.com/Josep88/MP10UF2-A5/master/img/exercici2/6.PNG)   
  
Entrem amb aquestes credencials:  
> mysql -u admin -padmin -h 127.0.0.1 -P6032  
>![1](https://raw.githubusercontent.com/Josep88/MP10UF2-A5/master/img/exercici2/5.PNG)   
  
Podem veure les bases de dades i les taules del ProxySQL:  
>![1](https://raw.githubusercontent.com/Josep88/MP10UF2-A5/master/img/exercici2/7.PNG)   
  
Inserim els nodes del clúster al hostgrup del balancejador que els permetrà llegir i escriure el tràfic:  
>![1](https://raw.githubusercontent.com/Josep88/MP10UF2-A5/master/img/exercici2/8.PNG)   

Podem comprovar els nodes:  
> SELECT * FROM mysql_servers;  
>![1](https://raw.githubusercontent.com/Josep88/MP10UF2-A5/master/img/exercici2/9.PNG)   
  
Crear l'usuari amb el que entrarem tant al proxy...  
> INSERT INTO mysql_users (username,password) VALUES ('sbuser','sbpass');  

...Com a un dels nodes:  
>![1](https://raw.githubusercontent.com/Josep88/MP10UF2-A5/master/img/exercici2/10.PNG)   

Carreguem les dades al RUNTIME:  
> LOAD MYSQL USERS TO RUNTIME;  
> SAVE MYSQL USERS TO DISK;  
> LOAD MYSQL SERVERS TO RUNTIME;  
> SAVE MYSQL SERVERS TO DISK;  
> LOAD MYSQL QUERY RULES TO RUNTIME;  
> SAVE MYSQL QUERY RULES TO DISK;  
> LOAD MYSQL VARIABLES TO RUNTIME;  
> SAVE MYSQL VARIABLES TO DISK;  
  
> LOAD ADMIN VARIABLES TO RUNTIME;  
> SAVE ADMIN VARIABLES TO DISK;  
  
I ara ja podem entrar amb aquest usuari directament al ProxySQL i aquest ens autoredireccionarà al node que toca.  
  
### Configura el necesari per fer que les SELECTS vagin a un hostgroup i la resta de comandes a un altre.
  
Modifiquem 2 nodes del cluster a un altre hostgroup:
20

Comprovem si el hostgroup per defecte del usuari es correcte, sino els modifiquem:
22

I activem aquests canvis i els guardem per fer-los permanents:
21

Introduïm les regles que volem (una per mantenir les SELECT FOR UPDATE al hostgrup 0, i un altre per enviar totes les SELECT al hostgroup 1):
23

Ho comprovem:
24

I guardem els canvis per fer-los permanents:
25

Per tant, amb aquests canvis les rutes quedarien aixi:
1. Totes les SELECT FOR UPDATE al hostgroup 0.
2. Totes les altres SELECT al hostgroup 1.
3. La resta al hostgroup 1.
  
***
|[Anterior](https://github.com/Josep88/MP10UF2-A5/blob/master/Exercicis/exercici1.md)|[Inici](https://github.com/Josep88/MP10UF2-A5)|[Següent](https://github.com/Josep88/MP10UF2-A5/blob/master/Exercicis/webgrafia.md)|
|:-:|:-:|:-:|
