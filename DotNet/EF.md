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
```c#
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
```c#
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

```c#
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
```c#
public interface IAdminProcedure
	{
		int AddAdmin(string firstName, string lastName, string email, string password);
	}
```
After that create a class and inherit the interface and implement the methods. But here in constructor you can get the EFDBContect from Dependency injection. Get that and save it in a variable.

```c#
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
```c#
_context.SaveChanges() // This method is used to save the data from DBSet to Database
```

After creating the Procedure you need to add those in <b>Program.cs</b> file.
```
builder.Services.AddTransient<IAdminProcedure, AdminProcedure>();
```


### Link to DB
Now in <b>Program.cs</b> file we need to add Database detials to connect to DB.
```c#
builder.Services.AddDbContext<EFDBContext>(options => options.UseNpgsql("YOUR_DB_CREDENTIALS"));
```

### Add changes in DB
To reflect the changes done in EF table in real DB we need to create the migration file from the code and we need to execute that code so that it will get reflected in the database.

To generate the migration file we will be executing the following code in terminal. This code must be executed inside the project folder.
```sh
dotnet ef migrations add "DESCRIPTION"
```
Once this is executed successfully, build file is automatically generated.

We can use the below code in terminal to run these migration files to change the DB
``` sh
dotnet ef database update
```
To run the above code successfully you need to have the below code placed under the <b>EFDBContext</b> class.
```c#
public class BloggingContextFactory : IDesignTimeDbContextFactory<EFDBContext>
    {
        public EFDBContext CreateDbContext(string[] args)
        {
            var optionsBuilder = new DbContextOptionsBuilder<EFDBContext>();
            optionsBuilder.UseNpgsql("YOUR_DB_CREDENTIALS");

            return new EFDBContext(optionsBuilder.Options);
        }
    }
```
This will help in getting the info on which DB it needs to access and change the tables.

## Other things to look out in EF
### How to auto generate the values
To auto generate the values in database (like incrementing the integer when you add a new row in the table), you need to use <b>DatabaseGenerated()</b> method in data anotations.

```c#
public class AdminDetails
	{
		[Key]
		[DatabaseGenerated(DatabaseGeneratedOption.Identity)] // This is used to auto generate unique id
		public long adminId { get; set; }
	}
```

Now in my code adminId details are auto generated.
### How to make primary key and foreign key relations in table

To make the foreign key relation in the table you have to link both the tables.
EF makes a property as foreign key property when its name matches with the primary key property of a related entity.

Let's take an example. I have two tables Admin and Institution. I need to make institution as a foreign key to admin table. For this I need to add following code.

This is how my Institution class looks like
```c#
[Table("institution_details", Schema = "main")]
	public class InstitutionDetails
	{
        [Key]
        [DatabaseGenerated(DatabaseGeneratedOption.Identity)] // This is used to auto generate unique id
        public long institutionId { get; set; }
        public string institutionName { get; set; }
        [Required]
        public string institutionEndingMail { get; set; }
        public string physicalAddress { get; set; }
        public string phoneNumber { get; set; }
        public string countryCode { get; set; }

        public ICollection<AdminDetails> Admins { get; set; } // We need to add the collection of AdminDetails as it is the one that has InstitutionId as foreign key
    }
```

This is how my AdminDetails class looks like
```c#
[Table("admin_details", Schema = "main")]
	public class AdminDetails
	{
		[Key]
		[DatabaseGenerated(DatabaseGeneratedOption.Identity)] // This is used to auto generate unique id
		public long adminId { get; set; }
		public string firstName { get; set; }
		public string lastName { get; set; }
		[Required]
		public string email { get; set; }
		public string password { get; set; }
		public DateTime createdAt { get; set; }
		public DateTime updatedAt { get; set; }

		//Foreign Key
		[ForeignKey("institutionId")]
        public long institutionId { get; set; }
		public InstitutionDetails Institution { get; set; }
    }
```

Once you add all those requeired one you are good to go.


<a id="links">### Learning Links</a>

[EF-Reference](https://www.entityframeworktutorial.net/code-first/table-dataannotations-attribute-in-code-first.aspx) - Check out this link to know more about EF.
