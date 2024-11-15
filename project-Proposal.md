Project : "How a student can easily pay hostel fees through his handy device."

1. Executive Summary: Students studying at Dhaka International University live in different hostels of the university. They have to pay their monthly hostel fee with painstaking attendance once every month. 
It can even be seen that in some months long queues and crowds have to be deposited.It is the year 2024.This is age of information technology.So our thinking is to touch technology in everything,or to modernize it.
And that's why me and my teammates all tried to create a medium.So that all the students studying in our university including us can be easily, hassle free and physically not present.
And anyone can pay hostel fees from anywhere through any device in their hand.

2. Explain the project background :
Student Class: This class stores basic student information, such as the student ID, name, and payment history.
Login Function: This function verifies the student by their student ID.
Main Menu: After logging in, the student can:
* Pay the hostel fee.
* View payment history.
* Logout.
Payment Functionality: Simulates a payment by asking for the amount and payment method, then adds the payment to the student's history.
Payment History: Displays the student’s previous payments.

3. Present a solution :

// HostelFeePaymentSystem.java

import java.util.ArrayList;
import java.util.Scanner;

class Student {
    private String studentId;
    private String name;
    private ArrayList<String> paymentHistory;

    public Student(String studentId, String name) {
        this.studentId = studentId;
        this.name = name;
        this.paymentHistory = new ArrayList<>();
    }

    public String getStudentId() {
        return studentId;
    }

    public String getName() {
        return name;
    }

    public void addPayment(String payment) {
        paymentHistory.add(payment);
    }

    public ArrayList<String> getPaymentHistory() {
        return paymentHistory;
    }
}

public class HostelFeePaymentSystem {
    private static ArrayList<Student> students = new ArrayList<>();
    private static Scanner scanner = new Scanner(System.in);
    private static Student currentStudent;

    public static void main(String[] args) {
        // Add some sample students
        students.add(new Student("DIU001", "Alice"));
        students.add(new Student("DIU002", "Bob"));
        students.add(new Student("DIU003", "Charlie"));

        System.out.println("Welcome to Dhaka International University Hostel Fee Payment System");
        login();

        if (currentStudent != null) {
            mainMenu();
        } else {
            System.out.println("Invalid login! Exiting...");
        }
    }

    private static void login() {
        System.out.print("Enter Student ID: ");
        String studentId = scanner.nextLine();

        for (Student student : students) {
            if (student.getStudentId().equals(studentId)) {
                currentStudent = student;
                System.out.println("Welcome, " + currentStudent.getName() + "!");
                return;
            }
        }
        System.out.println("Student not found.");
    }

    private static void mainMenu() {
        while (true) {
            System.out.println("\nMain Menu:");
            System.out.println("1. Pay Hostel Fee");
            System.out.println("2. View Payment History");
            System.out.println("3. Logout");

            System.out.print("Choose an option: ");
            int choice = scanner.nextInt();
            scanner.nextLine();  // Consume newline

            switch (choice) {
                case 1:
                    payHostelFee();
                    break;
                case 2:
                    viewPaymentHistory();
                    break;
                case 3:
                    System.out.println("Logging out...");
                    currentStudent = null;
                    return;
                default:
                    System.out.println("Invalid option. Please try again.");
                    break;
            }
        }
    }

    private static void payHostelFee() {
        System.out.print("Enter amount to pay: ");
        double amount = scanner.nextDouble();
        scanner.nextLine(); // Consume newline

        System.out.print("Enter payment method (Card, Mobile Banking, etc.): ");
        String method = scanner.nextLine();

        // Simulate payment process
        String paymentDetails = "Paid " + amount + " using " + method + ".";
        currentStudent.addPayment(paymentDetails);

        System.out.println("Payment successful! " + paymentDetails);
    }

    private static void viewPaymentHistory() {
        System.out.println("Payment History for " + currentStudent.getName() + ":");
        for (String payment : currentStudent.getPaymentHistory()) {
            System.out.println("- " + payment);
        }
    }
}



4. Define project deliverables and goals :
All students can pay hostel fees easily, hassle-free and from anywhere through any of their mobile devices.

5. Sample Output : (Like This)*

Welcome to Dhaka International University Hostel Fee Payment System
Enter Student ID: DIU001
Welcome, Alice!

Main Menu:
1. Pay Hostel Fee
2. View Payment History
3. Logout
Choose an option: 1
Enter amount to pay: 5000
Enter payment method (Card, Mobile Banking, etc.): Mobile Banking
Payment successful! Paid 5000.0 using Mobile Banking.

Main Menu:
1. Pay Hostel Fee
2. View Payment History
3. Logout
Choose an option: 2
Payment History for Alice:
- Paid 5000.0 using Mobile Banking.

Main Menu:
1. Pay Hostel Fee
2. View Payment History
3. Logout
Choose an option: 3
Logging out...
