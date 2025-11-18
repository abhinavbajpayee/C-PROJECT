#include <iostream>
#include <fstream>
#include <cstdlib>   
using namespace std;
class Habit {
protected:
    string name;
    int hours;
public:
    void input() {
    cin.ignore(); 
    cout << "Enter habit name: ";
    getline(cin, name);
    cout << "Enter hours: ";
    cin >> hours;
    if(hours > 24){
        cout << "Invalid Input! Hours cannot be more than 24.\n";
        cout << "Program Terminated.\n";
        exit(1);
    }}
virtual int score() {
        return 0;
    }
void show() {
        cout << "Habit:" << name << "|Hours:" << hours;
    }};

class PhysicalHabit : public Habit {
public:
    int score() override {
        return hours * 10;
    }};
class DigitalHabit : public Habit {
public:
    int score() override {
        return hours * 7;
    }};
void saveToFile(string user, int score) {
    ofstream file("habit_data.txt", ios::app);
    file << "User:" << user << "| Score:" << score << endl;
    file.close();
}
int main() {
    string username;
    cout << "Enter your name:";
    cin.ignore();            
    getline(cin, username);
    int choice;
    int totalScore = 0;
    do {
        cout << "1. Add Physical Habit\n";
        cout << "2. Add Digital Habit\n";
        cout << "3. Show Total Score\n";
        cout << "4. Save & Exit\n";
        cout << "Enter choice: ";
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
    } 
    while (choice != 4);
    return 0;
}
