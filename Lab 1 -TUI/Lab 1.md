# Lab 1
# Створення багатовіконного TUI з допомогою Jexer

## На "трійку"
Скріншот запущеної програми:
![image](https://github.com/ppc-ntu-khpi/tui-tamiilpho/assets/120334809/b8441aac-4aa4-4dc8-b568-b375fe2a1a54)

## На "чотири"
Переписаний код:
````java
import jexer.TAction;
import jexer.TApplication;
import jexer.TField;
import jexer.TText;
import jexer.TWindow;
import jexer.event.TMenuEvent;
import jexer.menu.TMenu;

import java.util.ArrayList;
import java.util.List;

/**
 *
 * @author Alexander 'Taurus' Babich
 */
public class TUIdemo extends TApplication {

    private static final int ABOUT_APP = 2000;
    private static final int CUST_INFO = 2010;
    private Bank bank;

    public static void main(String[] args) throws Exception {
        TUIdemo tdemo = new TUIdemo();
        (new Thread(tdemo)).start();
    }

    public TUIdemo() throws Exception {
        super(TApplication.BackendType.SWING);

        // Ініціалізація банку з деякими клієнтами
        bank = new Bank();
        bank.addCustomer(new Customer(1, "John Doe", new Account("Checking", 200.00)));
        bank.addCustomer(new Customer(2, "Jane Smith", new Account("Savings", 1500.00)));

        addToolMenu();
        // Користувацьке меню 'File'
        TMenu fileMenu = addMenu("&File");
        fileMenu.addItem(CUST_INFO, "&Customer Info");
        fileMenu.addDefaultItem(TMenu.MID_SHELL);
        fileMenu.addSeparator();
        fileMenu.addDefaultItem(TMenu.MID_EXIT);
        // Кінець меню 'File' 

        addWindowMenu();

        // Користувацьке меню 'Help'
        TMenu helpMenu = addMenu("&Help");
        helpMenu.addItem(ABOUT_APP, "&About...");
        // Кінець меню 'Help' 

        setFocusFollowsMouse(true);
        // Вікно клієнта
        ShowCustomerDetails();
    }

    @Override
    protected boolean onMenu(TMenuEvent menu) {
        if (menu.getId() == ABOUT_APP) {
            messageBox("About", "\t\t\t\t\t   Just a simple Jexer demo.\n\nCopyright \u00A9 2019 Alexander \'Taurus\' Babich").show();
            return true;
        }
        if (menu.getId() == CUST_INFO) {
            ShowCustomerDetails();
            return true;
        }
        return super.onMenu(menu);
    }

    private void ShowCustomerDetails() {
        TWindow custWin = addWindow("Customer Window", 2, 1, 40, 10, TWindow.NOZOOMBOX);
        custWin.newStatusBar("Enter valid customer number and press Show...");

        custWin.addLabel("Enter customer number: ", 2, 2);
        TField custNo = custWin.addField(24, 2, 3, false);
        TText details = custWin.addText("Owner Name: \nAccount Type: \nAccount Balance: ", 2, 4, 38, 8);
        custWin.addButton("&Show", 28, 2, new TAction() {
            @Override
            public void DO() {
                try {
                    int custNum = Integer.parseInt(custNo.getText());
                    Customer customer = bank.getCustomer(custNum);
                    if (customer != null) {
                        Account account = customer.getAccount();
                        details.setText(String.format("Owner Name: %s (id=%d)\nAccount Type: '%s'\nAccount Balance: $%.2f",
                                customer.getName(), custNum, account.getType(), account.getBalance()));
                    } else {
                        messageBox("Error", "Customer not found!").show();
                    }
                } catch (Exception e) {
                    messageBox("Error", "You must provide a valid customer number!").show();
                }
            }
        });
    }

    // Зразки класів
    public class Bank {
        private List<Customer> customers;

        public Bank() {
            customers = new ArrayList<>();
        }

        public void addCustomer(Customer customer) {
            customers.add(customer);
        }

        public Customer getCustomer(int id) {
            for (Customer customer : customers) {
                if (customer.getId() == id) {
                    return customer;
                }
            }
            return null;
        }
    }

    public class Customer {
        private int id;
        private String name;
        private Account account;

        public Customer(int id, String name, Account account) {
            this.id = id;
            this.name = name;
            this.account = account;
        }

        public int getId() {
            return id;
        }

        public String getName() {
            return name;
        }

        public Account getAccount() {
            return account;
        }
    }

    public class Account {
        private String type;
        private double balance;

        public Account(String type, double balance) {
            this.type = type;
            this.balance = balance;
        }

        public String getType() {
            return type;
        }

        public double getBalance() {
            return balance;
        }
    }
}
````
Скріншот запущеної програми:

![image](https://github.com/ppc-ntu-khpi/tui-tamiilpho/assets/120334809/24a80b3c-0e5c-4a1f-ad67-3969f6bd98c5)
