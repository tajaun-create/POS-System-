store_name = "Best Buy Retail Store"
# Tax and discount values
tax_rate = 0.10
discount_rate = 0.05
discount_amt = 5000
low_stock_count = 5

# Product catalog
products = {
    "rice": {"price": 500, "stock": 10},
    "sugar": {"price": 190, "stock": 7},
    "flour": {"price": 200, "stock": 10},
    "cornmeal": {"price": 220, "stock": 9},
    "sardines": {"price": 180, "stock": 13},
    "ting": {"price": 300, "stock": 12},
    "bread": {"price": 600, "stock": 12},
    "cornbeef": {"price": 750, "stock": 10},
    "eggs": {"price": 1500, "stock": 10},
    "oats": {"price": 175, "stock": 15}
}

  
    
# This function displays all products available in store
def show_products():
    print("\nAll available products")
    print("-" * 40)

    # Loop through each product in the dictionary
    for item in products:
        print(
            item.title(),
            "- Price: $", products[item]["price"],
            "| Stock:", products[item]["stock"]
        )
        
# This function adds an item to the cart
def add_item(cart):
    item = input("Enter product name: ").strip().lower()
    
    
    #To check if product exists
    if item not in products:
        print("Product not found.")
        return
    
    # Ask user for quantity
    try:
        quantity = int(input("Enter quantity: "))
    except ValueError:
        print("Invalid quantity.")
        return

    # Check if quantity is valid
    if quantity <= 0:
        print("Quantity must be greater than 0.")
        return

    # Check stock availability
    if quantity > products[item]["stock"]:
        print("Not enough stock available.")
        return

    # If item is already in cart, increase quantity only
    if item in cart:
        cart[item]["quantity"] = cart[item]["quantity"] + quantity
    else:
        # If item is not yet in cart, add price and quantity
        cart[item] = {
            "price": products[item]["price"],
            "quantity": quantity
        }

    # remove product from stock count
    products[item]["stock"] = products[item]["stock"] - quantity

    print("Item added to cart.")


#Function to remove an item or part of an item from the cart
def remove_item(cart):
    item = input("Enter product name to remove: ").lower()

    # To check if item is in the cart
    if item not in cart:
        print("Item not in cart.")
        return

    # Ask how many to remove
    try:
        quantity = int(input("Enter quantity to remove: "))
    except ValueError:
        print("Invalid quantity.")
        return

    # Check if quantity is valid
    if quantity <= 0:
        print("Quantity must be greater than 0.")
        return

    # If removing all or more than cart quantity, remove item fully
    if quantity >= cart[item]["quantity"]:
        removed_quantity = cart[item]["quantity"]
        products[item]["stock"] = products[item]["stock"] + removed_quantity
        del cart[item]
        print("Item removed completely from cart.")
    else:
        # Otherwise reduce quantity in cart
        cart[item]["quantity"] = cart[item]["quantity"] - quantity
        products[item]["stock"] = products[item]["stock"] + quantity
        print("Item quantity reduced in cart.")


#To show items in the cart
def view_cart(cart):
    # Check if cart is empty
    if not cart:
        print("Cart is empty.")
        return

    print("\nCart")
    print("-" * 40)

    subtotal = 0

    # Loop through items in cart
    for item in cart:
        quantity = cart[item]["quantity"]
        price = cart[item]["price"]
        total = quantity * price
        subtotal = subtotal + total

        print(item.title(), "x", quantity, "@", price, "= $", total)

    print("Subtotal: $", subtotal)


# To calculate the bill
def calculate_bill(cart):
    subtotal = 0

    # Add up total of all cart items
    for item in cart:
        quantity = cart[item]["quantity"]
        price = cart[item]["price"]
        subtotal = subtotal + (quantity * price)

    # Apply discount if subtotal is above the limit
    if subtotal > discount_amt:
        discount = subtotal * discount_rate
    else:
        discount = 0

    # Calculate tax after discount
    taxable_amount = subtotal - discount
    tax = taxable_amount * tax_rate
    total_due = taxable_amount + tax

    return subtotal, discount, tax, total_due

# This function prints the receipt
def print_receipt(cart, subtotal, discount, tax, total_due, amount_paid, change):
    print("\n" + "=" * 40)
    print(store_name)
    print("RECEIPT")
    print("=" * 40)

    # Print each item bought
    for item in cart:
        quantity = cart[item]["quantity"]
        price = cart[item]["price"]
        total = quantity * price
        print(item.title(), "x", quantity, "@", price, "= $", total)

    print("-" * 40)
    print("Subtotal: $", round(subtotal,2))
    print("Discount: $", round(discount,2))
    print("Tax: $", round(tax, 2))
    print("Total Due: $", round(total_due, 2))
    print("Amount Paid: $", amount_paid)
    print("Change: $", round(change, 2))
    print("-" * 40)
    print("Thank you for shopping!")
    print("=" * 40)


# This function handles checkout
def checkout(cart):
    # Check if cart is empty
    if not cart:
        print("Cart is empty.")
        return

    # Calculate bill
    subtotal, discount, tax, total_due = calculate_bill(cart)

    print("\nCheckout")
    print("Subtotal: $", subtotal)
    print("Discount: $", discount)
    print("Tax: $", round(tax, 2))
    print("Total Due: $", round(total_due, 2))

    # Ask for payment until it is enough
    while True:
        try:
            amount_paid = float(input("Enter amount paid: $"))
        except ValueError:
            print("Invalid amount.")
            continue

        if amount_paid < total_due:
            print("Payment is not enough.")
        else:
            break

    # Calculate change
    change = amount_paid - total_due

    # Print receipt
    print_receipt(cart, subtotal, discount, tax, total_due, amount_paid, change)

    # Empty cart after checkout
    cart.clear()


# This function displays low stock items
def low_stock_alert():
    print("\nLow Stock Alerts")
    print("-" * 40)

    found = False

    # Check which products are below the low stock limit
    for item in products:
        if products[item]["stock"] <= low_stock_count:
            print(item.title(), "- only", products[item]["stock"], "left")
            found = True

    # If no low stock items are found
    if found == False:
        print("No low-stock items.")


# This function cancels a transaction and restores stock
def cancel_transaction(cart):
    # Return cart quantities back to stock
    for item in cart:
        products[item]["stock"] = products[item]["stock"] + cart[item]["quantity"]

    # Empty the cart
    cart.clear()

    print("Transaction cancelled.")


# Main program function
def main():
    # Loop to allow more than one transaction
    while True:
        # Create a new empty cart
        cart = {}

        # Menu for one transaction
        while True:
            print("\nPOS MENU")
            print("1. Show Products")
            print("2. Add Item")
            print("3. Remove Item")
            print("4. View Cart")
            print("5. Checkout")
            print("6. Low Stock Alerts")
            print("7. Cancel Transaction")

            choice = input("Choose an option: ")

            if choice == "1":
                show_products()

            elif choice == "2":
                add_item(cart)

            elif choice == "3":
                remove_item(cart)

            elif choice == "4":
                view_cart(cart)

            elif choice == "5":
                checkout(cart)
                low_stock_alert()
                break

            elif choice == "6":
                low_stock_alert()

            elif choice == "7":
                cancel_transaction(cart)
                break

            else:
                print("Invalid option.")

        # Ask if user wants another transaction
        again = input("\nStart another transaction? (yes/no): ").lower()

        if again != "yes":
            print("System closed.")
            break


# Start the program
main()
