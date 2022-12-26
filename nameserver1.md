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

* Crie a zona direta, modificiando o domínio grupox.turma913.ifalara.local de acordo com o grupo pertencente:

```bash
 $ sudo cp /etc/bind/db.empty /etc/bind/zones/db.grupox.turma913.ifalara.local
```

* Realize a criação da zona reversa, inserindo a zona reversa da rede 10.9.13: 

```bash
 $ sudo cp /etc/bind/db.127 /etc/bind/zones/db.10.9.13.rev
```

---

### Configuração das zonas

* Configure a zona direta db.grupox.turma913.ifalara.local , de acordo com as informações do seu domínio:

```bash
    $ sudo nano db.labredes.ifalarapiraca.local 
```

* A configuração deve se assemelhar com o seguinte formato, fazendo valer as respectivas mudanças e inserções de cada domínio:
_Certifique-se de alterar o valor de serial para a data no formato ano-mês-dia, acrescida de dois dígitos 0 no final, além de mudar os endereços e os nomes de acordo com cada valor correspondente ao ENS160._

```
;
; BIND data file for internal network
;
$ORIGIN grupox.turma913.ifalara.local.
$TTL	3h
@	IN	SOA	ns1.grupox.turma913.ifalara.local. root.grupox.turma913.ifalara.local. (
			      2022122600	; Serial
			      3h	; Refresh
			      1h	; Retry
			      1w	; Expire
			      1h )	; Negative Cache TTL
;nameservers
@	IN	NS	ns1.grupox.turma913.ifalara.local.
@	IN	NS	ns2.grupox.turma913.ifalara.local.
;hosts
ns1.grupox.turma913.ifalara.local.	  IN	A	10.9.13.10
ns2.grupox.turma913.ifalara.local.	  IN	A	10.9.13.11
gw.grupox.turma913.ifalara.local.	  IN	A	10.9.13.100
www.grupox.turma913.ifalara.local.	  IN 	A	10.9.13.1   
bd.grupox.turma913.ifalara.local.	  IN 	A	10.9.13.1    
samba.grupox.turma913.ifalara.local.	  IN 	A	10.9.13.1    

```

* Configure a zona reversa, atentando-se aos mesmos detalhes, utilizando o seguinte comando:

```bash
    $ sudo nano db.10.9.13.rev
```

```
;
; BIND reverse data file of reverse zone for local area network 10.9.14.0/24
;
$TTL    604800
@       IN      SOA     grupox.turma913.ifalara.local. root.grupox.turma913.ifalara.local. (
                              2022122900         ; Serial
                         604800         ; Refresh
                          86400         ; Retry
                        2419200         ; Expire
                         604800 )       ; Negative Cache TTL

; name servers
@      IN      NS      ns1.grupox.turma913.ifalara.local.
@      IN      NS      ns2.grupox.turma913.ifalara.local.

; PTR Records
10   IN      PTR     ns1.grupox.turma913.ifalara.local.              ; 10.9.13.10
11   IN      PTR     ns2.grupox.turma913.ifalara.local.              ; 10.9.13.11
100  IN      PTR     dh1.grupox.turma913.ifalara.local.    	     ; 10.9.13.100
1    IN      PTR     gw.grupox.turma913.ifalara.local.               ; 10.9.13.1
```

---

