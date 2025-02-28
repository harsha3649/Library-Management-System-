# Library-Management-System-
import javax.swing.*;
import java.awt.*;
import java.awt.event.*;
import java.sql.*;

public class LibraryManagementSystem {
    private JFrame frame;
    private JTextField bookIDField, bookTitleField, authorField;
    private JTextArea displayArea;
    private Connection connection;

    public LibraryManagementSystem() {
        initializeGUI();
        connectDatabase();
    }

    private void initializeGUI() {
        frame = new JFrame("Library Management System");
        frame.setSize(500, 400);
        frame.setDefaultCloseOperation(JFrame.EXIT_ON_CLOSE);
        frame.setLayout(new GridLayout(5, 2));

        JLabel bookIDLabel = new JLabel("Book ID:");
        bookIDField = new JTextField();
        JLabel bookTitleLabel = new JLabel("Title:");
        bookTitleField = new JTextField();
        JLabel authorLabel = new JLabel("Author:");
        authorField = new JTextField();

        JButton addButton = new JButton("Add Book");
        JButton viewButton = new JButton("View Books");

        displayArea = new JTextArea();
        displayArea.setEditable(false);
        JScrollPane scrollPane = new JScrollPane(displayArea);

        frame.add(bookIDLabel);
        frame.add(bookIDField);
        frame.add(bookTitleLabel);
        frame.add(bookTitleField);
        frame.add(authorLabel);
        frame.add(authorField);
        frame.add(addButton);
        frame.add(viewButton);
        frame.add(scrollPane);

        addButton.addActionListener(e -> addBook());
        viewButton.addActionListener(e -> viewBooks());

        frame.setVisible(true);
    }

    private void connectDatabase() {
        try {
            connection = DriverManager.getConnection("jdbc:mysql://localhost:3306/library", "root", "password");
            Statement statement = connection.createStatement();
            statement.execute("CREATE TABLE IF NOT EXISTS books (id INT PRIMARY KEY, title VARCHAR(100), author VARCHAR(100))");
        } catch (SQLException e) {
            e.printStackTrace();
        }
    }

    private void addBook() {
        try {
            int id = Integer.parseInt(bookIDField.getText());
            String title = bookTitleField.getText();
            String author = authorField.getText();
            PreparedStatement statement = connection.prepareStatement("INSERT INTO books VALUES (?, ?, ?)");
            statement.setInt(1, id);
            statement.setString(2, title);
            statement.setString(3, author);
            statement.executeUpdate();
            JOptionPane.showMessageDialog(frame, "Book Added Successfully");
            bookIDField.setText("");
            bookTitleField.setText("");
            authorField.setText("");
        } catch (Exception e) {
            JOptionPane.showMessageDialog(frame, "Error: " + e.getMessage());
        }
    }

    private void viewBooks() {
        try {
            Statement statement = connection.createStatement();
            ResultSet resultSet = statement.executeQuery("SELECT * FROM books");
            displayArea.setText("ID\tTitle\tAuthor\n");
            while (resultSet.next()) {
                displayArea.append(resultSet.getInt("id") + "\t" + resultSet.getString("title") + "\t" + resultSet.getString("author") + "\n");
            }
        } catch (SQLException e) {
            e.printStackTrace();
        }
    }

    public static void main(String[] args) {
        SwingUtilities.invokeLater(LibraryManagementSystem::new);
    }
}
