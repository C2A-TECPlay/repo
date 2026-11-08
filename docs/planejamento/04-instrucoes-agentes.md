# Instrucoes para os agentes de IA

## Contexto comum

O trabalho e um cadastro de games avaliado pelo processo de colaboracao Git/GitHub. A stack e Web em `index.html` (HTML/CSS/jQuery), API .NET 10, CLI Python em `cli.py` e PostgreSQL. Todos os servicos, testes e medicao de cobertura rodam via Docker Compose.

Leia antes de implementar:

- `docs/planejamento/01-spec.md`
- `docs/planejamento/02-contrato-api.md`
- `docs/planejamento/03-issues-e-execucao.md`
- a issue designada e os arquivos existentes no branch de destino.

## Regras obrigatorias

1. Trabalhe somente na issue atribuida. Nao antecipe funcionalidades de outra issue.
2. Preserve os arquivos de producao permitidos: `index.html`, `cli.py`, arquivo unico da API, arquivo unico de banco e `README.md`. Nao crie outros arquivos de codigo de producao. Arquivos de testes, dependencia, configuracao e infraestrutura podem ser criados; liste e justifique cada um na PR.
3. A CLI chama a API por HTTP; nunca conecta diretamente ao PostgreSQL.
4. Mantenha rotas Web e CLI separadas conforme contrato, mesmo quando a logica interna possa ser compartilhada.
5. A API valida os dados em todos os caminhos. A validacao da CLI nao substitui validacao do servidor.
6. Nao enfraqueca autenticacao: operacoes funcionais exigem token; healthcheck pode ser publico; credenciais/token invalidos resultam em 401.
7. Nao grave segredos, senhas, tokens ou payloads completos em logs, testes versionados ou documentacao.
8. Toda operacao registra log JSON na API e, ao acessar banco, na camada DB com o mesmo `request_id`.
9. Preserve 100% de cobertura de linhas nos arquivos cobertos ja integrados e no codigo coberto alterado. Testes e comandos de cobertura devem funcionar em contêineres.
10. Atualize README e template da PR. Nao altere funcionalidades fora do escopo sem registrar a necessidade e comunicar os colegas.
11. Nao faca merge, nao aprove a propria PR, nao faca commit direto em `main` e nao crie tag antes do merge principal.

## Escopo de cada agente

| Agente | Responsabilidade sugerida |
|---|---|
| A | Issues #1, #4, #9, #10 |
| B | Issues #3, #7, #8, #11 |
| C | Issues #2, #5, #6, #12 |

Cada agente deve reportar ao concluir: resumo funcional, arquivos alterados, rotas afetadas, comandos Compose/testes executados, resultado de cobertura, arquivos auxiliares justificados e riscos/pendencias conhecidos.

## Prompt-base para uma issue

```text
Implemente somente a issue #[NUMERO] — [TITULO] neste repositorio.

Contexto: consulte docs/planejamento/01-spec.md, 02-contrato-api.md,
03-issues-e-execucao.md e esta issue. A stack, contratos, restricoes de
arquivos e regras de avaliacao nesses documentos sao obrigatorios.

Dependencias ja integradas: [LISTAR PRs/issues presentes no branch].
Escopo desta issue: [COPIAR descricao e criterios de aceite].

Antes de editar, inspecione o estado atual do repositorio e preserve mudancas
existentes. Nao implemente outra issue. Siga os arquivos de producao permitidos;
justifique na PR cada arquivo auxiliar criado/alterado. Atualize README.
Inclua testes da alteracao e mantenha 100% de cobertura de linhas de Program.cs
e Db.cs ja integrados, conforme aplicavel. Todos os comandos de execucao,
testes e cobertura devem funcionar via Docker Compose.

Nao crie PR, nao faca merge, nao aprove PR e nao crie tag/release. Ao final,
informe arquivos alterados, comportamento implementado, comandos executados,
resultado de testes/cobertura, justificativas dos auxiliares e pendencias.
```

## Regras de coordenacao

- Confirme o ciclo e dependencias antes de iniciar.
- Antes de editar `Program.cs` ou `Db.cs`, avise os outros agentes e combine a janela de alteracao; nao mantenha duas edicoes concorrentes desses arquivos compartilhados.
- Se a implementacao exigir mudar contrato, pare a mudanca dependente e proponha uma revisao da spec; nao altere silenciosamente formato de API.
- Agentes podem criar contribuicoes, mas os estudantes devem revisar, aprovar as PRs dos colegas e explicar as entregas na apresentacao.
