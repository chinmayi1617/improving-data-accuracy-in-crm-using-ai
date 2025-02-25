# improving-data-accuracy-in-crm-using-ai

4.	from flask import Flask, render_template, jsonify
5.	import pandas as pd
6.	import numpy as np
7.	from fuzzywuzzy import fuzz
8.	from sklearn.impute import SimpleImputer
9.	from sklearn.preprocessing import StandardScaler
10.	from sklearn.ensemble import RandomForestRegressor
11.	
12.	# Initialize Flask app
13.	app = Flask(__name__)
14.	
15.	# Example CRM Data (simulated)
16.	data = {
17.	    'CustomerID': [1, 2, 3, 4, 5, 6, 7, 8, 9],
18.	    'FirstName': ['John', 'Jane', 'John', 'Alice', 'Bob', 'Bob', 'Charles', 'Allice', 'John'],
19.	    'LastName': ['Doe', 'Smith', 'Doe', 'Johnson', 'Williams', 'Williams', 'Brown', 'Johnsson', 'Doe'],
20.	    'Email': ['john.doe@email.com', 'jane.smith@email.com', 'john.doe@email.com', 'alice.j@email.com', 'bob.williams@email.com', np.nan, 'charles.brown@email.com', np.nan, 'john.doe@email.com'],
21.	    'Phone': ['555-0101', '555-0102', '555-0101', '555-0104', np.nan, '555-0105', '555-0106', np.nan, '555-0101'],
22.	    'Age': [28, 35, 28, 45, 40, np.nan, 60, 30, np.nan],
23.	    'Revenue': [1000, 1500, 1000, 2000, 1200, 1300, 2500, 2300, 1200]
24.	}
25.	
26.	# Function to clean and improve CRM data
27.	def clean_and_improve_data(df):
28.	    # Handling missing values using imputation
29.	    imputer = SimpleImputer(strategy='mean')
30.	    df['Age'] = imputer.fit_transform(df[['Age']])
31.	    df['Revenue'] = imputer.fit_transform(df[['Revenue']])
32.	
33.	    # Fill missing email and phone
34.	    df['Email'].fillna('Unknown', inplace=True)
35.	    df['Phone'].fillna('Unknown', inplace=True)
36.	
37.	    # Normalize the numerical data for improved AI-based models
38.	    scaler = StandardScaler()
39.	    df[['Age', 'Revenue']] = scaler.fit_transform(df[['Age', 'Revenue']])
40.	
41.	    # Handle name similarity using fuzzy matching to improve consistency
42.	    for i in range(len(df)):
43.	        for j in range(i + 1, len(df)):
44.	            if fuzz.partial_ratio(df.iloc[i]['FirstName'], df.iloc[j]['FirstName']) > 85 and fuzz.partial_ratio(df.iloc[i]['LastName'], df.iloc[j]['LastName']) > 85:
45.	                df.iloc[j, df.columns.get_loc('FirstName')] = df.iloc[i]['FirstName']
46.	                df.iloc[j, df.columns.get_loc('LastName')] = df.iloc[i]['LastName']
47.	
48.	    return df
49.	
50.	@app.route('/')
51.	def index():
52.	    # Convert CRM data into DataFrame
53.	    df = pd.DataFrame(data)
54.	    
55.	    # Clean and improve data
56.	    improved_df = clean_and_improve_data(df)
57.	
58.	    # Return the cleaned and improved data as JSON for frontend to use
59.	    return render_template('index.html', tables=[improved_df.to_html(classes='data')])
60.	
61.	if __name__ == '__main__':
62.	    app.run(debug=True)
