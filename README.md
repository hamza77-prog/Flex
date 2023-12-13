#pragma once
#include <iostream>
#include<vector>
using namespace std;

class Course
{
public:
    string code;
    string name;
    string instructor;
    int credits;
    int capacity;
    vector<string> students;

    void Display()
    {
        cout << code << "\t" << name << "\t" << instructor << "\t" << credits << "\t" << capacity << endl;
        for (int i = 0; i < students.size(); i++)
            cout << students[i] << " ";
        cout << endl;
    }

    Course(){}


    Course(string c, string n, string ins, int crd, int cap, vector<string>stud)
    {
        code = c;
        name = n;
        instructor = ins;
        credits = crd;
        capacity = cap;
        students = stud;
    }

    Course& operator = (const Course& S1)
    {
        code = S1.code;
        name = S1.name;
        instructor = S1.instructor;
        credits = S1.credits;
        capacity = S1.capacity;
        students = S1.students;
    }
    bool operator == (const Course& S1) const
    {
        if (code == S1.code &&
            name == S1.name &&
            instructor == S1.instructor &&
            credits == S1.credits)
            return true;
        return false;
    }


  
};

//---------------- New File Student.h
#pragma once
#include <iostream>
#include <vector>
#include "Course.h"
using namespace std;

class Student
{
public:
	string name;
	string roll_num;
	int age;
	string contact;
	
	vector<tuple <string,string, vector<char>, int>> courses;  //vector in the format [course, instructor, attendance[]]

	Student()
	{
		name = "";
		roll_num = "";
		age = -1;
		contact = "";
	}

	Student(string n, string roll, int a, string c, vector<tuple<string, string, vector<char>, int>> v1)
	{
		name = n;
		age = a;
		roll_num = roll;
		contact = c;
		courses = v1;
	}


	

	void Display()
	{
		cout << endl;
		cout << name << "\t" << roll_num << "\t" << age << "\t" << contact << endl;
		cout << "Courses You have are: " << endl;
		for (int i = 0; i < courses.size(); ++i) {
			cout << "Course is: " << get<0>(courses[i]) << "\tInstructor: " << get<1>(courses[i]) << "\t Marks: "<< get<3>(courses[i]) << endl;
			cout << "Attendence is: " << endl;
			for (int j = 0; j < get<2>(courses[i]).size(); j++)
			{
				cout << get<2>(courses[i])[j]<<" ";
			}

		}
		cout << endl;
	}

	bool isNull()
	{
		if (name == "" && roll_num == "" && age == -1 && contact == "")
			return true;
		return false;
	}


	
};
//----------------------New File
#pragma once
#include "Student.h"
#include "Course.h"
#include "Validator.h"


class System: public Student, public Course, public Validator
{
	Student s1;
	Course c1;
public:

	System(){}
	void MainMenu()
	{
		system("cls");
		cout << "\t\t FLEX" << endl;
		cout << "Enter a respective Number to perform certain Task:" << endl;
		int c;
		cout << "1- Enroll a student\n2 - Course Registration\n3 - Attendance\n4 - Marks\n5 - Course Withdraw\n6 - Exit" << endl;
		cin >> c;
		if (c == 1)
		{
			EnrollStud();
		}
		else if (c == 2)
		{
			CrsReg();
		}
		else if (c == 3)
		{
			Login();
			s1.Display();
		}
		else if (c == 4)
		{
			Login();
			s1.Display();
		}
		else if (c == 5)
		{
			CrsWithDraw();
		}
		else
		{
			MainMenu();
		}

	}



	void EnrollStud()
	{
		Student d1;
		string pass;
		cout << "Name: ";
		cin >> d1.name;
		cout << "Roll Number: ";
		cin >> d1.roll_num;
		cout << "Contact: ";
		cin >> d1.contact;
		cout << "Enter Age:";
		cin>>d1.age;
		
		cout << "Enter Password: ";
		cin >> pass;
		WriteCred(d1.roll_num, pass);
		FileHandler::WriteStudentToFile(d1);
	}

	void CrsWithDraw()
	{
		system("cls");
		Login();
		cout << endl;
		s1.Display();
		cout << endl;
		vector<Course> crs = FileHandler::ReadCoursesFromFile("CrsData.txt");
		
		cout << "PRESS -1 to end" << endl;
		
		int k;
		cin >> k;
		if (k == -1)
			return;
		if (k < 0 && k >= crs.size())
			CrsReg();
		else
		{
			s1.courses.erase(s1.courses.begin()+k);
			FileHandler::WriteStudentToFile(s1);
		}
		cout << "Do You want to Exit? Press -1";
		int z;
		cin >> z;
		if (z == -1)
			return;
		else
			CrsReg();

	}





	void CrsReg()
	{
		system("cls");
		Login();
		cout << endl;
		s1.Display();
		cout << endl;
		vector<Course> crs = FileHandler::ReadCoursesFromFile("CrsData.txt");
		vector<Course> available;
		cout << "PRESS -1 to end" << endl;
		int index = -1;
		for (int i = 0; i < crs.size(); i++)
		{
			bool flag = 0;
			for (int j = 0; j < s1.courses.size(); j++)
			{
				if (crs[i].name == get<0>(s1.courses[j]) && crs[i].instructor == get<1>(courses[j]))
				{
					flag = 1;
					index = i;
				}
			}
			if (!flag && crs[i].capacity < 50)
				available.push_back(crs[i]);
			
		}

		cout << endl << endl;
		cout << "You can Register in Following Courses: " << endl;
		for (int i = 0; i < available.size(); i++)
			available[i].Display();
		int k;
		cin >> k;
		if (k == -1)
			return;
		if (k < 0 && k >= available.size())
			CrsReg();
		else
		{
			s1.courses.push_back(
				{ available[k].name, available[k].instructor, vector<char>(), 0 }
			);

			FileHandler::WriteStudentToFile(s1);
		}


		//to find address of available index in crs
		bool flag = 0;
		
		
		if (index != -1)
		{
			crs[index].students.push_back(s1.name);
			crs[index].capacity++;
		}

		FileHandler::WriteCoursesToFile("CrsData.txt", crs);
			
		cout << "Do You want to Exit? Press -1";
		int z;
		cin >> z;
		if (z == -1)
			return;
		else
			CrsReg();

	}
	


	void Login()
	{
		system("cls");
		cout << "Login" << endl;
		cout << "roll: " << endl;
		string roll;
		cin >> roll;
		cout << "Enter Password: " << endl;
		string p;
		cin >> p;
		if (Validator::Login(roll, p))
		{
			s1 = FileHandler::ReadStudentFromFile(roll);
			if (s1.isNull())
			{
				cout << "Not Found " << endl;
				system("cls");
				MainMenu();
			}
			else {
				system("cls");
				cout << "Hello" << endl;
				s1.Display();


			}
		}
	}

};
//-------------------
#pragma once
#include <iostream>
#include "FileHandler.h"
using namespace std;


class Validator: public FileHandler
{
public:
	bool Login(string roll, string Pass)
	{
		

		bool c = (FileHandler::Login(roll, Pass));
		cout << c;
		return c;
	}
};
//------------------- new file 
#pragma once

#include <iostream>
#include <fstream>
#include <sstream>
#include <string>
#include <fstream>
#include <vector>
#include <tuple>
using namespace std;

class FileHandler
{

public:
	FileHandler(){}

    bool Login(const string& roll, const string& password) {
        ifstream file("Cred.txt");

        if (!file.is_open()) {
            cerr << "Error opening file." << endl;
            return false;
        }

        string line;
        while (getline(file, line)) {
            istringstream iss(line);
            string storedRoll, storedPassword;

            if (getline(iss, storedRoll, ',') && getline(iss, storedPassword)) {
                if (storedRoll == roll && storedPassword == password) {
                    file.close();
                    return true;  // Login successful
                }
            }
        }

        file.close();
        return false;  // Login failed
    }

    void WriteCred(const string& r, const string& pass) {
        ofstream outFile("cred.txt", ios::app);

        if (outFile.is_open()) {
            outFile << r << "," << pass << endl;
            outFile.close();
            cout << "Data appended to cred.txt successfully." << endl;
        }
        else {
            cerr << "Error opening cred.txt for appending." << endl;
        }
    }

    void ReadFile(const string& filename) {
        ifstream inFile(filename);

        if (inFile.is_open()) {
            string line;
            while (getline(inFile, line)) {
                cout << line << endl;
            }

            inFile.close();
        }
        else {
            cerr << "Error opening " << filename << " for reading." << endl;
        }
    }


    void WriteStudentToFile(const Student& student) {
        // Create a filename based on the roll number
        string filename = student.roll_num + ".txt";

        // Open the file for writing
        ofstream file(filename);

        if (file.is_open()) {
            // Write student information to the file
            file << "Name: " << student.name << "\n";
            file << "Roll Number: " << student.roll_num << "\n";
            file << "Age: " << student.age << "\n";
            file << "Contact: " << student.contact << "\n";

            // Write course details
            file << "Courses:\n";
            for (const auto& course : student.courses) {
                file << "Course: " << get<0>(course) << "\tInstructor: " << get<1>(course)
                    << "\tMarks: " << get<3>(course) << "\n";
                file << "Attendance: ";
                for (char attendance : get<2>(course)) {
                    file << attendance << " ";
                }
                file << "\n\n";
            }

            cout << "Student data written to file: " << filename << endl;
            file.close();


        }
        else {
            cerr << "Error opening file for writing: " << filename << endl;
        }
    }

    Student ReadStudentFromFile(const string& rollNumber) {
        // Create a filename based on the roll number
        string filename = rollNumber + ".txt";

        // Open the file for reading
        ifstream file(filename);

        if (!file.is_open()) {
            cerr << "Error opening file: " << filename << endl;
            // Return a default-constructed Student object if the file doesn't exist
            return Student();
        }

        string line;
        Student student;

        while (getline(file, line)) {
            // Assuming the file format is as written by WriteStudentToFile
            if (line.find("Name: ") != string::npos) {
                // Extract name from the line
                student.name = line.substr(6);
            }
            else if (line.find("Roll Number: ") != string::npos) {
                // Extract roll number from the line
                student.roll_num = line.substr(13);
            }
            else if (line.find("Age: ") != string::npos) {
                // Extract age from the line
                student.age = stoi(line.substr(5));
            }
            else if (line.find("Contact: ") != string::npos) {
                // Extract contact from the line
                student.contact = line.substr(9);
            }
            else if (line.find("Course: ") != string::npos) {
                // Extract course details
                string course, instructor;
                vector<char> attendance;
                int marks;
                file >> course >> instructor >> marks;
                tuple<string, string, vector<char>, int> courseTuple = make_tuple(course, instructor, attendance, marks);
                student.courses.push_back(courseTuple);
                // Consume the newline character
                getline(file, line);
            }
            else if (line.find("Attendance: ") != string::npos) {
                // Extract attendance from the line
                string attendanceStr;
                getline(file, attendanceStr);
                istringstream iss(attendanceStr);
                char attend;
                while (iss >> attend) {
                    get<2>(student.courses.back()).push_back(attend);
                }
            }
        }

        file.close();
        return student;
    }


    static vector<Course> ReadCoursesFromFile(const string& filename) {
        vector<Course> courses;

        ifstream file(filename);
        if (!file.is_open()) {
            cerr << "Error opening file: " << filename << endl;
            return courses;
        }
        int i = 0;
        string line;
        while (getline(file, line)) {
            istringstream iss(line);
            string code, name, instructor;
            int credits, capacity;
            vector<string> students;

            if (iss >> code >> name >> instructor >> credits >> capacity) {
                string student;
                while (iss >> student) {
                    students.push_back(student);
                }

                courses.emplace_back(code, name, instructor, credits, capacity, students);
                courses[i].Display();
                cout << endl;
                i++;


            }
        }

        file.close();
        return courses;
    }

    void WriteCoursesToFile(const string& filename, const vector<Course>& courses) {
        ofstream file(filename);
        if (!file.is_open()) {
            cerr << "Error opening file for writing: " << filename << endl;
            return;
        }

        for (const auto& course : courses) {
            file << course.code << " " << course.name << " " << course.instructor << " "
                << course.credits << " " << course.capacity;

            for (const auto& student : course.students) {
                file << " " << student;
            }

            file << endl;
        }

        file.close();
        cout << "Courses written to file: " << filename << endl;
    }
};

//------------------------------- int main
#include "System.h"


int main()
{
   
	System s1;
	s1.MainMenu();
}
