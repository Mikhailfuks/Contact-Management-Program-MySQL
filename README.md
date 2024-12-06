CREATE TABLE contacts_table (
    id INT AUTO_INCREMENT PRIMARY KEY,
    firstName VARCHAR(255) NOT NULL,
    lastName VARCHAR(255) NOT NULL,
    phoneNumber VARCHAR(20),
    email VARCHAR(255)
);

#include <iostream>
#include <string>
#include <vector>
// Include MySQL Connector/C++ headers here  (e.g., mysql.h, mysql++.h)

using namespace std;

// Function to add a new contact
void addContact(MYSQL* mysql, const string& firstName, const string& lastName, const string& phoneNumber, const string& email) {
    string query = "INSERT INTO contacts_table (firstName, lastName, phoneNumber, email) VALUES ('" + firstName + "', '" + lastName + "', '" + phoneNumber + "', '" + email + "')";
    //In a real application, use prepared statements to prevent SQL injection!

    if (mysql_query(mysql, query.c_str())) {
        cerr << "Error adding contact: " << mysql_error(mysql) << endl;
    } else {
        cout << "Contact added successfully!" << endl;
    }
}

// Function to display all contacts
void displayContacts(MYSQL* mysql) {
    string query = "SELECT * FROM contacts_table";
    if (mysql_query(mysql, query.c_str())) {
        cerr << "Error fetching contacts: " << mysql_error(mysql) << endl;
        return;
    }

    MYSQL_RES* result = mysql_store_result(mysql);
    if (result) {
        MYSQL_ROW row;
        cout << "Contacts:" << endl;
        while ((row = mysql_fetch_row(result))) {
            cout << "ID: " << row[0] << ", Name: " << row[1] << " " << row[2] << ", Phone: " << (row[3] ? row[3] : "N/A") << ", Email: " << (row[4] ? row[4] : "N/A") << endl;
        }
        mysql_free_result(result);
    }
}

int main() {
    // ... MySQL connection setup ... Replace with your connection details
    MYSQL* mysql = mysql_init(NULL);
    if (!mysql_real_connect(mysql, "localhost", "your_user", "your_password", "contacts", 0, NULL, 0)) {
        cerr << "Failed to connect to MySQL: " << mysql_error(mysql) << endl;
        return 1;
    }

    // Example usage:
    addContact(mysql, "John", "Doe", "123-456-7890", "john.doe@example.com");
    addContact(mysql, "Jane", "Smith", "987-654-3210", "");
    displayContacts(mysql);

    // MySQL connection cleanup 
    mysql_close(mysql);
    return 0;
}
