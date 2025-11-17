#include <iostream>
#include <fstream>
#include <cstdlib>   // for exit()
using namespace std;

class Habit {
protected:
    string name;
    int hours;

public:
    void input() {
        cout << "Enter habit name:";
        cin >> name;
        cout << "Enter hours:";
        cin >> hours;

        if(hours > 24){
            cout << "Invalid Input! Hours cannot be more than 24.\n";
            cout << "Program Terminated.\n";
            exit(0);   // <-- Ends the program immediately
        }
    }

    virtual int score() {
        return 0;
    }

    void show() {
        cout << "Habit: " << name << " | Hours: " << hours;
    }
};

class PhysicalHabit : public Habit {
public:
    int score() override {
        return hours * 10;
    }
};

class DigitalHabit : public Habit {
public:
    int score() override {
        return hours * 7;
    }
};

// File Handling Function
void saveToFile(string user, int score) {
    ofstream file("habit_data.txt", ios::app);
    file << "User: " << user << " | Score: " << score << endl;
    file.close();
}

int main() {
    string username;
    cout << "Enter your name: ";
    cin >> username;

    int choice;
    int totalScore = 0;

    do {
        cout << "\n1. Add Physical Habit";
        cout << "\n2. Add Digital Habit";
        cout << "\n3. Show Total Score";
        cout << "\n4. Save & Exit";
        cout << "\nEnter choice: ";
        cin >> choice;

        if (choice == 1) {
            PhysicalHabit ph;
            ph.input(); 
            totalScore += ph.score();
            ph.show();
            cout << " | Score: " << ph.score() << endl;
        }
        else if (choice == 2) {
            DigitalHabit dh;
            dh.input();
            totalScore += dh.score();
            dh.show();
            cout << " | Score: " << dh.score() << endl;
        }
        else if (choice == 3) {
            cout << "Total Score: " << totalScore << endl;
        }
        else if (choice == 4) {
            saveToFile(username, totalScore);
            cout << "Saved! Goodbye.\n";
        }
    } while (choice != 4);

    return 0;
}
