Command to run the talk2db_code.py file - streamlit run talk2db_code.py

Note - We have all our SQL and NoSQL functions, database connections and UI defined and executing in one single file i.e talk2db_code.py. Also clarified each function and its working below.

In total there are 16 functions in the file:

1. create_connection()

- The create_connection function attempts to establish a connection to a MySQL database using the configuration details specified in db_config.
- The function uses mysql.connector.connect(**db_config) to try connecting to the MySQL database. The db_config variable is expected to be a dictionary containing the necessary connection details such as host, username, password, and database name.
- The connection.is_connected() method is used to check whether the connection to the database was successful.
- If there is an error while connecting to the database (e.g., incorrect credentials, network issues, etc.), the except Error as e block is executed.

2. create_mongo_connection(host, port, database_name)

- The create_mongo_connection function is designed to establish a connection to a MongoDB database.
- The function accepts three arguments: host, port, and database_name. These parameters are used to define where the MongoDB server is located and which database you want to connect to.
- Once the connection is established, it accesses the database specified by database_name by using the client[database_name] syntax. This returns a reference to the database.
- If the connection is successful, the function returns the db object, which is a reference to the MongoDB database. This object can be used to interact with collections, insert, update, or query data.
- If there is an exception (e.g., if the MongoDB server is not running, incorrect host or port, or any other error), it catches the exception and displays an error message using st.error(). This indicates that the connection failed and helps to debug the problem.

3. get_data_from_db(query)

- The get_data_from_db function is designed to execute a SQL query on a MySQL database and return the results.
- The function first tries to create a connection to the database.
- If successful, it executes the query.
- If the query executes successfully, it returns the results; otherwise, it handles errors and returns None.
- Finally, it ensures that the cursor and connection are closed properly.

4. sql_get_table_names_and_columns(connection)

- Retrieves the names of all tables in the connected MySQL database.
- For each table, it retrieves the columns and their respective data types.
- Returns the results in a structured format (list and dictionaries), making it easy to process or display the database schema information.

5. get_table_names_and_columns(connection)

- Retrieves all the tables from the connected MySQL database.
- For each table, it fetches the columns and their detailed information (including data types, whether they are nullable, etc.).
- Returns this data in a structured dictionary, making it easier to programmatically access table and column information.

6. get_collection_names_and_fields(database)

- Retrieves all collection names from the connected MongoDB database.
- For each collection, it fetches a sample document (if available) and extracts the field names (keys).
- Returns a structured dictionary with the collection names as keys and their respective fields as values.

7. sample_get_collection_names_and_fields(database)

- Retrieves all collection names from the MongoDB database.
- For each collection, it fetches a sample document and extracts the field names and their respective data types.
- Returns a structured dictionary with collection names as keys and field names with data types as values.

8. generate_dynamic_queries(database_details, base_queries)

- The generate_dynamic_queries function dynamically generates queries based on a set of base query templates and a database schema that includes collections and fields. The goal of this function is to generate queries for each collection by replacing placeholders in the base_queries list with specific details from the database, such as collection names and field names.
- This function is useful for dynamically generating queries based on a database schema, ensuring that the queries are context-sensitive by prioritizing specific fields (e.g., numeric fields) and handling different query structures (e.g., aggregate operations). It can be particularly helpful for database exploration, data analysis, and automation of query generation tasks.

9. generate_query_from_input(user_input, table_info)

- The function generate_query_from_input dynamically generates SQL queries based on user input and the provided table information. It processes the user input, identifies the table and relevant columns, and then constructs an appropriate SQL query.
- The function uses the nltk library to tokenize the user’s input and re (regular expressions) to search for specific patterns in the input.
- This function is useful for converting simple natural language input into SQL queries based on a database schema. It can be extended to support more complex query generation scenarios or handle more specific user requests by adding additional keyword patterns and logic.

10. generate_sample_query(user_input, collection_info)

- The generate_sample_query function dynamically generates MongoDB sample queries based on the user’s input, leveraging predefined templates for different types of queries like aggregation, sorting, filtering, and counting.
- The function first tokenizes the user_input by splitting the input string into lowercase words.
- The function checks if any of the tokens in the user input match collection names in collection_info. It assigns the matched collection name to the variable collection_name.
- Based on the presence of certain keywords in the user_input, the function selects a query category.
- The function calls a helper function generate_dynamic_queries, passing the collection information and the appropriate base query list based on the user’s input.
- After generating the queries, the function selects a random query from the list of available queries for the identified collection.
- If the input requests multiple queries (i.e., it contains the word “queries”), two random queries are selected. Otherwise, only one query is returned.
- If no recognized query patterns are found in the input, the function returns an error message stating that it couldn’t understand the query.

11. extract_table_and_column_with_condition(user_input, tables, table_column_mapping)

- The extract_table_and_column_with_condition function is designed to process user input and extract relevant information such as the table name, columns, and any conditions (typically from a WHERE clause). It performs tokenization on the user input, checks for table and column names, and maps conditions to SQL syntax.
- user_input (str): The user’s natural language query (e.g., “show me sales where amount is greater than 100”).
- tables (list): A list of valid table names (e.g., ["sales", "products"]).
- table_column_mapping (dict): A dictionary mapping each table to its corresponding columns (e.g., {"sales": ["amount", "date", "salesperson"], "products": ["name", "category"]}).
- result: table_name = "sales"
- columns = ["amount"]
- conditions = {"amount": (">", "100")}

12. match_query_pattern(user_input, collection_info)

- The input is tokenized using NLTK’s word_tokenize, converting the input into lowercase and filtering out question marks.
- It identifies if the user input contains the names of collections (from collection_info). If two collections are found, it proceeds with logic for joining them.
- If two collections are mentioned, it looks for matching fields between them (excluding _id). It constructs an aggregation query to join these collections using $lookup.
- It detects a WHERE condition, extracts the field, condition, and operator (such as is, greater than, less than, etc.). Operators are mapped to MongoDB query operators (e.g., $eq, $gt, $lt). If numeric values are present, they are converted to float, otherwise, regex is used for string matching.
- If the query mentions ordering, it extracts the sorting field and direction (ascending or descending). Adds a $sort stage to the aggregation query.
- It detects grouping fields (per, each, group) and aggregation functions (e.g., sum, count). It also handles optional HAVING conditions, building a query accordingly.
- Based on the parsed information, it constructs and returns an appropriate MongoDB aggregation query.

13. execute_sql_query(cursor, query)

- The execute_sql_query function you’ve provided is designed to execute a SQL query using a cursor object and return the results.
- The query passed to the function is executed using the cursor.execute(query) method.
- After execution, the cursor.fetchall() method retrieves all rows returned by the query. These rows are then stored in a list r, which is populated by iterating over the rows and appending each row to the list.
- The function returns the list r containing the result rows.
- If an error occurs during query execution, it is caught in the except block, and an error message is displayed using st.error(f"Error executing query: {e}").

14. execute_query(database, collection_name, field_name=None, action=None)

- The try block is used for error handling but doesn’t provide much feedback if an error occurs. Consider adding except blocks for specific exceptions (e.g., pymongo.errors.PyMongoError) to handle potential issues like connection problems or query syntax errors. This can help in debugging and improve user experience by providing clear error messages.
- In several actions (e.g., "group_by_sum", "group_by_avg"), you’re passing tuples of fields like field_name, agg_field.
- We are using st.session_state["query_results"] to store the results of queries.

15. handle_sql_query(user_input, tables, table_column_mapping, cursor)

SQL Query Handler (handle_sql_query):
	1.	Tokenizes the Input: It tokenizes the user input using NLTK to break the query into individual words (tokens).
	2.	Extracts Table, Columns, and Conditions: It identifies the table, columns, and any conditions from the user input (using an extract_table_and_column_with_condition function).
	3.	Handles Specific SQL Operations:
	- Inner Join: It processes a query asking for an inner join between two tables (e.g., sales and stores) and tries to find common columns for the join.
	- Conditions: It handles conditions like “greater than” or “less than” and constructs SQL queries accordingly.
	- Limit: It processes queries that ask for a limit on the number of rows returned.
	- Aggregations: It handles common aggregations such as AVG, SUM, and includes support for GROUP BY and ORDER BY.
	4.	Executes the Query: Once a valid query is constructed based on the user’s input, the function executes the query against the database using the execute_sql_query function.

16. handle_query(user_input, collection_info, database)

MongoDB Query Handler (handle_query):
	1.	Matches Action and Collection: It matches the user query to an action (e.g., “find data with condition”, “count documents”, etc.) and identifies the collection and field name involved.
	2.	Executes MongoDB Query: Based on the matched action, it runs the appropriate query on the MongoDB database using the execute_query function.
	3.	Handles Error for Non-Existent Collections: If the collection or field doesn’t exist, it returns an error message.


 The main purpose of the function is to convert user-friendly, natural language queries into structured SQL or MongoDB queries that can be executed on a database. It supports a range of operations like joins, filtering, aggregation, and sorting, depending on the query structure.


