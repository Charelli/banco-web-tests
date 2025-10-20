# Banco Web - Testes Automatizados com Cypress

Projeto de exemplo usado na Mentoria 2.0 para demonstrar como automatizar testes end-to-end de uma aplicação web com Cypress e JavaScript.

## Objetivo

Mostrar aos alunos da Mentoria 2.0 como organizar e implementar automação de testes com Cypress, incluindo:
- Estrutura de testes (specs)
- Reutilização com Custom Commands
- Geração de relatórios com `cypress-mochawesome-reporter`
- Integração com uma API e a aplicação web em desenvolvimento

Este projeto contém exemplos práticos de login e transferência bancária para fins didáticos.

## Componentes do projeto

- `cypress/` - pasta principal dos testes
  - `e2e/` - especificações de teste (ex.: `login.cy.js`, `transferencias.cy.js`)
  - `fixtures/` - dados estáticos usados nos testes (ex.: `credenciais.json`)
  - `support/` - configurações e comandos customizados
    - `commands/` - comandos organizados por funcionalidade (`common.js`, `login.js`, `transferencia.js`)
    - `commands.js` - importa os comandos customizados
    - `e2e.js` - arquivo de suporte carregado antes dos testes (registra o reporter)
- `cypress.config.js` - configuração do Cypress (baseUrl, reporter etc.)
- `package.json` - scripts e dependências do projeto
- `reports/` - relatórios gerados (quando configurado)
- `screenshots/` - capturas de tela geradas pelos testes

## Pré-requisitos

- Node.js (recomendado versão LTS)
- npm ou yarn
- Repositórios para execução dos testes:
  - API: https://github.com/juliodelimas/banco-api
  - Aplicação web: https://github.com/juliodelimas/banco-web

Observação: os testes esperam que a aplicação web esteja disponível em `http://localhost:4000` (configurado em `cypress.config.js`). A API indicada também deve estar disponível localmente se for usada pelos testes.

## Instalação

1. Clone este repositório e entre na pasta do projeto:

```powershell
git clone https://github.com/Charelli/banco-web-tests.git
cd banco-web-tests
```

2. Instale as dependências:

```powershell
npm install
```

3. Suba a API e a aplicação web necessárias (ver links acima). Por exemplo, caso você precise rodá-las localmente, siga as instruções dos repositórios `banco-api` e `banco-web`.

## Scripts úteis (no `package.json`)

- `npm test` ou `npm run test` - executa os testes em modo headless
- `npm run test-prod` - (alias para `cypress run`) execução em modo headless
- `npm run cy:headed` - executa os testes em modo headed (com navegador visível)
- `npm run cy:open` - abre a UI do Cypress (Test Runner)

## Como executar os testes

1. Certifique-se de que a aplicação web esteja rodando em `http://localhost:4000`.
2. Executar todos os testes em modo headless:

```powershell
npm test
```

3. Executar em modo com navegador visível:

```powershell
npm run cy:headed
```

4. Abrir o Test Runner interativo:

```powershell
npm run cy:open
```

## Relatórios

O projeto usa `cypress-mochawesome-reporter`. A configuração do reporter está em `cypress.config.js` e o plugin é registrado no `setupNodeEvents`.

Após a execução com `cypress run` o reporter gera relatórios (HTML/JSON) na pasta padrão configurada pelo plugin. Verifique a pasta `reports/` para o relatório HTML já incluído no repositório de exemplo.

## Estrutura e descrição dos testes

- `cypress/e2e/login.cy.js`
  - Cenários:
    - Login com dados válidos: preenche usuário e senha válidos (usando `fixtures/credenciais.json`) e verifica que a página de transferência é exibida.
    - Login com dados inválidos: tenta logar com credenciais inválidas e verifica a mensagem de erro exibida no toast.

- `cypress/e2e/transferencias.cy.js`
  - Cenários:
    - Transferência válida: faz login e realiza uma transferência entre contas (ex.: "Maria" -> "João") e verifica a mensagem de sucesso.
    - Transferência acima do limite sem token: tenta transferir valor maior que R$5.000,00 e verifica que é exibida a mensagem solicitando autenticação.

Screenshots de exemplo são salvos na pasta `screenshots/` durante as execuções (os arquivos já presentes são apenas exemplos).

## Custom Commands (comandos personalizados)

Os comandos customizados foram organizados em `cypress/support/commands/` para facilitar a reutilização e leitura:

- `cypress/support/commands/common.js`
  - Cypress.Commands.add('verificarMensagemNoToast', mensagem)
    - Verifica que o elemento `.toast` contém exatamente o texto informado.
    - Uso: `cy.verificarMensagemNoToast('Transferência realizada!')`

  - Cypress.Commands.add('selecionarOpcaoNaComboBox', (labelDoCampo, opcao))
    - Seleciona uma opção em um controle que tem um `label` com `for` apontando para o campo.
    - Uso: `cy.selecionarOpcaoNaComboBox('conta-origem', 'Maria')`

- `cypress/support/commands/login.js`
  - Cypress.Commands.add('fazerLoginComCrendenciaisValidas')
    - Faz login usando as credenciais válidas do fixture `credenciais.json`.
    - Uso: `cy.fazerLoginComCrendenciaisValidas()`

  - Cypress.Commands.add('fazerLoginComCredenciaisInvalidas')
    - Tenta logar com credenciais inválidas do fixture `credenciais.json`.
    - Uso: `cy.fazerLoginComCredenciaisInvalidas()`

- `cypress/support/commands/transferencia.js`
  - Cypress.Commands.add('realizarTransferencia', (contaOrigem, contaDestino, valor))
    - Executa fluxo de transferência preenchendo origem, destino e valor, e clicando em Transferir.
    - Uso: `cy.realizarTransferencia('Maria', 'João', '11')`

Dica: esses comandos combinam bem com fixtures para parametrizar cenários e manter os specs mais legíveis.

## Boas práticas e notas

- Mantenha fixtures e selectors atualizados quando a aplicação evoluir.
- Separe comandos por responsabilidade (como já feito em `commands/`) para facilitar manutenção.
- Para testes que dependem de estado (como saldos), prefira preparar dados via API (seed) ou usar endpoints para resetar o estado entre testes.

## Próximos passos sugeridos

- Adicionar scripts para gerar e publicar relatórios automaticamente (por exemplo, postprocessamento do mochawesome).
- Integrar CI (GitHub Actions) para executar os testes em cada PR.
- Cobrir novos fluxos: cadastro, extrato, pagamento de boletos, etc.

## Contato

Qualquer dúvida sobre o material da Mentoria 2.0, abra uma issue neste repositório ou entre em contato com o instrutor.
