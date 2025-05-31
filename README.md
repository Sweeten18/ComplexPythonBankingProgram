# ComplexPythonBankingProgram
coding a complex banking program using python language
import json
import os
import getpass

DATA_FILE = "data.json"

# Ensure data file exists
if not os.path.exists(DATA_FILE):
    with open(DATA_FILE, "w") as f:
        json.dump({}, f)

class BankSystem:
    def __init__(self):
        self.data = self.load_data()
        self.current_user = None

    def load_data(self):
        with open(DATA_FILE, "r") as f:
            return json.load(f)

    def save_data(self):
        with open(DATA_FILE, "w") as f:
            json.dump(self.data, f, indent=4)

    def create_account(self):
        print("\n--- Create Account ---")
        username = input("Enter username: ")
        if username in self.data:
            print("❌ Username already exists.")
            return
        pin = getpass.getpass("Choose a 4-digit PIN: ")
        self.data[username] = {
            "pin": pin,
            "balance": 0.0,
            "transactions": []
        }
        self.save_data()
        print("✅ Account created successfully!")

    def login(self):
        print("\n--- Login ---")
        username = input("Enter username: ")
        pin = getpass.getpass("Enter 4-digit PIN: ")
        if username in self.data and self.data[username]["pin"] == pin:
            self.current_user = username
            print(f"✅ Logged in as {username}")
        else:
            print("❌ Invalid credentials.")

    def logout(self):
        self.current_user = None
        print("🔒 Logged out.")

    def deposit(self):
        amount = float(input("Enter amount to deposit: "))
        if amount <= 0:
            print("❌ Invalid amount.")
            return
        self.data[self.current_user]["balance"] += amount
        self.data[self.current_user]["transactions"].append(f"Deposited ${amount:.2f}")
        self.save_data()
        print(f"💰 Deposited ${amount:.2f}")

    def withdraw(self):
        amount = float(input("Enter amount to withdraw: "))
        if amount <= 0:
            print("❌ Invalid amount.")
            return
        if amount > self.data[self.current_user]["balance"]:
            print("❌ Insufficient funds.")
            return
        self.data[self.current_user]["balance"] -= amount
        self.data[self.current_user]["transactions"].append(f"Withdrew ${amount:.2f}")
        self.save_data()
        print(f"🏧 Withdrew ${amount:.2f}")

    def check_balance(self):
        balance = self.data[self.current_user]["balance"]
        print(f"💼 Current Balance: ${balance:.2f}")

    def view_transactions(self):
        print("--- Transaction History ---")
        for t in self.data[self.current_user]["transactions"]:
            print(f"- {t}")

    def user_menu(self):
        while self.current_user:
            print("\n1. Deposit\n2. Withdraw\n3. Check Balance\n4. Transactions\n5. Logout")
            choice = input("Choose an option: ")
            match choice:
                case "1":
                    self.deposit()
                case "2":
                    self.withdraw()
                case "3":
                    self.check_balance()
                case "4":
                    self.view_transactions()
                case "5":
                    self.logout()
                case _:
                    print("❌ Invalid choice.")

    def main_menu(self):
        while True:
            print("\n=== Welcome to CLI Bank ===")
            print("1. Create Account\n2. Login\n3. Exit")
            choice = input("Select an option: ")
            match choice:
                case "1":
                    self.create_account()
                case "2":
                    self.login()
                    if self.current_user:
                        self.user_menu()
                case "3":
                    print("👋 Goodbye!")
                    break
                case _:
                    print("❌ Invalid option.")

if __name__ == "__main__":
    bank = BankSystem()
    bank.main_menu()
