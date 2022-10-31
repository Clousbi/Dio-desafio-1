# Entendendo como o Git funciona

## SHA1

A sigla SHA sisgnifica Secure Hash Algorithm (Algoritmo de Hash Seguro), é um conjunto de funções hash criptográficas projetadas pela NSA (Agência de Segurança Nacional dos EUA).

A encriptação gera conjunto de caracteres identificador de 40 dígitos. É uma forma curta de representar um arquivo.
Exemplo:
* echo "ola mundo" | openssl sha1
* [> (stdin) = f3fc32bj43b4bh2n23bh243n3jh1b2m3mjh3u2kn]

## Objetos fundamentais

O Git é um sistema de arquivos de conteúdo endereçável. Ótimo. O que isso significa? Isso significa que o coração do Git é um simples armazenamento chave-valor. Você pode inserir qualquer tipo de conteúdo nele, e ele lhe dará de volta uma chave que você pode usar para recuperar o conteúdo de volta em qualquer momento. Para demonstrar isso, você pode usar o comando de encanamento hash-object, que recebe alguns dados, armazena eles em seu diretório .git, e lhe devolve de volta a chave com o qual os dados são armazenados.

### Blobs

![Imagem](image.png)

### Trees

a tree (árvore), que resolve o problema de armazenar o nome de arquivo e também permite armazenar de forma conjunta um grupo de arquivos. O Git armazena o conteúdo em uma maneira similar a um sistema de arquivos UNIX, porém um pouco simplificado. Todo o conteúdo é armazenado como objetos tree e blob, com as trees correspondendo a entradas de um diretório UNIX e blobs correspondendo mais ou menos a inodes ou conteúdos de arquivos. Um único objeto tree contém uma ou mais entradas, cada uma contendo uma referência SHA-1 para um blob ou subtree com seu modo, tipo e nome de arquivo associados. 

Você pode criar facilmente a sua própria tree. O Git normalmente cria uma tree a partir do estado da sua área de stage ou index e escrevendo uma série de objetos tree a partir dela. Então, para criar um objeto tree, você primeiro precisa popular um index adicionando alguns arquivos. Para criar um index com apenas uma entrada - a primeira versão do do seu arquivo test.txt - você pode usar o comando update-index. Você usa esse comando para adicionar artificialmente a versão anterior do arquivo test.txt à nova área de stage. Você precisa passar a ele a opção --add porque o arquivo ainda não existe em sua área de stage (você nem precisa ter uma área de stage ainda) e a opção --cacheinfo porque o arquivo que você está adicionando não está em seu diretório mas está no seu banco de dados.

![Imagem](http://git-scm.com/figures/18333fig0901-tn.png)
![Imagem](http://git-scm.com/figures/18333fig0902-tn.png)

### Commits

Você tem três árvores que especificam os diferentes snapshots de seu projeto que você deseja acompanhar, mas o problema anterior mantém-se: você deve se lembrar de todos os três valores SHA-1, a fim de recuperar os snapshots. Você também não tem qualquer informação sobre quem salvou os snapshots, quando eles foram salvos, ou por que eles foram salvos. Esta é a informação básica que os objetos commit armazenam para você.

Para criar um objeto commit, você chama commit-tree e especifica uma única árvore SHA-1 e quais objetos commit, se houverem, diretamente precederam ele.

O formato de um objeto commit é simples: ele especifica a árvore de nível superior para o snapshot do projeto nesse momento; a informação do author/committer (autor do commit) obtido de suas opções de configuração user.name e user.email, com a timestamp atual; uma linha em branco, e em seguida, a mensagem de commit.

![Imagem](http://git-scm.com/figures/18333fig0903-tn.png)

## Sistema distribuído
## Segurança
