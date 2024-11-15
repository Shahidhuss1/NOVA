import json
import uuid
from termcolor import colored


class AutoPartInventory:
    def __init__(self, company_name='NOVASTAR Automotive LLC', filename='auto_parts_inventory.json'):
        self.company_name = company_name
        self.filename = filename
        self.inventory = self.load_inventory()

    def load_inventory(self):
        """Load inventory data from a file."""
        try:
            with open(self.filename, 'r') as file:
                return json.load(file)
        except (FileNotFoundError, json.JSONDecodeError):
            return {}

    def save_inventory(self):
        """Save inventory data to a file."""
        with open(self.filename, 'w') as file:
            json.dump(self.inventory, file, indent=4)

    def add_part(self, part_name, quantity, price, supplier_name, location):
        """Add a new auto part to the inventory."""
        part_id = str(uuid.uuid4())[:8]  # Generate a short unique ID for the part
        self.inventory[part_id] = {
            'part_name': part_name,
            'quantity': quantity,
            'price': price,
            'supplier_name': supplier_name,
            'location': location
        }
        self.save_inventory()
        print(colored(f"\n✔️ {self.company_name} - Part '{part_name}' added successfully with ID {part_id}.", 'green'))

    def update_part(self, part_id, quantity=None, price=None, supplier_name=None, location=None):
        """Update an existing part's information."""
        if part_id in self.inventory:
            if quantity is not None:
                self.inventory[part_id]['quantity'] = quantity
            if price is not None:
                self.inventory[part_id]['price'] = price
            if supplier_name is not None:
                self.inventory[part_id]['supplier_name'] = supplier_name
            if location is not None:
                self.inventory[part_id]['location'] = location
            self.save_inventory()
            print(colored(f"\n🔄 {self.company_name} - Part with ID {part_id} updated successfully.", 'yellow'))
        else:
            print(colored(f"\n❌ {self.company_name} - Part with ID {part_id} not found.", 'red'))

    def delete_part(self, part_id):
        """Delete a part from the inventory."""
        if part_id in self.inventory:
            del self.inventory[part_id]
            self.save_inventory()
            print(colored(f"\n🗑️ {self.company_name} - Part with ID {part_id} deleted successfully.", 'blue'))
        else:
            print(colored(f"\n❌ {self.company_name} - Part with ID {part_id} not found.", 'red'))

    def view_inventory(self):
        """View all auto parts in inventory."""
        if not self.inventory:
            print(colored(f"\n{self.company_name} - Inventory is currently empty.", 'red'))
        else:
            print(colored(f"\n🗂️ {self.company_name} - Auto Parts Inventory", 'cyan'))
            print("╒════════════╤════════════════════╤══════════╤══════════╤════════════════════╤════════════════════╕")
            print(f"{'Part ID':<12}{'Part Name':<20}{'Quantity':<10}{'Price':<10}{'Supplier Name':<20}{'Location':<20}")
            print("╘════════════╧════════════════════╧══════════╧══════════╧════════════════════╧════════════════════╛")
            for part_id, details in self.inventory.items():
                print(f"{part_id:<12}{details['part_name']:<20}{details['quantity']:<10}{details['price']:<10}{details['supplier_name']:<20}{details['location']:<20}")

    def search_part(self, part_id):
        """Search for a part by its ID."""
        if part_id in self.inventory:
            details = self.inventory[part_id]
            print(colored(f"\n🔍 {self.company_name} - Part Found:", 'green'))
            print("╒════════════╤════════════════════╤══════════╤══════════╤════════════════════╤════════════════════╕")
            print(f"{'Part ID':<12}{'Part Name':<20}{'Quantity':<10}{'Price':<10}{'Supplier Name':<20}{'Location':<20}")
            print("╘════════════╧════════════════════╧══════════╧══════════╧════════════════════╧════════════════════╛")
            print(f"{part_id:<12}{details['part_name']:<20}{details['quantity']:<10}{details['price']:<10}{details['supplier_name']:<20}{details['location']:<20}")
        else:
            print(colored(f"\n❌ {self.company_name} - Part with ID {part_id} not found.", 'red'))


def display_menu():
    """Display the menu options."""
    print("\n" + "═" * 50)
    print(colored(f"{' ' * 10}{'Welcome to NOVASTAR Automotive LLC'}", 'cyan'))
    print(colored(f"{' ' * 10}{'Auto Part Inventory Management System'}", 'magenta'))
    print("═" * 50)
    print("1. Add New Part")
    print("2. Update Part Information")
    print("3. Delete Part")
    print("4. View All Parts")
    print("5. Search Part by ID")
    print("6. Exit")
    print("═" * 50)


def main():
    inventory = AutoPartInventory()

    while True:
        display_menu()
        choice = input("\nEnter your choice (1-6): ")

        if choice == '1':
            part_name = input("\nEnter part name: ")
            quantity = int(input("Enter quantity: "))
            price = float(input("Enter price per unit: $"))
            supplier_name = input("Enter supplier name: ")
            location = input("Enter location (e.g., 'Aisle 1, Shelf B'): ")
            inventory.add_part(part_name, quantity, price, supplier_name, location)

        elif choice == '2':
            part_id = input("\nEnter part ID to update: ")
            print("\nLeave fields blank to skip update.")
            quantity = input("Enter new quantity (leave blank to skip): ")
            price = input("Enter new price (leave blank to skip): ")
            supplier_name = input("Enter new supplier name (leave blank to skip): ")
            location = input("Enter new location (leave blank to skip): ")

            quantity = int(quantity) if quantity else None
            price = float(price) if price else None
            supplier_name = supplier_name if supplier_name else None
            location = location if location else None

            inventory.update_part(part_id, quantity, price, supplier_name, location)

        elif choice == '3':
            part_id = input("\nEnter part ID to delete: ")
            inventory.delete_part(part_id)

        elif choice == '4':
            inventory.view_inventory()

        elif choice == '5':
            part_id = input("\nEnter part ID to search: ")
            inventory.search_part(part_id)

        elif choice == '6':
            print(colored("\nExiting NOVASTAR Automotive LLC - Auto Part Inventory Management System.", 'blue'))
            break

        else:
            print(colored("\n❌ Invalid choice. Please enter a valid number between 1 and 6.", 'red'))


if __name__ == "__main__":
    main()
