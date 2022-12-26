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

### Criação das zonas

* Crie a zona direta, modificiando o domínio grupox.turma913.ifalara.local de acordo com o grupo pertencente:

```bash
 $ sudo cp /etc/bind/db.empty /etc/bind/zones/db.grupox.turma913.ifalara.local
```

* Realize a criação da zona reversa, inserindo a zona reversa da rede 10.9.13: 

```bash
 $ sudo cp /etc/bind/db.127 /etc/bind/zones/db.10.9.13.rev
```

## Configuração das zonas

* Configure a zona direta db.grupox.turma913.ifalara.local , de acordo com as informações do seu domínio:

```bash
    $ sudo nano db.labredes.ifalarapiraca.local 
```
