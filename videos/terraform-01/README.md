# Terraform #01 - Fundamentos

📺 [O que são outputs, resources, data sources e providers. Terraform #01](https://youtu.be/dumzcv1VmkI)

Infraestrutura como código, ou IaC, é mais difícil de se trabalhar, exige conhecimentos de diferentes áreas, é mais burocrático para criar algo simples, mas mesmo assim tem se tornado cada vez mais popular. A contra-partida desses pontos negativos é que IaC representa um possível caminho para criar uma infraestrutura versionada, auditável, replicável e mais simples de se dar manutenção.

O Hashicorp Terraform é uma ferramenta de IaC (Infraestrutura como Código) para provisionar e gerenciar qualquer tipo de provedor de nuvem; infraestrutura on-premise; ou mesmo outros tipos de serviço, como o GitHub ou o CloudFlare.

## Requisitos

O Terraform é multiplataforma e possui versões para Windows, Linux, macOS, entre outros. Neste tutorial, utilizarei `Ubuntu 20.04`, mas os passos são quase idênticos para outras distribuições Linux.

Para instalação, precisaremos apenas descompactar o executável em um diretório que faça parte do PATH do nosso Linux. Para isso:

- Certifique-se de que os pacotes `unzip` e `wget` estão instalados com os comandos `apt update && apt install -y unzip wget`. Os dois `&` significam que, se o comando de trás der certo, executará o da frente. Ou seja, se o `apt update` executar com sucesso e conseguir atualizar a lista de pacotes do sistema, então executará o próximo comando, que é o `apt install -y ...` para instalar os pacotes `unzip` e `wget`.
- Acesse a página de download do Terraform ([https://www.terraform.io/downloads.html](https://www.terraform.io/downloads.html)), clique com o botão direito na arquitetura do seu processador, no meu caso x86-64bits, e copie o link.
- Agora abra seu terminal, alterne para o super usuário root e navegue até o diretório `/usr/local/bin`. O diretório `/usr` é destinado para aplicações e arquivos somente-leitura utilizados pelos usuários do Linux, ou seja, não possui arquivos necessários para o funcionamento do sistema Linux em si. Já o subdiretório localizado em `/usr/local/bin` é destinado para arquivos executáveis do Linux. Essas características tornam o `/usr/local/bin` perfeito para colocarmos o Terraform, já que ele será utilizado apenas pelo nosso usuário e o binário não será alterado durante o uso.
- Agora baixe o arquivo `.zip` que você copiou do site oficial com o comando `wget https://...` seguido pela URL lá do site.
- Com o arquivo baixado, descompacte-o escrevendo `unzip terraform-...zip`.
- Depois de descompactado, não precisaremos mais do arquivo `.zip`, então apague-o com o comando `rm terraform-...zip`. Se você for questionado se tem certeza que deseja remover o arquivo, aperta `y`, de `yes`, e depois `[Enter]` para confirmar a exclusão.
- Verifique o arquivo é executável com o comando `ls -l ./terraform`. Se essas `x` estiverem presentes, significa que o arquivo é executável. Se não estiver, torne o arquivo executável com o comando `chmod +x ./terraform`.
- Para conferir se tudo deu certo, sai do usuário `root` apertando `[Ctrl] + D` de dado, ou digite `exit`. Agora escreva `terraform --version`. Se retorna informações sobre a versão o Terraform, então tudo deu certo.

Ótimo, agora temos o Terraform instalado e podemos começar a brincar com um pouco de código.

## Exemplos

Vamos criar um diretório para nosso exemplo do Terraform:

- Escreva `mkdir -p ~/projects/dednets/terraform-example`. Este será o diretório do nosso projeto de exemplo. O comando `mkdir` serve para criar diretórios, como o nome sugere o `mk` de `make` e o `dir` de `directory`. Já o argumento `-p` significa que ele criará todos os diretórios `projects` e `dednets` recursivamente, até chegar no `terraform-example`. Além disso, se vc executar o comando novamente, verá que ele não retornará erro caso o diretório já exista, o que é ótimo se vc quiser usar esse comando no futuro para criar scripts, por exemplo.
- Com o diretório criado, acesse-o e abra-o com o seu editor de código favorito. Eu vou utilizar o Visual Studio Code, que é o meu preferido, mas vc pode utilizar o qualquer outro. No meu caso, com o comando `code .` eu abrirei o vscode no diretório atual. O `ponto` represente o diretório onde eu estou.
- Com o editor aberto, crie um novo arquivo chamado `main.tf`. Este arquivo conterá todo o conteúdo que iremos escrever hoje. Mais pra frente serão apresentados outros nomes de arquivos, que seguem padrões usados pela comunidade para organizar o código do Terraform.
- Os arquivos `.tf` seguem a sintaxe da linguagem de baixo nível própria do Terraform,  construída em cima do `HCL`, ou Hashicorp Configuration Language, que é uma linguagem de configuração criada pela Hashicorp e usada em seus produtos. Por design, o HCL é parecido com JSON, mas foi criado para ser menos verboso, além de adicionar suporte à comentários, numa linguagem amigável para humanos. Também existe o suporte para JSON como entrada, permitindo que seja fácil criar automações para scripts ou outras aplicações gerarem arquivos que possam ser convertidos para HCL.
- Teorias a parte, vamos pro código...

Exemplo 1 - Output

- crie o trecho de código:

```jsx
output "name" {
  value = "Luiz Felipe"
}
```

- Agora abra o terminal, no meu caso abriei usando o próprio vscode
- Execute o comando `terraform apply`, então confirme digitando `yes` e apertando `[Enter]`.
- Este será o recurso mais importante para debugging. Com o `output` é possível exibir o conteúdo de variáveis e objetos complexos, algo que será essencial no futuro próximo.

Exemplo 2 - Criar um local file

- Agora, imagine que você precisa gerar arquivos locais na sua máquina com o resultado de algo que foi criado no Terraform. Para isso, existe o recurso chamado `local_file` que faz parte do provider `local`.
- [https://registry.terraform.io/providers/hashicorp/local/latest/docs/resources/file](https://registry.terraform.io/providers/hashicorp/local/latest/docs/resources/file)

```jsx
resource "local_file" "name" {
  content  = "Luiz Felipe"
  filename = "${path.module}/name.txt"
}
```

- Antes de aplicarmos as alterações, precisaremos executar um novo comando antes, o `terraform init`. Este comando é responsável por verificar todos os requisitos para executar o Terraform neste diretório. Ele baixará módulos e providers quando necessário, além de configurar outros recursos, como manter o `terraform.tfstate` em um ambiente remoto, mas isso veremos no futuro. O importante para agora é o provider. O nosso recurso chamado `local_file` utiliza o provider `local`, que inclusive é o prefixo do recurso, antes do `_file`. O comando `terraform init` identificou que o provider não está disponível e por isso baixou ele para nossa máquina. Inclusive pode ser encontrado dentro do diretório `./.terraform/providers/`.
- Providers são plugins criados para o Terraform. Elas possuem versões próprias e extendem as funcionalidades do Terraform. Existem providers simples como o `local` onde o único resource é o `local_file`, mas existem providers muito complexos como o `aws`, que permite gerenciar quase todos os serviços disponíveis na nuvem AWS.
- Agora que temos o provider `local` devidamente baixado, execute o `terraform apply` novamente. Note que aconteceu algo diferente: o Terraform identificou que o `output` foi apagado e que o `local_file` foi criado. Este recurso de identificar deltas, ou variações, é um dos principais recursos do Terraform.
- Confirme o `apply` e veja o novo arquivo que foi criado. Ótimo, agora vc sabe como criar arquivos de texto utilizando um `resource` do Terraform. Os resources sãos os elementos mais importantes do Terraform e cada bloco desse tipo é responsável por descrever algo que deve ser criado, como uma rede virtual, uma entrada de DNS ou, como nesse caso, um arquivo local.

Exemplo 3 - provider do GitHub

- No exemplo anterior aprendemos a trabalhar com o nosso primeiro provider, o `local`, que é um dos mais simples disponíveis. Vamos agora para uma parte um pouco mais avançada.
- [https://registry.terraform.io/providers/integrations/github/latest/docs/data-sources/user](https://registry.terraform.io/providers/integrations/github/latest/docs/data-sources/user)
- O GitHub também possui um provider e utilizaremos ele para obter informações sobre um usuário e escrever essas informações em um arquivo, utilizar o `local_file`.
- Para obter as informações, utilizaremos um novo elemento do Terraform, o `data source`, que tem o seu bloco representado pela palavra `data`. Nesse exemplo o `data source` buscará pelo usuário `thenets`, então todas as informações sobre este usuário será retornada.
- Vamos usar o `output` para conferir os dados que foram retornados pelo `data source`.
- Como utilizamos um novo `provider`, agora é necessário executar o `terraform init` novamente.

```jsx
data "github_user" "thenets" {
  username = "thenets"
}

output "user_data" {
  value = data.github_user.thenets
}
```

- Destes dados, digamos que apenas dois campos nos interessam. Vamos pegar o valor `name` e o `blog`, agora é só colocá-los novamente num novo `output`.

```jsx
data "github_user" "thenets" {
  username = "thenets"
}

output "full_name" {
  value = data.github_user.thenets.name
}

output "blog_url" {
  value = data.github_user.thenets.blog
}
```

- Perfeito. Conseguimos os dados do nosso usuário "thenets".

Exemplo 4 - Juntar tudo

- Nesse ponto, você aprendeu o que são `providers`, `data sources`, `resources` e `outputs`.  Agora, vamos juntar tudo isso: o objetivo será obter os dados do usuário "thenets" do GitHub, escrever um arquivo `.yml` com o nome e o blog do usuário e exibir no output o nome do arquivo que foi criado.

```jsx
data "github_user" "thenets" {
  username = "thenets"
}

resource "local_file" "github_user" {
  content  = "name: ${data.github_user.thenets.name}\nblog: ${data.github_user.thenets.blog}\n"
  filename = "${path.module}/github_${data.github_user.thenets.login}.yml"
}

output "file_name" {
  value = "github_${data.github_user.thenets.login}.yml"
}
```

- O conteúdo do arquivo ficou um pouco difícil de ler, mas podemos melhorar isso e escrever o conteúdo do arquivo dessa maneira:

```jsx
data "github_user" "thenets" {
  username = "thenets"
}

resource "local_file" "github_user" {
  content  = <<EOF
name: ${data.github_user.thenets.name}
blog: ${data.github_user.thenets.blog}
EOF
  filename = "${path.module}/github_${data.github_user.thenets.login}.yml"
}

output "file_name" {
  value = "github_${data.github_user.thenets.login}.yml"
}
```

Fim