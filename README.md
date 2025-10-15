class Student:
    def __init__(self, name, surname, gender):
        self.name = name
        self.surname = surname
        self.gender = gender
        self.finished_courses = []
        self.courses_in_progress = []
        self.grades = {}

    def rate_lecturer(self, lecturer, course, grade):
        if isinstance(lecturer,
                      Lecturer) and course in self.courses_in_progress and course in lecturer.courses_attached:
            lecturer.grades.setdefault(course, []).append(grade)
        else:
            return 'Ошибка'

    def __str__(self):
        return (f'Имя: {self.name}\nФамилия: {self.surname}\n'
                f'Средняя оценка за домашнее задание: {self.average_grade():.1f}\n'
                f'Курсы в процессе изучения: {", ".join(self.courses_in_progress)}\n'
                f'Завершенные курсы: {", ".join(self.finished_courses)}')

    def average_grade(self):
        grades = [g for course in self.grades.values() for g in course]
        return sum(grades) / len(grades) if grades else 0

    def __lt__(self, other):
        if not isinstance(other, Student):
            print('Сравниваются объекты разных классов')
            return
        return self.average_grade() < other.average_grade()


class Mentor:
    def __init__(self, name, surname):
        self.name = name
        self.surname = surname
        self.courses_attached = []


class Lecturer(Mentor):
    def __init__(self, name, surname):
        super().__init__(name, surname)
        self.grades = {}

    def __str__(self):
        return (f'Имя: {self.name}\nФамилия: {self.surname}\n'
                f'Средняя оценка за лекции: {self.average_grade():.1f}')

    def average_grade(self):
        grades = [g for course in self.grades.values() for g in course]
        return sum(grades) / len(grades) if grades else 0

    def __lt__(self, other):
        if not isinstance(other, Lecturer):
            print('Сравниваются объекты разных классов')
            return
        return self.average_grade() < other.average_grade()


class Reviewer(Mentor):
    def rate_hw(self, student, course, grade):
        if isinstance(student, Student) and course in self.courses_attached and course in student.courses_in_progress:
            if course in student.grades:
                student.grades[course] += [grade]
            else:
                student.grades[course] = [grade]
        else:
            return 'Ошибка'

    def __str__(self):
        return f'Имя: {self.name}\nФамилия: {self.surname}'

#lecturer = Lecturer('Иван', 'Иванов')
#reviewer = Reviewer('Пётр', 'Петров')
#print(isinstance(lecturer, Mentor)) # True
#print(isinstance(reviewer, Mentor)) # True
#print(lecturer.courses_attached)    # []
#print(reviewer.courses_attached)    # []

#lecturer = Lecturer('Иван', 'Иванов')
#reviewer = Reviewer('Пётр', 'Петров')
#student = Student('Алёхина', 'Ольга', 'Ж')

#student.courses_in_progress += ['Python', 'Java']
#lecturer.courses_attached += ['Python', 'C++']
#reviewer.courses_attached += ['Python', 'C++']

#print(student.rate_lecturer(lecturer, 'Python', 7))  # None
#print(student.rate_lecturer(lecturer, 'Java', 8))  # Ошибка
#print(student.rate_lecturer(lecturer, 'С++', 8))  # Ошибка
#print(student.rate_lecturer(reviewer, 'Python', 6))  # Ошибка

#print(lecturer.grades)  # {'Python': [7]}

#Полевые испытания. Создаем по 2 экземпляра каждого класса

# Студенты
student1 = Student('Дмитрий', 'Шульга', 'м')
student1.courses_in_progress += ['Python']
student1.finished_courses += ['Git']

student2 = Student('Наталия', 'Столбовая', 'ж')
student2.courses_in_progress += ['Python']
student2.finished_courses += ['Git']

# Лекторы
lecturer1 = Lecturer('Алексей', 'Зайцев')
lecturer1.courses_attached += ['Python']

lecturer2 = Lecturer('Екатерина', 'Зайцева')
lecturer2.courses_attached += ['Python']

# Проверяющие
reviewer1 = Reviewer('Мурат', 'Ульбашев')
reviewer1.courses_attached += ['Python']

reviewer2 = Reviewer('Елена', 'Кузнецова')
reviewer2.courses_attached += ['Python']

#Выставлем оценки
# Проверяющие оценивают студентов
reviewer1.rate_hw(student1, 'Python', 9)
reviewer1.rate_hw(student2, 'Python', 8)
reviewer2.rate_hw(student1, 'Python', 10)
reviewer2.rate_hw(student2, 'Python', 7)

# Студенты оценивают лекторов
student1.rate_lecturer(lecturer1, 'Python', 9)
student2.rate_lecturer(lecturer1, 'Python', 8)
student2.rate_lecturer(lecturer2, 'Python', 10)

#Выводим результаты

print("\nИнформация о студентах:")
print(student1)
print()
print("\nИнформация о лекторах:")
print(lecturer1)
print()
print("\nИнформация о проверяющих:")
print(reviewer1)
print()

print(f'Сравнение студентов: {student1 < student2}')
print(f'Сравнение лекторов: {lecturer1 > lecturer2}')

#Подсчет средних оценок

def average_student_grade(students, course):
    total = grades = 0
    for s in students:
        if course in s.grades:
            total += sum(s.grades[course])
            grades += len(s.grades[course])
    return total / grades if grades else 0

def average_lecturer_grade(lecturers, course):
    total = grades = 0
    for l in lecturers:
        if course in l.grades:
            total += sum(l.grades[course])
            grades += len(l.grades[course])
    return total / grades if grades else 0

print(f'Средняя оценка студентов по курсу Python: {average_student_grade([student1, student2], "Python"):.2f}')
print(f'Средняя оценка лекторов по курсу Python: {average_lecturer_grade([lecturer1, lecturer2], "Python"):.2f}')