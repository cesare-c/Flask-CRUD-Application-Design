# CRUD Application Design using Additional Features in Flask
The application has three different web pages. The first one displays all the recorded transactions. This page is called Transaction Records and displays all the transactions entries created in the system. This page also gives an option to Edit and Delete the available entries. The option of adding an entry is also available on this page. The second page is Add Transaction which is used when the user chooses to add the entry on the previous page. The user adds the Date and Amount values for the new entry. The third page is Edit Transaction which is user navigated to upon clicking the edit entry option. On this page also, the date and amount are accepted as entries; however, these entries are then reflected against the ID that was being edited.

## Clone the Project Repository

```bash
git clone https://github.com/ibm-developer-skills-network/obmnl-flask_assignment.git
```

## Initial set up

In the `app.py `file, you need to import necessary modules from Flask and instantiate the Flask application.
For this lab, you will need to import the following functions from the flask library.

    - Flask - to instantiate the application
    - request - to process the GET and POST requests
    - url_for - to access the url for a given function using its decorator
    - redirect - to redirect access requests according to requirement
    - render_template - to render the html page

After importing the functions, instantiate the application to a variable `app`.

```py
# Import libraries
from flask import Flask, redirect, request, render_template, url_for

# Instantiate Flask functionality
app = Flask(__name__)
```

Next, let's create a list of sample transactions for testing purposes. You can assume that the transactions already exist on the interface when it is executed for the first time. Please note that this step is completely optional and does not affect the functionality you will develop in this lab. Add the code snippet as shown below to `app.py`

```py
# Sample data
transactions = [
    {'id': 1, 'date': '2023-06-01', 'amount': 100},
    {'id': 2, 'date': '2023-06-02', 'amount': -200},
    {'id': 3, 'date': '2023-06-03', 'amount': 300}
]
```

The order in which you will develop the functions is as follows:
1. Read
2. Create
3. Update
4. Delete
The reason to implement Read before the other functions is to be able to redirect to the page with all transactions every time a new transaction is created, updated, or deleted. Therefore, the function to read the existing transactions must exist before the others are implemented.

## Read Operation

To implement the Read operation, you need to implement a route that displays a list of all transactions. This route will handle `GET `requests, which are used to retrieve and display data in `app.py`

```py
# Read operation: List all transactions
@app.route("/")
def get_transactions():
    return render_template("transactions.html", transactions=transactions)
```

## Create Operation

For the **Create** operation, you will implement a route that allows users to add new transactions. This will involve handling both `GET` and `POST` HTTP requests -` GET `for displaying the form to the user and `POST `for processing the form data sent by the user. 

```py
# Create operation: Display add transaction form
# Route to handle the creation of a new transaction
@app.route("/add", methods=["GET", "POST"])
def add_transaction():
    # Check if the request method is POST (form submission)
    if request.method == 'POST':
        # Create a new transaction object using form field values
        transaction = {
            'id': len(transactions) + 1,            # Generate a new ID based on the current length of the transactions list
            'date': request.form['date'],           # Get the 'date' field value from the form
            'amount': float(request.form['amount']) # Get the 'amount' field value from the form and convert it to a float
        }
        # Append the new transaction to the transactions list
        transactions.append(transaction)

        # Redirect to the transactions list page after adding the new transaction
        return redirect(url_for("get_transactions"))
    
    # If the request method is GET, render the form template to display the add transaction form
    return render_template("form.html")
```
## Update Operation

For the Update operation, you need to implement a route that allows users to update existing transactions. You'll again handle both `GET `and `POST` HTTP requests -` GET` for displaying the current transaction data in a form, and `POST` for processing the updated data sent by the user.

```py
# Update operation: Display edit transaction form
# Route to handle the editing of an existing transaction
@app.route("/edit/<int:transaction_id>", methods=["GET", "POST"])
def edit_transaction(transaction_id):
    # Check if the request method is POST (form submission)
    if request.method == 'POST':
        # Extract the updated values from the form fields
        date = request.form['date']           # Get the 'date' field value from the form
        amount = float(request.form['amount'])# Get the 'amount' field value from the form and convert it to a float

        # Find the transaction with the matching ID and update its values
        for transaction in transactions:
            if transaction['id'] == transaction_id:
                transaction['date'] = date       # Update the 'date' field of the transaction
                transaction['amount'] = amount   # Update the 'amount' field of the transaction
                break                            # Exit the loop once the transaction is found and updated

        # Redirect to the transactions list page after updating the transaction
        return redirect(url_for("get_transactions"))
    
    # If the request method is GET, find the transaction with the matching ID and render the edit form
    for transaction in transactions:
        if transaction['id'] == transaction_id:
            # Render the edit form template and pass the transaction to be edited
            return render_template("edit.html", transaction=transaction)

    # If the transaction with the specified ID is not found, handle this case (optional)
    return {"message": "Transaction not found"}, 404
```

## Delete Operation

Finally, you need to implement a route that allows users to delete existing transactions.

```py
# Delete operation: Delete a transaction
# Route to handle the deletion of an existing transaction
@app.route("/delete/<int:transaction_id>")
def delete_transaction(transaction_id):
    # Find the transaction with the matching ID and remove it from the list
    for transaction in transactions:
        if transaction['id'] == transaction_id:
            transactions.remove(transaction)  # Remove the transaction from the transactions list
            break  # Exit the loop once the transaction is found and removed

    # Redirect to the transactions list page after deleting the transaction
    return redirect(url_for("get_transactions"))
```

## Finishing Steps and Running the Application

Check if the current script is the main program (that is, it wasn't imported from another script) with the conditional if `__name__ == "__main__":`.

If the condition is true, call `app.run(debug=True)` to start the Flask development server with debug mode enabled. This will allow you to view detailed error messages in your browser if something goes wrong.

```py
# Run the Flask app
if __name__ == "__main__":
    app.run(debug=True)
```

Run the file `app.py` from a terminal shell using the command:

```bash
python3.11 app.py
```
By default, Flask launches the application on LocalHost:5000. 

