import java.util.*;
import java.time.LocalDateTime;
import java.time.format.DateTimeFormatter;
import java.util.regex.Pattern;

public class PaymentSystem {
    private static final Scanner scanner = new Scanner(System.in);
    private static double balance = 10000.00;
    private static String username = "";
    private static String studentId = "";
    private static String password = "";
    private static final List<Transaction> transactionHistory = new ArrayList<>();
    private static final Map<Integer, Double> feeStructure = new HashMap<>();
    private static final int MAX_LOGIN_ATTEMPTS = 3;

    static class Transaction {
        private final LocalDateTime timestamp;
        private final String type;
        private final double amount;
        private final String paymentMethod;
        private final String status;

        Transaction(String type, double amount, String paymentMethod, String status) {
            this.timestamp = LocalDateTime.now();
            this.type = type;
            this.amount = amount;
            this.paymentMethod = paymentMethod;
            this.status = status;
        }

        // Added getters for Transaction class
        public LocalDateTime getTimestamp() { return timestamp; }
        public String getType() { return type; }
        public double getAmount() { return amount; }
        public String getPaymentMethod() { return paymentMethod; }
        public String getStatus() { return status; }
    }

    public static void main(String[] args) {
        try {
            initializeFeeStructure();
            System.out.println("Welcome to the University Payment System");

            if (!register()) {
                System.out.println("Registration required to proceed. Exiting...");
                return;
            }

            if (!login()) {
                System.out.println("Login failed. Please try again later.");
                return;
            }

            optionsMenu();
            System.out.println("Thank you for using our Payment System!");
        } catch (Exception e) {
            System.out.println("System error occurred: " + e.getMessage());
        } finally {
            scanner.close();
        }
    }

    private static void initializeFeeStructure() {
        feeStructure.put(1, 26500.00);
        feeStructure.put(2, 43750.00);
        feeStructure.put(3, 3000.00);
        feeStructure.put(4, 3500.00);
        feeStructure.put(5, 5000.00);
    }

    private static boolean isValidStudentId(String id) {
        return Pattern.matches("^\\d{8}$", id);
    }

    private static boolean isValidPassword(String pwd) {
        return Pattern.matches("^(?=.*[0-9])(?=.*[a-z])(?=.*[A-Z])(?=.*[@#$%^&+=])(?=\\S+$).{8,}$", pwd);
    }

    public static boolean register() {
        System.out.println("\n=== Registration ===");
        System.out.print("Would you like to register? (Yes/No): ");
        String response = scanner.nextLine().trim();

        if (!response.equalsIgnoreCase("Yes")) {
            return false;
        }

        System.out.print("Enter your full name: ");
        username = scanner.nextLine().trim();

        if (username.isEmpty()) {
            System.out.println("Invalid registration details!");
            return false;
        }

        do {
            System.out.print("Enter your student ID (8 digits): ");
            studentId = scanner.nextLine().trim();
            if (!isValidStudentId(studentId)) {
                System.out.println("Invalid student ID format. Please enter 8 digits.");
            }
        } while (!isValidStudentId(studentId));

        do {
            System.out.print("Enter your password (min 8 chars, must include uppercase, lowercase, number, and special char): ");
            password = scanner.nextLine().trim();
            if (!isValidPassword(password)) {
                System.out.println("Invalid password format. Please follow the requirements.");
            }
        } while (!isValidPassword(password));

        return true;
    }

    public static boolean login() {
        System.out.println("\n=== Login ===");
        if (username.isEmpty() || studentId.isEmpty()) {
            System.out.println("No registered user found!");
            return false;
        }

        int attempts = 0;
        while (attempts < MAX_LOGIN_ATTEMPTS) {
            System.out.print("Enter your student ID: ");
            String inputId = scanner.nextLine().trim();
            System.out.print("Enter your password: ");
            String inputPassword = scanner.nextLine().trim();

            if (inputId.equals(studentId) && inputPassword.equals(password)) {
                System.out.println("Welcome back, " + username + "!");
                return true;
            }

            System.out.println("Invalid credentials!");
            attempts++;
            if (attempts < MAX_LOGIN_ATTEMPTS) {
                System.out.println("Attempts remaining: " + (MAX_LOGIN_ATTEMPTS - attempts));
            }
        }

        System.out.println("Maximum login attempts exceeded.");
        return false;
    }

    public static void optionsMenu() {
        while (true) {
            try {
                System.out.println("\n=== Main Menu ===");
                for (int i = 1; i <= 5; i++) {
                    System.out.printf("%d. %s (BDT %.2f)%n", i, getFeeType(i), feeStructure.get(i));
                }
                System.out.println("6. View Transaction History");
                System.out.println("7. Check Balance");
                System.out.println("0. Exit");

                System.out.print("Enter your choice (0-7): ");
                int choice = Integer.parseInt(scanner.nextLine().trim());

                if (choice == 0) {
                    break;
                }

                switch (choice) {
                    case 1, 2, 3, 4, 5 -> viewOnAccount(choice);
                    case 6 -> viewTransactionHistory();
                    case 7 -> checkBalance();
                    default -> System.out.println("Invalid option. Please try again.");
                }
            } catch (NumberFormatException e) {
                System.out.println("Invalid input. Please enter a number.");
            } catch (Exception e) {
                System.out.println("An error occurred: " + e.getMessage());
            }
        }
    }

    public static void viewOnAccount(int feeType) {
        try {
            System.out.println("\n=== Account Information ===");
            System.out.printf("Current Balance: BDT %.2f%n", balance);
            System.out.printf("Selected Fee: BDT %.2f%n", feeStructure.get(feeType));
            System.out.println("1. Make Payment");
            System.out.println("2. View Due Amount");
            System.out.println("3. Back to Main Menu");

            System.out.print("Enter your choice (1-3): ");
            int choice = Integer.parseInt(scanner.nextLine().trim());

            switch (choice) {
                case 1 -> selectForPayment(feeType);
                case 2 -> System.out.printf("Due Amount: BDT %.2f%n", feeStructure.get(feeType));
                case 3 -> { /* return to main menu */ }
                default -> System.out.println("Invalid option selected.");
            }
        } catch (NumberFormatException e) {
            System.out.println("Invalid input. Please enter a number.");
        }
    }

    public static void selectForPayment(int feeType) {
        try {
            System.out.println("\n=== Payment Method ===");
            System.out.println("1. Mobile Payment (BKash/Nagad/Rocket)");
            System.out.println("2. Bank Transfer");
            System.out.println("3. Cancel Payment");

            System.out.print("Select payment method (1-3): ");
            int choice = Integer.parseInt(scanner.nextLine().trim());

            if (choice == 3) {
                return;
            }

            if (choice == 1 || choice == 2) {
                if (choice == 2 && !verifyBankPassword()) {
                    System.out.println("Bank verification failed. Payment cancelled.");
                    return;
                }
                processPayment(choice, feeType);
            } else {
                System.out.println("Invalid payment option selected.");
            }
        } catch (NumberFormatException e) {
            System.out.println("Invalid input. Please enter a number.");
        }
    }

    public static void processPayment(int method, int feeType) {
        try {
            System.out.println("\n=== Processing Payment ===");
            double feeAmount = feeStructure.get(feeType);
            System.out.printf("Required Fee Amount: BDT %.2f%n", feeAmount);
            System.out.print("Enter payment amount: BDT ");
            double amount = Double.parseDouble(scanner.nextLine().trim());

            if (amount <= 0) {
                System.out.println("Invalid amount. Payment cancelled.");
                return;
            }

            if (amount > balance) {
                System.out.println("Insufficient balance. Payment cancelled.");
                return;
            }

            if (amount > feeAmount) {
                System.out.println("Amount exceeds required fee. Payment cancelled.");
                return;
            }

            balance -= amount;
            String paymentMethod = method == 1 ? "Mobile Payment" : "Bank Transfer";
            Transaction transaction = new Transaction(
                    getFeeType(feeType),
                    amount,
                    paymentMethod,
                    "Successful"
            );
            transactionHistory.add(transaction);
            generateReceipt(transaction);

        } catch (NumberFormatException e) {
            System.out.println("Invalid amount format. Payment cancelled.");
        }
    }

    private static String getFeeType(int feeType) {
        return switch (feeType) {
            case 1 -> "Admission Fee";
            case 2 -> "Tuition Fee";
            case 3 -> "Hostel Fee";
            case 4 -> "Improvement Fee";
            case 5 -> "Other Payments";
            default -> "Unknown Fee";
        };
    }

    private static boolean verifyBankPassword() {
        final String BANK_PASSWORD = "1234";
        int attempts = 3;

        while (attempts > 0) {
            System.out.print("Enter your bank account password: ");
            String inputPassword = scanner.nextLine().trim();

            if (inputPassword.equals(BANK_PASSWORD)) {
                return true;
            }

            attempts--;
            System.out.println("Invalid password! Attempts remaining: " + attempts);
        }

        return false;
    }

    public static void generateReceipt(Transaction transaction) {
        DateTimeFormatter formatter = DateTimeFormatter.ofPattern("yyyy-MM-dd HH:mm:ss");

        System.out.println("\n====== PAYMENT RECEIPT ======");
        System.out.println("Student Name: " + username);
        System.out.println("Student ID: " + studentId);
        System.out.println("Date: " + transaction.getTimestamp().format(formatter));
        System.out.println("Payment Type: " + transaction.getType());
        System.out.println("Payment Method: " + transaction.getPaymentMethod());
        System.out.printf("Amount Paid: BDT %.2f%n", transaction.getAmount());
        System.out.printf("Remaining Balance: BDT %.2f%n", balance);
        System.out.println("Transaction Status: " + transaction.getStatus());
        System.out.println("============================");
    }

    public static void viewTransactionHistory() {
        if (transactionHistory.isEmpty()) {
            System.out.println("\nNo transactions found.");
            return;
        }

        System.out.println("\n=== Transaction History ===");
        DateTimeFormatter formatter = DateTimeFormatter.ofPattern("yyyy-MM-dd HH:mm:ss");

        for (Transaction t : transactionHistory) {
            System.out.println("----------------------------");
            System.out.println("Date: " + t.getTimestamp().format(formatter));
            System.out.println("Type: " + t.getType());
            System.out.printf("Amount: BDT %.2f%n", t.getAmount());
            System.out.println("Method: " + t.getPaymentMethod());
            System.out.println("Status: " + t.getStatus());
        }
    }

    public static void checkBalance() {
        System.out.println("\n=== Balance Information ===");
        System.out.printf("Current Balance: BDT %.2f%n", balance);
    }
}
