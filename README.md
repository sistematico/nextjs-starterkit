# Next.js VPS Starter Kit

Um template completo para deploy de aplicações Next.js em VPS usando Ansible, systemd e Nginx como proxy reverso.

![Next.js](https://img.shields.io/badge/Next.js-15.5.4-black?logo=next.js)
![TypeScript](https://img.shields.io/badge/TypeScript-5.x-blue?logo=typescript)
![Tailwind CSS](https://img.shields.io/badge/Tailwind-4.x-38bdf8?logo=tailwindcss)
![Bun](https://img.shields.io/badge/Bun-Runtime-f472b6?logo=bun)

## 🚀 Características

- **Next.js 15** com TypeScript e Tailwind CSS v4
- **Ansible** para provisionamento automatizado
- **Nginx** como proxy reverso com SSL/TLS
- **Systemd** para gerenciamento de serviços
- **Bun** como runtime de produção
- **Biome** para linting e formatação
- **Scripts de deploy** automatizados

## 📁 Estrutura do Projeto

```
nextjs-starterkit/
├── src/app/              # Aplicação Next.js
├── ansible/              # Configuração de infraestrutura
│   ├── roles/
│   │   ├── nginx/        # Proxy reverso e SSL
│   │   ├── ssl-certificates/  # Certificados Let's Encrypt
│   │   └── systemd/      # Gerenciamento de serviços
│   └── main.yml          # Playbook principal
├── scripts/              # Scripts de automação
└── public/               # Assets estáticos
```

## 🛠️ Pré-requisitos

### Local
- **Node.js** 18+ ou **Bun** 1.0+
- **Ansible** 2.9+

### VPS
- **Ubuntu 20.04+** ou **Debian 11+**
- **Acesso SSH** com sudo
- **Domínio** apontando para o servidor

## ⚡ Início Rápido

### 1. Clone o Template

```bash
git clone <seu-repo> meu-projeto-nextjs
cd meu-projeto-nextjs
```

### 2. Instalar Dependências

```bash
# Com Bun (recomendado)
bun install

# Ou com npm
npm install
```

### 3. Configurar Variáveis

Edite `ansible/group_vars/all.yml`:

```yaml
# Configuração do site
domain_name: "meudominio.com"
ssl_certificate_email: "seu@email.com"

# Configuração da aplicação
app_port: 3000

# Configuração do serviço
service_user: "nginx"
service_working_directory: "/var/www/{{ domain_name }}"
```

### 4. Executar Desenvolvimento

```bash
bun dev
# ou
npm run dev
```

Acesse [http://localhost:3000](http://localhost:3000)

## 🚀 Deploy em Produção

### 1. Configurar Inventário

Crie ou edite `ansible/inventory` ou use host direto:

```bash
# Editar scripts/ansible.sh
MACHINE="seu-servidor"  # IP ou hostname
```

### 2. Provisionar Servidor

```bash
./scripts/ansible.sh
```

Este comando irá:
- ✅ Instalar e configurar Nginx
- ✅ Configurar certificados SSL com Let's Encrypt
- ✅ Criar serviço systemd
- ✅ Configurar proxy reverso
- ✅ Aplicar configurações de segurança

### 3. Deploy da Aplicação

```bash
./scripts/deploy.sh
```

Este script:
- ✅ Faz build da aplicação
- ✅ Para o serviço atual
- ✅ Atualiza os arquivos
- ✅ Reinicia o serviço

## 🔧 Configuração Avançada

### Variáveis do Nginx

```yaml
# ansible/group_vars/all.yml
nginx_ssl_protocols: "TLSv1.2 TLSv1.3"
nginx_ssl_ciphers: "ECDHE-ECDSA-AES128-GCM-SHA256:..."
```

### Configurações do Systemd

```yaml
# Limites de recursos
service_limit_nofile: "65535"
service_limit_nproc: "2048"

# Segurança
service_security_no_new_privileges: true
service_security_private_tmp: true
service_security_protect_system: "full"

# Memória (opcional)
service_memory_accounting: true
service_memory_high: "800M"
service_memory_max: "1G"
```

### SSL/TLS

```yaml
ssl_certificate_staging: false  # true para teste
ssl_certificate_force_renewal: false
certbot_auto_renew: true
```

## 📜 Scripts Disponíveis

```bash
# Desenvolvimento
bun dev          # Servidor de desenvolvimento com Turbopack
bun build        # Build de produção
bun start        # Servidor de produção

# Qualidade de código
bun lint         # Verificar código com Biome
bun format       # Formatar código

# Deploy
./scripts/ansible.sh    # Provisionar servidor
./scripts/deploy.sh     # Deploy da aplicação
```

## 🔒 Segurança

O template inclui:
- **SSL/TLS automático** via Let's Encrypt
- **Headers de segurança** no Nginx
- **Isolamento de processos** no systemd
- **Configurações de firewall** (opcional)
- **Usuário não-privilegiado** para execução

## 🌐 Nginx como Proxy Reverso

O Nginx é configurado para:
- Servir assets estáticos diretamente
- Fazer proxy das rotas dinâmicas para Next.js
- Gerenciar certificados SSL automaticamente
- Aplicar headers de segurança
- Comprimir responses (gzip/brotli)

## 📊 Monitoramento

Comandos úteis no servidor:

```bash
# Status do serviço
sudo systemctl status meudominio.com

# Logs da aplicação
sudo journalctl -u meudominio.com -f

# Status do Nginx
sudo systemctl status nginx

# Renovação SSL
sudo certbot certificates
```

## 🔄 Atualizações

### Atualizar Aplicação

```bash
git pull origin main
./scripts/deploy.sh
```

### Atualizar Configuração do Servidor

```bash
# Editar ansible/group_vars/all.yml
./scripts/ansible.sh
```

## 📝 Personalização

### Para Diferentes Frameworks

O template pode ser adaptado para:
- **Nuxt.js**: Alterar `service_exec_start`
- **SvelteKit**: Modificar comandos de build
- **Remix**: Ajustar configurações de proxy

### Para Diferentes Bancos de Dados

Adicione roles do Ansible para:
- PostgreSQL
- MongoDB  
- Redis
- MySQL

## 🆘 Solução de Problemas

### Aplicação não inicia

```bash
sudo journalctl -u meudominio.com -n 50
```

### SSL não funciona

```bash
sudo certbot certificates
sudo certbot renew --dry-run
```

### Nginx retorna 502

```bash
sudo nginx -t
sudo systemctl reload nginx
```

## 📚 Documentação Adicional

- [Next.js Documentation](https://nextjs.org/docs)
- [Ansible Documentation](https://docs.ansible.com/)
- [Nginx Documentation](https://nginx.org/en/docs/)
- [Let's Encrypt Documentation](https://letsencrypt.org/docs/)

## 🤝 Contribuindo

1. Fork o projeto
2. Crie uma branch para sua feature
3. Commit suas mudanças
4. Push para a branch
5. Abra um Pull Request

## 📄 Licença

Este projeto está sob a licença MIT. Veja o arquivo `LICENSE` para mais detalhes.

## 🙋‍♂️ Suporte

Se você encontrar problemas ou tiver dúvidas:
1. Verifique a [seção de troubleshooting](#-solução-de-problemas)
2. Abra uma [issue no GitHub](../../issues)
3. Consulte a documentação das ferramentas utilizadas

---

⭐ **Se este template foi útil, considere dar uma estrela no repositório!**
