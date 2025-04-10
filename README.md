# BookHarvest
BookHarvest is a Python pipeline that extracts book data (titles, authors, ratings, covers) from APIs, web pages, and CSVs, stores it in SQLite, and serves it via a Flask API.

# Overview
This project is a data engineering pipeline designed to extract, transform, and store book-related data from multiple sources, including APIs, web pages, and CSV files. The pipeline focuses on collecting information about books (e.g., title, author, rating, and cover image URL) from various genres, such as religious books and Harry Potter titles, and storing them in a SQLite database for further analysis. Additionally, a Flask-based REST API is provided to retrieve the stored book data. The project is built using Python and leverages libraries like requests, BeautifulSoup, pandas, sqlite3, and Flask.

# Features
Data Extraction: Fetches book data from:
OpenLibrary API (religious books)
Goodreads web pages (Christian books)
Google Books API (Harry Potter books)
ReadAnyBook website
Local CSV files
Data Transformation: Processes raw data into a consistent format, extracting key attributes like title, author, rating, and cover image URL.
Data Storage: Stores transformed data in a SQLite database for persistence and easy querying.
Data Retrieval: A Flask API endpoint to fetch book data from the database.
Modular Design: Organized into classes for extraction, transformation, loading, and API serving, promoting reusability and maintainability.
Project Structure
The project consists of several Python classes and a Flask API, each responsible for a specific part of the pipeline:

# Extractors:
religiousBookExtractor: Fetches religious book data from OpenLibrary API and Goodreads.
harrypoterbookextractor: Retrieves Harry Potter book data from the Google Books API.
readanybookextractor: Scrapes book data from the ReadAnyBook website.
csvbookextractor: Reads book data from CSV files.
Transformers:
religiousBooksTransformer: Processes religious book data from APIs and web pages.
harrypoterbooktransformer: Transforms Harry Potter book data from the Google Books API.
readanybooktransformer: Cleans and formats data from ReadAnyBook.
CSVBookExtractor: Merges and processes book data from two CSV files based on ISBN.
Loader:
load_the_database: Stores transformed book data into a SQLite database and supports reading from it.
API:
app.py: A Flask application providing a REST API endpoint to retrieve books from the SQLite database.
Prerequisites
To run this project, ensure you have the following installed:

Python 3.8+
Required Python packages (install via pip):
bash



pip install requests beautifulsoup4 pandas sqlite3 flask
Setup
Clone the Repository:
bash



git clone https://github.com/FogapNjinju/BookHarvest.git
cd BookHarvest
Install Dependencies:
bash



pip install -r requirements.txt
If a requirements.txt file does not exist, manually install the required packages listed above.
Configure API Keys:
The harrypoterbookextractor class uses the Google Books API, which requires an API key. Replace the placeholder key in the class with your own:
python



self.api_key = 'YOUR_GOOGLE_BOOKS_API_KEY'
Obtain a Google Books API key from the Google Cloud Console.
Prepare CSV Files (if using csvbookextractor):
Ensure the CSV files containing book data are accessible and their paths are correctly specified in the csvbookextractor class.
Set Up SQLite Database:
Specify the database path in the load_the_database class and ensure it matches the path used in the Flask API (books.db by default):
python



self.db_path = 'books.db'
Usage
Running the ETL Pipeline
Create a Script (e.g., main.py) to orchestrate the ETL process:
python



from religiousBookExtractor import religiousBookExtractor
from religiousBooksTransformer import religiousBooksTransformer
from load_the_database import load_the_database

# Initialize classes
extractor = religiousBookExtractor()
transformer = religiousBooksTransformer()
loader = load_the_database('books.db')

# Extract religious books from OpenLibrary API
books_data = extractor.fetch_religious_books_method_one()

# Transform the data
transformed_books = transformer.books_and_their_attributes_from_api(books_data, max_books=5)

# Load into database
loader.store_books_in_db(transformed_books)

# Read from database
stored_books = loader.read_books_from_db()
print(stored_books)
Execute the Script:
bash



python main.py
Running the Flask API
Create a Script (e.g., app.py) with the Flask API code:
python



import sqlite3
from flask import Flask, request, jsonify

app = Flask(__name__)

def connection():
    conn = sqlite3.connect('books.db')
    conn.row_factory = sqlite3.Row
    return conn

@app.route('/books', methods=['GET'])
def get_books():
    conn = connection()
    cursor = conn.cursor()
    cursor.execute('SELECT * FROM books LIMIT 10000')
    books = cursor.fetchall()
    conn.close()
    return jsonify([dict(book) for book in books])

if __name__ == '__main__':
    app.run(debug=True, host='0.0.0.0', port=5000)
Run the API:
bash



python app.py
The API will be available at http://0.0.0.0:5000/books.
Use a tool like curl or a web browser to access the endpoint:
bash



curl http://localhost:5000/books
API Endpoint:
GET /books: Retrieves up to 10,000 books from the database in JSON format. Each book includes fields like id, title, author, score, and cover_url.
Example Output
ETL Pipeline: Stores books in the database and retrieves them as:
text



[(1, 'The Bible', 'Various Authors', 4.8, 'http://covers.openlibrary.org/b/id/12345-L.jpg'), ...]
API Response: Returns JSON data like:
json



[
    {"id": 1, "title": "The Bible", "author": "Various Authors", "score": 4.8, "cover_url": "http://covers.openlibrary.org/b/id/12345-L.jpg"},
    ...
]
Limitations
Rate Limits: APIs like Google Books and OpenLibrary may impose rate limits. Ensure your API key has sufficient quota.
Web Scraping: The Goodreads and ReadAnyBook extractors rely on web scraping, which may break if the website structure changes.
Data Consistency: The transformers assume specific data formats; unexpected API or web page changes may require code updates.
API Scalability: The Flask API currently returns up to 10,000 records without pagination, which may not scale well for large datasets.
Future Improvements
Add support for more data sources (e.g., Amazon Books, LibraryThing).
Implement pagination for web scraping and the Flask API to handle larger datasets.
Add data validation and cleaning steps to handle missing or malformed data.
Create a CLI or GUI for easier interaction with the pipeline and API.
Introduce logging to track pipeline execution and API requests.
Contributing
Contributions are welcome! To contribute:

Fork the repository.
Create a new branch (git checkout -b feature/your-feature).
Make your changes and commit (git commit -m 'Add your feature').
Push to the branch (git push origin feature/your-feature).
Open a pull request.
#License
This project is licensed under the MIT License. See the  file for details.

#Contact
For questions or feedback, please contact the project maintainer at andrewfogap@icloud.com.
