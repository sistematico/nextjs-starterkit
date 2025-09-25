# Systemd Role

Esta role cria e gerencia serviços systemd de forma agnóstica, permitindo configuração flexível através de variáveis.

## Variáveis Disponíveis

### Configurações Básicas

| Variável | Valor Padrão | Descrição |
|----------|--------------|-----------|
| `service_description` | `"{{ domain_name }} Service"` | Descrição do serviço |
| `service_after` | `"network.target"` | Dependências do serviço |
| `service_type` | `"simple"` | Tipo do serviço systemd |
| `service_user` | `"nginx"` | Usuário que executa o serviço |
| `service_group` | `"{{ service_user }}"` | Grupo que executa o serviço |

### Configurações de Execução

| Variável | Valor Padrão | Descrição |
|----------|--------------|-----------|
| `service_environment` | `"NODE_ENV=production"` | Variáveis de ambiente |
| `service_working_directory` | `"/var/www/{{ domain_name }}"` | Diretório de trabalho |
| `service_exec_start` | `"/home/{{ service_user }}/.bun/bin/bun run start"` | Comando de inicialização |
| `service_exec_stop` | `"/usr/bin/kill -TERM $MAINPID"` | Comando de parada |

### Configurações de Restart

| Variável | Valor Padrão | Descrição |
|----------|--------------|-----------|
| `service_restart` | `"always"` | Política de restart |
| `service_restart_sec` | `"10s"` | Intervalo entre restarts |
| `service_start_limit_interval_sec` | não definido | Intervalo para limite de starts |
| `service_start_limit_burst` | não definido | Número máximo de starts por intervalo |

### Limites de Recursos

| Variável | Valor Padrão | Descrição |
|----------|--------------|-----------|
| `service_limit_nofile` | `"65535"` | Limite de arquivos abertos |
| `service_limit_nproc` | `"2048"` | Limite de processos |
| `service_memory_accounting` | `false` | Habilitar contabilização de memória |
| `service_memory_high` | não definido | Limite soft de memória |
| `service_memory_max` | não definido | Limite hard de memória |

### Configurações de Segurança

| Variável | Valor Padrão | Descrição |
|----------|--------------|-----------|
| `service_security_no_new_privileges` | `true` | Impedir novos privilégios |
| `service_security_private_tmp` | `true` | Usar /tmp privado |
| `service_security_protect_system` | `"full"` | Proteção do sistema |
| `service_security_protect_home` | `false` | Proteção do diretório home |
| `service_security_read_only_paths` | não definido | Caminhos somente leitura |

### Install Section

| Variável | Valor Padrão | Descrição |
|----------|--------------|-----------|
| `service_wants` | `"multi-user.target"` | Target de instalação |

## Exemplos de Uso

### Configuração Básica (Next.js)

```yaml
service_description: "Meu App Next.js"
service_user: "www-data"
service_working_directory: "/var/www/meuapp"
service_exec_start: "/usr/bin/node server.js"
```

### Configuração com Node.js e PM2

```yaml
service_description: "API Node.js"
service_user: "nodejs"
service_working_directory: "/opt/api"
service_exec_start: "/usr/local/bin/pm2-runtime start ecosystem.config.js"
service_environment: "NODE_ENV=production"
```

### Configuração com Limites de Memória

```yaml
service_memory_accounting: true
service_memory_high: "512M"
service_memory_max: "1G"
service_start_limit_interval_sec: "60"
service_start_limit_burst: "5"
```

### Configuração de Segurança Avançada

```yaml
service_security_protect_home: true
service_security_read_only_paths: "/etc /usr"
service_security_private_tmp: true
```

## Personalização por Projeto

Para usar este template em diferentes projetos, simplesmente defina as variáveis apropriadas no seu `group_vars/all.yml` ou `host_vars/` específico do projeto.

### Exemplo para Aplicação Python/Django

```yaml
service_description: "Django API Server"
service_user: "django"
service_group: "django"
service_working_directory: "/opt/django-app"
service_exec_start: "/opt/django-app/venv/bin/gunicorn --bind 127.0.0.1:8000 myapp.wsgi:application"
service_environment: "DJANGO_SETTINGS_MODULE=myapp.settings.production"
```

### Exemplo para Aplicação Go

```yaml
service_description: "Go Web Server"
service_user: "golang"
service_working_directory: "/opt/go-app"
service_exec_start: "/opt/go-app/bin/server"
service_environment: "GO_ENV=production"
```

## Estrutura de Arquivos

```
roles/systemd/
├── README.md
├── meta/
│   └── main.yml
├── tasks/
│   └── main.yml
├── handlers/
│   └── main.yml
└── templates/
    └── service.j2
```

## Funcionalidades da Role

### Tasks Principais

1. **Criação do Serviço**: Gera o arquivo de serviço systemd a partir do template
2. **Configuração de Sudoers**: Permite ao usuário do serviço gerenciá-lo sem senha
3. **Gerenciamento do Serviço**: Habilita e inicia o serviço automaticamente

### Handlers Disponíveis

- `reload systemd`: Recarrega os daemons do systemd
- `restart service`: Reinicia o serviço
- `start service`: Inicia o serviço
- `stop service`: Para o serviço
- `enable service`: Habilita o serviço
- `disable service`: Desabilita o serviço

### Variáveis de Gerenciamento

| Variável | Valor Padrão | Descrição |
|----------|--------------|-----------|
| `service_name` | `"{{ domain_name }}"` | Nome do arquivo de serviço |
| `service_enabled` | `true` | Se o serviço deve ser habilitado |
| `service_initial_state` | `"started"` | Estado inicial do serviço |
| `service_auto_restart` | `true` | Se deve reiniciar após mudanças |
| `service_sudoers_name` | `"{{ domain_name \| replace('.', '_') }}"` | Nome da regra sudoers |

Esta estrutura agnóstica permite reutilizar a role em qualquer projeto, simplesmente ajustando as variáveis conforme necessário.