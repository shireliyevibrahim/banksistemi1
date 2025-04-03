# banksistemi1
import json
import os

# Fayl adları
ACCOUNTS_FILE = "accounts.txt"
TRANSACTIONS_FILE = "transactions.txt"

# Bank Hesabını Yaradan Funksiya
def create_account(fullname, card_number, cvv, exp_date, currency, role):
    account = {
        "fullname": fullname,
        "card_number": card_number,
        "cvv": cvv,
        "exp_date": exp_date,
        "currency": currency,
        "role": role,  # Manager / Customer
        "balance": 0.0
    }
    
    with open(ACCOUNTS_FILE, "a") as f:
        f.write(json.dumps(account) + "\n")
    print(f"Hesab yaradıldı: {fullname}")

# Pul köçürmə funksiyası
def transfer_money(from_card, to_card, amount):
    accounts = load_accounts()
    
    sender = next((acc for acc in accounts if acc["card_number"] == from_card), None)
    receiver = next((acc for acc in accounts if acc["card_number"] == to_card), None)

    if sender and receiver and sender["balance"] >= amount:
        sender["balance"] -= amount
        receiver["balance"] += amount
        save_accounts(accounts)
        log_transaction(from_card, to_card, amount)
        print(f"{amount} {sender['currency']} köçürüldü.")
    else:
        print("Əməliyyat uğursuz!")

# Bütün hesabları yükləyən funksiya
def load_accounts():
    if not os.path.exists(ACCOUNTS_FILE):
        return []
    
    with open(ACCOUNTS_FILE, "r") as f:
        return [json.loads(line.strip()) for line in f.readlines()]

# Yenilənmiş hesab məlumatlarını saxlayan funksiya
def save_accounts(accounts):
    with open(ACCOUNTS_FILE, "w") as f:
        for account in accounts:
            f.write(json.dumps(account) + "\n")

# Əməliyyatları log edən funksiya
def log_transaction(from_card, to_card, amount):
    log_entry = f"{from_card} -> {to_card}: {amount}\n"
    with open(TRANSACTIONS_FILE, "a") as f:
        f.write(log_entry)
    print("Əməliyyat qeydə alındı.")

# Kredit və Komissiya funksiyaları
def apply_commission(amount, type="fixed", value=5):
    """Komissiya hesablayır: sabit və ya faiz nisbətində."""
    if type == "fixed":
        return max(0, amount - value)
    elif type == "percent":
        return amount * (1 - value / 100)
    return amount

# Kredit hesablama
def calculate_credit(amount, interest_rate, cashback=0, loyalty_bonus=0):
    """Faiz dərəcəsi, cashback və loyalty bonusuna görə kredit hesablayır."""
    total_payable = amount * (1 + interest_rate / 100)
    return total_payable - cashback - loyalty_bonus

# Məsələn, yeni hesab yaratmaq
create_account("Fatimə Məmmədova", "1234567890123456", "123", "12/27", "AZN", "Customer")

# Məsələn, pul köçürmək
transfer_money("1234567890123456", "9876543210987654", 50)
