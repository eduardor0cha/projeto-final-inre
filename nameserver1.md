# Configuração do servidor DNS Master(NS1)

Antes de continuar, certifique-se de ter cumprido os passos descritos no arquivo [README.md](https://github.com/eduardor0cha/projeto-final-inre/blob/main/README.md)

---

## Configuração e instalação do servidor DNS Master

* Instale o serviço Bind9 

```bash
 $ sudo apt-get install bind9 dnsutils bind9-doc 
```

* Agora, verifique se o serviço está ativo:

```bash
 $ sudo systemctl status bind9
```

* Para o armazenamento das zonas, utilize o seguinte comando para criar o diretório em questão:

```bash
 $ sudo mkdir /etc/bind/zones
```

---

### Criação das zonas

* Crie a zona direta, modificiando o domínio grupo3.turma913.ifalara.local de acordo com o grupo pertencente:

```bash
 $ sudo cp /etc/bind/db.empty /etc/bind/zones/db.grupo3.turma913.ifalara.local
```



* Realize a criação da zona reversa, inserindo na rede 10.9.13: 

```bash
 $ sudo cp /etc/bind/db.127 /etc/bind/zones/db.10.9.13.rev
```

---

### Configuração das zonas

* Configure a zona direta db.grupo3.turma913.ifalara.local , de acordo com as informações do seu domínio:

```bash
    $ sudo nano grupo3.turma913.ifalara.local
```

* A configuração deve se assemelhar com o seguinte formato, fazendo valer as respectivas mudanças e inserções de cada domínio:
_Certifique-se de alterar o valor de serial para a data no formato ano-mês-dia, acrescida de dois dígitos 0 no final, além de mudar os endereços e os nomes de acordo com cada valor correspondente ao ENS160. Atente-se ao fato de que a data, na checagem, deve sempre estar atualizada._

```
;
; BIND data file for internal network
;
$ORIGIN grupo3.turma913.ifalara.local.
$TTL	3h
@	IN	SOA	ns1.grupo3.turma913.ifalara.local. root.grupo3.turma913.ifalara.local. (
			      2022122600	; Serial
			      3h	; Refresh
			      1h	; Retry
			      1w	; Expire
			      1h )	; Negative Cache TTL
;nameservers
@	IN	NS	ns1.grupo3.turma913.ifalara.local.
@	IN	NS	ns2.grupo3.turma913.ifalara.local.
;hosts
ns1.grupox.turma913.ifalara.local.	  IN	A	10.9.13.110
ns2.grupox.turma913.ifalara.local.	  IN	A	10.9.13.112
gw.grupox.turma913.ifalara.local.	  IN	A	10.9.13.106
www.grupox.turma913.ifalara.local.	  IN 	A	10.9.13.215
bd.grupox.turma913.ifalara.local.	  IN 	A	10.9.13.216   
samba.grupox.turma913.ifalara.local.	  IN 	A	10.9.13.108

```

* Configure a zona reversa, atentando-se aos mesmos detalhes, utilizando o seguinte comando:

```bash
    $ sudo nano db.10.9.13.rev
```

```
;
; BIND reverse data file of reverse zone for local area network 10.9.13.0/24
;
$TTL    604800
@       IN      SOA     grupo3.turma913.ifalara.local. root.grupo3.turma913.ifalara.local. (
                              2022122600         ; Serial
                         604800         ; Refresh
                          86400         ; Retry
                        2419200         ; Expire
                         604800 )       ; Negative Cache TTL

; name servers
@      IN      NS      ns1.grupo3.turma913.ifalara.local.
@      IN      NS      ns2.grupo3.turma913.ifalara.local.

; PTR Records
110   IN      PTR     ns1.grupo3.turma913.ifalara.local.              ; 10.9.13.110
112   IN      PTR     ns2.grupo3.turma913.ifalara.local.              ; 10.9.13.112
106   IN      PTR     gw.grupo3.turma913.ifalara.local.    	      ; 10.9.13.106
215   IN      PTR     www.grupo3.turma913.ifalara.local.              ; 10.9.13.215
216   IN      PTR     bd.grupo3.turma913.ifalara.local.               ; 10.9.13.216
108   IN      PTR     samba.grupo3.turma913.ifalara.local.            ; 10.9.13.108
```

---

* Para informar onde os arquivos foram salvos, edite o named.conf.local: 

```bash
    $ sudo nano /etc/bind/named.conf.local
```
_Adicione as duas zonas, e edite-as de acordo com os domínios dispostos, como no exemplo a seguir:_
_O allow-transfer permite a conexão direta entre o DNS Master (DNS1) e DNS Slave. O transfer permite a conexão, onde se localiza o IP do NS2._
```
//
// Do any local configuration here
//

// Consider adding the 1918 zones here, if they are not used in your
// organization
//include "/etc/bind/zones.rfc1918";

zone "grupo3.turma913.ifalara.local" {
	type master;
	file "/etc/bind/zones/db.grupo3.turma913.ifalara.local";
	allow-transfer{ 10.9.13.112; };  
	allow-query{any;};
};

zone "13.9.10.in-addr.arpa" IN {
	type master;
	file "/etc/bind/zones/db.10.9.13.rev";
	allow-transfer{ 10.9.13.112; };
};

```

---

## Checagem 

*  Uma vez editados, cheque, agora, a sintaxe cada um dos arquivos, através, respectivamente, dos seguintes comandos:
_Com tais comandos, você poderá rever as alterações feitas em cada uma das zonas._

```bash
    $sudo named-checkconf
```

```bash
    $ cd /etc/bind/zones
    
$ sudo named-checkzone grupox.turma913.ifalara.local db.grupo3.turma913.ifalara.local
zone grupo3.turma913.ifalara.local/IN: loaded serial 1

$ sudo named-checkzone 13.9.10.in-addr.arpa db.10.9.13.rev
zone 13.9.10.in-addr.arpa/IN: loaded serial 1

```

---

* Agora, vamos editar os endereços. Para isso, edite o diretório /etc/default/named, e acrescente, no final, a linha _OPTONS= -4 -u bind_

```bash
    $sudo nano /etc/default/named
 ```

```bash
    # run resolvconf?
RESOLVCONF=no

# startup options for the server
OPTIONS="-4 -u bind"
```
* Após habilitar novamente o bind9, configure o dns nas respectivas formas ns1 e ns2.

```bash
                nameservers: 
                addresses:
                - 10.9.13.110
                - 10.9.13.112
                search: [grupo3.turma913.ifalara.local]
 ```
 
 * Edite o arquivo de configuração do netplan pelo comando:
 * 
 ```bash
    $ sudo nano /etc/netplan/50-cloud-init.yaml 
 ```
 
 _Configure o arquivo, de modo a se assemelhar com o seguinte, modificando os devidos endereços ns1 e ns2 de acordo com o ens160:_
 
  ```bash
    network:
    ethernets:
        enp0s3:                        # interface local
            addresses: [10.9.13.10/24]  # ip/mascara
            gateway4: 10.9.13.1         # ip do gateway
            dhcp4: false               # 'false' para conf. estatica 
            nameservers:               # servidores dns
                addresses:
                - 10.9.13.110            # ip do ns1
                - 10.9.13.112            # ip do ns2
                search: [grupo3.turma913.ifalara.local] # domínio
    version: 2
 ```

---

### Testes Finais

* Execute o comando a seguir, e verifique se os DNs estão corretos:

```bash
    $ systemd-resolve --status enp0s3
 ```
 
 * Se corretos, realize, por fim, o comando dig para as máquinas e os endereços respectivos do NS1 e NS2, como nos comandos a seguir:
 
 ```bash
    $ dig ns1.grupo3.turma913.ifalara.local
 ```
 
  ```bash
    $ dig -x 10.9.13.112
 ```
_Neste caso, o dig está sendo direcionado para o NS2._

---

Pronto! Você concluiu a instalação e configuração do DNS Master.
