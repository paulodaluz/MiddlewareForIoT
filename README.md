# Middleware for IoT

API acadêmica de Internet das Coisas para cadastrar coletores e produtos, consultar seus dados e realizar operações de atualização e exclusão. Usa Express e Mongoose, com persistência MongoDB. Desenvolvida por Paulo da Luz e Pablo Germano Maronezi Zilli.

[![JavaScript](https://img.shields.io/badge/JavaScript-F7DF1E?logo=javascript&logoColor=black)](https://developer.mozilla.org/pt-BR/docs/Web/JavaScript)
[![License MIT](https://img.shields.io/badge/License-MIT-blue)](LICENSE)

## 📋 Table of contents

- [✨ Features](#features)
- [🧪 Technologies](#technologies)
- [🛠️ Environment setup](#environment-setup)
- [🚀 Getting started](#getting-started)
- [🔌 API usage](#api-usage)
- [🗂️ Project structure](#project-structure)
- [✅ Testing and quality](#testing-and-quality)
- [🔎 Troubleshooting](#troubleshooting)
- [🤝 Contributing](#contributing)
- [📜 License](#license)

<a id="features"></a>

## ✨ Features

- Cadastro, consulta, listagem, atualização e exclusão de coletores e produtos.
- Coletores com identificação do dispositivo, usuário e leituras de produtos.
- Produtos com nome, preço, descrição e identificação do dispositivo.
- Validação de campos nas rotas de criação e atualização de produtos.
- Resposta HTTP de verificação em `/`.

As rotas não implementam autenticação. Os requisitos acadêmicos do README original descreviam também assinatura de dados; não há implementação de assinatura identificada no código atual.

<a id="technologies"></a>

## 🧪 Technologies

| Tecnologia | Versão declarada | Finalidade |
| --- | --- | --- |
| [Node.js](https://nodejs.org/) | Não fixada; 14.21.3 usada na validação | Execução |
| [Express](https://expressjs.com/) | `^4.17.1` | Rotas HTTP |
| [Mongoose](https://mongoosejs.com/) | `^5.6.11` | Persistência MongoDB |
| [express-validator](https://express-validator.github.io/) | `^6.2.0` | Validação dos produtos |
| [body-parser](https://github.com/expressjs/body-parser) | `1.19.0` | Corpo das requisições |
| [nodemon](https://nodemon.io/) | `^1.19.1` | Reinício no desenvolvimento |

As faixas são do `package.json`, não versões exatas instaladas. `request` `2.88.0` também está declarado; o servidor não o importa.

<a id="environment-setup"></a>

## 🛠️ Environment setup

Requer Git, Node.js e npm. O projeto não fixa Node.js em `engines`, `.nvmrc` ou `.node-version`. **Node.js 14.21.3 com npm 6.14.18** foi escolhido para a reprodução da pilha histórica; não é uma versão declarada pelo repositório nem uma atualização de dependências.

```bash
nvm install 14.21.3
nvm use 14.21.3
git clone https://github.com/paulodaluz/MiddlewareForIoT.git
cd MiddlewareForIoT
npm install
```

Não há lockfile versionado: use `npm install`, não `npm ci`. Este guia adota npm, disponível com o Node.js, sem prometer uma resolução de dependências idêntica entre instalações. Não há Husky, hooks versionados ou script `prepare` para ativar.

### MongoDB and port

`index.js` contém uma URI MongoDB diretamente no código. Não há `.env.example`, leitor de `.env` nem variável `MONGO_URI` implementada. Para executar contra um banco próprio, a configuração `uri` precisa ser revista no arquivo, sem publicar credenciais. A execução integral depende desse ajuste e de um MongoDB acessível; nenhum banco foi instalado nesta tarefa.

A única variável de ambiente lida pelo servidor é **`port` em letras minúsculas**; o padrão é `3333`. Definir `PORT` em maiúsculas não altera a porta.

<a id="getting-started"></a>

## 🚀 Getting started

Após configurar um banco próprio:

```bash
npm start
```

O script executa `nodemon ./index.js`. Também é possível executar uma vez com `node index.js`. A URL padrão é `http://localhost:3333`:

```bash
curl http://localhost:3333/
```

O handler retorna `{"message":"Tudo 100%!"}`. Essa resposta apenas comprova que o servidor HTTP atende; não confirma o MongoDB. Para uma porta alternativa em um shell POSIX, use `port=4300 npm start`.

Validação realizada com Node.js 14.21.3/npm 6.14.18: dependências instaladas, sintaxe de `index.js` verificada e resposta HTTP 200 de `/` confirmada na porta temporária 4300. O processo foi iniciado com um preloader externo ao repositório que bloqueou consultas DNS SRV/TXT para impedir acesso ao MongoDB configurado no código; foi encerrado após a checagem. Isso **não valida** a conexão MongoDB, as credenciais ou as operações CRUD.

<a id="api-usage"></a>

## 🔌 API usage

Não há prefixo de versão nem autenticação implementada. Os caminhos são definidos em `src/routes/`:

| Método | Caminho | Finalidade |
| --- | --- | --- |
| GET | `/` | Resposta HTTP de verificação |
| POST | `/api/coletores/cadastrarColetor` | Cadastrar coletor |
| GET | `/api/coletores/listarColetores` | Listar coletores |
| GET | `/api/coletores/buscarColetor/:coletor_id` | Consultar coletor; ver limitação abaixo |
| PUT | `/api/coletores/alterarColetor/:coletor_id` | Atualizar coletor |
| DELETE | `/api/coletores/deletarColetor/:coletor_id` | Excluir coletor |
| POST | `/api/produtos/cadastrarProduto` | Cadastrar produto |
| GET | `/api/produtos/listarProdutos` | Listar produtos |
| GET | `/api/produtos/buscarProduto/:produto_id` | Consultar produto |
| PUT | `/api/produtos/alterarProduto/:produto_id` | Atualizar produto |
| DELETE | `/api/produto/deletarProduto/:produto_id` | Excluir produto; `produto` no singular |

Exemplo de leitura após configurar e popular seu próprio ambiente:

```bash
curl http://localhost:3333/api/produtos/listarProdutos
```

Criação e atualização de produtos usam JSON com `nome`, `preco`, `descricao` e o header `idDispositivo`; o validador também exige `idDispositivo`. Coletores usam `coletorNome`, `usuario`, `leitura` e o mesmo header. Consulte os schemas e controllers para os formatos: o cadastro de coletor acessa `leitura[0]`. As operações com dados não foram verificadas contra um banco nesta tarefa.

<a id="project-structure"></a>

## 🗂️ Project structure

| Caminho | Responsabilidade |
| --- | --- |
| `index.js` | MongoDB, middlewares, montagem das rotas e servidor |
| `src/routes/` | Rotas e validação dos campos |
| `src/controller/` | Operações de coletores e produtos |
| `src/models/` | Schemas Mongoose |
| `simpleMock.json` | Dados de exemplo versionados; confira-os antes de usar |

<a id="testing-and-quality"></a>

## ✅ Testing and quality

Não há scripts de build, teste, lint ou formatação. O JavaScript é executado diretamente. Verificação de sintaxe disponível:

```bash
node --check index.js
```

Ela não verifica acesso ao banco nem correção dos controllers.

<a id="troubleshooting"></a>

## 🔎 Troubleshooting

| Situação | Evidência e encaminhamento |
| --- | --- |
| Conexão com MongoDB falha | A URI está embutida em `index.js`; configurar `.env` sozinho não altera a conexão. Use um banco próprio e revise a configuração antes de iniciar. |
| Porta não muda | A variável lida é `port`, em minúsculas. |
| Exclusão de produto retorna 404 | A rota implementada usa `/api/produto/`, no singular. |
| Busca de coletor não usa o ID esperado | A rota declara `coletor_id`, mas `buscarColetor` lê `produto_id`. Correção de código fica pendente. |
| Divergência de licença | `package.json` declara ISC, mas o arquivo LICENSE contém MIT. O README referencia o arquivo de licença existente; a divergência de metadados fica pendente. |

A captura de VS Code permanece pendente: não foi possível verificar a aparência atual do editor do proprietário nem executar o backend com um banco de teste configurado.

<a id="contributing"></a>

## 🤝 Contributing

Crie uma branch a partir de `master`, mantenha as instruções compatíveis com os arquivos do projeto e descreva a alteração em um pull request. Execute as verificações aplicáveis descritas acima; registre limitações de serviços externos e ambiente. Não inclua credenciais, dados pessoais, `node_modules` ou arquivos gerados na entrega.

<a id="license"></a>

## 📜 License

O arquivo [LICENSE](LICENSE) contém a licença **MIT**, com copyright de **Pablo Maronezi e Paulo da Luz (2021)**. Esses avisos foram preservados. A declaração ISC no manifesto diverge desse arquivo e requer revisão posterior.

---

<p align="center">Made with 💜 by <a href="https://github.com/paulodaluz">Paulo da Luz</a></p>
