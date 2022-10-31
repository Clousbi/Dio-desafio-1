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

## 1. Clone do projeto
O primeiro passo para obter o código-fonte do projeto é cloná-lo em nossas máquinas, para que seus arquivos fiquem disponíveis localmente.

## 2. Criação da Branch
Ao criar uma Branch, estamos criando uma nova ramificação, totalmente independente, para podermos alterar os arquivos do projeto sem interferir nos originais.

Esse processo é considerado uma boa prática quando se está trabalhando em nova funcionalidade.

## 3. Commits
Conforme vão sendo criados e alterados os arquivos, elas vão sendo divididas em commits. É importante que a descrição de cada commit seja objetiva, pois ela vai ficar salva no histórico das alterações.

## 4. Hora do Push
Uma vez que a funcionalidade está totalmente finalizada, devemos enviar nossa Branch, com todas as alterações, de volta ao repositório remoto. Assim, ela ficará disponível para os demais contribuidores do projeto poderem ver e alterar.

## 5 – Merge para juntar tudo
Para mesclar as modificações de sua Branch com os arquivos originais do projeto da Branch principal ou máster, você pode utilizar o comando Merge. Após isso, é necessário dar um commit e um push, para enviar a ramificação máster mesclada ao repositório remoto e deixar tudo disponível para os demais contribuidores.

Existe também o Pull Request, que geralmente tem relação com a contribuição em projetos open source. Basicamente, ele ocorre quando se pede para o dono do repositório que suas modificações sejam incluídas nele.


## Chave SSh

O SSH é um protocolo de rede que permite que a conexão com determinados servidores por meio de uma comunicação criptografada, trazendo mais segurança para as transações de dados.

O Github permite que você crie chaves SSH para que você gerencie tudo de maneira remota, com segurança e sem precisar fornecer seu nome de usuário e token de acesso pessoal toda vez que quiser acessar.

O site do Github possui a documentação para essa configuração, entretanto, com base nessa documentação e outras fontes de pesquisa, escrevi um tutorial passo-a-passo e adicionei algumas explicações para facilitar o entendimento.

Para executar os comandos deste tutorial, você já deve ter o git instalado na máquina e utilizar o terminal "Git Bash". É possível também executar no "WSL Bash" caso você esteja utilizando a distro do Linux no Windows.

### Chaves já existentes
O primeiro passo é executar um comando para saber se já existem chaves ssh na máquina. Por padrão o nome delas devem ser um desses: "id_rsa.pub", "id_ecdsa.pub" ou "id_ed25519.pub".

* Para listar as existentes, executar o comando: ls -al ~/.ssh

### Gerar uma nova chave
Caso não exista nenhum par de chaves existentes, precisamos gerar um novo par de chaves. Falamos "par de chaves" porque assim que gerarmos uma chave, serão criados dois arquivos, um público (.pub) e um privado. O conteúdo do arquivo público é o que futuramente colocaremos no github para fazer a conexão.

* Para criar uma chave ed25519, executar: ssh-keygen -t ed25519 -C "your_email@example.com"
* Para criar uma chave rsa, executar: ssh-keygen -t rsa -b 4096 -C "your_email@example.com"

### Adicionar chave privada no ssh-agent

O ssh-agent é um gerenciador de chaves ssh. Para que a conexão funcione, devemos adicionar a chave privada nesse gerenciador. Para isso vamos executar os códigos:

* Rodar o ssh-agent: eval $(ssh-agent -s)
* Incluir a chave privada: ssh-add ~/.ssh/id_ed25519

### Copiar chave pública
Agora que já adicionamos a chave privada no ssh-agent, vamos copiar a chave pública que faz par com ela, para incluirmos no nosso github. No mesmo terminal executar:

* No Windows: clip < ~/.ssh/id_ed25519.pub. (Automaticamente o conteúdo da sua chave pública será copiado para a área de transferência.)
* No Linux: cat ~/.ssh/id_ed25519.pub. (O conteúdo da chave pública aparecerá no terminal para ser selecionado e copiado.)
* MacOS: pbcopy < ~/.ssh/id_ed25519.pub

### Adicionar chave no Github
* Abra o Github e vá no ícone de perfil > Settings, no canto superior direito.
* Na barra lateral de configurações do usuário, clique em "SSH and GPG keys".
* Clique no botão "New SSH key"
* No campo "Título", adicione um rótulo descritivo para a nova chave. Por exemplo, se estiver usando seu computador pessoal, você pode chamar essa chave de "Computador pessoal".
* Cole a chave pública que está na área de transferência no campo "Chave".
* Clique em "Add SSH key" e pronto!

### Testando a conexão SSH
* Executar o seguinte comando: ssh -T git@github.com
* Aguardar as mensagens. Digitar "yes" para continuar.
* Verifique se a mensagem resultante contém seu nome de usuário e o sucesso da sua autenticação.

## Token

Um token de acesso, de forma simplificada, seria uma chave em um formato como e962e591092e9830f8ec6c2a4166e8655b768e88 que te permite ter acesso a algo. No caso do Github você usaria uma chave assim no lugar da senha da sua conta ao executar operações pela linha de comando ou na API.

### Como criar 

Primeiro você precisa que o email da sua conta já tenha sido verificado. Depois, em https://github.com clique na sua foto do perfil e vá em Settings. No lado esquerdo você verá Developer settings. Clique nessa opção e depois em Personal access tokens.

Na parte superior da página você terá um botão para criar seus tokens, Generate new token. Ao clicar nele você pode dar um nome para o novo token, como Trabalho, Projeto da Faculdade, etc.

No restante da página você poderá selecionar as permissões que quer dar para esse token. Para selecionar seus repositórios pela linha de comando, selecione o repo.

No final da página, ao clicar em Generate token, seu token será gerado e exibido para você. Copie agora e guarde em um lugar seguro. Por motivos de segurança, assim que sair dessa página, você nunca mais verá esse token novamente. Se perder, basta criar outro.

Agora, quando for usar a linha de comando para fazer operações de Git ou quando for criar uma aplicação que usa a API do Github, basta usar esse token no lugar da sua senha.

O Token de Acesso funciona apenas para operações HTTPS, não servindo para SSH.

### Configurando o Token no seu projeto

Considerando a configuração no ambiente Linux, esse processo será feito no terminal, no momento em que você for acessar um repositório e realizar operações como pull, push, clone ou commit, ele irá solicitar a autenticação. Quando for realizar a confirmação, digite seu login do Git Hub e no lugar da senha, digite o token que você acabou de gerar.

Por exemplo, ao clonar um repositório, você vai digitar: git clone url do repositório, como na imagem a seguir:

![Imagem](https://i0.wp.com/onebitcode.com/wp-content/uploads/2022/02/21.png?w=672&ssl=1)

Em seguida, ele vai solicitar que você digite seu nome de usuário:

![Imagem](https://i0.wp.com/onebitcode.com/wp-content/uploads/2022/02/22.png?w=676&ssl=1)

E depois, vai solicitar a senha. Nesse caso, você não vai mais digitar a senha da sua conta como era antes. Agora você vai digitar o Token que você acabou de gerar.

![Imagem](https://i0.wp.com/onebitcode.com/wp-content/uploads/2022/02/23.png?w=785&ssl=1)

Depois de colocar o Token e digitar Enter para confirmar, ele vai começar a clonar seu repositório.

![Imagem](https://i0.wp.com/onebitcode.com/wp-content/uploads/2022/02/24.png?w=807&ssl=1)

Seguindo esses passos, você conseguirá realizar a autenticação por Token do Git Hub, tendo mais segurança nos seus projetos.