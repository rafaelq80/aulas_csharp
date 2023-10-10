<h1>Ajustes no Projeto Blog Pessoal - Render</h1>



<h2>👣 Passo 01- Criar a Classe DateTimeOffsetConverter</h2>



Vamos criar a Classe **DateTimeOffsetConverter**, que será responsável por converter a data no formato **DateTimeOffset** para o formato **UniversalTime**, compatível com **PostgreSQL**:

Dentro do projeto **blogpessoal**, na pasta **Configuration**, vamos criar a Classe **DateTimeOffsetConverter**:

1. Clique com o botão direito do mouse sobre a **pasta Configuration**, do projeto **blogpessoal** e na sequência, clique na opção **Adicionar 🡪 Classe**.
2. No item **Nome**, digite o nome da Classe (**DateTimeOffsetConverter**)

Vamos implementar a Classe **DateTimeOffsetConverter**, como mostra a imagem abaixo:

```c#
using Microsoft.EntityFrameworkCore.Storage.ValueConversion;

namespace blogpessoal.Configuration
{
    public class DateTimeOffsetConverter : ValueConverter<DateTimeOffset, DateTimeOffset>
    {
        public DateTimeOffsetConverter()
            : base(
                d => d.ToUniversalTime(),
                d => d.ToUniversalTime()
            )
        { }
   
    }
}
```

Vamos analisar o código:

<div align="center"><img src="https://i.imgur.com/seDX9DK.png" title="source: imgur.com" /></div>

**Linha 01:** Importamos o Namespace do **Entity Framework Core**, que contém a Classe **ValueConverter**.

**Linha 05:** Cria a assinatura do Método **DateTimeOffsetConverter**, que herda a Classe **ValueConverter**. A Classe **ValueConverter** é responsável por criar uma conversão de um tipo de dado usado no **Entity Framework Core** para um outro tipo de dado utilizado no Banco de dados. O primeiro parâmetro representa o tipo do Objeto (Objeto C#, no exemplo o DateTimeOffset) e o segundo parâmetro representa o tipo de dado que será armazenado no Banco de dados. 

**DateTimeOffset** é um formato de dado reconhecido pelo C# e pelo Banco de dados SQL Server, entretanto outros Banco de dados Relacionais, como o Postgres SQL por exemplo, utilizam um outro formato equivalente para armazenar dados do tipo DateTimeOffset, chamado **Timestamp with Timezone**. 

Ambos os formatos seguem o padrão **UTC (Universal Time Coordinated)** e o fuso horário padrão é o UTC 00:00. O fuso horário padrão do Brasil (Brasília) está a UTC -03:00 de Grenwich. Caso não seja feito o ajuste do fuso horário, a hora será persistida seguindo o padrão UTC 00:00, com uma diferença de 3 horas a mais em relação ao fuso horário padrão do Brasil.

**Exemplo:**

**10:00 🡢 13:00** 

O Banco de dados **SQL Server** reconhece o formato **DateTimeOffset** e permite ajustar o fuso horário subtraindo as 3 horas a mais, por exemplo. No caso dos outros Bancos de dados Relacionais, se tentarmos guardar um dado do tipo DateTimeOffset ajustando o fuso horário subtraindo as 3 horas adicionais, será retornado um erro informando que não é possível mudar o fuso horário padrão UTC 00:00 (hora zero de Grenwich) e nenhum Objeto não será persistido no Banco de dados, retornado um **HTTP Satus Code 500 🡢 Internal Server Error**. 

Para corrigir este problema, de modo a funcionar em qualquer Banco de dados relacional, utilzaremos a Classe **ValueConverter** onde faremos a conversão do Objeto C# e do Registro do Banco de dados para o formato UTC, através do Método **ToUniversalTime()**.

**Linhas 7 a 12:** Cria o Método Construtor com 2 parâmetros herdados da Classe **ValueConverter**: **TModel e TProvider**.  **TModel** é o tipo de dado do Objeto C# e **TProvider** é o tipo de dado do Banco de dados, ou seja, ambos receberão o formato **DateTimeOffset**. Na sequência, ambos os parâmetros serão convertidos para UTC nas linhas 9 e 10 através do Método **ToUniversalTime()**.

A Classe **DateTimeOffsetConverter** serpa utilizada na Classe **AppDbContext** para automatizar o processo, de modo que todo e qualquer atributo das Classes Model, que estiverem no formato **DateTimeOffset**, serão convertidos para o formato UTC.

<br />

<div align="left"><img src="https://i.imgur.com/wHTDfQ2.png" title="source: imgur.com" width="4%"/> <a href="https://learn.microsoft.com/pt-br/ef/core/modeling/value-conversions?tabs=data-annotations#bulk-configuring-a-value-converter" target="_blank"><b>Documentação: Classe ValueConverter</b></a></div>

<div align="left"><img src="https://i.imgur.com/wHTDfQ2.png" title="source: imgur.com" width="4%"/> <a href="https://learn.microsoft.com/pt-br/dotnet/api/system.datetime.touniversaltime?view=net-7.0" target="_blank"><b>Documentação: Método ToUniversalTime()</b></a></div>

<br />

<h2>👣 Passo 02- Atualizar a Classe AppDbContext</h2>



Vamos atualizar Classe **AppDbContext**, para que todo e qualquer atributo das Classes Model, que estiverem no formato **DateTimeOffset**, sejam convertidos para o formato UTC. 

No **Gerenciador de Soluções** abra a Classe **AppDbContext**, do projeto **blogpessoal**, localizada na pasta **Data**.

2. Insira o Método abaixo no final da Classe **AppDbContext**, depois do Método sobrescrito **SaveChangesAsync**:

```c#
// Ajusta a Data para o formato UTC - Compatível com qualquer Banco de dados Relacional
protected override void ConfigureConventions(ModelConfigurationBuilder configurationBuilder)
{
    configurationBuilder
        .Properties<DateTimeOffset>()
        .HaveConversion<DateTimeOffsetConverter>();
}
```

Vamos analisar o código do Método:

<div align="center"><img src="https://i.imgur.com/UFlDqfi.png" title="source: imgur.com" /></div>

**Linhas 69 a 74:** Implementamos o Método **ConfigureConventions**, responsável por definir como o Entity Framework lidará com alguns tipos de dados, antes de persistir no Banco de dados, definindo os conversores adequados para cada um. Observe que o Método **ConfigureConventions**, pertence a Classe **DbContext**, e neste caso ele foi sobrescrito. 

**Linhas 71 a 73:** Define que todos os atributos do tipo DateTimeOffset (linha 72), serão convertidos para o formato definido UTC, definido na Classe DateTimeOffsetConverter (linha 73).

Veja o código completo da Classe **AppDbContext**:

```c#
using blogpessoal.Configuration;
using blogpessoal.Model;
using Microsoft.EntityFrameworkCore;

namespace blogpessoal.Data
{
    public class AppDbContext: DbContext
    {
        public AppDbContext(DbContextOptions<AppDbContext> options) : base(options) { }

        protected override void OnModelCreating(ModelBuilder modelBuilder)
        {
            modelBuilder.Entity<Postagem>().ToTable("tb_postagens");
            modelBuilder.Entity<Tema>().ToTable("tb_temas");
            modelBuilder.Entity<User>().ToTable("tb_usuarios");

            modelBuilder.Entity<Postagem>()
                .HasOne(p => p.Tema)
                .WithMany(t => t.Postagem)
                .HasForeignKey("TemaId")
                .OnDelete(DeleteBehavior.Cascade);

             modelBuilder.Entity<Postagem>()
                .HasOne(p => p.Usuario)
                .WithMany(u => u.Postagem)
                .HasForeignKey("UsuarioId")
                .OnDelete(DeleteBehavior.Cascade);

        }

        // Registrar DbSet - Objeto responsável por manipular a Tabela

        public DbSet<Postagem> Postagens { get; set; } = null!;
        public DbSet<Tema> Temas { get; set; } = null!;
        public DbSet<User> Users { get; set; } = null!;

        public override Task<int> SaveChangesAsync(CancellationToken cancellationToken = default)
        {
            var insertedEntries = this.ChangeTracker.Entries()
                                   .Where(x => x.State == EntityState.Added)
                                   .Select(x => x.Entity);

            foreach (var insertedEntry in insertedEntries)
            {
                //Se uma propriedade da Classe Auditable estiver sendo criada. 
                if (insertedEntry is Auditable auditableEntity)
                {
                    auditableEntity.Data = DateTimeOffset.Now;
                }
            }

            var modifiedEntries = ChangeTracker.Entries()
                       .Where(x => x.State == EntityState.Modified)
                       .Select(x => x.Entity);

            foreach (var modifiedEntry in modifiedEntries)
            {
                //Se uma propriedade da Classe Auditable estiver sendo atualizada.  
                if (modifiedEntry is Auditable auditableEntity)
                {
                    auditableEntity.Data = DateTimeOffset.Now;
                }
            }

            return base.SaveChangesAsync(cancellationToken);
        }

        // Ajusta a Data para o formato UTC - Compatível com o Postgres
        protected override void ConfigureConventions(ModelConfigurationBuilder configurationBuilder)
        {
            configurationBuilder
                .Properties<DateTimeOffset>()
                .HaveConversion<DateTimeOffsetConverter>();
        }

    }
}
```

<br />

<div align="left"><img src="https://i.imgur.com/wHTDfQ2.png" title="source: imgur.com" width="4%"/> <a href="https://learn.microsoft.com/en-us/dotnet/api/microsoft.entityframeworkcore.dbcontext.configureconventions?view=efcore-7.0" target="_blank"><b>Documentação: Método ConfigureConventions</b></a></div>

<br />

<h2>👣 Passo 03- Atualizar a Classe PostagemService</h2>



Vamos atualizar a Classe **PostagemService**:

**Método GetAll()**

```c#
public async Task<IEnumerable<Postagem>> GetAll()
{
    return await _context.Postagens
        .AsNoTracking()
        .Include(p => p.Tema)
        .Include(p => p.Usuario)
        .ToListAsync();
}
```

Acrescente o Método **.AsNoTracking()** para não exibir os dados das postagens nas entidades **Tema** e **Usuario**, no Método GetAll(). 

**Método GetByTitulo(string titulo)**

```c#
public async Task<IEnumerable<Postagem>> GetByTitulo(string titulo)
{
    var Postagem = await _context.Postagens
        .AsNoTracking()
        .Include(p => p.Tema)
        .Include(p => p.Usuario)
        .Where(p => p.Titulo.ToUpper()
             .Contains(titulo.ToUpper())
        )
        .ToListAsync();
    
    return Postagem;
}
```

Acrescente o Método **.AsNoTracking()** para não exibir os dados das postagens nas entidades **Tema** e **Usuario**, no Método GetByTitulo(). 

Acrescente o Método **ToUpper()** no Atributo e na Variável para manter a consulta Case Insentive (Ignorar Maiúsculas e Minúsculas) . O PostgresSQL do Render não está no formato Case Insensitive. 

<br />

<h2>👣 Passo 04- Atualizar a Classe TemaService</h2>



Vamos atualizar a Classe **TemaService**:

**Método GetByDescricao(string descricao)**

```c#
public async Task<IEnumerable<Tema>> GetByDescricao(string descricao)
{
    var Tema = await _context.Temas
        .Include(t => t.Postagem)
        .Where(T => T.Descricao.ToUpper()
             .Contains(descricao.ToUpper())
        )
        .ToListAsync();
    
    return Tema;
}
```

Acrescente o Método **ToUpper()** no Atributo e na Variável para manter a consulta Case Insentive (Ignorar Maiúsculas e Minúsculas) . O PostgresSQL do Render não está no formato Case Insensitive. 

Ao final, atualize o repositório no Github e aguarde o deploy ser finalizado.

<br />

<h2>👣 Passo 05 - Configurar o Fuso Horário do Servidor</h2>



Vamos configurar o fuso horário do servidor, para que o horário de criação e atualização da postagem seja persistido corretamente:

1. Na barra de menu lateral do WebService, clique na opção **Environment**:

<div align="center"><img src="https://i.imgur.com/WroFgOo.png" title="source: imgur.com" /></div>

2. Na sequência clique no botão **Add Environment Variable**:

<div align="center"><img src="https://i.imgur.com/OTgpqQg.png" title="source: imgur.com" /></div>

3. Na tela **Environment Variables**, crie uma variável chamada **TZ** e insira o valor **America/Sao_Paulo**, como mostra a imagem abaixo:

<div align="center"><img src="https://i.imgur.com/RBPZKtM.png" title="source: imgur.com" /></div>

4. Clique no botão **Save Changes** para concluir a configuração. 
5. O Deploy da aplicação será refeito. Aguarde a conclusão e inicie os testes da aplicação no próximo passo.

<br />

