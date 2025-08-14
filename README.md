# OIBDIP.
import java.util.ArrayList;
import java.util.Scanner;

// Class for Transaction
class Transaction {
    String type;
    double amount;
    double balanceAfter;

    Transaction(String type, double amount, double balanceAfter) {
        this.type = type;
        this.amount = amount;
        this.balanceAfter = balanceAfter;
    }

    public String toString() {
        return type + " - ₹" + amount + " | Balance: ₹" + balanceAfter;
    }
}

// Class for Account
class Account {
    private String userId;
    private String pin;
    private double balance;
    private ArrayList<Transaction> transactions;

    Account(String userId, String pin, double balance) {
        this.userId = userId;
        this.pin = pin;
        this.balance = balance;
        this.transactions = new ArrayList<>();
    }

    public boolean validate(String id, String p) {
        return userId.equals(id) && pin.equals(p);
    }

    public void deposit(double amount) {
        balance += amount;
        transactions.add(new Transaction("Deposit", amount, balance));
        System.out.println("Deposit Successful! Current Balance: ₹" + balance);
    }

    public void withdraw(double amount) {
        if (amount <= balance) {
            balance -= amount;
            transactions.add(new Transaction("Withdraw", amount, balance));
            System.out.println("Withdrawal Successful! Current Balance: ₹" + balance);
        } else {
            System.out.println("Insufficient Balance!");
        }
    }

    public void transfer(Account receiver, double amount) {
        if (amount <= balance) {
            balance -= amount;
            receiver.balance += amount;
            transactions.add(new Transaction("Transfer to " + receiver.userId, amount, balance));
            receiver.transactions.add(new Transaction("Transfer from " + userId, amount, receiver.balance));
            System.out.println("Transfer Successful! Current Balance: ₹" + balance);
        } else {
            System.out.println("Insufficient Balance!");
        }
    }

    public void showTransactionHistory() {
        if (transactions.isEmpty()) {
            System.out.println("No Transactions Found!");
        } else {
            System.out.println("Transaction History:");
            for (Transaction t : transactions) {
                System.out.println(t);
            }
        }
    }
}

// ATM Class
class ATM {
    private Scanner sc;
    private Account currentAccount;
    private Account[] accounts;

    ATM(Account[] accounts) {
        this.sc = new Scanner(System.in);
        this.accounts = accounts;
    }

    public void start() {
        System.out.println("===== Welcome to ATM =====");
        System.out.print("Enter User ID: ");
        String id = sc.nextLine();
        System.out.print("Enter PIN: ");
        String pin = sc.nextLine();

        for (Account acc : accounts) {
            if (acc.validate(id, pin)) {
                currentAccount = acc;
                showMenu();
                return;
            }
        }
        System.out.println("Invalid User ID or PIN!");
    }

    private void showMenu() {
        int choice;
        do {
            System.out.println("\n===== ATM Menu =====");
            System.out.println("1. Transaction History");
            System.out.println("2. Withdraw");
            System.out.println("3. Deposit");
            System.out.println("4. Transfer");
            System.out.println("5. Quit");
            System.out.print("Enter your choice: ");
            choice = sc.nextInt();

            switch (choice) {
                case 1:
                    currentAccount.showTransactionHistory();
                    break;
                case 2:
                    System.out.print("Enter amount to withdraw: ");
                    double wAmt = sc.nextDouble();
                    currentAccount.withdraw(wAmt);
                    break;
                case 3:
                    System.out.print("Enter amount to deposit: ");
                    double dAmt = sc.nextDouble();
                    currentAccount.deposit(dAmt);
                    break;
                case 4:
                    sc.nextLine(); // clear buffer
                    System.out.print("Enter receiver User ID: ");
                    String rId = sc.nextLine();
                    Account receiver = null;
                    for (Account acc : accounts) {
                        if (acc != currentAccount && acc.validate(rId, acc.pin)) {
                            receiver = acc;
                            break;
                        }
                    }
                    if (receiver != null) {
                        System.out.print("Enter amount to transfer: ");
                        double tAmt = sc.nextDouble();
                        currentAccount.transfer(receiver, tAmt);
                    } else {
                        System.out.println("Receiver not found!");
                    }
                    break;
                case 5:
                    System.out.println("Thank you for using ATM!");
                    break;
                default:
                    System.out.println("Invalid choice!");
            }
        } while (choice != 5);
    }
}

// Main Class
public class ATMInterface {
    public static void main(String[] args) {
        Account acc1 = new Account("user1", "1234", 1000);
        Account acc2 = new Account("user2", "5678", 2000);
        Account[] accounts = { acc1, acc2 };

        ATM atm = new ATM(accounts);
        atm.start();
    }
}

