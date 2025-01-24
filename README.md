# Instalação do ImunifyAV no HestiaCP

## Introdução

O **ImunifyAV** é um dos antivírus mais reconhecidos para servidores web Linux. Ele é capaz de escanear e limpar backdoors, shells, vírus, páginas de phishing, scripts maliciosos e outros tipos de malware. Alguns painéis, como Plesk, cPanel e CyberPanel, possuem integração direta com ele, mas também é possível instalá-lo em servidores sem painel de controle ou em painéis sem compatibilidade oficial.

Existem duas versões do ImunifyAV:
1. **Versão gratuita**: Apenas permite o escaneamento.
2. **Versões pagas**: Incluem licenças para limpeza automática de malware.

Este guia explica como instalar o ImunifyAV em servidores com o painel HestiaCP ou VestaCP, mas também pode ser adaptado para servidores sem painel de controle. Ele requer **PHP** e é compatível com os seguintes sistemas operacionais:

## Supported operating systems

- **CentOS/RHEL** 7, 8, 9
- **CloudLinux** OS 7, 8, 9
- **Ubuntu 16.04** (LTS only), 18.04, 20.04 (LTS), 22.04 (cPanel, Plesk, DirectAdmin, and standalone), and 24.04
- **Debian 9** (up to Imunify v6.11 (including)), 10 (requires buster-backports), 11 & 12 (Plesk, DirectAdmin, and stand-alone)
- **AlmaLinux 8,9**
- **Rocky Linux 8,9** (cPanel, Plesk, and standalone)
## Virtualization

- **OpenVZ** - works for Virtuozzo 7 with kernel 3.10.0-1160.80.1.vz7.191.4 or newer.

## Hardware

- **RAM: 1GB**
- **HDD: 20GB** available disk space
- **CPU: 64bit** version on x86_64 processors only

---

## Como instalar o ImunifyAV

### Passo 1: Baixar o instalador do repositório oficial
Execute o seguinte comando para baixar o instalador:
```bash
wget https://repo.imunify360.cloudlinux.com/defence360/imav-deploy.sh -O imav-deploy.sh
```

### Passo 2: Criar os diretórios necessários
Crie os diretórios de configuração e integração:
```bash
mkdir -p /etc/sysconfig/imunify360
```

### Passo 3: Criar o arquivo de integração
Edite o arquivo de integração com o seguinte comando:
```bash
vim /etc/sysconfig/imunify360/integration.conf
```
Adicione o seguinte conteúdo ao arquivo, substituindo "usuario" e "dominio.com" pelos valores correspondentes ao seu servidor:
```ini
[paths]
ui_path = /home/usuario/web/dominio.com/public_html/imav
ui_path_owner = usuario:usuario

[pam]
service_name = system-auth

[integration_scripts]
admins = /etc/sysconfig/imunify360/get-admins-script.sh
users = /etc/sysconfig/imunify360/get-users-script.sh
domains = /etc/sysconfig/imunify360/get-domains-script.sh
```

### Passo 4: Criar os arquivos de configuração

#### (a) Arquivo para administrador
Edite o arquivo para configurar o administrador:
```bash
vim /etc/sysconfig/imunify360/get-admins-script.sh
```
Adicione o seguinte conteúdo, substituindo os valores conforme necessário:
```json
{
  "data": [
    {
      "name": "root",
      "unix_user": "root",
      "locale_code": "PT_br",
      "email": "admin@dominio.com",
      "is_main": true
    }
  ],
  "metadata": {
    "result": "ok"
  }
}
```

#### (b) Arquivo para domínios
Edite o arquivo para configurar os domínios:
```bash
vim /etc/sysconfig/imunify360/get-domains-script.sh
```
Adicione a lista de domínios configurados no servidor:
```json
{
  "data": {
    "dominio.com": {
      "document_root": "/home/usuario/web/dominio.com",
      "is_main": true,
      "owner": "usuario"
    },
    "subdominio.dominio.com": {
      "document_root": "/home/usuario/web/subdominio.dominio.com/",
      "is_main": false,
      "owner": "usuario"
    }
  },
  "metadata": {
    "result": "ok"
  }
}
```

#### (c) Arquivo para usuários
Edite o arquivo para configurar os usuários:
```bash
vim /etc/sysconfig/imunify360/get-users-script.sh
```
Adicione os usuários relacionados aos domínios:
```json
{
  "data": [
    {
      "id": 1001,
      "username": "usuario",
      "owner": "usuario",
      "domain": "dominio.com",
      "package": {
        "name": "package",
        "owner": "usuario"
      },
      "email": "email@dominio.com",
      "locale_code": "PT_br"
    },
    {
      "id": 1001,
      "username": "usuario",
      "owner": "usuario",
      "domain": "subdominio.dominio.com",
      "package": {
        "name": "package",
        "owner": "usuario"
      },
      "email": "email@dominio.com",
      "locale_code": "PT_br"
    }
  ],
  "metadata": {
    "result": "ok"
  }
}
```

### Passo 5: Executar o instalador
Se você possui uma chave de licença, execute o seguinte comando, substituindo "sua_chave" pela chave adquirida:
```bash
bash imav-deploy.sh --key sua_chave
```
Para a versão gratuita, execute apenas:
```bash
bash imav-deploy.sh
```

### Passo 6: Acessar o painel do ImunifyAV
Após a instalação, você poderá acessar o painel através da URL configurada em `ui_path`, por exemplo Usuário: root, senha do root:
```
https://dominio.com/imav
```

---

## Notas finais

- Caso você adicione novos usuários no painel HestiaCP, eles serão automaticamente refletidos na interface web do ImunifyAV.
- Para servidores sem painel, este processo também é válido, com ajustes nas configurações conforme necessário.

- Se precisar de ajuda, entre em contato com o suporte do ImunifyAV ou o suporte do seu provedor.

  > Para mais informações, consulte a [página oficial do ImunifyAV](https://www.imunify360.com/).
