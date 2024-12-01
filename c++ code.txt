#include <iostream>
#include <fstream>
#include <vector>
#include <map>
#include <sstream>
#include <algorithm>

using namespace std;

struct Student {
    string rollNumber;
    string name;
    string year;
};

string getYear(int startYear) {
    int currentYear = 2024;
    int diff = currentYear - startYear;
    switch (diff) {
        case 0: return "First Year";
        case 1: return "Second Year";
        case 2: return "Pre-Final Year";
        case 3: return "Final Year";
        default: return "Unknown Year";
    }
}

string allotRoom(vector<vector<bool>>& roomAvailability, int yearPriority, int roomsPerFloor) {
    int totalFloors = roomAvailability.size();
    int startFloor = totalFloors - yearPriority;
    for (int floor = startFloor; floor >= 0; --floor) {
        for (int room = 0; room < roomsPerFloor; ++room) {
            if (!roomAvailability[floor][room]) {
                roomAvailability[floor][room] = true;
                return to_string(floor + 1) + to_string(room + 1).insert(0, 2 - to_string(room + 1).length(), '0');
            }
        }
    }
    return "No Room Available";
}

int main() {
    ifstream inputFile("sample1.txt");
    ofstream outputFile("output1.txt");

    if (!inputFile.is_open() || !outputFile.is_open()) {
        return 1;
    }

    vector<Student> students;
    string line;

    while (getline(inputFile, line)) {
        if (!line.empty()) {
            stringstream ss(line);
            Student student;
            ss >> student.rollNumber;
            ss.ignore();
            getline(ss, student.name);
            int startYear = stoi(student.rollNumber.substr(0, 2)) + 2000;
            student.year = getYear(startYear);
            students.push_back(student);
        }
    }
    inputFile.close();

    map<string, int> yearPriority = {
        {"Final Year", 1},
        {"Pre-Final Year", 2},
        {"Second Year", 3},
        {"First Year", 4}
    };

    sort(students.begin(), students.end(), [&](const Student& a, const Student& b) {
        return yearPriority[a.year] < yearPriority[b.year];
    });

    int totalFloors, roomsPerFloor;
    cin >> totalFloors >> roomsPerFloor;
    vector<vector<bool>> roomAvailability(totalFloors, vector<bool>(roomsPerFloor, false));

    for (const auto& student : students) {
        string room = allotRoom(roomAvailability, yearPriority[student.year], roomsPerFloor);
        outputFile << student.rollNumber << " " << student.name << " (" << student.year << ") - Room " << room << endl;
    }

    outputFile.close();
    return 0;
}
