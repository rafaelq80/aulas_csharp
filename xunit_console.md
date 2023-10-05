<h1>Criando um Projeto de Testes xUnit no VSCode</h1>



<h2>1. Criar O Projeto de Testes do xUnit</h2>



1. Na **Área de Trabalho**, abra a pasta **aspnet**.

<div align="center"><img src="https://i.imgur.com/3LnSg06.png" title="source: imgur.com" /></div>

2. Clique com o botão direito do mouse sobre a pasta **blogpessoal** e clique na opção **Abrir com com Code**.

3. A pasta será aberta no **VSCode**, como mostra a figura abaixo e dentro dela veremos a pasta do projeto **blogpessoal**:

<div align="center"><img src="https://i.imgur.com/pMk1sAM.png" title="source: imgur.com" /></div>

4. Crie uma pasta chamada **blogpessoal_test** dentro da pasta **BLOGPESSOAL**. Após criar a pasta, o seu projeto estará semelhante a figura abaixo:

<div align="center"><img src="https://i.imgur.com/W1EZcJQ.png" title="source: imgur.com" /></div>

5. Abra o Terminal, através do Menu **Terminal 🡒 New Terminal**

<div align="center"><img src="https://i.imgur.com/4rdobXK.png?1" title="source: imgur.com" /></div>

6. Será aberta a tela do **Terminal** na parte inferior da janela do **VSCode**.

<div><img src="https://i.imgur.com/FfXSnoB.png" title="source: imgur.com" /></div>

<br />

Agora vamos criar o Projeto **xUnit** para escrever os testes automatizados na nossa aplicação ASP.NET:

1. Digite o comando abaixo no **Terminal** para checar se o **.NET SDK** está instalado:

```bash
dotnet --info
```

2. O resultado do comando será semelhante a imagem abaixo:

<div><img src="https://i.imgur.com/exrpbGe.png" title="source: imgur.com" /></div>

3. Digite os comandos abaixo para voltar para acessar a pasta **blogpessoal_test**:

```bash
cd blogpessoal_test
```

4. Digite o comando abaixo no **Terminal** para **Criar um novo Projeto do xUnit**:

```bash
dotnet new xunit
```

5. A saída do comando será semelhante ao conteúdo abaixo:

```bash
O modelo "xUnit Test Project" foi criado com êxito.

Processando ações pós-criação...
Restaurando C:\Users\rafae\OneDrive\Área de Trabalho\aspnet\blogpessoal\blogpessoal.csproj:
  Determinando os projetos a serem restaurados...
  C:\Users\rafae\OneDrive\Área de Trabalho\aspnet\blogpessoa
  l\blogpessoal.csproj restaurado (em 1,67 sec).
A restauração foi bem-sucedida.
```

6. Observe na **Guia Explorer**, que o projeto foi criado:

<div align="center"><img src="https://i.imgur.com/Q2Uj9Us.png" title="source: imgur.com" /></div>

7. Na **Guia Explorer** vamos excluir o arquivo **UnitTest1.cs**:

<div align="center"><img src="https://i.imgur.com/yauotVT.png" title="source: imgur.com" /></div>

8. Digite o comando abaixo no terminal para adicionar no projeto **blogpessoal_test** o arquivo **.gitignore**:

```bash
dotnet new gitignore
```

<br />

Na sequência, vamos configurar o projeto **blogpessoal_test** como um projeto de testes do projeto **blogpessoal**:

1. Digite o comando abaixo no terminal para adicionar o projeto **blogpessoal** como referência do projeto **blogpessoal_test**:

```bash
dotnet add blogpessoal_test.csproj reference ../blogpessoal/blogpessoal.csproj
```

2. A saída do comando será semelhante ao conteúdo abaixo:

```bash
A referência ‘..\blogpessoal\blogpessoal.csproj’ foi adicionada ao projeto.
```

3. A partir deste ponto, siga as instruções do Cookbook a partir do **Passo 03 - Instalar os pacotes**.

<br />

<h2>2. Executar O Projeto de Testes do xUnit</h2>



Vamos executar os testes escritos no projeto **blogpessoal_test**:

1. Digite o comando abaixo no terminal para executar todos os testes escritos no projeto **blogpessoal_test**:

```bash
dotnet test
```

2. A saída do comando será semelhante ao conteúdo abaixo:

```bash
  Determinando os projetos a serem restaurados...
  Todos os projetos estão atualizados para restauração.
  blogpessoal -> C:\Users\rafae\OneDrive\Área de Trabalho\aspnet\blogpessoal\bl
  ogpessoal\bin\Debug\net7.0\blogpessoal.dll
  blogpessoal_test -> C:\Users\rafae\OneDrive\Área de Trabalho\aspnet\blogpesso
  al\blogpessoal_test\bin\Debug\net7.0\blogpessoal_test.dll
Execução de teste para C:\Users\rafae\OneDrive\Área de Trabalho\aspnet\blogpessoal\blogpessoal_test\bin\Debug\net7.0\blogpessoal_test.dll (.NETCoreApp,Version=v7.0)
Ferramenta de Linha de Comando de Execução de Teste da Microsoft (R) Versão 17.7.1 (x64)
Copyright (c) Microsoft Corporation. Todos os direitos reservados.

Iniciando execução de teste, espere...
1 arquivos de teste no total corresponderam ao padrão especificado.

Aprovado!  – Com falha:     0, Aprovado:     4, Ignorado:     0, Total:     4, Duração: 1 s - blogpessoal_test.dll (net7.0)
```

Observe que todos os testes foram aprovados!

