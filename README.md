# task_day_8_9
links:
http://localhost:8080/student/index
http://localhost:8080/course/index
http://localhost:8080/enrollment/index

what i do :
1- add grad to Enrollment.groovy
2- i create 2 services classes (EnrollmentService, GpaCalculatorService)
   by using these commands (grails create-service university.GpaCalculator , grails create-service university.EnrollmentService)
3- write the code for 2 services , GpaCalculator this to caluclate the avg of student , EnrollmentService this to avoid student to get the course 2 times 
   package university


4- add the inject in th eenrollmentserver by write this (GpaCalculatorService gpaCalculatorService) and add the fin from gpaCalculatorService tocal the GPA 
  BigDecimal calculateStudentGpa(Student student) {
        def grades = Enrollment.findAllByStudent(student)
                .collect { it.grade }
                .findAll { it != null }
        return gpaCalculatorService.calculateGpa(grades)
    }

5- then in studentcontroller i make inject for enrollmentservice by write (EnrollmentService enrollmentService)


