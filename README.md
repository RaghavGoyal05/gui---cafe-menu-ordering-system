# gui---cafe-menu-ordering-system
import tkinter as tk
from tkinter import messagebox

# Menu items and prices
menu = {
    "Coffee": 50,
    "Tea": 30,
    "Sandwich": 100,
    "Cake": 150,
    "Juice": 80
}

# Global variables
order = {}
loyalty_points = 0


# Functions
def add_to_order(item):
    """Add selected item to the order."""
    if item in order:
        order[item] += 1
    else:
        order[item] = 1
    update_order_summary()


def update_order_summary():
    """Update the order summary display."""
    order_summary.delete("1.0", tk.END)
    total = 0
    for item, quantity in order.items():
        price = menu[item] * quantity
        total += price
        order_summary.insert(tk.END, f"{item}: {quantity} x ₹{menu[item]} = ₹{price}\n")
    order_summary.insert(tk.END, f"\nTotal: ₹{total}")


def checkout():
    """Handle checkout process."""
    global loyalty_points
    total = sum(menu[item] * quantity for item, quantity in order.items())
    
    # Apply loyalty points
    if loyalty_points > 0:
        use_points = messagebox.askyesno("Loyalty Points", f"You have {loyalty_points} points. Use them for a discount?")
        if use_points:
            discount = min(loyalty_points, total)
            total -= discount
            loyalty_points -= discount
            messagebox.showinfo("Discount Applied", f"Loyalty points redeemed: ₹{discount}")
    
    # Calculate new loyalty points
    new_points = total // 50
    loyalty_points += new_points
    
    # Display receipt
    receipt = f"--- Receipt ---\n"
    for item, quantity in order.items():
        receipt += f"{item}: {quantity} x ₹{menu[item]} = ₹{menu[item] * quantity}\n"
    receipt += f"\nTotal: ₹{total}\n"
    receipt += f"Loyalty points earned: {new_points}\n"
    receipt += f"Total loyalty points: {loyalty_points}\n"
    messagebox.showinfo("Receipt", receipt)
    
    # Reset order
    order.clear()
    update_order_summary()


# GUI Setup
root = tk.Tk()
root.title("Cafe Ordering System")
root.geometry("500x600")

# Menu Display
menu_label = tk.Label(root, text="Menu", font=("Arial", 18))
menu_label.pack()

menu_frame = tk.Frame(root)
menu_frame.pack()

for item, price in menu.items():
    btn = tk.Button(menu_frame, text=f"{item} - ₹{price}", font=("Arial", 14), 
                    command=lambda i=item: add_to_order(i))
    btn.pack(fill=tk.X, pady=5)

# Order Summary
order_label = tk.Label(root, text="Your Order", font=("Arial", 18))
order_label.pack()

order_summary = tk.Text(root, height=15, width=50, font=("Arial", 14))
order_summary.pack(pady=10)

# Checkout Button
checkout_button = tk.Button(root, text="Checkout", font=("Arial", 16), bg="green", fg="white", command=checkout)
checkout_button.pack(pady=20)

# Run the GUI
root.mainloop()
