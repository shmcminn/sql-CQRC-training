# SQL training for CQ Roll Call 

SQL stands for Structured Querly Lanugage, and is — in my humble opinion — one of the easiest coding languages to learn and the most immediately useful for journalists. This is because the *syntax*, or words that the language expects we use to tell it to do what we want, is most similar to spoken English. 

I bet you can more or less figure out what this code does, without knowing any SQL.

```
SELECT member_name, age, party from congress_members_database
WHERE state == "California"
```

The reason why SQL is so powerful for journalism is it lets us ask very specific questions of the data, and get reproducable answers. That means if an agency puts out the same dataset quarterly, you can run the same SQL script on it every three months and get the latest answers to your questions. 

There's lots of fancy software you can get to run SQL and store databases in, but if you have Mozilla Firefox on your computer you actually already have a *relational database manager* ready to go.

### To install SQLite in Firefox
#### Step 1
Go here **in Firefox** and click Add to Firefox

https://addons.mozilla.org/en-US/firefox/addon/sqlite-manager/

#### Step 2 
Hit install, then close Firefox and reopen it

#### Step 3
Go to "tools" on the top of your browser and click on SQLite Manager

