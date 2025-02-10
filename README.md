# Finance-Tracker
Application that tracks personal finances, allowing users to add income, expenses, and categorize them. Provide monthly summaries and reports.

import sqlite3
import datetime

def initialize_db():
    """Creates database and tables if they don't exist."""
    conn = sqlite3.connect("finance_tracker.db")
    cursor = conn.cursor()
    cursor.execute('''CREATE TABLE IF NOT EXISTS transactions (
                        id INTEGER PRIMARY KEY AUTOINCREMENT,
                        date TEXT,
                        type TEXT,
                        category TEXT,
                        amount REAL
                    )''')
    conn.commit()
    conn.close()

def add_transaction(transaction_type, category, amount):
    """Adds a new income or expense record."""
    conn = sqlite3.connect("finance_tracker.db")
    cursor = conn.cursor()
    cursor.execute("INSERT INTO transactions (date, type, category, amount) VALUES (?, ?, ?, ?)",
                   (datetime.date.today(), transaction_type, category, amount))
    conn.commit()
    conn.close()
    print("Transaction added successfully!\n")

def view_transactions():
    """Displays all transactions."""
    conn = sqlite3.connect("finance_tracker.db")
    cursor = conn.cursor()
    cursor.execute("SELECT * FROM transactions")
    rows = cursor.fetchall()
    conn.close()
    for row in rows:
        print(row)

def generate_monthly_summary():
    """Generates a summary of income and expenses for the current month."""
    conn = sqlite3.connect("finance_tracker.db")
    cursor = conn.cursor()
    month = datetime.date.today().strftime("%Y-%m")
    
    cursor.execute("SELECT SUM(amount) FROM transactions WHERE type='Income' AND date LIKE ?", (month+'%',))
    income = cursor.fetchone()[0] or 0
    
    cursor.execute("SELECT SUM(amount) FROM transactions WHERE type='Expense' AND date LIKE ?", (month+'%',))
    expenses = cursor.fetchone()[0] or 0
    
    conn.close()
    
    print("\nMonthly Summary:")
    print(f"Total Income: ${income:.2f}")
    print(f"Total Expenses: ${expenses:.2f}")
    print(f"Net Savings: ${income - expenses:.2f}\n")

def main():
    initialize_db()
    while True:
        print("1. Add Income")
        print("2. Add Expense")
        print("3. View Transactions")
        print("4. Monthly Summary")
        print("5. Exit")
        choice = input("Choose an option: ")
        
        if choice == "1":
            category = input("Enter category (Salary, Bonus, etc.): ")
            amount = float(input("Enter amount: "))
            add_transaction("Income", category, amount)
        elif choice == "2":
            category = input("Enter category (Food, Rent, Bills, etc.): ")
            amount = float(input("Enter amount: "))
            add_transaction("Expense", category, amount)
        elif choice == "3":
            view_transactions()
        elif choice == "4":
            generate_monthly_summary()
        elif choice == "5":
            print("Exiting... Goodbye!")
            break
        else:
            print("Invalid choice, please try again!\n")

if __name__ == "__main__":
    main()
