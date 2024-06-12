#include <iostream>
#include <fstream>
#include <cstring>
#include <ctime>


using namespace std;
struct Donor
{
    char name[50];
    char address[100];
    char mobileNumber[20];
    char bloodGroup[5];
    char lastDonationDate[20];
};


struct Node
{
    Donor donor;
    Node* next;
};


void displayMenu();
void addDonor(Node*& head);
void searchDonor(Node* head);
void displayDonorDatabase(Node* head);
void updateDonor(Node* head);
void deleteDonor(Node*& head);
void recentDonors(Node* head);
void sortDonors(Node*& head);
bool compareDates(const char* date1, const char* date2);
time_t parseDate(const char* date);

int main()
{

    int choice;
    Node* head = nullptr;
    while (true)
    {
        displayMenu();
        cout << "Enter your choice: ";
        cin >> choice;
        switch (choice)
        {
        case 1:
            addDonor(head);
            break;
        case 2:
            searchDonor(head);
            break;
        case 3:
            displayDonorDatabase(head);
            break;
        case 4:
            updateDonor(head);
            break;
        case 5:
            deleteDonor(head);
            break;
        case 6:
            recentDonors(head);
            break;
        case 7:
            sortDonors(head);
            break;
        case 8:
            cout << "Thank you for using the blood donation system. Goodbye!\n";
            return 0;
        default:
            cout << "Invalid choice. Please try again.\n";
        }
    }

    return 0;

}

void displayMenu()

{

    cout << "\nBlood Donation System\n";
    cout << "1. Add Donor\n";
    cout << "2. Search Donor\n";
    cout << "3. Display Donor Database\n";
    cout << "4. Update Donor\n";
    cout << "5. Delete Donor\n";
    cout << "6. Recent Donors\n";
    cout << "7. Sort Donors\n";
    cout << "8. Exit\n";
}

void addDonor(Node*& head)
{

    Donor donor;
    cout << "\nEnter Donor Details:\n";
    cout << "Name: ";
    cin >> donor.name;
    cout << "Address: ";
    cin.ignore();
    cin.getline(donor.address, 100);
    cout << "Mobile Number: ";
    cin >> donor.mobileNumber;
    cout << "Blood Group: ";
    cin >> donor.bloodGroup;
    cout << "Last Donation Date (YYYY-MM-DD): ";
    cin >> donor.lastDonationDate;
    ofstream file("donor_database.txt", ios::app);
    if (!file)
    {
        cout << "Error opening the donor database file.\n";
        return;

    }

    file << donor.name << ";" << donor.address << ";" << donor.mobileNumber << ";" << donor.bloodGroup << ";" << donor.lastDonationDate << endl;
    file.close();

    Node* newNode = new Node{donor, head};

    head = newNode;

    cout << "Donor added successfully!\n";
}

void searchDonor(Node* head)
{
    char bloodGroup[5];
    cout << "\nEnter the blood group to search: ";
    cin >> bloodGroup;
    Node* current = head;

    bool found = false;
    while (current != nullptr)
    {
        if (strcmp(current->donor.bloodGroup, bloodGroup) == 0)
        {
            cout << "\nDonor found:\n";
            cout << "Name: " << current->donor.name << endl;
            cout << "Address: " << current->donor.address << endl;
            cout << "Mobile Number: " << current->donor.mobileNumber << endl;
            cout << "Blood Group: " << current->donor.bloodGroup << endl;
            cout << "Last Donation Date: " << current->donor.lastDonationDate << endl;
            found = true;
        }

        current = current->next;
    }

    if (!found)
    {
        cout << "No donor found with the specified blood group.\n";

    }
}


void displayDonorDatabase(Node* head)
{

    Node* current = head;

    cout << "\nDonor Database:\n";
    while (current != nullptr)
    {
        cout << "\nName: " << current->donor.name << endl;
        cout << "Address: " << current->donor.address << endl;
        cout << "Mobile Number: " << current->donor.mobileNumber << endl;
        cout << "Blood Group: " << current->donor.bloodGroup << endl;
        cout << "Last Donation Date: " << current->donor.lastDonationDate << endl;
        current = current->next;

    }
}

void updateDonor(Node* head)
{

    char name[50];
    cout << "\nEnter the name of the donor to update: ";
    cin >> name;
    Node* current = head;
    bool found = false;
    while (current != nullptr)
    {
        if (strcmp(current->donor.name, name) == 0)
        {
            found = true;
            cout << "\nEnter new details for " << name << ":\n";
            cout << "Address: ";
            cin.ignore();
            cin.getline(current->donor.address, 100);
            cout << "Mobile Number: ";
            cin >> current->donor.mobileNumber;
            cout << "Blood Group: ";
            cin >> current->donor.bloodGroup;
            cout << "Last Donation Date: ";
            cin >> current->donor.lastDonationDate;
            break;

        }

        current = current->next;
    }

    if (found)
    {
        ofstream file("donor_database.txt");
        if (!file)
        {
            cout << "Error opening the donor database file.\n";
            return;
        }
        current = head;

        while (current != nullptr)
        {

            file << current->donor.name << ";" << current->donor.address << ";" << current->donor.mobileNumber << ";" << current->donor.bloodGroup << ";" << current->donor.lastDonationDate << endl;
            current = current->next;
        }

        file.close();

        cout << "Donor information updated successfully.\n";
    }

    else
    {
        cout << "Donor not found.\n";
    }
}

void deleteDonor(Node*& head)
{

    char name[50];
    cout << "\nEnter the name of the donor to delete: ";
    cin >> name;

    Node* current = head;

    Node* prev = nullptr;

    bool found = false;

    while (current != nullptr)
    {
        if (strcmp(current->donor.name, name) == 0)
        {
            if (prev == nullptr)
            {
                head = current->next;
            }
            else
            {
                prev->next = current->next;
            }
            delete current;
            found = true;
            break;

        }
        prev = current;

        current = current->next;
    }
    if (found)
    {

        ofstream file("donor_database.txt");

        if (!file)
        {
            cout << "Error opening the donor database file.\n";
            return;
        }

        current = head;

        while (current != nullptr)
        {
            file << current->donor.name << ";" << current->donor.address << ";" << current->donor.mobileNumber << ";" << current->donor.bloodGroup << ";" << current->donor.lastDonationDate << endl;
            current = current->next;
        }
        file.close();
        cout << "Donor deleted successfully.\n";
    }

    else
    {
        cout << "Donor not found.\n";
    }
}

bool compareDates(const char* date1, const char* date2)
{
    return parseDate(date1) <= parseDate(date2);
}

time_t parseDate(const char* date)
{
    struct tm tm = {0};
    sscanf(date, "%d-%d-%d", &tm.tm_year, &tm.tm_mon, &tm.tm_mday);
    tm.tm_year -= 1900; // Years since 1900
    tm.tm_mon -= 1;     // Months since January
    return mktime(&tm);
}

void recentDonors(Node* head)

{
    time_t t = time(nullptr);
    tm* now = localtime(&t);
    char currentDate[20];
    strftime(currentDate, sizeof(currentDate), "%Y-%m-%d", now);
    Node* current = head;
    bool found = false;
    cout << "\nRecent Donors (within the last 30 days):\n";


    while (current != nullptr)

    {
        time_t donationDate = parseDate(current->donor.lastDonationDate);
        double diffDays = difftime(t, donationDate) / (60 * 60 * 24);
        if (diffDays <= 30)
        {
            cout << "\nName: " << current->donor.name << endl;
            cout << "Address: " << current->donor.address << endl;
            cout << "Mobile Number: " << current->donor.mobileNumber << endl;
            cout << "Blood Group: " << current->donor.bloodGroup << endl;
            cout << "Last Donation Date: " << current->donor.lastDonationDate << endl;
            found = true;
        }
        current = current->next;
    }
    if (!found)
    {
        cout << "No recent donors found.\n";
    }
}
void sortDonors(Node*& head)
{
    if (head == nullptr || head->next == nullptr)
    {
        return;
    }
    Node* sorted = nullptr;
    Node* current = head;
    while (current != nullptr)
    {
        Node* next = current->next;
        if (sorted == nullptr || strcmp(current->donor.name, sorted->donor.name) <= 0)
        {
            current->next = sorted;
            sorted = current;
        }
        else
        {
            Node* temp = sorted;
            while (temp->next != nullptr && strcmp(current->donor.name, temp->next->donor.name) > 0)
            {
                temp = temp->next;
            }
            current->next = temp->next;
            temp->next = current;
        }
        current = next;
    }
    head = sorted;
    cout << "Donors sorted by name.\n";
}


