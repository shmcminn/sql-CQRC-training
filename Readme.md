# SQL training for CQ Roll Call 

SQL stands for Structured Querly Lanugage, and is — in my humble opinion — one of the easiest coding languages to learn and the most immediately useful for journalists. This is because the *syntax*, or words that the language expects use to use to tell it to do what we want, is most similar to spoken English. 

I bet you can more or less figure out what this code does, without knowing any SQL.

```
SELECT member_name, age, party FROM congress_members_database
WHERE state IS "California"
```

The reason why SQL is so powerful for journalism is it lets us ask very specific questions of the data, and get reproducable answers. That means if an agency puts out the same dataset quarterly, you can run the same SQL script on it every three months and get the latest answers to your questions. Also, if we want to change just one thing in our analysis — say, look at a different state — all we have to do is modify that one line of code, vs. having to go through and do an entire Excel analysis again.

There's lots of fancy software you can get to run SQL and store databases in, but if you have Mozilla Firefox on your computer you actually already have a *relational database manager* ready to go.

### To install SQLite in Firefox
##### Step 1
Go [here](https://addons.mozilla.org/en-US/firefox/addon/sqlite-manager/) **in Firefox** and click Add to Firefox

https://addons.mozilla.org/en-US/firefox/addon/sqlite-manager/

##### Step 2 
Hit install, then close Firefox and reopen it

##### Step 3
Go to "tools" on the top of your browser and click on SQLite Manager

### Download 2016 presidential candidate expenditure data
Right click on potus_expenditures_2016.csv, select "save as"

Save to computer

### Loading data into SQLite

Open up SQLite and click on the "new" icon in the top left. This will create a new database that you can store tables inside.

You can name this database whatever you want, and save it anywhere on your computer. I'm calling this candidate_expenditures

**Note:** This will create a candidate_expenditures.sqlite file on your computer that you can go back to in SQLite and the tables you're working on will still be there.

You now have your first database set up. Woo!

<img id="" src="http://media.cq.com/images/giphy-downsized.gif">

We want to insert a table into the database, which SQLite has a handy import CSV tool for.

Click on the blue icon with an arrow in the top toolbar, which opens up the Import Wizard.

<img src="http://media.cq.com/images/screenshot2017-04-17at3.39.26pm.png" alt="" /> 

Click "Select File" and choose the csv file you downloaded earlier

Make sure to select "First row contains column names," which tells SQLite that we have headers we want it to recognize.

<img src="http://media.cq.com/images/screenshot2017-04-17at1.11.33pm.png" alt="" />

The other settings we can leave in place because it's a standard CSV file. If it was a TSV or a file type that used some other delimter, you would want to modify the settings on this screen. 

Hit OK, which will get SQLite to start parsing the CSV data. 

<img id="" src="http://media.cq.com/images/screenshot2017-04-17at1.06.45pm.png">

Click OK when it asks if you want to modify the new table you're creating.

This next screen asks how we want to handle columns. The only thing we need to pay attention to are the Data Type fields.

Select the appropriate data types (for this one, everything is a VARCHAR except for disb_amt, which is a FLOAT, and disb_dt, which is DATETIME)

<img id="" src="http://media.cq.com/images/screenshot2017-04-17at1.09.24pm.png">

### Analyzing the data

Just like reporting any story, we want to treat our analysis of data like an interview. Think of smart questions based on what your "source" knows, then ask those questions in a direct way. 

First we need to see what info the data may have, which we can do with our first SQL query. Go to the Execute SQL tab and type this in, then click Run SQL.

```
SELECT * FROM potus_expenditures_2016
LIMIT 100
```

This is the most basic form for a SQL query: Select something from a table. In this case, we're selecting everything using the wildcard symbold (*), which means we will get a display of everything in the table.

(Since this is such a big file and we don't want to kill your computer right now, add that `LIMIT 100` at the bottom of your query. This just shows us the first 100 rows.)

<img src="http://media.cq.com/images/screenshot2017-04-17at3.40.53pm.png" alt="" />

The first thing I notice here is each row has the name of a candidate `cand_nm`, and how much they spent on a single expenditure `disb_amt`.

One question I have from this data, which goes from 2014 though the first quarter of 2016, is who spent the most money out of the presidential candidates in this time frame?

In plain English, we'd ask this question as something along the lines of: "When you group the expenditures by each candidate, who had the highest sum of disbursement amounts?" 

In SQL, we'd write it as:

```
SELECT cand_nm, sum(disb_amt) FROM potus_expenditures_2016
GROUP BY cand_nm
ORDER BY sum(disb_amt) desc
```

Let's break that down:

First, we `SELECT` the column containing the candidate names, and make a new column with the sum of all disbursement amounts. Remember we need to specify we're selecting `FROM potus_expenditures_2016`

```SELECT cand_nm, sum(disb_amt) FROM potus_expenditures_2016```

Then, we want to `GROUP` the data `BY` candidate names

```GROUP BY cand_nm```

Last, just to make reading it easier, we `ORDER` the data `BY` those sums, descedning 

```ORDER BY sum(disb_amt) desc```

So go ahead and hit Run SQL and you should get something lke this.

<img src="http://media.cq.com/images/screenshot2017-04-17at2.14.07pm.png" alt="" />

Now we can see who spent the most, and by how much. Look how Trump just barely made the top five!

<img src="http://media.cq.com/images/giphy-downsized-trumptu.gif" alt="" />

Using the same functions, we can also see what the candidates spent the most on.

```
SELECT disb_desc, cand_nm, disb_amt FROM potus_expenditures_2016
GROUP BY disb_desc, cand_nm
ORDER BY disb_amt desc
```

<img src="http://media.cq.com/images/screenshot2017-04-17at2.24.06pm.png" alt="" />

At this point it looks like the highest category for anyone had been Clinton's spending on online ad buys. 

But look at the top two entries for Cruz - Postage/List Rental and Media/List rental. It looks like these categories might not be consistent, and should really be combined to give us an accurate picture of how much was spent on list rentals, for example.

Let's switch gears and look at just Trump's spending, since we know he ended up winning.

We're going to add a new function here: `WHERE`. This does exactly what it sounds like: it gives us only rows where a condition is true.

```
SELECT cand_nm, recipient_nm, disb_desc, disb_amt FROM potus_expenditures_2016
WHERE cand_nm LIKE '%Trump%'
ORDER BY disb_amt desc
```

Notice how instead of just saying `cand_nm IS 'Trump, Donald J.'`, we used `LIKE` instead. This gives us the flexibility to ask for any rows that contain Trump. The percent signs here act as an "anything" symbol, so we asked for rows `WHERE` the cand_nm value starts with anything, then has 'Trump', then has anything after that.

And why did we put quotes around Trump's name? Technically, because its a string — which is one of the types of variables used in computer programming. An easier way to remember this is if you're typing something SQLite won't recognize — a name, date, place, etc — you need to wrap it in quotes to tel SQLite you want *exactly* that. Words that SQLite recognizes like `SELECT`, `FROM` and `IS`, as well as numbers, don't need quotes.

We also selected the `recipient_nm` category here, to see who was getting these large payments. The `disb_desc` column, again, tells us what they were for.

<img src="http://media.cq.com/images/screenshot2017-04-17at2.36.29pm.png" alt="" />

Look at all the giant payments going to one company. This makes me think of another question I want to ask: Who did our president pay the most to during this part of  his campaign? 

```
SELECT cand_nm, recipient_nm, sum(disb_amt) FROM potus_expenditures_2016
WHERE cand_nm LIKE '%Trump%'
GROUP BY recipient_nm
ORDER BY sum(disb_amt) desc
```

See how we drop the `disb_desc` column here, since two expenditures could go to the same vendor for payments with different descriptions. If we included it, it would just arbitrarily choose one of the descriptions, which may or may not be true for all payments to that vendor.

<img src="http://media.cq.com/images/screenshot2017-04-17at3.42.22pm.png" alt="" />

From this we can see Rick Reed Media, Inc. got a ton more money than the other people Trump was paying. For our last query, let's see what all those payments were for.

```
SELECT cand_nm, recipient_nm, disb_desc, disb_amt FROM potus_expenditures_2016
WHERE cand_nm LIKE '%Trump%' AND recipient_nm LIKE '%RICK REED%'
ORDER BY disb_amt desc
```

Notice how we asked SQLite to use two `WHERE` statements? All it takes was an `AND`, and it now pulls only records where the candidate is Trump AND the recipient is "something something Rick Reed something something" (because of the percent signs).

<img src="http://media.cq.com/images/screenshot2017-04-17at2.45.00pm.png" alt="" />

## More resources

[SQL syntax cheatsheet, by MaryJo Webster at the Star Tribune](http://mjwebster.github.io/DataJ/SQLite/SQLiteCheatSheet.pdf)

[Guide to doing common Excel tasks in SQL, also by Webster](https://docs.google.com/document/d/1spB5gs82X1oVmCweuFWHtlZ-hA3xSlYhvIbL7dmAx88/edit)

[A similar, more detailed intro to SQL, by Troy Thibodeaux at AP](https://github.com/tthibo/SQL-Tutorial)

[Not for SQL, but a good primer on the mindset of "interviewing" data, by David Eads of NPR](http://training.npr.org/visual/what-to-do-with-a-big-pile-of-data/)