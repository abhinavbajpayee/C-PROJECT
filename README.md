#include <iostream>
#include <fstream>
#include <string>
#include <vector>
#include <ctime>
#include <cstdlib>  // for rand()
using namespace std;

// 🔹 Forward declaration
class Analytics;

// =======================================================
// CLASS: Habit (Base Class)
// =======================================================
class Habit {
protected:
    string name;
    int hours;
public:
    Habit(string n = "Unknown", int h = 0) : name(n), hours(h) {}
    virtual void input() {
        cout << "Enter habit name: ";
        cin >> name;
        cout << "Enter hours spent today: ";
        cin >> hours;
    }
    virtual void display() const {
        cout << "Habit: " << name << " | Hours: " << hours << endl;
    }
    virtual int score() const = 0; // 🔹 Pure virtual (for polymorphism)
    friend class Analytics;
};

// =======================================================
// DERIVED CLASS 1: PhysicalHabit
// =======================================================
class PhysicalHabit : public Habit {
public:
    PhysicalHabit(string n = "Workout", int h = 0) : Habit(n, h) {}
    int score() const override {
        return hours * 10; // more hours = better score
    }
};

// =======================================================
// DERIVED CLASS 2: DigitalHabit
// =======================================================
class DigitalHabit : public Habit {
public:
    DigitalHabit(string n = "Coding", int h = 0) : Habit(n, h) {}
    int score() const override {
        return hours * 7; // slightly lesser score weight
    }
};

// =======================================================
// CLASS: User
// =======================================================
class User {
    string username;
    vector<Habit*> habits;
    static int userCount; // 🔹 Static member

public:
    User(string name = "Guest") : username(name) {
        userCount++;
    }

    void addHabit(Habit* h) {
        habits.push_back(h);
    }

    void showHabits() const {
        cout << "\n📊 Habits for " << username << ":\n";
        for (auto h : habits) {
            h->display();
            cout << "Score: " << h->score() << "\n";
        }
    }

    int totalScore() const {
        int total = 0;
        for (auto h : habits)
            total += h->score();
        return total;
    }

    // 🔹 Operator Overloading: Compare users by consistency
    bool operator>(const User &other) const {
        return this->totalScore() > other.totalScore();
    }

    static void showUserCount() {
        cout << "\n👥 Total Users: " << userCount << endl;
    }

    string getName() const { return username; }

    friend class Analytics;
};
int User::userCount = 0; // Definition of static member

// =======================================================
// CLASS: Analytics (Friend Class)
// =======================================================
class Analytics {
public:
    static void showTrends(const User &u) {
        cout << "\n📈 Habit Progress for " << u.username << ":\n";
        int total = u.totalScore();
        int bars = total / 10;
        cout << "Progress: ";
        for (int i = 0; i < bars; i++) cout << "█";
        cout << " (" << total << " points)\n";

        // AI-like feedback
        if (total > 150)
            cout << "🔥 Excellent consistency! Keep it up!\n";
        else if (total > 80)
            cout << "💪 Good progress! Stay steady!\n";
        else
            cout << "🌱 Small steps matter. Try to build a routine!\n";
    }
};

// =======================================================
// FILE HANDLING: Save user progress
// =======================================================
void saveToFile(const User &u) {
    ofstream file("habit_data.txt", ios::app);
    if (!file) {
        cerr << "Error opening file!\n";
        return;
    }
    file << "User: " << u.getName() << " | Score: " << u.totalScore() << endl;
    file.close();
}

// =======================================================
// RANDOM QUOTES GENERATOR
// =======================================================
string getMotivationalQuote() {
    string quotes[] = {
        "Discipline is stronger than motivation.",
        "Little progress each day adds up to big results.",
        "The secret of success is consistency.",
        "Dream big. Work hard. Stay humble.",
        "Your future depends on what you do today."
    };
    int idx = rand() % 5;
    return quotes[idx];
}

// =======================================================
// MAIN FUNCTION
// =======================================================
int main() {
    srand(time(0)); // for random quotes
    cout << "==============================\n";
    cout << " 🧠 AI-Based Habit Tracker\n";
    cout << "==============================\n";

    string username;
    cout << "\nEnter your name: ";
    cin >> username;

    User user1(username);
    int choice;

    do {
        cout << "\n1. Add Physical Habit";
        cout << "\n2. Add Digital Habit";
        cout << "\n3. Show All Habits";
        cout << "\n4. Analyze Progress";
        cout << "\n5. Save to File";
        cout << "\n6. Exit";
        cout << "\nEnter choice: ";
        cin >> choice;

        switch (choice) {
        case 1: {
            PhysicalHabit* ph = new PhysicalHabit();
            ph->input();
            user1.addHabit(ph);
            break;
        }
        case 2: {
            DigitalHabit* dh = new DigitalHabit();
            dh->input();
            user1.addHabit(dh);
            break;
        }
        case 3:
            user1.showHabits();
            break;
        case 4:
            Analytics::showTrends(user1);
            cout << "\n🤖 Quote: " << getMotivationalQuote() << endl;
            break;
        case 5:
            saveToFile(user1);
            cout << "✅ Data saved successfully!\n";
            break;
        case 6:
            cout << "Exiting tracker... Goodbye!\n";
            break;
        default:
            cout << "Invalid choice!\n";
        }
    } while (choice != 6);

    User::showUserCount();

    return 0;
}
