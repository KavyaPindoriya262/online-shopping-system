#include <iostream>
#include <string>
#include <vector>
#include <iomanip>
using namespace std;

// (Abstraction)
class Product {
protected:
    string name;
    double price;
    int id;

public:
    Product(int id, string n, double p) : id(id), name(n), price(p) {}
    
    // polymorphism
    virtual void display() const = 0;
    virtual double calculateDiscountedPrice(int quantity) const = 0;
    
    // Common getters
    string getName() const { return name; }
    double getPrice() const { return price; }
    int getId() const { return id; }
    
    virtual ~Product() {} // Virtual destructor
};

// Inheritance
class Electronics : public Product {
private:
    string brand; // Encapsulation - private data

public:
    Electronics(int id, string n, double p, string b) : Product(id, n, p), brand(b) {}
    
    void display() const override {
        cout << "ID: " << id << ", Name: " << name << " (" << brand << "), Price: Rs." << price << endl;
    }
    
    double calculateDiscountedPrice(int quantity) const override {
        return price * quantity * 0.9; // 10% discount for electronics
    }
};

// Inheritance
class Course : public Product {
private:
    string level; // Encapsulation

public:
    Course(int id, string n, double p, string l) : Product(id, n, p), level(l) {}
    
    void display() const override {
        cout << "ID: " << id << ", Name: " << name << " (" << level << "), Price: Rs." << price << endl;
    }
    
    double calculateDiscountedPrice(int quantity) const override {
        return price * quantity; // No discount for courses
    }
};

// CartItem class with encapsulation
class CartItem {
private:
    Product* product;
    int quantity;

public:
    CartItem(Product* p, int q) : product(p), quantity(q) {}
    
    double getTotal() const {
        return product->calculateDiscountedPrice(quantity);
    }
    
    Product* getProduct() const { return product; }
    int getQuantity() const { return quantity; }
};

// ShoppingCart class
class ShoppingCart {
private:
    vector<CartItem*> items;
    string customerName;

public:
    ShoppingCart(string name) : customerName(name) {}
    
    void addItem(Product* product, int qty) {
        items.push_back(new CartItem(product, qty));
    }
    
    double getTotalBill() const {
        double total = 0;
        for (const auto& item : items) {
            total += item->getTotal();
        }
        return total;
    }
    
    void displayBill() const {
        cout << " === Bill for " << customerName << " ===" << endl;
        cout << left << setw(10) << "ID" << setw(20) << "Name" << setw(10) << "Qty" << setw(12) << "Total" << endl;
        cout << "----------------------------------------" << endl;
        double grandTotal = 0;
        for (const auto& item : items) {
            cout << left << setw(10) << item->getProduct()->getId()
                 << setw(20) << item->getProduct()->getName()
                 << setw(10) << item->getQuantity()
                 << setw(12) << fixed << setprecision(2) << item->getTotal() << endl;
            grandTotal += item->getTotal();
        }
        cout << "----------------------------------------" << endl;
        cout << "Grand Total: Rs. " << fixed << setprecision(2) << grandTotal << endl;
        cout << "Thank you for shopping!" << endl;
    }
    
    ~ShoppingCart() {
        for (auto& item : items) {
            delete item;
        }
    }
};

// OnlineShop class
class OnlineShop {
private:
    vector<Product*> products;

public:
    OnlineShop() {
        // Initialize products
        products.push_back(new Electronics(1, "Samsung Phone", 15000, "Samsung"));
        products.push_back(new Electronics(2, "HP Laptop", 40000, "HP"));
        products.push_back(new Course(3, "C++ Course", 3000, "Advanced"));
        products.push_back(new Course(4, "Java Course", 4000, "Beginner"));
    }
    
    void displayProducts() const {
        cout << "Available Products:" << endl;
        for (const auto& prod : products) {
            prod->display();
        }
    }
    
    Product* findProduct(int id) const {
        for (const auto& prod : products) {
            if (prod->getId() == id) {
                return prod;
            }
        }
        return nullptr;
    }
    
    ~OnlineShop() {
        for (auto& prod : products) {
            delete prod;
        }
    }
};

int main() {
    string name;
    cout << "Enter your name: ";
    getline(cin, name);
    
    OnlineShop shop;
    ShoppingCart cart(name);
    
    shop.displayProducts();
    
    int choice;
    do {
        cout << "1. Add item to cart (enter ID) 2. Show bill and checkout Choice: ";
        cin >> choice;
        if (choice == 1) {
            int id, qty;
            cout << "Enter product ID: ";
            cin >> id;
            Product* prod = shop.findProduct(id);
            if (prod) {
                cout << "Enter quantity: ";
                cin >> qty;
                cart.addItem(prod, qty);
                cout << "Item added!" << endl;
            } else {
                cout << "Product not found!" << endl;
            }
        } else if (choice == 2) {
            cart.displayBill();
        }
    } while (choice != 2);
    
    return 0;
}
