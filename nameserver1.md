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
ns1.grupox.turma913.ifalara.local.	  IN	A	10.9.14.10
ns2.grupox.turma913.ifalara.local.	  IN	A	10.9.14.11
dh1.grupox.turma913.ifalara.local.	  IN	A	10.9.14.100
gw.grupox.turma913.ifalara.local.	  IN 	A	10.9.14.1          
desktophost1    CNAME     dh1                 ; CNAME é um apelido
```
