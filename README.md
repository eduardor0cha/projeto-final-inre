# Projeto Final da Disciplina de Serviço de Redes

Antes de executar os passos desse tutorial, atente-se a fazer todos os passos desse arquivo (README.md) em todas as máquinas que participarão da rede. As configurações específicas para cada máquina ficarão em arquivos separados, e esses estarão linkados ao final dessa página.

---

# Equipe

- Carlos Eduardo Rocha Nunes
- David da Silva Feitosa Junior
- Giancarlo Lúcio do Nascimento
- Giselle Batista Costa

---

## Requisitos mínimos

Nesse projeto, foram utilizadas máquinas virtuais com o sistema operacional Ubuntu 20.04.5 LTS, o qual possui como requisitos **mínimos**:

- Processador: Dual-Core (dois núcleos) 1.0GHz ou superior;
- Memória RAM: 1GB (32 bits); 2GB ou superior (64 bits);
- Espaço livre em disco: 7,0GB (para instalação do sistema);
- Unidade de CD/DVD ou uma porta USB para mídia de instalação;
- Recursos gráficos: tenha uma placa de vídeo on-board ou off-board com, no mínimo, 128MB de memória dedicada para rodar o Unity e os elementos gráficos disponíveis sem "sofreguidão";
- Acesso à internet para atualização opcional do sistema (poupa tempo depois da instalação).

Fonte: [Manual do Ubuntu/Requisitos mínimos do sistema](https://pt.wikibooks.org/wiki/Manual_do_Ubuntu/Requisitos_mínimos_do_sistema)

---

## Definição de IPs e nomes

A tabelas indicam os IPs, nomes e domínio utilizados nesse projeto.

| IP de subrede WAN | IP de subrede LAN | IP de broadcast WAN | IP de broadcast LAN | Domínio (zona)                |
| ----------------- | ----------------- | ------------------- | ------------------- | ----------------------------- |
| 10.9.13.0/24      | 192.168.13.32/28  | 10.9.13.255/24      | 192.168.13.47/28    | grupo3.turma913.ifalara.local |

| VM    | IP rede WAN | IP rede LAN   | FQDN                              |
| ----- | ----------- | ------------- | --------------------------------- |
| GW    | 10.9.13.106 | 192.168.13.41 | gw.grupo3.turma913.ifalara.local  |
| SAMBA | 10.9.13.108 | 192.168.13.42 | smb.grupo3.turma913.ifalara.local |
| NS1   | 10.9.13.110 | 192.168.13.43 | ns1.grupo3.turma913.ifalara.local |
| NS2   | 10.9.13.112 | 192.168.13.44 | ns2.grupo3.turma913.ifalara.local |
| WEB   | 10.9.13.215 | 192.168.13.45 | www.grupo3.turma913.ifalara.local |
| BD    | 10.9.13.216 | 192.168.13.46 | bd.grupo3.turma913.ifalara.local  |

---

## Configuração nas interfaces de rede

Edite o arquivo `00-installer-config.yaml` localizado no diretório `/etc/netplan/`, e deixe-o no seguinte formato:

```
# This is the network config written by 'subiquity'
network:
  renderer: networkd
  ethernets:
    ens160:
      dhcp4: false
      addresses: [<endereço WAN da máquina>/<máscara>]
      gateway4: <endereço do gateway da rede WAN>
      nameservers:
         addresses: [<lista de endereços de nameserver>]
         search: [<domínio>]
    ens192:
      dhcp4: false
      addresses: [<endereço LAN da máquina>/<máscara>]
      #nameservers:
      #   addresses:
      #     -
      #     -
      #   search: []
  version: 2
```

- Exemplo:

> ```
> # This is the network config written by 'subiquity'
> network:
>   renderer: networkd
>   ethernets:
>     ens160:
>       dhcp4: false
>       addresses: [10.9.13.106/24]
>       gateway4: 10.9.13.1
>       nameservers:
>          addresses: [10.9.13.110, 10.9.13.112]
>          search: [grupo3.turma913.ifalara.local]
>     ens192:
>       dhcp4: false
>       addresses: [192.168.13.41/28]
>       #gateway4:
>       #nameservers:
>       #   addresses:
>       #     -
>       #     -
>       #   search: []
>   version: 2
> ```

Uma vez editado o arquivo, salve e aplique as mudanças:

```bash
sudo netplan apply
```

---

## Definição dos FQDNs

Em cada máquina, defina o seu respectivo FQDN através do comando:

```bash
sudo hostnamectl set-hostname <fqdn>
```

- Exemplo:

> ```bash
> sudo hostnamectl set-hostname gw.grupo3.turma913.ifalara.local
> ```

---

## Definição específica de cada máquina

A depender da função designada para cada máquina, siga o tutorial de configuração adequado:

- [Gateway](https://github.com/eduardor0cha/projeto-final-inre/blob/main/gateway.md)
- [SAMBA](https://github.com/eduardor0cha/projeto-final-inre/blob/main/samba.md)
- [Nameserver 1]()
- [Nameserver 2](https://github.com/eduardor0cha/projeto-final-inre/blob/main/nameserver2.md)
