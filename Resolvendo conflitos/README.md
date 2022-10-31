# Resolvendo conflitos

## Git pull

O comando git pull é usado para buscar e baixar conteúdo de repositórios remotos e fazer a atualização imediata ao repositório local para que os conteúdos sejam iguais. Fazer o merge de alterações upstream remotas no repositório local é algo comum em fluxos de trabalho de colaboração baseados em Git. O comando git pull é a combinação de dois outros comandos, o git fetch, seguido do git merge. No primeiro estágio da operação, o git pull executa o comando git fetch, que abrange a ramificação local para qual a HEAD aponta. Quando o conteúdo é baixado, o git pull insere o fluxo de trabalho de merge. O commit de merge é criado e a HEAD é atualizada para apontar o novo commit.

### Como funciona

O comando git pull primeiro executa o git fetch, que baixa o conteúdo do repositório remoto especificado. Então, o git merge é executado e faz merge das refs e dos heads do conteúdo remoto para o novo commit de merge local. Para mostrar melhor o processo de pull e de merge, veja o exemplo a seguir. Suponha que a gente tenha um repositório com uma ramificação principal e uma origem remota.

![Imagem](https://wac-cdn.atlassian.com/dam/jcr:63e58c34-b273-4e48-a6b1-6e3ba4d4a0ea/01%20bubble%20diagram-01.svg?cdnVersion=610)

Nesse caso, o git pull baixa todas as alterações a partir do ponto de divergência entre o local e a ramificação principal. Nesse exemplo, o ponto é E. O git pull busca os commits remotos divergentes, que são A-B-C. O processo de pull então cria o commit de merge local que tem o conteúdo dos novos commits remotos divergentes.

![Imagem](https://wac-cdn.atlassian.com/dam/jcr:0269bb2d-eb7f-43d8-80a2-8afa88d11eea/02%20bubble%20diagram-02.svg?cdnVersion=610)

No diagrama acima, é possível ver o novo commit H. Esse commit de merge é novo e armazena o conteúdo dos commits remotos A-B-C e uma mensagem de log combinada. Essa é uma de várias estratégias de merge de git pull. O git pull também pode receber a opção --rebase para usar a estratégia de merge de rebase em vez do commit de merge. O próximo exemplo mostra como o pull de rebase funciona. Suponha que a gente esteja no ponto de partida do primeiro diagrama e tenha executado git pull --rebase.

![Imagem](https://wac-cdn.atlassian.com/dam/jcr:d5633068-d448-4140-953e-2ab31553ce10/03%20bubble%20diagram-03-updated@2x%20kopiera.png?cdnVersion=610)

Neste diagrama, veja que o rebase pull não cria o commit H. Em vez disso, o rebase copiou os commits remotos A--B--C e rescreveu os commits locais E--F--G para aparecerem depois deles no histórico de commits de origem/principal local.

### Discussão sobre o git pull

Você pode pensar no git pull como a versão do Git do comando svn update. É uma maneira fácil de sincronizar seu repositório local com alterações upstream. O diagrama a seguir explica cada etapa do processo de extração.

![Imagem](https://wac-cdn.atlassian.com/dam/jcr:9c543e76-04df-429e-af48-43a5276d7f4f/04-06%20Git%20pull%20discussion.svg?cdnVersion=610)



Primeiro, você pensa que o repositório está sincronizado, mas então o git fetch revela que a versão principal da origem progrediu desde a última verificação. Em seguida, o git merge integra de imediato a ramificação principal remota na local.

### Git pull e sincronização

O git pull é um dos muitos comandos que têm a responsabilidade de "sincronizar" conteúdo remoto. O comando git remote é usado para especificar em quais terminais remotos os comandos devem operar. O comando git push é usado para fazer upload de conteúdo em repositórios remotos.

O comando git fetch pode ser confundido com o git pull. Ambos são usados para baixar conteúdo remoto. Há diferenças de segurança importantes entre o git pull e o git fetch. O git fetch é a opção que pode ser considerada "segura", enquanto o git pull pode ser considerado "pouco seguro". O git fetch baixa o conteúdo remoto e não altera o estado do repositório local. Já o git pull baixa o conteúdo remoto e, de imediato, tenta alterar o estado local para que ele corresponda àquele conteúdo. Isso pode causar um estado conflituoso acidental no repositório local.

### Fazendo pull por rebase

A opção --rebase pode ser usada para garantir o histórico linear, evitando o merge de commits desnecessários. Muitos desenvolvedores preferem o rebase em vez do merge, porque é como dizer: "Eu quero colocar minhas alterações acima do que todo mundo tem feito". Neste sentido, usar git pull com o sinalizador --rebase é mais como uma atualização de svn do que como um git pull simples.

Na verdade, o pull com --rebase é um fluxo de trabalho tão comum que existe uma opção de configuração dedicada para ele

[git config --global branch.autosetuprebase always]

Depois de executar esse comando, todos os comandos git pull vão ser integrados via git rebase em vez de git merge.