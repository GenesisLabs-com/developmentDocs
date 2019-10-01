# Add User in MongoDB

After Writing Mongo and press enter, you have to write that command

```
db.createUser(
   {
      user: "rnssol",
      pwd: "rnssol",
      roles: [ { role: "readWrite", db: "colorplatform" } ]
   }
);
```

From Database To database and then server name. You can add more parameters in case of security check 'ON'

```
db.copyDatabase("DATABASENAME", "DATABASENAME", "localhost:27018")
```
