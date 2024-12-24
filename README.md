import java.util.*;
import java.util.stream.Collectors;

class Customer {
    int customerId;
    String name;
    LinkedList<Shipment> shipmentHistory;

    public Customer(int customerId, String name) {
        this.customerId = customerId;
        this.name = name;
        this.shipmentHistory = new LinkedList<>();
    }

    public void addShipment(Shipment shipment) {
        shipmentHistory.add(shipment);
        shipmentHistory.sort(Comparator.comparing(Shipment::getDate)); // Sort by date
    }

    public List<Shipment> getRecentShipments(int count) {
        return shipmentHistory.stream().limit(count).collect(Collectors.toList());



    }
}

class Shipment {
    int shipmentId;
    Date date;
    String status;
    int deliveryTime;

    public Shipment(int shipmentId, Date date, String status, int deliveryTime) {
        this.shipmentId = shipmentId;
        this.date = date;
        this.status = status;
        this.deliveryTime = deliveryTime;
    }

    public Date getDate() {
        return date;
    }
}

class ShipmentPriorityQueue {
    private final PriorityQueue<Shipment> queue;

    public ShipmentPriorityQueue() {
        this.queue = new PriorityQueue<>(Comparator.comparingInt(s -> s.deliveryTime));
    }

    public void addShipment(Shipment shipment) {
        queue.offer(shipment);
    }

    public Shipment processNextShipment() {
        return queue.poll();
    }
}

class TreeNode {
    String cityName;
    int cityId;
    List<TreeNode> children;

    public TreeNode(String cityName, int cityId) {
        this.cityName = cityName;
        this.cityId = cityId;
        this.children = new ArrayList<>();
    }

    public void addChild(TreeNode child) {
        children.add(child);
    }

    public void printTree(String prefix) {
        System.out.println(prefix + cityName);
        for (TreeNode child : children) {
            child.printTree(prefix + "--");
        }
    }
}

public class CargoTrackingSystem {
    static List<Customer> customers = new ArrayList<>();
    static ShipmentPriorityQueue priorityQueue = new ShipmentPriorityQueue();
    static TreeNode deliveryTree;

    public static void main(String[] args) {
        Scanner scanner = new Scanner(System.in);
        initializeDeliveryTree();

        while (true) {
            System.out.println("\nCargo Tracking System Menu:");
            System.out.println("1. Add New Customer");
            System.out.println("2. Add Shipment for Customer");
            System.out.println("3. View Recent Shipments for Customer");
            System.out.println("4. Process Next Shipment");
            System.out.println("5. Display Delivery Routes");
            System.out.println("6. Exit");
            System.out.print("Enter your choice: ");

            int choice = scanner.nextInt();
            scanner.nextLine();

            switch (choice) {
                case 1 -> addCustomer(scanner);
                case 2 -> addShipment(scanner);
                case 3 -> viewRecentShipments(scanner);
                case 4 -> processShipment();
                case 5 -> deliveryTree.printTree("");
                case 6 -> {
                    System.out.println("Exiting system. Goodbye!");
                    return;
                }
                default -> System.out.println("Invalid choice. Please try again.");
            }
        }
    }

    private static void addCustomer(Scanner scanner) {
        System.out.print("Enter Customer ID: ");
        int id = scanner.nextInt();
        scanner.nextLine();
        System.out.print("Enter Customer Name: ");
        String name = scanner.nextLine();
        customers.add(new Customer(id, name));
        System.out.println("Customer added successfully.");
    }

    private static void addShipment(Scanner scanner) {
        System.out.print("Enter Customer ID: ");
        int customerId = scanner.nextInt();
        scanner.nextLine();

        Customer customer = customers.stream().filter(c -> c.customerId == customerId).findFirst().orElse(null);
        if (customer == null) {
            System.out.println("Customer not found.");
            return;
        }

        System.out.print("Enter Shipment ID: ");
        int shipmentId = scanner.nextInt();
        System.out.print("Enter Delivery Time (in days): ");
        int deliveryTime = scanner.nextInt();
        scanner.nextLine();
        System.out.print("Enter Shipment Status: ");
        String status = scanner.nextLine();

        Shipment shipment = new Shipment(shipmentId, new Date(), status, deliveryTime);
        customer.addShipment(shipment);
        priorityQueue.addShipment(shipment);

        System.out.println("Shipment added successfully.");
    }

    private static void viewRecentShipments(Scanner scanner) {
        System.out.print("Enter Customer ID: ");
        int customerId = scanner.nextInt();

        Customer customer = customers.stream().filter(c -> c.customerId == customerId).findFirst().orElse(null);
        if (customer == null) {
            System.out.println("Customer not found.");
            return;
        }

        List<Shipment> recentShipments = customer.getRecentShipments(5);
        System.out.println("Recent Shipments:");
        for (Shipment shipment : recentShipments) {
            System.out.println("Shipment ID: " + shipment.shipmentId + ", Date: " + shipment.date + ", Status: " + shipment.status);
        }
    }

    private static void processShipment() {
        Shipment shipment = priorityQueue.processNextShipment();
        if (shipment == null) {
            System.out.println("No shipments to process.");
        } else {
            System.out.println("Processing shipment ID: " + shipment.shipmentId);
        }
    }

    private static void initializeDeliveryTree() {
        deliveryTree = new TreeNode("Headquarters", 0);
        TreeNode city1 = new TreeNode("City A", 1);
        TreeNode city2 = new TreeNode("City B", 2);
        TreeNode city3 = new TreeNode("City C", 3);

        deliveryTree.addChild(city1);
        deliveryTree.addChild(city2);
        city1.addChild(new TreeNode("City A1", 4));
        city2.addChild(city3);
    }
}
