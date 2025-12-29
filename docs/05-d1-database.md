# Module 5: D1 Database Setup (30 minutes)

Create a D1 database and add tables via the Cloudflare Dashboard.

---

## What You'll Learn

- Create a D1 database
- Create tables with SQL
- Insert data
- Query data from your Worker

---

## Step 1: Navigate to D1

1. Go to [https://dash.cloudflare.com](https://dash.cloudflare.com)
2. In the left sidebar, click **Build** → **Storage & databases** → **D1 SQL database**
3. You'll see the D1 overview page

---

## Step 2: Create a D1 Database

1. Click **Create database** button
2. Enter a database name (e.g., `workshop-db`, `photo-app`)
   - Can contain letters, numbers, hyphens
   - Must be lowercase
3. Click **Create database**
4. Wait for creation (usually 10-30 seconds)

---

## Step 3: Create a Table

Once your database is created:

1. Click on your database name
2. Click the **Console** tab
3. You'll see a SQL editor

Now let's create a table for photo captions:

```sql
CREATE TABLE photos (
  id INTEGER PRIMARY KEY AUTOINCREMENT,
  filename TEXT NOT NULL,
  caption TEXT,
  created_at DATETIME DEFAULT CURRENT_TIMESTAMP
);
```

4. Paste this SQL into the editor
5. Click **Execute** button
6. You should see a success message

---

## Step 4: Insert Sample Data

In the same SQL editor, add some sample data:

```sql
INSERT INTO photos (filename, caption) VALUES
('photo1.jpg', 'Beautiful sunset at the beach'),
('photo2.jpg', 'Mountain landscape view'),
('photo3.jpg', 'City lights at night');
```

1. Paste this into the editor
2. Click **Execute**
3. You should see "3 rows inserted"

---

## Step 5: Query Your Data

Let's verify the data was inserted:

```sql
SELECT * FROM photos;
```

1. Paste this into the editor
2. Click **Execute**
3. You should see 3 rows of data

---

## Step 6: Understand the Table Structure

```
photos table:
┌────┬──────────────┬──────────────────────────┬─────────────────┐
│ id │ filename     │ caption                  │ created_at      │
├────┼──────────────┼──────────────────────────┼─────────────────┤
│ 1  │ photo1.jpg   │ Beautiful sunset...      │ 2024-01-15...   │
│ 2  │ photo2.jpg   │ Mountain landscape...    │ 2024-01-15...   │
│ 3  │ photo3.jpg   │ City lights at night     │ 2024-01-15...   │
└────┴──────────────┴──────────────────────────┴─────────────────┘
```

---

## Step 7: Get Database Connection Info

You'll need this to connect from your Worker:

1. Click **Settings** tab
2. Look for **Database ID**
3. Copy the ID (you'll need it in the next module)

Example:
```
xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
```

---

## Step 8: Understand D1 Pricing

D1 is very affordable:

| Item | Cost |
|------|------|
| Storage | $0.50 per GB/month |
| Reads | Free (first 10M/month) |
| Writes | Free (first 100k/month) |

For this workshop, you'll stay in the free tier.

---

## Key Takeaways

✅ Created a D1 database  
✅ Created a table with SQL  
✅ Inserted sample data  
✅ Queried the data  
✅ Got database connection info  

---

## Dashboard Navigation Summary

```
Cloudflare Dashboard
├── Build (left sidebar)
│   ├── Storage & databases
│   │   ├── D1 SQL database
│   │   │   ├── Create database
│   │   │   ├── Console (SQL editor)
│   │   │   ├── Settings (Database ID)
│   │   │   └── Backups
```

---

## Common SQL Commands

| Command | What It Does |
|---------|-------------|
| `CREATE TABLE` | Create a new table |
| `INSERT INTO` | Add data to table |
| `SELECT` | Query data |
| `UPDATE` | Modify existing data |
| `DELETE` | Remove data |
| `DROP TABLE` | Delete entire table |

---

## Next Steps

Ready to connect R2 and D1? Go to **[Module 6: Photo Gallery App](./06-photo-gallery.md)** to build the complete app.

---

## Troubleshooting

### SQL error when creating table?
- Check for typos in column names
- Make sure column types are valid (TEXT, INTEGER, DATETIME)
- Check parentheses and commas

### Can't see data after insert?
- Run `SELECT * FROM photos;` to verify
- Check the table name is correct
- Try refreshing the page

### Database not showing?
- Wait 30 seconds for creation
- Refresh the page
- Try a different browser

---

## Resources

- [D1 Documentation](https://developers.cloudflare.com/d1/)
- [SQL Tutorial](https://www.w3schools.com/sql/)
- [SQLite Documentation](https://www.sqlite.org/docs.html)
- [Getting Started with D1](https://developers.cloudflare.com/d1/get-started/)
