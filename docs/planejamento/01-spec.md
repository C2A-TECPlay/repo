# Especificacao do projeto — Cadastro de games

## Objetivo

Construir um CRUD de games com interface Web e CLI, usando a mesma API REST e o mesmo banco PostgreSQL. O foco da entrega da disciplina e demonstrar colaboracao, revisao e integracao com Git/GitHub. A aplicacao inteira, os testes e a medicao de cobertura devem ser executaveis por Docker Compose.

## Stack

- Web: `index.html` unico, com HTML, CSS, jQuery e JavaScript embutidos.
- API: .NET 10; endpoints e logica em `Program.cs` (ou equivalente unico acordado).
- Acesso a dados: um arquivo de producao, por exemplo `Db.cs`.
- CLI: `cli.py`, usando somente HTTP para falar com a API.
- Banco: PostgreSQL.
- Execucao: Docker Compose. O host do professor precisa apenas de Docker/Compose, navegador e terminal.

Os nomes dos arquivos da API e do acesso a dados devem ser acordados antes da primeira PR. Nao criar outros arquivos de codigo de producao. Arquivos de testes, dependencias, Docker, configuracao e infraestrutura sao permitidos; toda PR que os criar ou alterar deve lista-los e justificar sua necessidade.

## Entidade Game

| Campo | Tipo/forma | Obrigatoriedade e validacao |
|---|---|---|
| `id` | inteiro gerado pelo banco | Retornado pela API; nao informado na criacao. |
| `title` | string | Obrigatorio; de 1 a 100 caracteres depois de remover espacos externos. |
| `platform` | string enumerada | Obrigatorio; `PC`, `PlayStation`, `Xbox`, `Switch`, `Mobile` ou `Outro`. |
| `genre` | string enumerada | Obrigatorio; `Ação`, `Aventura`, `RPG`, `Estratégia`, `Esporte`, `Corrida`, `Puzzle` ou `Outro`. |
| `release_year` | inteiro | Obrigatorio; entre 1970 e o ano UTC atual + 1, inclusive. |
| `rating` | numero decimal | Obrigatorio; entre 0 e 10, inclusive. |
| `notes` | string ou nulo | Opcional; no maximo 500 caracteres. |

Os valores de genero sao transmitidos com acentuacao UTF-8 conforme esta tabela. Plataforma e genero sao comparados sem diferenciar maiusculas/minusculas na validacao, mas respostas devem usar os valores canonicos acima. Nao converter silenciosamente valores desconhecidos em `Outro`.

A API e a autoridade final de validacao. A CLI tambem valida localmente para melhorar a experiencia, usando as mesmas regras. O banco deve reforcar restricoes estaveis (nao nulo, comprimentos, enumeracoes e rating); o limite dinamico de ano e validado na API.

## Regras gerais

- Dados Web e CLI pertencem a mesma tabela/conjunto de dados.
- A CLI nunca acessa PostgreSQL diretamente.
- Rotas Web e CLI sao distintas, mesmo quando realizam a mesma operacao.
- Rotas funcionais exigem autenticacao. Healthchecks podem ser publicos.
- Sem credenciais/token ou com credenciais/token invalidos: HTTP 401.
- Nao registrar nem devolver senhas, tokens ou segredos em logs/erros.
- Cada PR principal inclui testes aplicaveis, README atualizado e 100% de cobertura de linhas em `Program.cs` e `Db.cs` ja integrados (e no codigo coberto adicional tocado pela PR).
- `index.html`, testes, infraestrutura e configuracao ficam fora do calculo obrigatorio de cobertura.

## Busca, CSV e indicadores

Filtros combinaveis acordados:

1. `platform`: igualdade exata apos normalizacao canonica.
2. `genre`: igualdade exata apos normalizacao canonica.
3. `release_year_from` e `release_year_to`: limites inclusivos; cada limite pode ser omitido.
4. `q`: opcional, busca parcial sem diferenciar maiusculas/minusculas em `title` e `notes`.

O minimo exigido de tres filtros e atendido por plataforma, genero e faixa de ano. Filtros omitidos nao restringem os resultados. Intervalo com inicio maior que fim retorna HTTP 400.

Paginacao padrao: `page=1`, `page_size=5`; `page >= 1`, `1 <= page_size <= 100`. Ordenacao: `sort_by` limitado a `title`, `platform`, `genre`, `release_year` ou `rating`, e `sort_order` limitado a `asc`/`desc`. Ordenacao inclui `id` como desempate para resultados estaveis.

Os tres indicadores comuns a Web e CLI:

1. total de games;
2. media das avaliacoes, arredondada a duas casas decimais;
3. quantidade de games por plataforma, incluindo plataformas com zero registros.

CSV aplica os filtros mas ignora `page` e `page_size`, exportando todos os resultados filtrados. Sem filtros, exporta todos os games.

## Autenticacao e configuracao

Recomendacao de implementacao: um usuario inicial configurado por variaveis de ambiente, senha armazenada/verificada por hash, login Web por `POST /api/web/login` e emissao de bearer token pela rota CLI `POST /api/cli/token`. Web e CLI enviam bearer token nas chamadas protegidas; a CLI tambem aceita o token pela variavel de ambiente `GAMES_API_TOKEN`. A equipe deve fixar no contrato o formato de resposta, algoritmo/validade do token e nomes de variaveis antes da primeira implementacao de autenticacao.

Segredos reais nao entram no repositorio. README deve mostrar apenas valores de exemplo explicitamente ficticios e explicar como configurar os segredos localmente.

## Logs

Cada operacao Web/CLI gera uma linha JSON no stdout da API e, se acessar o banco, outra linha no stdout do acesso a dados. Campos obrigatorios: `timestamp` ISO 8601 UTC, `level` (`INFO`, `WARN`, `ERROR`), `layer` (`api` ou `db`), `client` (`web` ou `cli`), `operation`, `status` (`success` ou `error`), `request_id` e `message` curto. API e banco usam o mesmo `request_id` na requisicao. Registrar sucessos e falhas de autenticacao sem incluir segredos. Logs devem ser consultaveis por `docker compose logs`.

## Arquivos de producao permitidos

| Arquivo | Responsabilidade |
|---|---|
| `index.html` | Interface Web completa, estilos e JavaScript/jQuery embutidos. |
| `cli.py` | CLI completa. |
| `Program.cs` (nome acordado) | Endpoints, autenticacao, validacao e logica da API. |
| `Db.cs` (nome acordado) | Acesso ao PostgreSQL, consultas e logs da camada DB. |
| `README.md` | Execucao, uso, configuracao, testes e documentacao das issues. |

## Fora de escopo

Cadastro de usuarios, recuperacao de senha, perfis/permissoes, CI/CD obrigatorio, cobertura de branches e acesso direto ao banco pela CLI.
