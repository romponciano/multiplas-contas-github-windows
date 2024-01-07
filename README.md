# Configure Múltiplas Contas do Github no Windows

### Tabela de Conteúdos

1. [Criar chave SSH para cada conta](#criar-chave-ssh-para-cada-conta)  
2. [Adicionar chaves SSH ao SSH Agent](#adicionar-chaves-ssh-ao-ssh-agent)
3. [Adicionar chave pública SSH ao Github](#adicionar-chave-pública-ssh-ao-github)  
4. [Criar um arquivo de Configuração](#criar-um-arquivo-de-configuração)  
5. [Clonagem usando diferentes contas](#clonagem-usando-diferentes-contas)
6. [Commits e Push usando diferentes contas](#commits-e-push-usando-diferentes-contas)  


### Criar chave SSH para cada conta

Exemplo de Contas: 

**https:/<span></span>/github.com<span></span>/user-a**
**https:/<span></span>/github.com<span></span>/user-b**. 

Abrir o terminal

Certifique-se de que o diretório atual é sua pasta **.ssh**.
```sh
     $ cd C:/users/{YOUR_USERNAME}/.ssh
```

Gerar chaves ssh
```sh
     ssh-keygen -t rsa -C "your-email-address" -f "git-username"
```

**ssh-keygen**: Esta é a utilidade de linha de comando para gerar chaves SSH.

**-t rsa**: Especifica o tipo de chave a ser criado. Neste caso, especifica RSA como o tipo de criptografia a ser usado para gerar o par de chaves.

**-C "seu-endereço-de-email"**: Esta opção adiciona um comentário à chave. Geralmente é usado para associar a chave a um identificador ou rótulo, frequentemente um endereço de e-mail. Substitua "seu-endereço-de-email" pelo seu endereço de e-mail real.

**-f "nome-github"**: Especifica o nome do arquivo para a chave gerada. Substitua "nome-github" por um nome de arquivo de sua escolha. Isso é frequentemente usado quando você deseja gerar várias chaves para diferentes propósitos ou serviços.


#### Exemplo
```sh
     ssh-keygen -t rsa -C "user-a@gmail.com" -f "git-user-a"
     ssh-keygen -t rsa -C "user-b@gmail.com" -f "git-user-b"
```

After entering the command the terminal will ask for passphrase, you can leave it empty or specify a pass. <b>If you put a password, which is recommended, you have to type this password on every pull/push.</b>

Agora, após criar as chaves, você terá 4 arquivos na pasta .ssh.

A chave pública terá uma extensão __.pub__. A chave privada estará lá sem nenhuma extensão, ambas tendo o mesmo nome que você passou após a opção -f no comando acima.

### Adicionar chaves SSH ao SSH Agent

No Windows, você precisa ativar o ssh-agent. Portanto, abra o PowerShell com permissão de Administrador.

Configure para iniciar automaticamente.
```
Get-Service ssh-agent | Set-Service -StartupType Automatic
```

Iniciar o serviço
```sh
    Start-Service ssh-agent
```

Verificar se status está Running
```sh
    Get-Service ssh-agent
```

Agora nós temos que adicionar as chaves PRIVADAS no Agente SSH
```sh
     ssh-add C:\\Users\\{YOUR_USERNAME}\\.ssh\\git-user-a
     ssh-add C:\\Users\\{YOUR_USERNAME}\\.ssh\\git-user-b
```

Você pode ler mais sobre o Agente SSH no Windows [aqui.](https://learn.microsoft.com/en-us/windows-server/administration/openssh/openssh_keymanagement)


### Adicionar chave pública SSH ao Github

Agora você precisa adicionar as CHAVES PÚBLICAS às contas correspondentes do GitHub.

Para fazer isso, precisamos:

__1. Copiar a chave pública__

Abra a chave __.pub__ com o Visual Studio Code copie o conteúdo.

__2. Cole a chave pública no Github__

Faça login na Conta do GitHub
Acesse Configurações > Chaves SSH e GPG > Nova Chave SSH
Cole sua chave pública copiada e dê a ela um Título de sua escolha. <b>Certifique-se de remover qualquer espaço ou nova linha no final.</b>

### Crie o arquivo Config

O arquivo ~/.ssh/config nos permite especificar muitas opções de configuração para o SSH.

Se o arquivo config ainda não existe, crie um (certifique-se de estar no diretório C:\Users\{SEU_NOME_DE_USUÁRIO}\.ssh).

Crie um arquivo <b>sem extensão</b> no Windows

1. Abra o Bloco de Notas
2. Salve-o com o nome "config" <b>Não se esqueça das "</b>
3. Selecione a extensão "Todos os arquivos (.)"
4. Salve e feche

Agora precisamos adicionar essas linhas ao arquivo, cada bloco correspondendo a cada conta que criamos anteriormente.

```config
Host git-user-a
    HostName github.com
    User git
    IdentityFile C:/Users/{YOUR_USERNAME}/.ssh/user-a

Host git-user-b
    HostName github.com
    User git
    IdentityFile C:/Users/{YOUR_USERNAME}/.ssh/user-b
```

**Host** apelido para esta configuração
**HostName** site do github
**User** é git
**IdentifyFile** caminho para a chave privada


### Clonagem usando diferentes contas

Certifique-se de fechar o terminal atual e abrir um novo para que as alterações tenham efeito.

Por exemplo:
To clone a repository:
 ```git
     git clone git@{host-nickname-on-config-file}-{your-username}:{owner-user-name}/{the-repo-name}.git

     [e.g.] git clone git@git-user-a:user-a/repository-to-clone.git
 ```


### Commits e Push usando diferentes contas

Para garantir que seus commits e pushes de cada repositório usem o usuário correto, você precisa configurar user.email e user.name em cada repositório recém-clonado ou já existente.

Para fazer isso, use os seguintes comandos.

```git
     git config user.email "user-a@gmail.com"
     git config user.name "User A"
```


Troque o origin em repositórios já clonados:
```git
     git remote set-url origin git@{host-nickname-on-config-file}-{your-username}:{owner-user-name}/{the-repo-name}.git
```

Agora pode usar:
```git
     git push
     
     git pull
```
