# Relatório de Resgate
- Equipe: João Victor (Investigador Git), Gustavo (Responsavel Tecnico), Samuel (Responsavel pela Recuperação), Filipi (Relator)
- Branch de trabalho: rest
## Diagnóstico
Principais erros:
- Erro de compilação no arquivo EntregaService
- Login com falha lógica
- Informações sensíveis expostas

Evidências:
- Quantas branches existem? Contando os ponteiros apontados no histórico, temos 8 branches: resgate/equipe-rest (a sua atual), release-dev, teste-login-descartavel, hotfix-login, feature-cadastro, docs-readme, main e backup-antigo.
- Qual é a branch inicialmente ativa? release-dev.
- Quais commits parecem relacionados aos problemas? As mensagens deixam rastros claros das falhas descritas:
64f88f6 refactor: remover classe aparentemente sem uso: Suspeito de ser a exclusão do arquivo da Porta 2.
9a6d3b0 fix: liberar login para homologação: Provável causa do erro de login da Porta 3.
0cd80f6 docs: simplificar readme: O momento em que a documentação foi destruída na Porta 4.
6572d8a chore: salvar credenciais para facilitar testes: O vazamento de segurança da Porta 5, que inclusive possui a tag commit-perigoso facilitando a identificação.
- Quais branches podem guardar versões úteis? As branches paralelas hotfix-login, docs-readme e a tag v1.0.0-funcional (que está na main) indicam locais onde os arquivos estavam corretos antes das quebras.

Commits relevantes:
- Arquivo Excluído (Porta 2): No commit 64f88f6, o autor Igor Reis removeu o arquivo src/main/java/br/edu/entregas/util/Validador.java (indicado por 15 ---------------). Este é o arquivo que precisará ser restaurado.
- Erro de Compilação (Porta 1): No commit a70ee84, Henrique Nunes modificou o arquivo EntregaService.java tentando "acelerar cadastro de mercadorias". Esta é a alteração que quebrou a compilação que deve ser consertado.
- Destruição do README (Porta 4): No commit 0cd80f6, Gustavo Melo "simplificou" a documentação apagando quase tudo (1 insertion(+), 18 deletions(-)).
- Vazamento de Segurança (Porta 5): No commit 6572d8a (que já tem a tag commit-perigoso), Felipe Rocha criou o arquivo config/application.properties para "salvar credenciais".
- Erro de Login (Porta 3): No commit 9a6d3b0, o mesmo Felipe Rocha alterou o LoginService.java para "liberar login para homologacao", o que introduziu a falha lógica de aceitar qualquer senha.
## Comandos Git utilizados
- git status: Utilizado para verificar o estado atual da árvore de trabalho, rastrear arquivos modificados e confirmar em qual branch a equipe estava posicionada.   
- git branch -a: Utilizado para listar todas as branches locais e remotas disponíveis no repositório, permitindo mapear onde estavam as versões úteis e a branch de resgate.   
- git log --oneline --all --graph --decorate: Utilizado para exibir o gráfico completo do histórico de commits, facilitando a identificação visual de branches paralelas, tags e a origem dos problemas.   
- git log --stat: Utilizado para inspecionar o histórico de commits detalhando quais arquivos foram modificados, adicionados ou removidos em cada alteração, servindo como mapa principal da investigação.   
- git diff: Utilizado para comparar o código atual com versões anteriores funcionais (como a tag v1.0.0-funcional), revelando exatamente quais linhas haviam sido alteradas indevidamente nos serviços de entrega e de login.   
- git restore: Utilizado para descartar alterações indesejadas e restaurar arquivos específicos (como EntregaService.java, Validador.java, LoginService.java e README.md) diretamente de uma versão ou tag anterior segura.   
- git rm: Utilizado para remover o arquivo sensível de configuração (config/application.properties) do rastreamento do Git de forma segura.   
- git switch: Utilizado para alternar entre branches, permitindo a criação da branch exclusiva de resgate e a posterior integração na branch main.   git merge --no-ff: Utilizado para realizar a integração final da branch de resgate na main, preservando explicitamente o histórico de ramificações do projeto.   
## Commits relevantes
- 356ec6f (Tag: v1.0.0-funcional, main, backup-antigo): Versão estável anterior utilizada como referência para restaurar os arquivos corrompidos.   
- 6572d8a (Tag: commit-perigoso / Autor: Felipe Rocha): Commit responsável por introduzir a falha de segurança ao versionar credenciais sensíveis e token no arquivo config/application.properties.   
- 9a6d3b0 (Branch: teste-login-descartavel / Autor: Felipe Rocha): Commit que modificou o LoginService.java, inserindo a falha lógica de autenticação que aceitava credenciais incorretas.   
- 0cd80f6 (Autor: Gustavo Melo): Commit que simplificou indevidamente e apagou a documentação útil do README.md.   
- 64f88f6 (Autor: Igor Reis): Commit responsável pela exclusão indevida da classe utilitária de validação (Validador.java).   
- a70ee84 (Autor: Henrique Nunes): Commit que alterou o construtor da mercadoria e o método de persistência no EntregaService.java, quebrando a compilação do projeto.   
## Validação final
- Compilação via Maven: Executou-se o comando mvn clean package, obtendo-se o status BUILD SUCCESS sem nenhum erro de compilação, comprovando que as dependências, as classes resgatadas (Validador.java) e as correções nos serviços (EntregaService.java) foram integradas corretamente.   
- Validação de Execução e Segurança de Credenciais: O sistema foi executado na IDE, validando as regras de autenticação com base no README.md recuperado:   O usuário correto (admin) com a senha correta (12345678) foi devidamente autorizado.   Tentativas de login com credenciais incorretas (usuário ou senha inválidos) foram rejeitadas com segurança pelo sistema.   O fluxo de cadastro de mercadorias passou a exibir corretamente o item associado ao seu respectivo endereço de entrega.   
- Auditoria de Segurança e o Histórico do Git:O arquivo de configuração sensível (config/application.properties) foi removido da versão atual da árvore de trabalho e o caminho foi adicionado ao .gitignore para prevenir reincidências.   
Apenas apagar o arquivo de configuração da versão atual não elimina o segredo do histórico do Git. O Git opera registrando snapshots e alterações históricas; logo, tudo o que foi commitado anteriormente permanece gravado de forma permanente nos commits antigos do repositório (podendo ser acessado por comandos como git show). A remoção definitiva exigiria ferramentas avançadas de reescrita de histórico (como o git filter-repo), tornando a inclusão acidental de dados sensíveis um risco crítico permanente caso o repositório já tenha sido exposto publicamente.
