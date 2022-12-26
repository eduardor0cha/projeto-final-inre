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


