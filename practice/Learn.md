Recap of Weeks 1 & 2

Welcome to Week 3! You have successfully built a strong foundation in the first two weeks:

Week 1: Python Essentials 🐍 | You mastered Python basics, learning to think like an analyst by building functions to calculate KPIs and classify operational status (Normal/Warning/Critical). You understood the core loop: Operations ➡️ Data ➡️ Insight ➡️ Action.

Week 2: Data Wrangling with Pandas 🐼 | You tackled the messy reality of data. Using Pandas, you learned to ingest CSVs, clean missing sensor readings, handle duplicates, and aggregate thousands of rows into executive summaries using groupby and rolling windows.


🕳️ The Gap We Fill This Week

In Weeks 1 and 2, we mostly worked with static files (CSVs) that were handed to us. In the real world, data lives elsewhere. It flows in real-time from weather stations via APIs, sits in massive corporate SQL databases, or is buried in PDF reports on government portals. As an Operational Data Analyst, you must be a "Data Hunter." You cannot wait for data to be emailed to you; you must go out, fetch it, integrate it, and combine it with your internal logs.

This week, you will learn to connect Python to the outside world. You will pull live weather data to predict logistics delays, query SQL databases for historical maintenance records, and build a Multi-Source Pipeline that merges internal operations with external context.


Weekly Brief
🔍 Synopsis

This week expands your data horizon beyond static files. You will master data sourcing techniques essential for modern operations:

APIs 🌐: Fetching real-time data (weather, currency rates, commodity prices) using HTTP requests.

Web Scraping 🕷️: Extracting structured data from public reports and open data portals (e.g., KNBS, EPRA).

Databases (SQL) 🗄️: Connecting to relational databases (SQLite/PostgreSQL) to query millions of operational records efficiently.

Integration 🔀: Building a unified workflow that combines API data, database records, and local files into a single analytical DataFrame.


🎓 What to Expect as a Learner

Live Data Feeds ⚡: You will write code that talks to live servers. If the internet is down, your code breaks—this teaches you error handling and resilience!

SQL Integration 🛠️: You will write SQL queries (SELECT, JOIN, WHERE) inside Python scripts using pandas.read_sql.

The "Multi-Source" Challenge 🧩: Your final assignment requires merging three different data sources (e.g., Internal Sales CSV + Weather API + Holiday Calendar) to answer a complex business question.

Soft Skills Focus 🗣️: Lightning Talks! You will have strictly 5 minutes to explain a complex technical integration to the cohort. Brevity and clarity are key.


🎯 Weekly Learning Outcomes

By the end of Week 3, you will be able to:

[Outcome 1] Fetch and parse data from RESTful APIs using the requests library, handling authentication and JSON responses. 🔑

[Outcome 2] Extract data from web sources (HTML tables, PDFs) using BeautifulSoup and open data portals. 🕸️

[Outcome 3] Query relational databases using SQL fundamentals (SELECT, JOIN, GROUP BY) directly within Python. 🗃️

[Outcome 4] Integrate multi-source data by merging API feeds, SQL query results, and local files into a single Pandas DataFrame. 🧬

[Outcome 5] Handle errors gracefully when external services fail (timeouts, bad responses). 🛡️

[Outcome 6] Communicate complexity by delivering a concise 5-minute Lightning Talk explaining the value of external data integration. ⚡


🛠️ Tools Required for Week 3
Category	Tools & Libraries
Existing Stack 🎒	Anaconda, VS Code/Jupyter Lab, Git/GitHub, Pandas, NumPy
New Libraries 🧪	

requests (HTTP calls)




beautifulsoup4 / bs4 (HTML scraping)




sqlalchemy (Database connection)




pandas (Extended: read_json, read_html, read_sql)


Optional Extras 📄	pdfplumber or tabula-py (PDF table extraction)
Accounts Needed 🔑	

Free API Keys (Provided in LMS for OpenWeatherMap, ExchangeRate-API)




GitHub Account (For pushing multi-source scripts)


💡 Quick Tip for the Week

"The best hunters plan their route." Before writing a single line of code this week, sketch out where your data is coming from and how it connects. It will save you hours of debugging! 🗺️


Working with APIs (The Real-Time Pulse)
💡 Concept: Software Talking to Software

APIs (Application Programming Interfaces) allow different software applications to communicate with each other seamlessly. In operations, APIs provide crucial real-time context that helps you pivot instantly. Instead of looking at past logs in a vacuum, APIs allow you to pull in live variables:

🌦️ Weather data affecting supply chain logistics and delivery times.

⛽ Fuel prices affecting operational margins and transport costs.

💱 Exchange rates affecting import duties and international purchasing power.


Core Architecture
🔑 HTTP Basics

When you request data from an API, your script acts as a client sending a GET request to a server. The server responds with a status code indicating the outcome:

200 OK 🟩: Success! The data is yours.

404 Not Found 🟨: The endpoint or resource does not exist.

500 Server Error 🟥: The external server crashed or is facing issues.


🗂️ JSON Parsing

API responses typically arrive in JSON (JavaScript Object Notation) format, which Python reads as deeply nested dictionaries and lists. Your job as an analyst is to parse this structure and "flatten" it into a structured Pandas DataFrame.


🔒 Authentication

Most professional APIs require an API Key (a unique password for your script). Never hard-code keys directly into your scripts where they can be accidentally pushed to GitHub! Instead, store them safely using environment variables.


Applied Activity: Enriching Logistics Data with Weather

We are continuing with our operational dataset from Weeks 1 & 2. Now, we want to see if real-time environmental factors like heavy rain affect our depot throughput. We will write a script to fetch live weather data for our specific depot locations.

💻 The Python Implementation
import pandas as pd
import requests
import json
from datetime import datetime

# --- CONTINUATION FROM WEEK 2 ---
# Recall our cleaned 'df' from Week 2 containing depot operations.
# Let's target the unique cities where our core depots are located:
cities = ['Mombasa', 'Nairobi', 'Kisumu']

# Simulating an API Key (In reality, keep this secret!)
API_KEY = "your_demo_api_key_here"
BASE_URL = "http://api.openweathermap.org/data/2.5/weather"

weather_data_list = []

print("Fetching real-time weather data...")

for city in cities:
    params = {
        'q': city,
        'appid': API_KEY,
        'units': 'metric' # Fetches temperature in Celsius
    }
    
    try:
        # Make the live HTTP request with a 5-second safety timeout
        response = requests.get(BASE_URL, params=params, timeout=5)
        
        # Check if the request was successful
        if response.status_code == 200:
            data = response.json()
            
            # Navigate and extract relevant fields from the nested JSON response
            weather_record = {
                'city': data['name'],
                'temperature': data['main']['temp'],
                'humidity': data['main']['humidity'],
                'condition': data['weather'][0]['description'],
                'wind_speed': data['wind']['speed'],
                'fetch_time': datetime.now()
            }
            weather_data_list.append(weather_record)
            print(f"  ✓ Successfully fetched data for {city}")
        else:
            print(f"  ✗ Failed to fetch {city}: Status Code {response.status_code}")
            
    except requests.exceptions.RequestException as e:
        print(f"  ✗ Connection error for {city}: {e}")

# Convert our list of parsed dictionaries directly into a clean DataFrame
df_weather = pd.DataFrame(weather_data_list)

print("\n=== Live Weather Data DataFrame ===")
print(df_weather)



🎯 Merging Strategy Preview

Connecting the Dots: In a production pipeline, your next step would be to execute a Pandas merge. By linking df_weather with your historical operations dataset on the matching city and date columns, you can perform advanced correlation analyses—proving exactly how much a rainy day slows down your regional supply chain! 📈


Web Data & External Sources
💡 Concept: Hunting Beyond APIs

Not all data comes to you wrapped in a clean, developer-friendly API package. Crucial operational indicators, like government economic updates (KNBS), regulatory pricing revisions (EPRA), industry benchmarks, and competitor prices, often live behind the scenes on public websites or buried deep inside PDF reports.

To capture this data, an Operational Data Analyst must know how to pull information directly from the web:

HTML Tables 🕸️: Using Pandas to automatically scan web pages and extract structured tabular data instantly.

BeautifulSoup 🧪: Parsing specific custom HTML elements, tags, and classes when data is scattered or tables aren't formatted cleanly.

Open Data Portals 🇰🇪: Accessing repositories like the Kenya Open Data Hub to enrich internal company metrics with national demographic or macroeconomic context.


🛠️ Applied Activity: Scraping Regulatory Fuel Prices

In logistics and supply chain analytics, fuel is one of the most volatile operational costs. We need the latest official regulated fuel prices from a regulatory body's website to cross-reference against our internal fleet management and sales data to track margin leakage.

💻 The Python Implementation
import pandas as pd

# Scenario: Many government sites publish monthly data directly in HTML tables.
# This URL is a simulated endpoint representing a public utility board
url = "https://example-kenya-energy-board.go.ke/monthly-prices"

try:
    print("Scanning web page for HTML tables...")
    # Pandas reads the raw HTML and returns a list of all <table> elements found
    tables = pd.read_html(url)
    
    # Assume the first table on the page index [0] contains our pricing matrix
    df_prices = tables[0]
    
    # Clean up column names immediately for downstream database integration
    df_prices.columns = ['Product', 'Region', 'Price_KES', 'Effective_Date']
    
    # Robust Type Casting: Force strings to numeric/datetime values safely
    df_prices['Price_KES'] = pd.to_numeric(df_prices['Price_KES'], errors='coerce')
    df_prices['Effective_Date'] = pd.to_datetime(df_prices['Effective_Date'], errors='coerce')
    
    print("\n=== Scraped Regulatory Prices ===")
    print(df_prices.head())

except Exception as e:
    print(f"\n Scraping failed (Expected in sandbox demo without a live URL): {e}")
    
    # Fallback Pipeline: Standard operational safeguard for classroom simulations
    print("Activating local fallback mechanism...")
    fallback_data = {
        'Product': ['Super Petrol', 'Diesel', 'Kerosene'], 
        'Region': ['Nairobi', 'Nairobi', 'Nairobi'],
        'Price_KES': [194.50, 179.30, 168.20],
        'Effective_Date': ['2026-06-14', '2026-06-14', '2026-06-14']
    }
    df_prices = pd.DataFrame(fallback_data)
    df_prices['Effective_Date'] = pd.to_datetime(df_prices['Effective_Date'])
    
    print("\n===  Using Fallback Demo Data ===")
    print(df_prices)



🛑 Pro-Tips for Web Hunters

The Web Scraping Code of Conduct 📜

Check the robots.txt file: Always append /robots.txt to a website's domain (e.g., example.com/robots.txt) to check if the site owner permits automated bots.

Don't flood the server: Use a small delay (time.sleep()) between requests if scraping multiple pages. You want to extract data, not launch an accidental DDoS attack!

SQL & Database Fundamentals
💡 Concept: Scaling Beyond Flat Files

As an analyst, you will quickly find that Excel starts to crawl and crash when files hit 1 million rows. Relational Databases (SQL) handle billions of rows without breaking a sweat. In the enterprise world, your company's lifeblood data sits securely inside SQL databases.

You cannot always rely on an engineer to export CSVs for you; you must know how to speak directly to the database and request exactly what you need.


🔑 Relational Database Core Concepts

Tables 📊: Structured grids consisting of vertical columns and horizontal rows.

Primary Key (PK) 🆔: A unique identifier for every single row in a specific table (e.g., equip_id).

Foreign Key (FK) 🔗: A column in one table that links directly to a Primary Key in another table to establish an official relationship.

⚡ Why SQL First?

The Golden Rule of Data Architecture: It is significantly faster and more resource-efficient to filter 10 million rows directly inside the database engine than it is to load the entire massive file into your local Python memory just to filter it down there. Filter at the source!


🛠️ Applied Activity: Querying Maintenance Logs

Our historical facility maintenance records are stored in an enterprise database. We need to cross-reference our hardware setup by joining the Equipment table with the Maintenance_Logs table to isolate average repair times per pump model type.

💻 The Python Implementation
import pandas as pd
from sqlalchemy import create_engine

# Create a local, lightweight in-memory SQLite database for this run.
# (In a corporate setting, this string points to an external server, 
# e.g., 'postgresql://user:password@hostname:5432/ops_db')
engine = create_engine('sqlite:///ops_database.db')

# Setup Dummy Tables (Simulating our pre-existing corporate DB)
data_equipment = {
    'equip_id': [101, 102, 103],
    'type': ['Pump_A', 'Pump_B', 'Pump_A'],
    'location': ['Mombasa', 'Nairobi', 'Mombasa']
}
data_logs = {
    'log_id': [1, 2, 3, 4],
    'equip_id': [101, 102, 101, 103],
    'downtime_hours': [2.5, 4.0, 1.5, 3.0],
    'issue_type': ['Seal_Leak', 'Motor_Fail', 'Seal_Leak', 'Sensor_Error']
}

# Seed the database with our simulated tables
pd.DataFrame(data_equipment).to_sql('Equipment', engine, if_exists='replace', index=False)
pd.DataFrame(data_logs).to_sql('Maintenance_Logs', engine, if_exists='replace', index=False)

print("Simulated database populated with 'Equipment' and 'Maintenance_Logs' tables.")

# The SQL Pipeline Query
# We calculate: Total incidents and average downtime grouped cleanly by Equipment Type.
query = """
SELECT 
    e.type AS Equipment_Type,
    COUNT(l.log_id) AS Total_Incidents,
    AVG(l.downtime_hours) AS Avg_Downtime_Hours
FROM Equipment e
JOIN Maintenance_Logs l ON e.equip_id = l.equip_id
WHERE l.downtime_hours > 0
GROUP BY e.type
ORDER BY Avg_Downtime_Hours DESC;
"""

# Execute Query and stream results directly into a structured Pandas DataFrame
print("Routing SQL query through SQLAlchemy engine...")
df_sql_results = pd.read_sql(query, engine)

print("\n=== SQL Aggregation Results ===")
print(df_sql_results)



🏗️ SQL Component Breakdown

SELECT / AVG() 🧮: Tells the server exactly which metrics and dimensions to compute and surface.

JOIN ... ON 🤝: Merges both separate tables together on their matching asset code bridge (equip_id).

GROUP BY 🧱: Instructs the database engine to aggregate the numeric records down into distinct, readable summary lines based on the equipment profile type.

Multi-Source Data Pipeline
💡 Concept: The Power of Integration

The true power of an Operational Data Analyst comes to life when separate data streams are combined. Isolated numbers don't tell the full story, but integrated data reveals clear business answers:

$$\text{Internal Sales Logs} + \text{External Weather Feeds} + \text{Regulatory Prices} = \text{Deep Operational Insights}$$

To construct this comprehensive perspective, you will use two primary data assembly methods:

Merging (pd.merge()) 🤝: Combining distinct datasets side-by-side using shared key columns (exactly like an SQL JOIN).

Concatenating (pd.concat()) 🥞: Stacking datasets with identical column structures on top of one another.

📜 Data Lineage

When building automated pipelines, you must maintain clear Data Lineage—a documented audit trail tracking exactly where every single column originated (e.g., Column A came from the SQL Server, Column B from an API response, and Column C from a local CSV). This ensures data integrity and simplifies future troubleshooting.


🛠️ Applied Activity: The Unified Operations View

We are going to merge our internal operational logs, the live weather API data from Module 1, and the summarized SQL maintenance statistics from Module 3 into a single master tracking DataFrame.

💻 The Python Implementation
import pandas as pd

# 1. Initialize our core internal operations logs (Simulated from Week 2 data)
df_ops = pd.DataFrame({
    'date': ['2026-06-12', '2026-06-13'],
    'city': ['Mombasa', 'Nairobi'],
    'sales_volume': [200, 150]
})
df_ops['date'] = pd.to_datetime(df_ops['date'])

# Simulating the structured df_weather DataFrame built in Module 1
df_weather = pd.DataFrame({
    'city': ['Mombasa', 'Nairobi', 'Kisumu'],
    'temperature': [28.5, 21.2, 24.8],
    'condition': ['Heavy Rain', 'Clear Sky', 'Light Drizzle']
})

print("Initiating multi-source merge pipeline...")

# 2. Merge internal operational data with live external weather parameters
# We use a 'left' join to preserve all core operational records regardless of API matches
df_unified = pd.merge(df_ops, df_weather, on='city', how='left')

print("\n=== Unified Dataset (Ops + Weather) ===")
print(df_unified)

# 3. Incorporate context from our SQL Infrastructure (Broadcasting summary records)
# Using the average downtime metrics computed from the SQL tables in Module 3
avg_downtime_mombasa = 2.0  # Derived from SQL result for Pump_A deployed in Mombasa
df_unified['context_avg_downtime'] = avg_downtime_mombasa

print("\n=== Final Enriched Master Dataset ===")
print(df_unified)



📈 Operational Insight Realized

The Analysis Objective: Now that the data sources are combined, you can run a targeted query: "On days with Heavy Rain, does our logistics sales_volume drop significantly compared to days with a Clear Sky?" This cross-source analysis is only possible because you bridged the gap between your internal operations and external environments!



Soft Skills Thread - Lightning Talks
⚡Activity: The 5-Minute Technical Pitch

As an analyst, writing clean code is only half the battle. Your success hinges on your ability to clearly explain your technical discoveries to non-technical business leaders who control corporate budgets.


🎯 The Task

You have successfully linked external weather feeds with internal logistics tracking logs. Now, you must pitch the commercial value of this technical pipeline to a busy Operations Director.


⏱️ The Constraints

Time Limit: Exactly 5 minutes. (You will be cut off at 5:30!)

Slide Limit: Strictly 3 slides max.


🗺️ Presentation Blueprint

 ┌───────────────────────────────────────────────────────────────────────┐
 │                              SLIDE 1                                   │
 │                            The Problem                                 │
 │  "We suffer regular throughput drops on unpredictable weekdays, cost-  │
 │   ing us thousands in idled fleet labor without a clear root cause."   │
 └────────────────────────────────────────────────────────────────────────┘
                                     │
                                     ▼
 ┌────────────────────────────────────────────────────────────────────────┐
 │                              SLIDE 2                                   │
 │                           The Solution                                 │
 │  "I built an automated pipeline connecting our internal depot systems  │
 │   to live external weather tracking APIs using Python."                │
 └────────────────────────────────────────────────────────────────────────┘
                                     │
                                     ▼
 ┌────────────────────────────────────────────────────────────────────────┐
 │                              SLIDE 3                                   │
 │                             The Value                                  │
 │  "Data proves localized rain causes a 15% drop. We can now proactively │
 │   schedule preventive maintenance during storms to eliminate downtime."│
 └────────────────────────────────────────────────────────────────────────┘



🎯 Key Evaluation Criteria

Simplifying Complexity: Can you explain an API join without using confusing developer jargon?

Time Discipline: Can you deliver a complete, impactful narrative before the timer sounds?

Pressure Management: Can you answer unexpected, bottom-line questions during a fast-paced executive Q&A session?




From File Analyst to Data Hunter

Week 3 has officially transformed you from an analyst confined to local, static files into a Data Hunter capable of sourcing, extracting, and engineering live information from across the web! You now possess the tools to build resilient pipelines that adapt to the real world.


🧭 The Sourcing Toolkit You Mastered

                 ┌──────────────────────────────────────┐
                 │        YOUR NEW DATA HORIZON         │
                 └──────────────────────────────────────┘
                                    │
       ┌────────────────────────────┼────────────────────────────┐
       ▼                            ▼                            ▼
 🌐 Real-Time                  🕷️ Unstructured               🗄️ Enterprise
     APIs                        Web Scraping                  SQL Databases
 (requests + JSON)            (Pandas + BeautifulSoup)       (JOINs + aggregations)



1. APIs (The Real-Time Pulse) 🌐

You learned how to establish live connections with external servers using the requests library. You mastered the basics of HTTP web protocols, handled authentication protocols safely, and learned to parse complex, nested JSON payloads into clean, analytical Pandas DataFrames.

2. Web Scraping (The Public Commons) 🕷️

When official developer portals don't exist, you don't give up. You gained the skills to extract structured data from public government portals, economic reports, and utility boards using pd.read_html() and BeautifulSoup, giving you access to critical external benchmarks.

3. SQL & Databases (The Data Warehouse) 🗄️

You moved past the data volume limits of Excel. By mastering foundational relational database queries (SELECT, JOIN, GROUP BY), you now know how to communicate directly with corporate data warehouses containing billions of rows, ensuring you filter data at the source for optimal efficiency.

4. Pipeline Integration (The Unified View) 🧬

You engineered a robust Multi-Source Pipeline. By executing advanced Pandas joins (pd.merge()), you combined internal operational logs with real-time weather metrics and historical database context. This multi-dimensional view allows you to perform advanced, root-cause business analyses.

5. Strategic Communication (The Executive Pitch) 🗣️

You proved that technical skills mean nothing without business buy-in. Through high-pressure 5-Minute Lightning Talks, you practiced condensing complex integration workflows into clear, value-driven presentations tailored specifically for busy operations executives.


🚀 The Road Ahead

Your New Reality: You no longer need to wait around for data to be emailed to you in a pristine CSV file. You have the skills to go out, capture it from any repository, and merge it yourself to solve complex operational problems. Happy hunting! 🏹📊