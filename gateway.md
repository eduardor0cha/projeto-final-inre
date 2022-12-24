# Configruação do servidor gateway

Antes de continuar, certifique-se de ter cumprido os passos descritos no arquivo [README.md](https://github.com/eduardor0cha/projeto-final-inre/blob/main/README.md)

---

## Configurar firewall

O primeiro passo para a configuração, é habilitar o firewall.

1. Garanta a permissão para SSH para não perder a conexão com a VM:

   ```bash
   sudo ufw allow ssh

   ```

2. Habilite o firewall:

   ```bash
   sudo ufw enable
   ```

3. Verifique se o firewall está de fato habilitado:

   ```bash
   sudo ufw status
   ```

   > Deve aparecer algo nessa linha:
   >
   > ```bash
   > Status: active
   >
   > To                         Action      From
   > --                         ------      ----
   > 22/tcp                     ALLOW       Anywhere
   > 22/tcp (v6)                ALLOW       Anywhere (v6)
   > ```

4. Agora, habilite o encaminhamento de pacotes, editando o arquivo `sysctl.conf` localizado na pasta `/etc/ufw` e descomente a linha **`net/ipv4/ip_forward=1`**:

   ```bash
   sudo nano /etc/ufw/systcl.conf
   ```

---

## Configurar arquivo de execução pós-boot

1. Adicione um script ao arquivo de `rc.local` localizado no diretório `/etc/` no seguinte formato:

   ```bash
   sudo nano /etc/rc.local
   ```

   ```bash
   #!/bin/bash

   # /etc/rc.local

   # Default policy to drop all incoming packets.
   # Politica padrão para bloquear (drop) todos os pacotes de entrada
   iptables -P INPUT DROP
   iptables -P FORWARD DROP

   # Accept incoming packets from localhost and the LAN interface.
   # Aceita pacotes de entrada a partir das interfaces localhost e the LAN.
   iptables -A INPUT -i lo -j ACCEPT
   iptables -A INPUT -i ens192 -j ACCEPT

   # Accept incoming packets from the WAN if the router initiated the connection.
   # Aceita pacotes de entrada a partir da WAN se o roteador iniciou a conexao
   iptables -A INPUT -i ens160 -m conntrack \
   --ctstate ESTABLISHED,RELATED -j ACCEPT

   # Forward LAN packets to the WAN.
   # Encaminha os pacotes da LAN para a WAN
   iptables -A FORWARD -i ens192 -o ens160 -j ACCEPT

   # Forward WAN packets to the LAN if the LAN initiated the connection.
   # Encaminha os pacotes WAN para a LAN se a LAN inicar a conexao.
   iptables -A FORWARD -i ens160 -o ens192 -m conntrack \
   --ctstate ESTABLISHED,RELATED -j ACCEPT

   # NAT traffic going out the WAN interface.
   # Trafego NAT sai pela interface WAN
   iptables -t nat -A POSTROUTING -o ens160 -j MASQUERADE

   #Recebe pacotes na porta 445 da interface externa do gw e encaminha para o servidor interno na porta 445
   iptables -A PREROUTING -t nat -i ens160 -p tcp --dport 445 -j DNAT --to 192.168.13.42:445
   iptables -A FORWARD -p tcp -d 192.168.13.42 --dport 445 -j ACCEPT

   #Recebe pacotes na porta 139 da interface externa do gw e encaminha para o servidor interno na porta 139
   iptables -A PREROUTING -t nat -i ens160 -p tcp --dport 139 -j DNAT --to 192.168.13.42:139
   iptables -A FORWARD -p tcp -d 192.168.13.42 --dport 139 -j ACCEPT

   #Recebe pacotes na porta 53 da interface externa do gw e encaminha para o servidor DNS Master interno na porta 53
   iptables -A PREROUTING -t nat -i ens160 -p udp --dport 53 -j DNAT --to 192.168.13.27:53
   iptables -A FORWARD -p udp -d 192.168.13.27 --dport 53 -j ACCEPT

   # rc.local needs to exit with 0
   # rc.local precisa sair com 0
   exit 0
   ```

2. Trasnforme o arquivo em executável:

   ```bash
   sudo chmod 755 /etc/rc.local
   ```

3. Reinicie a máquina para que o arquivo execute o script:

   ```bash
   sudo reboot
   ```

---

## Configurar máquinas que receberão o novo gateway

Na máquina que em que for definido o gateway, edite o arquivo `00-installer-config.yaml` localizado no diretório `/etc/netplan/`. Remova o gateway4 da interface `ens160` e adicione na interface `ens192` com o IP do gateway:

```bash
sudo nano /etc/netplan/00-installer-config.yaml
```

Exemplo:

```bash
# This is the network config written by 'subiquity'
network:
  renderer: networkd
  ethernets:
    ens160:
      dhcp4: false
      addresses: [10.9.13.108/24]
      nameservers:
         addresses:
           - 10.9.13.110
           - 10.9.13.112
    ens192:
      dhcp4: false
      addresses: [192.168.13.42/28]
      gateway4: 192.168.13.41
  version: 2
```

---

## Conclusão

Parabéns! Se tudo ocorreu como o planejado, a sua máquina já está desempenhando a função de gateway!
