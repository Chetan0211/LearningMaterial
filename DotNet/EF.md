# Entity Framework
## What is Entity Framework?
Entity Framework Core (EF Core) is a popular Object-Relational Mapper (ORM) framework included in .NET Core. It acts as a bridge between your .NET Core applications and relational databases.
## Setting up EF in DotNet core
Here we will be setting up the EF and for this we will be using Postgres SQL as our database.
### Add Nuget packages
You need to add EntityFramework package and Npgsql for EF package<br/>

<b><i>Microsoft.EntityFrameworkCore</i></b><br/>
<b><i>Microsoft.EntityFrameworkCore.Design</i></b><br/>
<b><i>Npgsql.EntityFrameworkCore.PostgreSQL</i></b><br/>
<b>NOTE:</b> Check the version while you download. It needs to match with .net version <br/>
Once these are installed you are good to go with typing the code.

### Create EFDBContext class and Extend DBContext
We need to create a class named EFDBContext and inherit DBContext to it.
```
public class EFDBContext : DbContext
	{
		public EFDBContext(DbContextOptions<EFDBContext> options) : base(options)
		{
		}
	}
```


### Create tables
We need to create the tables using the class.
Let's take an example. Let's create admin table
```
[Table("admin_details", Schema = "main")]
	public class AdminTable
	{
		[Key]
		public long adminId { get; set; }
		public string firstName { get; set; }
		public string lastName { get; set; }
		public string email { get; set; }
		public string password { get; set; }
		public DateTime createdAt { get; set; }
		public DateTime updatedAt { get; set; }
	}
```

After creating the AdminTable class, we need to create DBSet for that class in EFDBContext.

```
public class EFDBContext : DbContext
	{
		public EFDBContext(DbContextOptions<EFDBContext> options) : base(options)
		{
		}

        DbSet<AdminTable> adminTable;
	}
```

Like wise we need to createa all the tables and add those in EFDBContext.

<b>NOTE:</b> For data annotations look into the Learning [Link](#links) given below.<br/>

### Create functions to perform CRUD operations
To perform CRUD operations we can use the field created in EFDBContext. Each field contains respected table data. Using Linq operations we can perform all the operations.
Best way to perform these operations is to keep each operation in a method and keep all the methods performed on a table in one class. In that way code looks little simple.

Now let's see how to create it.

First we need to create an interface class and write all the functions it needs to perform.
```
public interface IAdminProcedure
	{
		int AddAdmin(string firstName, string lastName, string email, string password);
	}
```
After that create a class and inherit the interface and implement the methods. But here in constructor you can get the EFDBContect from Dependency injection. Get that and save it in a variable.

```
public class AdminProcedure : IAdminProcedure
	{
		EFDBContext _context;
		public AdminProcedure(EFDBContext context)
		{
			_context = context;
		}

        public int AddAdmin(string firstName, string lastName, string email, string password)
        {
            //Implement the code
        }
    }
```
So as you can see using the <b>_context</b> you can actually get the tables and perform CRUD on them. To save the modified data into database you need to use
```
_context.SaveChanges() // This method is used to save the data from DBSet to Database
```

After creating the Procedure you need to add those in <b>Program.cs</b> file.
```
builder.Services.AddTransient<IAdminProcedure, AdminProcedure>();
```


### Link to DB
Now in <b>Program.cs</b> file we need to add Database detials to connect to DB.
```
builder.Services.AddDbContext<EFDBContext>(options => options.UseNpgsql("YOUR_DB_CREDENTIALS"));
```

## Other things to look out in EF
### How to auto generate the values
To auto generate the values in database (like incrementing the integer when you add a new row in the table), you need to use <b>DatabaseGenerated()</b> method in data anotations.

```
public class AdminDetails
	{
		[Key]
		[DatabaseGenerated(DatabaseGeneratedOption.Identity)] // This is used to auto generate unique id
		public long adminId { get; set; }
	}
```

Now in my code adminId details are auto generated.
### How to make primary key and foreign key relations in table
(Yet to complete this part)

<a id="links">### Learning Links</a>

[EF-Reference](https://www.entityframeworktutorial.net/code-first/table-dataannotations-attribute-in-code-first.aspx) - Check out this link to know more about EF.
