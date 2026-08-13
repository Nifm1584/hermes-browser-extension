# Hermes Browser Extension — Fork Nadigital / Nifm1584

> Fork próprio do projeto oficial `abundantbeing/hermes-browser-extension` para uso na infraestrutura Hermes remota.

## 1. Identificação do fork

| Campo | Valor |
|---|---|
| Repositório upstream | https://github.com/abundantbeing/hermes-browser-extension |
| Nosso fork | https://github.com/Nifm1584/hermes-browser-extension |
| Branch principal | `main` |
| Versão inicial preservada | `v0.2.0` |
| Commit inicial upstream | `5e93ae3c2d73aff138d17f1cc5419488fb4a0046` — 2026-08-12 |
| Histórico upstream | Preservado integralmente |
| Licença upstream | MIT |

## 2. Objetivo deste fork

1. Manter uma cópia própria e independente do projeto oficial.
2. Preservar o código caso o upstream seja removido, arquivado ou alterado de forma incompatível.
3. Permitir customizações futuras específicas para a arquitetura Hermes.
4. Continuar acompanhando o desenvolvimento do upstream.
5. Incorporar seletivamente atualizações relevantes do upstream.
6. Manter rastreabilidade clara entre nossa versão e o projeto original.

## 3. Arquitetura planejada

Cenário de produção alvo:

```
Chrome/Edge (máquina local)
  → Hermes Browser Extension
    → conexão segura
      → Hermes Gateway/API remoto
        → Hermes Agent na VPS
          → MCPs, skills, ferramentas, GitHub, Docker, Portainer...
```

A extensão funciona como ponte entre:
- **Navegador local** no computador do usuário
- **Hermes remoto** hospedado na VPS

O objetivo é enviar o contexto do navegador local para o Hermes remoto via Remote Gateway.

## 4. Modo Remote Gateway

Documentação oficial do modo remoto:

> When you configure a remote Gateway URL and API key/browser token, context is sent to that remote Hermes API server. Same-LAN or private VPN hosts can use `http://host:8642`; public/proxied hosts should use `https://`. Set `API_SERVER_ENABLED=true`, `API_SERVER_HOST=0.0.0.0`, `API_SERVER_KEY`, and a narrow `API_SERVER_CORS_ORIGINS=chrome-extension://<extension-id>` on the Hermes host. Do not expose a Hermes API server naked to the public internet.

Requisitos do Remote Gateway na VPS:
- `API_SERVER_ENABLED=true`
- `API_SERVER_HOST=0.0.0.0`
- `API_SERVER_KEY=<token seguro>`
- `API_SERVER_CORS_ORIGINS=chrome-extension://<extension-id>`
- HTTPS obrigatório quando exposto externamente
- Nunca expor API do Hermes diretamente à internet sem proteção

## 5. Segurança — regras preservadas do upstream

### 5.1 Permissões mantidas

| Permissão | Motivo |
|---|---|
| `activeTab` | Inspecionar aba ativa após usuário abrir a extensão |
| `downloads` | Salvar imagens/artefatos gerados somente após ação explícita |
| `scripting` | Injetar content script quando necessário |
| `sidePanel` | Renderizar o painel lateral |
| `storage` | Armazenar configurações locais |
| `tabs` | Ler títulos/URLs de abas abertas |

### 5.2 Permissões NÃO adicionadas (primeira etapa)

- `debugger`
- `nativeMessaging`
- `cookies`
- `history`
- `bookmarks`
- Permissões de controle autônomo do navegador

### 5.3 Proteções preservadas

- Contexto da página tratado como **untrusted**
- Redação de tokens, API keys, credenciais e informações sensíveis
- Bloqueio de páginas sensíveis: `chrome://`, `edge://`, `devtools://`, bancos, carteiras crypto, password managers, checkout/payment, health, government tax
- Credential-bearing tab URLs omitidas dos prompts
- Conteúdo da página wrapped como untrusted context
- Hermes Assist não clica Send/Post/Submit, não navega, não opera autonomamente
- API key/browser token armazenado em `chrome.storage.local` com máscara UI e botão "Clear stored token"

## 6. O que permanece igual ao upstream

Nesta primeira etapa, **nenhuma alteração funcional** foi realizada:
- Estrutura de diretórios idêntica
- Manifesto e permissões preservados
- Código fonte sem modificações
- Documentação original intacta
- Histórico Git completo
- Workflows CI/CD existentes preservados

## 7. O que poderá ser customizado futuramente

Etapas futuras a serem analisadas separadamente:
- Integração específica com a VPS nilto / Portainer / Docker Swarm
- Ajustes no Remote Gateway para nossa infraestrutura Traefik
- Customizações de tema/aparência alinhadas à identidade Nadigital
- Pontos de extensão para ferramentas específicas do nosso Hermes
- Quaisquer alterações envolvendo `debugger`, CDP, `nativeMessaging`, automação de cliques/navegação

## 8. Estratégia de sincronização com upstream

### Comandos locais recomendados

```bash
# 1. Clonar nosso fork
cd /opt/data
git clone https://github.com/Nifm1584/hermes-browser-extension.git
cd hermes-browser-extension

# 2. Registrar upstream
git remote add upstream https://github.com/abundantbeing/hermes-browser-extension.git

# 3. Buscar alterações do upstream
git fetch upstream

# 4. Atualizar branch main a partir do upstream
git checkout main
git merge upstream/main

# 5. Resolver conflitos mantendo nossas customizações
# 6. Push para nosso fork
git push origin main
```

### Política para incorporar atualizações

1. Avaliar changelog e releases do upstream antes de atualizar
2. Identificar mudanças relevantes para nossa arquitetura
3. Fazer merge seletivo, não blind rebase
4. Registrar em `SYNC-LOG.md` quais alterações foram incorporadas
5. Testar em ambiente de homologação antes de promover para produção
6. Nunca modificar o upstream original

### Critérios de atualização

- Security patches: sempre incorporar imediatamente
- Mudanças em Remote Gateway: avaliar com cuidado
- Novas permissões: bloquear até análise explícita
- Mudanças funcionais: analisar caso a caso

## 9. Manutenção local/offline

```bash
# Clone local com tracking de upstream
git clone https://github.com/Nifm1584/hermes-browser-extension.git hermes-browser-extension-nadigital
cd hermes-browser-extension-nadigital
git remote add upstream https://github.com/abundantbeing/hermes-browser-extension.git
```

Manter cópia local em `/opt/data/hermes-browser-extension-nadigital`.

## 10. Próximos passos recomendados

1. Testar o fork no Chrome/Edge com load unpacked
2. Configurar conexão com Remote Gateway na VPS
3. Validar fluxo de envio de contexto do navegador para Hermes remoto
4. Avaliar necessidade de customizações específicas
5. Estabelecer processo de sync periódico com upstream

---

**Nota**: Esta é a base do nosso fork. Nenhuma alteração funcional foi realizada no código do projeto original. Toda customização futura será documentada separadamente e seguirá a arquitetura definida.
