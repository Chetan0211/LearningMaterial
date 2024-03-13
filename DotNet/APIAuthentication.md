# API Authentication
There are 3 ways to authenticate the API.<br/>
1. HTTP Basic Authentication
2. API Key Authentication
3. OAuth Authentication

## **1. HTTP Basic Authentication**
(TODO: Yet to complete)

## **2. API Key Authentication**
Here we will be using some random key and sending that key in the header. This key will be checked in backend in middleware, if the key matches it will continue or it will throw an error.

In the backend we need to write the middleware code.
It looks something like below.
```c#
public class APIKeyMiddleware
	{
		readonly RequestDelegate _next;
		public APIKeyMiddleware(RequestDelegate next)
		{
			_next = next;
		}

		public async Task InvokeAsync(HttpContext context)
		{
			if(!context.Request.Headers.TryGetValue("apikey", out var extractedAPiKey))
			{

				context.Response.StatusCode = 401;
				await context.Response.WriteAsync(new { errorMessage = "Authentication failed" }.ToString());
				return;
			}
			if (!extractedAPiKey.Equals("SomeRandomAPIkey"))
			{
                context.Response.StatusCode = 401;
                await context.Response.WriteAsync(new { errorMessage = "Authentication failed" }.ToString());
                return;
            }

			await _next(context);
        }
	}
```

As you see above my middleware code, my constructor has a parameter **RequestDelegate**. This is used to continue the request if it satisfies the authentication.

It also contains **InvokeAsync** method with a parameter **HttpContext**. This will be invoked when ever there is a request going through the pipeline. Here we will be checking the Authentication of the request and if this passes we will be using **RequestDelegate** to pass the **HttpContent** with the pipeline. If the request fails to authenticate then the code will be returned.

Once this code is done we need to add the middleware in the pipeline. This is done in **Program.cs** file.

```c#
var app = builder.Build();

// Configure the HTTP request pipeline.
if (app.Environment.IsDevelopment())
{
    app.UseSwagger();
    app.UseSwaggerUI();
}

app.UseHttpsRedirection();

app.UseMiddleware<APIKeyMiddleware>(); // Here we added out middleware code. This will be executed in the pipeline.

app.UseAuthorization();

app.MapControllers();

app.Run();
```

## **3. OAuth Authentication**
(TODO: Yet to complete)
