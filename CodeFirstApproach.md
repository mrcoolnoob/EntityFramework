# Code First Approach

### 1. Create a VS project

It can be WPF, console, or web.,

### 2. Install EntityFramework using Nuget Package Manager

### 3. We will create a class Post

This will be the class which will be actually get translated to the table in the database.

![image-20230907002648042](https://i.imgur.com/IjASsAY.png)

### 4. Create a DB context

It's an abstraction for the database which we used to communicate with the database such as loading or storing data etc.

All we have to do is create a class that derives from **DbContext** and then defines one or more DbSets (table in it).

![image-20230907003238379](https://i.imgur.com/5J1LSnf.png)

### 5. Specify a `Connection String` to database

Go to the **app.config** file. Create a **Connection String** element and respective element inside it.

![image-20230907003629490](https://i.imgur.com/QoqRVNe.jpg)

![image-20230907003558427](https://i.imgur.com/Pw6GUIm.png)

> We need the **providerName** to work with the `System.Data.SqlClient`. **SSPI** indicates that we're using the windows authentication.
>
> You can also provide name attribute to the connection string such as `Name="DefaultConnection"` by default if you don't provide it then it would be same as your **DbContext**. In case, we have the name other than the **DbContext** then we will be needing to define it explicitly in the constructor as follow.

```sh
public plutoContext(): base("name=DefaultConnection")
{

}
```

### 6. Enabling code first migrations

This is the command you run only one time.

```sh
enable-migrations
```

## Changing the database

Now, when we want to make a change in the database. We will start with the code i.e. we will update the respective class and it will be shown by a migration.

```sh
# add-migration <NAME_OF_MIGRATION>
add-migration CreatePost
```

This will create a new folder migrations. This is kind fo a change script but in automatted manner. 

## Applying the migrations to the database

```sh
Update-Database
```

## One-To-Many Relationship

Typically, you can see that one-to-many relationship is being shown by the ICollection. However you can also use the IList using IList can be better in a way such that IList also derives from ICollection but it also gives you the ability to add or remove item to the collection it also provides an indexer which allows you to access an element of the collection using index. 

```c#
public virtual ICollection<Course> Courses { get; set; }
```

**Virtual** keyword is not really important it is more of a use for lazy loading which we will discuss later.

## Table Thing

![image-20230907223931182](https://i.imgur.com/p4h9tIS.png)

It is absolutely essential to initialize your list properties as shown in the constructor above. Otherwise, you will get the Null exception.  

## Migrations

Say we have a database which already have the migrations. But your project don't have the migrations. So, then we need to use the switch to **ignore the changes** it tells that we already have the all the models in the database that's why we need to do nothing on the database.

```sh
add-migration InitialModel -IgnoreChanges
```

Say if we have already created a migration which you don't want to apply then you can use the **Force** flag to overwrite the existing migration given the same name of the migrations as follow.

```sh
add-migration InitialModel -IgnoreChanges -Force
```

The above line will create the empty migration and overriding the existing migration.

### Can we have multiple migrations

No, you can have only one migration at a time i.e. before you create a new migration you need to push the previous migration to the database using the command `Update-Database`.

## Populating data in database

The thing is what we need to do is populate our database say with records when we run the migration. We can totally do this using the following commands.

```sh
Sql("<sql_query>") 
```

![image-20230907231301378](https://i.imgur.com/tWZv683.png)

This **Sql** function is not only limited to insertion of records but you can run any query such as updating the existing records as follow.

```sh
Sql("Update Courses SET Category_Id=1")
```

## Migrations if data is already there

### Renaming the column

![image-20230907234626862](https://i.imgur.com/o49S15k.png)

OR

![image-20230907234843694](https://i.imgur.com/BmAxsAZ.png)

In down, the code is opposite to the Up i.e. how to undo the Up thing.

## Preserving the existing data in a table before deleting it

We create a table with the name started with **_** i.e. for Categories table we need to rename to the table **_Categories**. 

![image-20230907235523270](https://i.imgur.com/jbU60oK.png)

![image-20230907235556447](https://i.imgur.com/N5VrWek.png)

## Recovering from mistakes

In order to fix a mistake actually we need to make another migrations.

## Downgrading the Database

![image-20230908001126209](https://i.imgur.com/z4yWfOY.png)

### Option 2: Using a existing Database

![image-20230908001223605](https://i.imgur.com/Zglqzyu.png)

To go back to the latest migration. Simply run

```sh
Update-Database
```

## How to populate the database with data

When you run the command

```sh
enable-migrations
```

The Migrations folder is created which contains the **configuration.cs** file.

In this file there is a method **seed** which we can use to populate our database with dummy data once all the migrations are being run.

> Remember! This approach is only for the development i.e. populating database for the development purpose.

![image-20230908002405264](https://i.imgur.com/MypcC16.jpg)

For populating data for the production you can create an empty migration and put in the **Sql** commands in their to fill in the database.

