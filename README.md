import java.util.ArrayList;
import java.util.Scanner;
import java.time.LocalDate;
import java.time.format.DateTimeFormatter;
import java.time.format.DateTimeParseException;
import java.time.format.ResolverStyle;


abstract class LaundryService {


    private String serviceName;
    private double pricePerKg;


    public LaundryService(String serviceName, double pricePerKg) {
        this.serviceName = serviceName;
        this.pricePerKg = pricePerKg;
    }


    public String getServiceName() {
        return serviceName;
    }


    public double getPricePerKg() {
        return pricePerKg;
    }


    public abstract double calculateCost(double weight);
}


class WashFold extends LaundryService {


    public WashFold() {
        super("Wash & Fold", 35.00);
    }


    @Override
    public double calculateCost(double weight) {
        return getPricePerKg() * weight;
    }
}


class WashIron extends LaundryService {


    public WashIron() {
        super("Wash & Iron", 55.00);
    }


    @Override
    public double calculateCost(double weight) {
        return getPricePerKg() * weight;
    }
}


class DryCleaning extends LaundryService {


    public DryCleaning() {
        super("Dry Cleaning", 120.00);
    }


    @Override
    public double calculateCost(double weight) {
        return getPricePerKg() * weight;
    }
}


class Bedding extends LaundryService {


    public Bedding() {
        super("Comforters & Bedding", 80.00);
    }


    @Override
    public double calculateCost(double weight) {
        return getPricePerKg() * weight;
    }
}


class StainRemoval extends LaundryService {


    public StainRemoval() {
        super("Stain Removal", 95.00);
    }


    @Override
    public double calculateCost(double weight) {
        return getPricePerKg() * weight;
    }
}


class Customer {


    private String customerName;


    public Customer(String customerName) {
        this.customerName = customerName;
    }


    public String getCustomerName() {
        return customerName;
    }
}


class LaundryOrder {


    private Customer customer;
    private LaundryService service;
    private double weight;
    private LocalDate pickupDate;
    private LocalDate deliveryDate;
    private boolean express;


    public LaundryOrder(Customer customer,
                        LaundryService service,
                        double weight,
                        LocalDate pickupDate,
                        LocalDate deliveryDate,
                        boolean express) {


        this.customer = customer;
        this.service = service;
        this.weight = weight;
        this.pickupDate = pickupDate;
        this.deliveryDate = deliveryDate;
        this.express = express;
    }


    public double getBaseCost() {
        return service.calculateCost(weight);
    }


    public double getExpressFee() {
        return express ? getBaseCost() * 0.20 : 0.0;
    }


    public double getTotalCost() {
        return getBaseCost() + getExpressFee();
    }


    public void displayReceipt() {


        DateTimeFormatter formatter =
                DateTimeFormatter.ofPattern("MM-dd-uuuu");


        System.out.println("\n==========================================");
        System.out.println("          LAUNDRY SCHEDULE RECEIPT");
        System.out.println("==========================================");


        System.out.println("Customer Name   : "
                + customer.getCustomerName());


        System.out.println("Service Selected: "
                + service.getServiceName());


        System.out.printf("Price per Kg    : PHP %.2f%n",
                service.getPricePerKg());


        System.out.printf("Laundry Weight  : %.2f kg%n", weight);


        System.out.println("Pickup Date     : "
                + pickupDate.format(formatter));


        System.out.println("Delivery Date   : "
                + deliveryDate.format(formatter));


        System.out.println("Service Type    : "
                + (express ? "Express (+20%)" : "Standard"));


        System.out.println("------------------------------------------");


        System.out.printf("Base Cost       : PHP %.2f%n",
                getBaseCost());


        System.out.printf("Express Fee     : PHP %.2f%n",
                getExpressFee());


        System.out.println("------------------------------------------");


        System.out.printf("Total Amount Due: PHP %.2f%n",
                getTotalCost());


        System.out.println("==========================================");
    }
}


public class Main {


    private static Scanner scanner = new Scanner(System.in);


    private static ArrayList<LaundryService> services =
            new ArrayList<>();


    private static ArrayList<LaundryOrder> orders =
            new ArrayList<>();


    public static void main(String[] args) {


        initializeServices();


        boolean running = true;


        while (running) {


            displayMenu();


            System.out.print("Enter your choice: ");
            int choice = readInt();


            switch (choice) {


                case 1:
                    createOrder();
                    break;


                case 2:
                    displayServices();
                    break;


                case 3:
                    displayAllOrders();
                    break;


                case 4:
                    running = false;
                    System.out.println(
                        "\nThank you for using the Laundry Pickup & Delivery System!");
                    break;


                default:
                    System.out.println(
                        "\nInvalid choice! Please select 1-4.");
            }
        }


        scanner.close();
    }


    public static void initializeServices() {


        services.add(new WashFold());
        services.add(new WashIron());
        services.add(new DryCleaning());
        services.add(new Bedding());
        services.add(new StainRemoval());
    }


    public static void displayMenu() {


        System.out.println("\n==========================================");
        System.out.println("     LAUNDRY PICKUP & DELIVERY SYSTEM");
        System.out.println("==========================================");
        System.out.println("1. Create Laundry Schedule");
        System.out.println("2. View Available Services");
        System.out.println("3. View All Orders");
        System.out.println("4. Exit");
        System.out.println("==========================================");
    }


    public static void displayServices() {


        System.out.println("\n========== AVAILABLE SERVICES ==========");


        for (int i = 0; i < services.size(); i++) {


            LaundryService service = services.get(i);


            System.out.printf("%d. %-25s PHP %.2f / kg%n",
                    i + 1,
                    service.getServiceName(),
                    service.getPricePerKg());
        }


        System.out.println("========================================");
    }


    public static void createOrder() {


        System.out.println("\n========== CREATE LAUNDRY SCHEDULE ==========");


        String name;


        while (true) {


            System.out.print("Enter Customer Name: ");
            name = scanner.nextLine().trim();


            if (!name.isEmpty()) {
                break;
            }


            System.out.println("Customer name cannot be empty!");
        }


        Customer customer = new Customer(name);


        displayServices();


        int serviceChoice;


        while (true) {


            System.out.print("Select Service (1-5): ");
            serviceChoice = readInt();


            if (serviceChoice >= 1 &&
                serviceChoice <= services.size()) {
                break;
            }


            System.out.println("Invalid service! Please select 1-5.");
        }


        LaundryService selectedService =
                services.get(serviceChoice - 1);


        double weight;


        while (true) {


            System.out.print("Enter Laundry Weight (kg): ");
            weight = readDouble();


            if (Double.isFinite(weight) &&
                weight > 0 && weight <= 50) {
                break;
            }


            System.out.println(
                "Invalid weight! Enter a value above 0 and up to 50 kg.");
        }


        LocalDate pickupDate;


        pickupDate = readDate(
        "Enter Pickup Date (MM-DD-YYYY): ");


LocalDate deliveryDate;


while (true) {


    deliveryDate = readDate(
            "Enter Delivery Date (MM-DD-YYYY): ");


    if (deliveryDate.isAfter(pickupDate)) {
        break;
    }


    System.out.println(
            "Delivery date must be after pickup date. Please try again.");
}


        boolean express;


        while (true) {


            System.out.print("Express Order? (Y/N): ");
            String answer = scanner.nextLine().trim();


            if (answer.equalsIgnoreCase("Y")) {


                express = true;
                break;


            } else if (answer.equalsIgnoreCase("N")) {


                express = false;
                break;


            } else {


                System.out.println("Invalid input! Enter Y or N.");
            }
        }


        LaundryOrder order = new LaundryOrder(
                customer,
                selectedService,
                weight,
                pickupDate,
                deliveryDate,
                express
        );


        orders.add(order);


        order.displayReceipt();


        System.out.println(
                "\nLaundry schedule successfully created!");
    }


    public static void displayAllOrders() {


        System.out.println("\n========== ALL LAUNDRY ORDERS ==========");


        if (orders.isEmpty()) {


            System.out.println("No laundry orders found.");
            return;
        }


        for (int i = 0; i < orders.size(); i++) {


            System.out.println("\nOrder Number: " + (i + 1));


            orders.get(i).displayReceipt();
        }


        System.out.println("\nTotal Orders: " + orders.size());
    }


    public static int readInt() {


        while (true) {


            try {


                return Integer.parseInt(
                        scanner.nextLine().trim());


            } catch (NumberFormatException e) {


                System.out.print(
                        "Invalid input! Enter a whole number: ");
            }
        }
    }


    public static double readDouble() {


        while (true) {


            try {


                return Double.parseDouble(
                        scanner.nextLine().trim());


            } catch (NumberFormatException e) {


                System.out.print(
                        "Invalid input! Enter a valid number: ");
            }
        }
    }


    public static LocalDate readDate(String message) {


        DateTimeFormatter formatter =
                DateTimeFormatter.ofPattern("MM-dd-uuuu")
                        .withResolverStyle(ResolverStyle.STRICT);


        while (true) {


            System.out.print(message);
            String dateInput = scanner.nextLine().trim();


            try {


                return LocalDate.parse(dateInput, formatter);


            } catch (DateTimeParseException e) {


                System.out.println(
                    "Invalid date! Use MM-DD-YYYY format (example: 05-26-2027).");
            }
        }
    }
}

