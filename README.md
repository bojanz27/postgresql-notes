# postgresql-notes
Just a c/p of handly postgres commands and utils 

### Scaffold PostgreSQL EFCore DbContext (package manager console)

```
Scaffold-DbContext "Host=localhost;Database=mydatabase;Username=myuser;Password=mypassword" Npgsql.EntityFrameworkCore.PostgreSQL -o Models
```
