# Business-analysis-random-projects
Providing actionable insights on datasets and creating reports to share with stakeholders. 

Online Education Queries

-- ddarkwah is a view which contains two merged tables that is career track info and career track student enrollment

SELECT 
    *
FROM
    ddarkwah;


-- number of enrolled students monthly? 
SELECT 
    MONTH(date_enrolled) AS enrollment_month,
    COUNT(DISTINCT student_id) AS enrolled_students
FROM
    career_track_student_enrollments
GROUP BY enrollment_month
ORDER BY enrollment_month;


-- Select the month with the most enrollments
SELECT 
    MONTH(date_enrolled) AS enrollment_month,
    COUNT(DISTINCT student_id) AS enrolled_students
FROM
    career_track_student_enrollments
GROUP BY enrollment_month
ORDER BY enrolled_students DESC
LIMIT 1;


-- Which career track do students enroll most in
SELECT 
    cti.track_name,
    COUNT(DISTINCT cte.student_id) AS enrolled_students
FROM
    career_track_student_enrollments cte
        JOIN
    career_track_info cti ON cte.track_id = cti.track_id
GROUP BY cti.track_name
ORDER BY enrolled_students DESC
LIMIT 1;



-- What is the career track completion rate? Can you say if it’s increasing,decreasing, or staying constant with time?
SELECT 
    cti.track_name,
    COUNT(DISTINCT CASE
            WHEN cte.date_completed IS NOT NULL THEN cte.student_id
        END) AS completed_students,
    COUNT(DISTINCT cte.student_id) AS total_enrolled_students,
    IFNULL(ROUND(COUNT(DISTINCT CASE
                            WHEN cte.date_completed IS NOT NULL THEN cte.student_id
                        END) / COUNT(DISTINCT cte.student_id) * 100,
                    2),
            0) AS completion_rate
FROM
    career_track_student_enrollments cte
        JOIN
    career_track_info cti ON cte.track_id = cti.track_id
GROUP BY cti.track_name;



-- How long does it typically take students to complete a career track? What
-- type of subscription is most suitable for students who aim to complete a
-- career track: monthly, quarterly, or annual? --
SELECT 
 cti.track_name,
    AVG(DATEDIFF(cte.date_completed, cte.date_enrolled)) AS average_completion_duration
FROM
    career_track_student_enrollments cte
        JOIN
    career_track_info cti ON cte.track_id = cti.track_id
WHERE
    cte.date_completed IS NOT NULL
GROUP BY cti.track_name;


 
SELECT 
    cti.track_name,
    AVG(DATEDIFF(date_completed, date_enrolled)) AS avg_completion_time,
    CASE
        WHEN AVG(DATEDIFF(date_completed, date_enrolled)) <= 30 THEN 'Monthly'
        WHEN AVG(DATEDIFF(date_completed, date_enrolled)) >= 90 THEN 'Quarterly'
        ELSE 'Annual'
    END AS subscription_type
FROM
    career_track_student_enrollments cte
        JOIN
    career_track_info cti ON cte.track_id = cti.track_id
WHERE
    date_completed IS NOT NULL
GROUP BY cti.track_name
ORDER BY avg_completion_time DESC;
    
    








