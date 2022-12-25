# Configuração do servidor DNS Slave (NS2)
Antes de continuar, certifique-se de ter cumprido os passos descritos no arquivo README.md

## Configuração e instalação do servidor DNS secundário (slave)
 
* Instale o bind9
 
  ```
  $ sudo apt-get install bind9 dnsutils bind9-doc -y
    ```

* Verifique o status do serviço

  ```
  $ sudo systemctl status bind9
    ```
 
* Caso não esteja, ative-o:


  ```
  $ sudo systemctl enable bind9
    ```
 
## Configuração de zonas
Edite o arquivo:

  ```
  $ sudo nano /etc/bind/named.conf.local
  ```

* Seguindo o padrão a seguir:

  _Atente-se às configurações realizadas na instalação do ns1 - Master. Não esquecer de definir o 'masters', que se refere, justamente, ao ns1._
   
  ```
  zone "grupo3.turma913.ifalara.local" {
   type slave;
   file "/etc/bind/zones/db.grupo3.turma913.ifalara.local";
   masters { 10.9.13.110; };
  };

  zone "13.9.10.in-addr.arpa" IN {
   type slave;
   file "/etc/bind/zones/db.10.9.13.rev";
   masters { 10.9.13.110; };
  };
  ˋˋˋ
 
### Cheque se não há erros de sintaxe.
Se após o seguinte comando não houver retorno, parabéns! Você concluiu a instalação do ns2.

```
  $ sudo named-checkconf
  ```
