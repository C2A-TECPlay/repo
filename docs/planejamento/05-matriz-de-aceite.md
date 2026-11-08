# Matriz de aceite e testes

Use esta matriz na implementacao e na revisao. Toda execucao de testes/cobertura deve ocorrer em contêineres iniciados pelo Compose. Nao medir cobertura de `index.html`, testes, infraestrutura ou configuracao.

## Regras de validacao do Game

| Caso | Esperado |
|---|---|
| `title` ausente, vazio, somente espacos ou acima de 100 caracteres | API rejeita com 400; CLI apresenta validacao local equivalente. |
| `platform` ou `genre` ausente/desconhecido | 400; nao substituir silenciosamente por `Outro`. |
| `release_year` nao inteiro, abaixo de 1970 ou acima do ano UTC atual + 1 | 400. |
| `rating` ausente, nao numerico, abaixo de 0 ou acima de 10 | 400. |
| `notes` acima de 500 caracteres | 400; ausente/nulo e aceito. |
| Dados validos | Criacao/edicao persiste e retorna os valores canonicos. |

## Matriz por issue

| Issue | Verificacoes funcionais principais | Testes/cobertura e demonstracao |
|---|---|---|
| #1 | `docker compose up`; health Web 200; login valido; login invalido 401; segredos configuraveis. | Testar middleware/autenticacao/logs/configuracao sem expor segredo; cobertura 100% dos arquivos cobertos presentes. |
| #2 | Criar e editar cada campo; rejeitar casos invalidos; dados persistem no Postgres. | Testes de validacao, endpoints e acesso a dados; README orienta formulario. |
| #3 | Tabela exibe 4 campos; pagina de 5 itens; ordenacao; selecao/exclusao; recarregar preserva estado dos dados no banco. | Testes de listagem/paginacao/ordenacao/exclusao e logs DB. |
| #4 | Combinar plataforma, genero e faixa de ano; limpar filtros; busca assincrona; intervalo invertido rejeitado. | Casos isolados/combinados, sem resultados e pagina seguinte. |
| #5 | Exportar todos os registros filtrados; exportar todos sem filtros; nao truncar na pagina atual. | Verificar cabecalho, quoting de virgula/aspas/quebra de linha e encoding UTF-8. |
| #6 | Tres graficos alimentados pelo banco; valores atualizam ao recarregar; plataforma sem registros aparece com zero. | Validar total, media arredondada, distribuicao; conferir README e endpoint. |
| #7 | Ajuda/subcomandos; health CLI 200; obter token; comando protegido sem token recebe 401; token por env funciona. | Testar token invalido/expirado, API chamada por HTTP, nunca acesso SQL pela CLI. |
| #8 | Criar/editar via CLI com mesmas regras da Web; novo/alterado registro visivel na Web. | Validacao local e servidor, persistencia, logs e exemplos README. |
| #9 | Listar com paginacao/ordenacao; excluir por id; alteracao visivel na Web. | Testar id invalido/inexistente, autenticacao e logs. |
| #10 | Filtros combinaveis na CLI; resultado equivale a mesma consulta Web. | Cobrir filtros, limites, ordenacao, paginacao, invalidos e vazio. |
| #11 | Exportar via CLI todos os resultados filtrados, nao apenas a pagina. | Verificar caminho/arquivo, CSV e filtros equivalentes a Web. |
| #12 | Exibir total, media e quantidade por plataforma. | Comparar valores da CLI com `/api/web/dashboard` sobre mesmo conjunto de dados. |

## Logs

Para uma operacao Web e uma CLI que acessam o banco, confirmar no stdout/container logs:

- uma linha JSON `layer=api` e uma `layer=db` por operacao com mesmo `request_id`;
- `client` correto (`web`/`cli`), `operation`, status e timestamp UTC;
- sucesso e falha de autenticacao registrados;
- ausencia de senha, bearer token, segredo ou payload completo.

Healthchecks podem nao gerar log DB, pois nao consultam dados.

## Compose e fluxo de verificacao do avaliador

1. Clonar o `main` final em ambiente limpo.
2. Executar somente os comandos documentados no README para configurar variaveis locais e iniciar `docker compose up`.
3. Conferir healthchecks, login Web, CRUD Web e dados no banco.
4. Executar login/token CLI e CRUD/busca/CSV/indicadores CLI pelo Compose.
5. Consultar logs com o comando documentado.
6. Rodar testes e cobertura pelo Compose; confirmar 100% de cobertura de linhas de `Program.cs` e `Db.cs` integrados.
7. Confirmar que nao foi necessario instalar .NET, Python, PostgreSQL, dependencias ou ferramentas de teste no host.

## Checklist de revisao de cada PR

- [ ] Issue atribuida ao autor e criterio de aceite atendido.
- [ ] PR aponta para `main` do repositorio principal; descricao usa `Closes #N`/`Fixes #N`.
- [ ] Template preenchido; endpoints, autenticacao, logs e arquivos alterados descritos.
- [ ] Cada arquivo auxiliar criado/alterado esta listado e justificado.
- [ ] Testes executados via Compose e resultado informado.
- [ ] Cobertura de linhas exigida e 100% nos arquivos abrangidos.
- [ ] README atualizado com comandos e uso da funcionalidade.
- [ ] Dois colegas aprovaram antes do merge.
- [ ] Merge sera feito por Rebase and merge; apos PR principal, tag/release aponta para o commit integrado em `main`.
