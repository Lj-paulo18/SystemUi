import javafx.application.Application;
import javafx.geometry.Pos;
import javafx.scene.Scene;
import javafx.scene.control.*;
import javafx.scene.layout.*;
import javafx.stage.Stage;

import java.util.ArrayList;
import java.util.List;

public class PharmacySystemUI extends Application {

    private Pharmacy pharmacy = new Pharmacy("QuickCare Pharmacy");

    public static void main(String[] args) {
        launch(args);
    }

    @Override
    public void start(Stage primaryStage) {
        primaryStage.setTitle("Pharmacy System");

        // Create UI components
        TabPane tabPane = new TabPane();

        // Tab 1: Add Medicine
        Tab addMedicineTab = new Tab("Add Medicine");
        VBox addMedicineLayout = new VBox(10);
        addMedicineLayout.setAlignment(Pos.CENTER);

        TextField nameField = new TextField();
        nameField.setPromptText("Enter Medicine Name");
        TextField priceField = new TextField();
        priceField.setPromptText("Enter Price");
        TextField quantityField = new TextField();
        quantityField.setPromptText("Enter Quantity");
        Button addButton = new Button("Add Medicine");

        addButton.setOnAction(e -> {
            String name = nameField.getText();
            double price = Double.parseDouble(priceField.getText());
            int quantity = Integer.parseInt(quantityField.getText());
            pharmacy.addMedicine(new Medicine(name, price, quantity));
            nameField.clear();
            priceField.clear();
            quantityField.clear();
            Alert alert = new Alert(Alert.AlertType.INFORMATION, "Medicine added successfully!", ButtonType.OK);
            alert.showAndWait();
        });

        addMedicineLayout.getChildren().addAll(nameField, priceField, quantityField, addButton);
        addMedicineTab.setContent(addMedicineLayout);

        // Tab 2: View Medicines
        Tab viewMedicinesTab = new Tab("View Medicines");
        VBox viewMedicinesLayout = new VBox(10);
        viewMedicinesLayout.setAlignment(Pos.CENTER);

        TableView<Medicine> tableView = new TableView<>();
        tableView.setEditable(false);
        tableView.getColumns().addAll(createColumn("Name", "name"), createColumn("Price", "price"), createColumn("Quantity", "quantity"));

        Button refreshButton = new Button("Refresh");
        refreshButton.setOnAction(e -> tableView.getItems().setAll(pharmacy.getMedicines()));
        viewMedicinesLayout.getChildren().addAll(tableView, refreshButton);
        viewMedicinesTab.setContent(viewMedicinesLayout);

        // Tab 3: Sell Medicine
        Tab sellMedicineTab = new Tab("Sell Medicine");
        VBox sellMedicineLayout = new VBox(10);
        sellMedicineLayout.setAlignment(Pos.CENTER);

        TextField sellNameField = new TextField();
        sellNameField.setPromptText("Enter Medicine Name");
        TextField sellQuantityField = new TextField();
        sellQuantityField.setPromptText("Enter Quantity to Sell");
        Button sellButton = new Button("Sell Medicine");

        sellButton.setOnAction(e -> {
            String name = sellNameField.getText();
            int quantity = Integer.parseInt(sellQuantityField.getText());
            boolean success = pharmacy.sellMedicine(name, quantity);
            if (success) {
                Alert alert = new Alert(Alert.AlertType.INFORMATION, "Medicine sold successfully!", ButtonType.OK);
                alert.showAndWait();
            } else {
                Alert alert = new Alert(Alert.AlertType.ERROR, "Medicine not available or insufficient quantity.", ButtonType.OK);
                alert.showAndWait();
            }
            sellNameField.clear();
            sellQuantityField.clear();
        });

        sellMedicineLayout.getChildren().addAll(sellNameField, sellQuantityField, sellButton);
        sellMedicineTab.setContent(sellMedicineLayout);

        // Tab 4: Exit
        Tab exitTab = new Tab("Exit");
        Button exitButton = new Button("Exit System");
        exitButton.setOnAction(e -> primaryStage.close());
        VBox exitLayout = new VBox(10);
        exitLayout.setAlignment(Pos.CENTER);
        exitLayout.getChildren().add(exitButton);
        exitTab.setContent(exitLayout);

        // Add tabs to TabPane
        tabPane.getTabs().addAll(addMedicineTab, viewMedicinesTab, sellMedicineTab, exitTab);
        tabPane.setTabClosingPolicy(TabPane.TabClosingPolicy.UNAVAILABLE);

        // Set up the Scene and Stage
        Scene scene = new Scene(tabPane, 600, 400);
        primaryStage.setScene(scene);
        primaryStage.show();
    }

    // Helper method to create table columns
    private TableColumn<Medicine, String> createColumn(String title, String property) {
        TableColumn<Medicine, String> column = new TableColumn<>(title);
        column.setCellValueFactory(cellData -> {
            switch (property) {
                case "name":
                    return new javafx.beans.property.SimpleStringProperty(cellData.getValue().getName());
                case "price":
                    return new javafx.beans.property.SimpleStringProperty(String.format("%.2f", cellData.getValue().getPrice()));
                case "quantity":
                    return new javafx.beans.property.SimpleStringProperty(String.valueOf(cellData.getValue().getQuantity()));
                default:
                    return null;
            }
        });
        return column;
    }
}

// Modified Pharmacy and Medicine classes for JavaFX
class Pharmacy {
    private String name;
    private List<Medicine> medicines;

    public Pharmacy(String name) {
        this.name = name;
        this.medicines = new ArrayList<>();
    }

    public void addMedicine(Medicine medicine) {
        for (Medicine m : medicines) {
            if (m.getName().equalsIgnoreCase(medicine.getName())) {
                m.setQuantity(m.getQuantity() + medicine.getQuantity());
                return;
            }
        }
        medicines.add(medicine);
    }

    public List<Medicine> getMedicines() {
        return medicines;
    }

    public boolean sellMedicine(String name, int quantity) {
        for (Medicine m : medicines) {
            if (m.getName().equalsIgnoreCase(name) && m.getQuantity() >= quantity) {
                m.setQuantity(m.getQuantity() - quantity);
                return true;
            }
        }
        return false;
    }
}

class Medicine {
    private String name;
    private double price;
    private int quantity;

    public Medicine(String name, double price, int quantity) {
        this.name = name;
        this.price = price;
        this.quantity = quantity;
    }

    public String getName() {
        return name;
    }

    public double getPrice() {
        return price;
    }

    public int getQuantity() {
        return quantity;
    }

    public void setQuantity(int quantity) {
        this.quantity = quantity;
    }
}
