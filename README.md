# Homework-4
 ## Домашнее задание к лекции «Объекты и классы. Инкапсуляция, наследование и полиморфизм»

 
class Mentor:
    def __init__(self, name, surname):
        self.name = name
        self.surname = surname
        self.courses_attached = []

    def __str__(self):
        return f'Имя: {self.name}\nФамилия: {self.surname}'

class Lecturer(Mentor):
    def __init__(self, name, surname):
        super().__init__(name, surname)
        self.grades = {}

    def receive_grade(self, course, grade):
        if course in self.courses_attached:
            self.grades.setdefault(course, []).append(grade)

    def __str__(self):
        average_grade = self._calculate_average_grade()
        return f'{super().__str__()}\nСредняя оценка за лекции: {average_grade:.2f}'

    def _calculate_average_grade(self):
        all_grades = [grade for grades_list in self.grades.values() for grade in grades_list]
        return sum(all_grades) / len(all_grades) if all_grades else 0

    def __lt__(self, other):
        if not isinstance(other, Lecturer):
            raise NotImplementedError('Cannot compare objects of different types')
        return self._calculate_average_grade() < other._calculate_average_grade()

class Reviewer(Mentor):
    def rate_hw(self, student, course, grade):
        if isinstance(student, Student) and course in self.courses_attached and course in student.courses_in_progress:
            student.grades.setdefault(course, []).append(grade)

    def __str__(self):
        return f'{super().__str__()}\nПреподаватель на курсах: {", ".join(self.courses_attached)}'

class Student:
    def __init__(self, name, surname, gender):
        self.name = name
        self.surname = surname
        self.gender = gender
        self.finished_courses = []
        self.courses_in_progress = []
        self.grades = {}

    def rate_lecturer(self, lecturer, course, grade):
        if isinstance(lecturer, Lecturer) and course in lecturer.courses_attached and course in self.courses_in_progress:
            lecturer.receive_grade(course, grade)

    def finish_course(self, course_name):
        if course_name in self.courses_in_progress:
            self.finished_courses.append(course_name)
            self.courses_in_progress.remove(course_name)
    
    def __str__(self):
        average_grade = self._calculate_average_grade()
        courses_in_progress = ', '.join(self.courses_in_progress)
        finished_courses = ', '.join(self.finished_courses)
        return (f'Имя: {self.name}\nФамилия: {self.surname}\n'
                f'Средняя оценка за домашние задания: {average_grade:.2f}\n'
                f'Курсы в процессе изучения: {courses_in_progress}\n'
                f'Завершенные курсы: {finished_courses}')

    def _calculate_average_grade(self):
        all_grades = [grade for grades_list in self.grades.values() for grade in grades_list]
        return sum(all_grades) / len(all_grades) if all_grades else 0

    def __lt__(self, other):
        if not isinstance(other, Student):
            raise NotImplementedError('Cannot compare objects of different types')
        return self._calculate_average_grade() < other._calculate_average_grade()

# Определения классов и функций остаются без изменений...

# Создание объектов и настройка курсов
student_1 = Student('Чебураш', 'Ушастов', 'M')
student_2 = Student('Геннадий', 'Зубастов', 'М')
student_3 = Student('Лариса', 'Крысова', 'F')

lecturer_1 = Lecturer('Олег', 'Вещий')
lecturer_2 = Lecturer('Хаттаб', 'Стариков')
lecturer_3 = Lecturer('Прекраса', 'Васильева')

reviewer_1 = Reviewer('Малюта', 'Скуратов')
reviewer_2 = Reviewer('Копец', 'Морозова')
reviewer_3 = Reviewer('Лаврентий', 'Палыч')

# Настройка курсов для студентов, преподавателей и рецензентов
students = [student_1, student_2, student_3]
lecturers = [lecturer_1, lecturer_2, lecturer_3]
reviewers = [reviewer_1, reviewer_2, reviewer_3]

for student in students:
    student.courses_in_progress += ['Python', 'ООП в Python']
    student.finished_courses += ['Введение в программирование', 'Git',   'GitHub']

for lecturer in lecturers:
    lecturer.courses_attached += ['Python']

for reviewer in reviewers:
    reviewer.courses_attached += ['Python', 'Git', 'GitHub']

# Оценки от рецензентов студентам
reviewer_1.rate_hw(student_1, 'Python', 10)
reviewer_2.rate_hw(student_2, 'Python', 9)
reviewer_3.rate_hw(student_3, 'Python', 9)

# Оценки от студентов лекторам
student_1.rate_lecturer(lecturer_1, 'Python', 9)
student_2.rate_lecturer(lecturer_2, 'Python', 10)
student_3.rate_lecturer(lecturer_3, 'Python', 8)

student_1.finish_course('Python')

# Вывод информации о студентах
print("Студенты:")
for student in students:
    print(student)
    print()

# Вывод информации о лекторах
print("Лекторы:")
for lecturer in lecturers:
    print(lecturer)
    print()

# Вывод информации о рецензентах
print("Рецензенты:")
for reviewer in reviewers:
    print(reviewer)
    print()

