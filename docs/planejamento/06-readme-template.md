# Cadastro de Games

Aplicacao CRUD de games com interface Web e CLI, API REST .NET 10 e banco PostgreSQL. Todos os componentes, testes e cobertura sao executados em contêineres via Docker Compose.

## Requisitos no host

- Docker com Docker Compose.
- Navegador e terminal.
- Nao e necessario instalar .NET, Python, PostgreSQL ou ferramentas de teste no host.

## Configuracao local

1. Clone o repositorio e entre na pasta do projeto.
2. Copie o arquivo de exemplo de variaveis locais (nao versionado) e configure credenciais de desenvolvimento.
3. Nao use credenciais reais em exemplos ou no repositorio.

```powershell
Copy-Item .env.example .env
```

Documentar aqui cada variavel necessaria (usuario inicial, senha, chave de assinatura do token, credenciais do Postgres e portas), seu uso e como gerar valores locais seguros.

## Iniciar a aplicacao

```sh
docker compose up --build
```

Documentar a URL Web e os comandos para verificar os healthchecks Web/CLI. Para encerrar, usar `Ctrl+C`; para remover containers da execucao, documentar o comando Compose apropriado sem apagar volumes de dados inadvertidamente.

## Login Web

Documentar como abrir a pagina, autenticar com o usuario inicial configurado localmente e usar o CRUD. Nao colocar senha real neste documento.

## CLI

Obter token via endpoint CLI conforme contrato e disponibiliza-lo para o container por `GAMES_API_TOKEN`. Documentar comandos Compose exatos para solicitar token, ajuda, criar/editar/listar/excluir, pesquisar, exportar CSV e exibir indicadores.

```sh
docker compose run --rm cli --help
```

Substituir/ajustar o exemplo conforme o servico e entrypoint definitivos do Compose.

## Dados de exemplo

Documentar como preparar dados para demonstracao, incluindo pelo menos multiplas plataformas/generos e anos distintos. Usar apenas dados ficticios.

## Logs

```sh
docker compose logs -f api
```

Documentar como visualizar logs JSON e identificar linhas da API e do banco correlacionadas pelo mesmo `request_id`. Nao incluir segredos nos comandos.

## Testes e cobertura

```sh
docker compose run --rm tests
```

Substituir pelos comandos Compose definitivos para executar testes unitarios e medir cobertura. Documentar a verificacao de 100% de cobertura de linhas nos arquivos cobertos da API e do acesso a dados ja integrados. Garantir que o fluxo funciona em clone limpo.

## Funcionalidades por issue

Manter esta tabela atualizada a cada PR principal:

| Issue | Funcionalidade | Como usar |
|---|---|---|
| #1 | Estrutura Web, login e healthcheck | ... |
| #2 | Cadastro e edicao Web | ... |
| #3 | Listagem e exclusao Web | ... |
| #4 | Busca avancada Web | ... |
| #5 | Exportacao CSV Web | ... |
| #6 | Dashboard Web | ... |
| #7 | Estrutura e autenticacao CLI | ... |
| #8 | Cadastro e edicao CLI | ... |
| #9 | Listagem e exclusao CLI | ... |
| #10 | Busca avancada CLI | ... |
| #11 | Exportacao CSV CLI | ... |
| #12 | Indicadores CLI | ... |

## Contrato e validacao do game

Documentar os campos, valores aceitos e validacoes conforme `docs/planejamento/01-spec.md` e `02-contrato-api.md`.
