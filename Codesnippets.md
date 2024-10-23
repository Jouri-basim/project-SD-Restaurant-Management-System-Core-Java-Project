1. Controller Class
This class manages the flow between the user interface and the database, handling actions such as login, navigation between scenes, and user interaction.

Login:
The userLogin() method checks if the user is a manager based on their input and triggers the appropriate login process.
Example:
private void userLogin() {
    printMessageToView("Login as manager? (Y/N)");
    String key = cView.userInput();
    if (key.equalsIgnoreCase("Y")) {
        loginCheck(true);  
    } else {
        loginCheck(false); 
    }
}

Main Loop:
The mainLoop() method controls the navigation through the system's various functionalities based on the current scene.
Example: 
public void mainLoop() {
    while (state == 0) {
        switch (this.scene) {
            case SCENE_MAIN_MENU:
                selectMenu();
                break;
            case SCENE_LOGIN:
                userLogin();    
                break;
            case SCENE_ORDER:
                selectOrderMenu();
                break;
            case SCENE_EMPLOYEE_LIST:
                showStaffList();
                break;
            case SCENE_EDIT_EMPLOYEE:
                chooseEditStaffMode();
                break;
            case SCENE_EDIT_MENU_ITEM:
                chooseEditMenuItemMode();
                break;
            default:
                this.scene = SCENE_MAIN_MENU;
                break;
        }
    }
    cView.finish(); 
}

2. Database Class
This class handles data-related tasks, such as loading files, saving orders, and editing menu items.

Loading Files:
The loadFiles() method loads essential data from files, such as staff, managers, menu items, and wage information, and prepares the system for use.
Example:
public void loadFiles() throws DatabaseException {
    loadStaffFile();
    loadManagerFile();
    Collections.sort(staffList, new StaffComparator());
    loadMenuFile();
    loadWageInfoFile();
}

Adding a New Order:
The addOrder() method adds a new order to the database and generates a unique order ID for each new order.
Example: 
public int addOrder(int staffID, String staffName) {
    int newOrderID = ++todaysOrderCounts;
    Order newOrder = new Order(staffID, staffName);
    newOrder.setOrderID(newOrderID);
    orderList.add(newOrder);
    return newOrderID;
}

Editing a Menu Item:
The editMenuItemData() method allows updating an existing menu item's details, such as name, price, and menu type.
Example: 
public void editMenuItemData(int id, String newName, double newPrice, byte menuType) throws DatabaseException {
    MenuItem rMenuItem = findMenuItemByID(id);
    rMenuItem.setName(newName);
    rMenuItem.setPrice(newPrice);
    rMenuItem.setType(menuType);
}

3. Order Class
This class manages customer orders, including the items ordered and their quantities.

Adding an Item to an Order:
The addItem() method adds a menu item to the order and updates the total quantity if the item was already added.
Example: 
public void addItem(MenuItem rNewMenuItem, byte quantity) {
    boolean found = false;
    for (OrderDetail detail : orderDetailList) {
        if (rNewMenuItem.getID() == detail.getItemID()) {
            detail.addQuantity(quantity);
            found = true;
            break;
        }
    }
    if (!found) {
        OrderDetail newDetail = new OrderDetail(rNewMenuItem, quantity);
        orderDetailList.add(newDetail);
    }
    calculateTotal();
}


Calculating the Total Price:
The calculateTotal() method calculates the total cost of all items in the order.
Example:
public void calculateTotal() {
    total = 0;
    for (OrderDetail detail : orderDetailList) {
        total += detail.getTotalPrice();
    }
}

4. MenuItem Class
This class represents individual menu items, such as food and beverages, in the restaurant.

Setting Item State:
The setState() method sets a menu item as a promotional item with a temporary reduced price.
Example: public void setState(byte newState, double tempPrice) {
    this.state = newState;
    this.promotion_price = tempPrice;
} 

Getting Item Price:
The getPrice() method retrieves the item’s price, applying any promotional discount if available.
Example: 
public double getPrice() {
    if (this.state != 0 && this.promotion_price != 0) {
        return this.promotion_price;
    } else {
        return this.price;
    }
}
