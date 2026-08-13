# SeederLinux

O SeederLinux automatiza a preparação e a personalização de estações Linux para ambientes corporativos. Ele monta bundles de instalação a partir de scripts Bash, grava as configurações no PostgreSQL e disponibiliza a gestão pelo painel web.

## Stack

- PHP para a API e o painel administrativo
- PostgreSQL para organizações, variáveis e scripts
- Bash para instalação e configuração das estações
- HTML, CSS e JavaScript para as interfaces

## Ordem oficial dos scripts

O bundle executa os scripts nesta ordem. Os três scripts de sessão são condicionais: somente o correspondente ao display manager da estação é incluído no bundle final.

| Ordem | Script | Responsabilidade |
|---:|---|---|
| 1 | `core_dns.sh` | DNS, NTP e hostname |
| 2 | `core_repositories.sh` | Repositórios APT |
| 3 | `core_packages.sh` | Pacotes base e dependências |
| 4 | `core_legados.sh` | Compatibilidade com sistemas legados |
| 5 | `core_apps.sh` | Aplicações corporativas |
| 6 | `core_domain.sh` | Integração com domínio AD |
| 7 | `core_ssh.sh` | Acesso e políticas SSH |
| 8 | `core_browser.sh` | Políticas de navegadores |
| 9 | `core_inventory.sh` | Agente OCS Inventory |
| 10 | `core_printers.sh` | CUPS e impressoras |
| 11 | `core_vnc.sh` | Acesso remoto VNC |
| 12 | `core_conky.sh` | Monitor Conky |
| 13 | `core_config.sh` | Configurações persistentes |
| 14 | `core_branding.sh` | Wallpaper, logo e tema |
| 15 | `core_logon.sh` | Ações de entrada do usuário |
| 16 | `core_password_change.sh` | Alteração de senha |
| 17 | `core_logoff.sh` | Ações de saída do usuário |
| 18 | `core_session_lightdm.sh` | Sessão LightDM |
| 19 | `core_session_gdm3.sh` | Sessão GDM3 |
| 20 | `core_session_sddm.sh` | Sessão SDDM |
| 21 | `core_agent.sh` | Agente SeederLinux |
| 22 | `core_proxy.sh` | Proxy do sistema |

## Instalação

1. Instale PHP com os módulos de PostgreSQL, PostgreSQL, Bash, Git, cURL e `jq`.
2. Crie o banco PostgreSQL e aplique `install/schema.sql`.
3. Configure a conexão do banco no arquivo de configuração do servidor.
4. Publique o conteúdo do projeto no servidor web com suporte a PHP.
5. Acesse `login.html` para entrar no painel.
6. Crie ou selecione uma organização, preencha as variáveis e gere o bundle.

As informações específicas de infraestrutura, virtual host, permissões e operação ficam em [SERVIDOR.md](SERVIDOR.md).

## Gerar o catálogo de scripts

O catálogo inicial é gerado a partir dos arquivos em `scripts/core/`:

```bash
python3 install/gen_insert_core.py
```

Isso recria `install/insert_core_scripts.sql` com os 22 scripts e a ordem oficial. Depois, aplique o SQL no banco de instalação ou use o procedimento de sincronização descrito em `SERVIDOR.md`.

## Gerar um bundle

1. Entre no painel administrativo.
2. Selecione a organização.
3. Confira as variáveis obrigatórias, incluindo as senhas em base64 quando indicado pelo formulário.
4. Gere e baixe o bundle.

O sistema bloqueia a geração quando uma variável cadastrada deixa um placeholder obrigatório sem valor. Placeholders que aparecem apenas em comentários e não pertencem ao catálogo de variáveis são permitidos.

## Sincronizar scripts do GitHub

A sincronização atualiza no banco o conteúdo dos scripts versionados. O servidor pode ser informado por variável de ambiente ou por argumento:

```bash
SEEDER_SERVER=https://seu-servidor.example ./install/seeder-sync-scripts.sh
./install/seeder-sync-scripts.sh --server https://seu-servidor.example
```

O valor padrão operacional e os requisitos do servidor estão documentados em `SERVIDOR.md`.

## Estrutura principal

- `api/` — API PHP
- `assets/` — estilos, imagens e JavaScript
- `install/` — schema, gerador e scripts de instalação
- `scripts/core/` — scripts que compõem os bundles
- `downloads/` — agente distribuído às estações
- `SERVIDOR.md` — operação e infraestrutura
- `tests/` — testes automatizados
