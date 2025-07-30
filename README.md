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

EnrollmentService code :
package university

import grails.gorm.transactions.Transactional  // This enables transaction management

/**
 * Grails Service for managing student enrollments
 */
@Transactional
class EnrollmentService {

    /**
     * Saves a new enrollment if the student is not already enrolled in the course.
     * Throws an exception if a duplicate enrollment is attempted.
     */
    Enrollment saveEnrollment(Enrollment enrollment) {
        // Check for duplicate enrollment
        def existing = Enrollment.findByStudentAndCourse(enrollment.student, enrollment.course)
        if (existing) {
            throw new IllegalArgumentException("This student is already enrolled in this course.")
        }
        // Save the new enrollment
        return enrollment.save(flush: true)
    }

    // to return the obj of Enrollment based in the id
    // Serializable for long id , string is , UUID
    Enrollment get(Serializable id) {
        Enrollment.get(id)
    }

    // returns list of Enrollment based in filter like max ,min ....
    // map to know what the filter do
    //params.max = 10
    //params.sort = 'enrollmentDate'
    //params.order = 'desc'
    // enrollmentService.list(params)
    // fun will run like this Enrollment.list(max:10, sort:'enrollmentDate', order:'desc')
    List<Enrollment> list(Map args) {
        Enrollment.list(args)
    }

    Long count() {
        // from GORM return number of row (records) in the Enrollment
        Enrollment.count()
    }

    void delete(Serializable id) {
        Enrollment.get(id)?.delete(flush: true)
    }
}


GpaCalculatorService code :
package university

import grails.gorm.transactions.Transactional

@Transactional(readOnly = true) // there is no update just read the data
class GpaCalculatorService {


    BigDecimal calculateGpa(List<String> grades) {
        // check if there is a grad or not , if there is no garde make it 0
        if (!grades || grades.isEmpty()) return 0.0

        // this to convart the a,b,c ... to number from 0 to 4 by use the fun convertGradeToPoint
        def gradePoints = grades.collect { convertGradeToPoint(it) }
                // if there i null or garde not exist ignor it
                .findAll { it != null }

        if (gradePoints.isEmpty()) return 0.0

        // cal GPA
        //      sum of grades       number of grades   to make the number after . just 2
        //                          or the courses
        return (gradePoints.sum() / gradePoints.size()).setScale(2, BigDecimal.ROUND_HALF_UP)
    }

    private BigDecimal convertGradeToPoint(String grade) {
        switch(grade) {
            case 'A': return 4.0
            case 'B': return 3.0
            case 'C': return 2.0
            case 'D': return 1.0
            case 'F': return 0.0
            default: return null // Incomplete or unknown grade
        }
    }
}


4- add the inject in th eenrollmentserver by write this (GpaCalculatorService gpaCalculatorService) and add the fin from gpaCalculatorService tocal the GPA 
  BigDecimal calculateStudentGpa(Student student) {
        def grades = Enrollment.findAllByStudent(student)
                .collect { it.grade }
                .findAll { it != null }
        return gpaCalculatorService.calculateGpa(grades)
    }

5- then in studentcontroller i make inject for enrollmentservice by write (EnrollmentService enrollmentService)


