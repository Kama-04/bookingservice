# bookingservice
Выполнил работу: Абакаров Камиль (3исп11-24вб) java
package com.example.demo1;

import javafx.application.Application;
import javafx.collections.FXCollections;
import javafx.collections.ObservableList;
import javafx.geometry.Insets;
import javafx.scene.Scene;
import javafx.scene.control.*;
import javafx.scene.layout.GridPane;
import javafx.scene.layout.VBox;
import javafx.stage.Stage;

import java.util.ArrayList;
import java.util.List;
import java.util.Random;

public class BookingService extends Application {
    private final ObservableList<String> eventsList = FXCollections.observableArrayList(
            "Концерт Макана", "Концерт Макса Коржа", "Театр печали");

    private final List<Seat> seatsData = new ArrayList<>();

    @Override
    public void start(Stage primaryStage) {
        GridPane root = new GridPane();
        root.setPadding(new Insets(20));
        root.setHgap(10);
        root.setVgap(10);

        Label eventLabel = new Label("Мероприятие:");
        ComboBox<String> eventComboBox = new ComboBox<>(eventsList);
        eventComboBox.setPromptText("Выберите мероприятие");

        Label seatsLabel = new Label("Количество мест:");
        Spinner<Integer> seatsSpinner = new Spinner<>(1, 10, 1);

        Button searchButton = new Button("Поиск");
        searchButton.setOnAction(e -> searchSeats(eventComboBox.getValue(), seatsSpinner.getValue()));

        root.add(eventLabel, 0, 0);
        root.add(eventComboBox, 1, 0);
        root.add(seatsLabel, 0, 1);
        root.add(seatsSpinner, 1, 1);
        root.add(searchButton, 0, 2, 2, 1);

        Scene scene = new Scene(root, 300, 150);
        primaryStage.setScene(scene);
        primaryStage.setTitle("Сервис бронирования мест");
        primaryStage.show();
    }

    private void searchSeats(String event, int seats) {
        if (event == null || event.isEmpty()) {
            showAlert(Alert.AlertType.WARNING, "Ошибка", "Мероприятие не выбрано", "Выберите мероприятие из списка.");
            return;
        }

        if (seats <= 0) {
            showAlert(Alert.AlertType.WARNING, "Ошибка", "Некорректное количество мест", "Введите число мест больше 0.");
            return;
        }

        Random random = new Random();
        seatsData.clear();
        for (int i = 1; i <= seats; i++) {
            boolean isSeatAvailable = random.nextBoolean();
            Seat seat = new Seat(i, isSeatAvailable);
            seatsData.add(seat);
        }

        displaySeatsStatus(event);
    }

    private void displaySeatsStatus(String event) {
        VBox vbox = new VBox(10);
        for (Seat seat : seatsData) {
            CheckBox seatCheckbox = new CheckBox("Место " + seat.getNumber() + " - " +
                    (seat.isAvailable() ? "Свободно" : "Занято"));
            seatCheckbox.setDisable(!seat.isAvailable()); // Запрет выбора занятых мест
            vbox.getChildren().add(seatCheckbox);
        }

        Button reserveButton = new Button("Забронировать");
        reserveButton.setOnAction(e -> saveReservation(event));

        vbox.getChildren().add(reserveButton);

        showAlertWithSeatsList(Alert.AlertType.INFORMATION, "Успех", "Состояние мест", "Места на мероприятии '" + event + "':", vbox);
    }

    private void saveReservation(String event) {
        List<Integer> reservedSeats = new ArrayList<>();
        for (Seat seat : seatsData) {
            if (!seat.isAvailable()) {
                reservedSeats.add(seat.getNumber());
            }
        }

        // Здесь можно добавить логику сохранения информации о бронировании, например, в базу данных или файл
        System.out.println("Забронированные места на мероприятии '" + event + "': " + reservedSeats);
        showAlert(Alert.AlertType.INFORMATION, "Успех", "Бронирование успешно сохранено",
                "Забронированные места: " + reservedSeats);
    }

    private void showAlertWithSeatsList(Alert.AlertType alertType, String title, String header, String content, VBox vbox) {
        Alert alert = new Alert(alertType);
        alert.setTitle(title);
        alert.setHeaderText(header);
        alert.setContentText(content);
        alert.
                getDialogPane().setContent(vbox);
        alert.showAndWait();
    }

    private void showAlert(Alert.AlertType alertType, String title, String header, String content) {
        Alert alert = new Alert(alertType);
        alert.setTitle(title);
        alert.setHeaderText(header);
        alert.setContentText(content);
        alert.showAndWait();
    }

    public static void main(String[] args) {
        launch(args);
    }

    private static class Seat {
        private int number;
        private boolean available;

        public Seat(int number, boolean available) {
            this.number = number;
            this.available = available;
        }

        public int getNumber() {
            return number;
        }

        public boolean isAvailable() {
            return available;
        }
    }
}


HELLOCONTROLLER.JAVA
package com.example.demo1;

import javafx.fxml.FXML;
import javafx.scene.control.Label;

public class HelloController {
    @FXML
    private Label welcomeText;

    @FXML
    protected void onHelloButtonClick() {
        welcomeText.setText("Welcome to JavaFX Application!");
    }
}

HELLO-VIEW.FXML
<?xml version="1.0" encoding="UTF-8"?>

<?import javafx.geometry.Insets?>
<?import javafx.scene.control.Label?>
<?import javafx.scene.layout.VBox?>

<?import javafx.scene.control.Button?>
<VBox alignment="CENTER" spacing="20.0" xmlns:fx="http://javafx.com/fxml"
      fx:controller="com.example.demo1.HelloController">
    <padding>
        <Insets bottom="20.0" left="20.0" right="20.0" top="20.0"/>
    </padding>

    <Label fx:id="welcomeText"/>
    <Button text="Hello!" onAction="#onHelloButtonClick"/>
</VBox>



module com.example.demo1 {
    requires javafx.controls;
    requires javafx.fxml;

MODULE-INFO.JAVA
    opens com.example.demo1 to javafx.fxml;
    exports com.example.demo1;
}
