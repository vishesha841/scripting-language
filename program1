// Dummy data for available seats
let courses = {
    "CS101": { max_capacity: 25, current_enrollment: 20 },
    "CS102": { max_capacity: 30, current_enrollment: 30 }
};

// Function to handle course registration
function registerForCourse(courseId) {
    let course = courses[courseId];
    if (course.current_enrollment < course.max_capacity) {
        course.current_enrollment++;
        document.getElementById(`availability-${courseId}`).innerText = course.max_capacity - course.current_enrollment;
        alert("You have successfully registered for the course!");

        // Disable the register button if the course is now full
        if (course.current_enrollment === course.max_capacity) {
            document.querySelector(`.course[data-course-id="${courseId}"] .register-button`).disabled = true;
            document.querySelector(`.course[data-course-id="${courseId}"] .register-button`).innerText = "Course Full";
        }

        // Here, you would also send this update to the server/database via an AJAX request or fetch API
    } else {
        alert("Sorry, this course is full.");
    }
}

// Example validation for course registration form (if needed)
function validateRegistrationForm() {
    let studentId = document.getElementById("studentId").value;
    let courseId = document.getElementById("courseId").value;

    if (studentId.trim() === "" || courseId.trim() === "") {
        alert("Both student ID and course ID are required.");
        return false;
    }

    // Additional validation logic here, like checking prerequisites

    return true;
}
app.post('/register', async (req, res) => {
    const { studentId, courseId } = req.body;

    // Check if the student has met the prerequisites
    const prerequisites = await getPrerequisites(courseId);
    for (let prerequisite of prerequisites) {
        const isCompleted = await hasStudentCompletedCourse(studentId, prerequisite.course_id);
        if (!isCompleted) {
            return res.status(400).json({ error: 'You have not met the prerequisites for this course.' });
        }
    }

    // Check if the course is full
    const course = await getCourseDetails(courseId);
    if (course.current_enrollment >= course.max_capacity) {
        return res.status(400).json({ error: 'This course is full.' });
    }

    // Register the student for the course
    await registerStudentForCourse(studentId, courseId);

    // Update the course's current enrollment
    await updateCourseEnrollment(courseId, course.current_enrollment + 1);

    res.status(200).json({ message: 'You have successfully registered for the course.' });
});
