# Dbms
Perfect!  
Here’s the **organized SQL queries with sample example codes** based on your file:  
(You can directly practice these for tomorrow.)

---

### **Student-Society-Enrollment Practical Queries with Sample SQL Code:**

---

**1. Retrieve names of students enrolled in any society.**
```sql
SELECT DISTINCT StudentName
FROM STUDENT
WHERE RollNo IN (SELECT RollNo FROM ENROLLMENT);
```

**2. Retrieve all society names.**
```sql
SELECT SocName FROM SOCIETY;
```

**3. Retrieve student names starting with letter 'A'.**
```sql
SELECT StudentName
FROM STUDENT
WHERE StudentName LIKE 'A%';
```

**4. Retrieve student details studying in "Computer Science" or "Chemistry".**
```sql
SELECT *
FROM STUDENT
WHERE Course IN ('Computer Science', 'Chemistry');
```

**5. Retrieve student names whose roll no starts with 'X' or 'Z' and ends with '0'.**
```sql
SELECT StudentName
FROM STUDENT
WHERE (RollNo LIKE 'X%' OR RollNo LIKE 'Z%') AND RollNo LIKE '%0';
```

**6. Find society details with more than N TotalSeats (user input).**
```sql
SELECT *
FROM SOCIETY
WHERE TotalSeats > 50;  -- Example, replace 50 with user input
```

**7. Update mentor name of a specific society.**
```sql
UPDATE SOCIETY
SET MentorName = 'Dr. Sharma'
WHERE SocID = 'S01';
```

**8. Find society names with more than five students enrolled.**
```sql
SELECT SocName
FROM SOCIETY
WHERE SocID IN (
    SELECT SID
    FROM ENROLLMENT
    GROUP BY SID
    HAVING COUNT(RollNo) > 5
);
```

**9. Find the youngest student enrolled in society 'S06'.**
```sql
SELECT StudentName
FROM STUDENT
WHERE RollNo IN (
    SELECT RollNo
    FROM ENROLLMENT
    WHERE SID = 'S06'
)
ORDER BY DOB DESC
LIMIT 1;
```

**10. Find the most popular society (based on enrollments).**
```sql
SELECT SocName
FROM SOCIETY
WHERE SocID = (
    SELECT SID
    FROM ENROLLMENT
    GROUP BY SID
    ORDER BY COUNT(RollNo) DESC
    LIMIT 1
);
```

**11. Find the two least popular societies.**
```sql
SELECT SocName
FROM SOCIETY
WHERE SocID IN (
    SELECT SID
    FROM ENROLLMENT
    GROUP BY SID
    ORDER BY COUNT(RollNo) ASC
    LIMIT 2
);
```

**12. Find student names who are not enrolled in any society.**
```sql
SELECT StudentName
FROM STUDENT
WHERE RollNo NOT IN (SELECT RollNo FROM ENROLLMENT);
```

**13. Find student names enrolled in at least two societies.**
```sql
SELECT StudentName
FROM STUDENT
WHERE RollNo IN (
    SELECT RollNo
    FROM ENROLLMENT
    GROUP BY RollNo
    HAVING COUNT(SID) >= 2
);
```

**14. Find society names with maximum students enrolled.**
```sql
SELECT SocName
FROM SOCIETY
WHERE SocID = (
    SELECT SID
    FROM ENROLLMENT
    GROUP BY SID
    ORDER BY COUNT(RollNo) DESC
    LIMIT 1
);
```

**15. Find names of all students enrolled and corresponding society names.**
```sql
SELECT StudentName, SocName
FROM STUDENT S
JOIN ENROLLMENT E ON S.RollNo = E.RollNo
JOIN SOCIETY SO ON E.SID = SO.SocID;
```

**16. Find student names enrolled in any of three societies ('Debating', 'Dancing', 'Sashakt').**
```sql
SELECT StudentName
FROM STUDENT
WHERE RollNo IN (
    SELECT RollNo
    FROM ENROLLMENT
    WHERE SID IN (
        SELECT SocID
        FROM SOCIETY
        WHERE SocName IN ('Debating', 'Dancing', 'Sashakt')
    )
);
```

**17. Find society names where mentor's name includes 'Gupta'.**
```sql
SELECT SocName
FROM SOCIETY
WHERE MentorName LIKE '%Gupta%';
```

**18. Find societies where enrolled students are only 10% of its capacity.**
```sql
SELECT SocName
FROM SOCIETY
WHERE SocID IN (
    SELECT SID
    FROM ENROLLMENT
    GROUP BY SID
    HAVING COUNT(RollNo) = 0.1 * (SELECT TotalSeats FROM SOCIETY WHERE SocID = SID)
);
```

**19. Display vacant seats for each society.**
```sql
SELECT SocName, (TotalSeats - IFNULL(EnrolledCount, 0)) AS VacantSeats
FROM SOCIETY LEFT JOIN (
    SELECT SID, COUNT(*) AS EnrolledCount
    FROM ENROLLMENT
    GROUP BY SID
) E ON SOCIETY.SocID = E.SID;
```

**20. Increment Total Seats of each society by 10%.**
```sql
UPDATE SOCIETY
SET TotalSeats = TotalSeats + (TotalSeats * 0.10);
```

**21. Add field 'Enrollment Fees Paid' (`YES`/`NO`) in Enrollment table.**
```sql
ALTER TABLE ENROLLMENT
ADD COLUMN FeesPaid ENUM('YES', 'NO') DEFAULT 'NO';
```

**22. Update date of enrollment for specific societies.**
```sql
UPDATE ENROLLMENT
SET DateOfEnrollment = '2025-01-01'
WHERE SID = 'S01';

UPDATE ENROLLMENT
SET DateOfEnrollment = CURRENT_DATE()
WHERE SID = 'S02';

UPDATE ENROLLMENT
SET DateOfEnrollment = '2025-01-01'
WHERE SID = 'S03';
```

**23. Create a view tracking society names with total enrollments.**
```sql
CREATE VIEW Society_Enrollment_View AS
SELECT SocName, COUNT(ENROLLMENT.RollNo) AS TotalEnrolled
FROM SOCIETY
JOIN ENROLLMENT ON SOCIETY.SocID = ENROLLMENT.SID
GROUP BY SocName;
```

**24. Find student names enrolled in all societies.**
```sql
SELECT StudentName
FROM STUDENT
WHERE RollNo IN (
    SELECT RollNo
    FROM ENROLLMENT
    GROUP BY RollNo
    HAVING COUNT(DISTINCT SID) = (SELECT COUNT(*) FROM SOCIETY)
);
```

**25. Count societies with more than 5 students enrolled.**
```sql
SELECT COUNT(*)
FROM (
    SELECT SID
    FROM ENROLLMENT
    GROUP BY SID
    HAVING COUNT(RollNo) > 5
) AS Subquery;
```

**26. Add 'Mobile Number' column in student table with default value '9999999999'.**
```sql
ALTER TABLE STUDENT
ADD COLUMN MobileNo VARCHAR(10) DEFAULT '9999999999';
```

**27. Find total number of students older than 20 years.**
```sql
SELECT COUNT(*)
FROM STUDENT
WHERE TIMESTAMPDIFF(YEAR, DOB, CURDATE()) > 20;
```

**28. Find names of students born in 2001 and enrolled in at least one society.**
```sql
SELECT StudentName
FROM STUDENT
WHERE YEAR(DOB) = 2001
AND RollNo IN (SELECT RollNo FROM ENROLLMENT);
```

**29. Count societies whose name starts with 'S', ends with 'T', and have at least 5 students enrolled.**
```sql
SELECT COUNT(*)
FROM SOCIETY
WHERE SocName LIKE 'S%T'
AND SocID IN (
    SELECT SID
    FROM ENROLLMENT
    GROUP BY SID
    HAVING COUNT(RollNo) >= 5
);
```

**30. Display Society Name, Mentor Name, Total Capacity, Total Enrolled, Unfilled Seats.**
```sql
SELECT SocName, MentorName, TotalSeats,
       IFNULL(EnrolledCount, 0) AS TotalEnrolled,
       (TotalSeats - IFNULL(EnrolledCount, 0)) AS UnfilledSeats
FROM SOCIETY
LEFT JOIN (
    SELECT SID, COUNT(*) AS EnrolledCount
    FROM ENROLLMENT
    GROUP BY SID
) E ON SOCIETY.SocID = E.SID;
```

---

### **Database Administration Commands:**

**Create user:**
```sql
CREATE USER 'newuser'@'localhost' IDENTIFIED BY 'password';
```

**Create role:**
```sql
CREATE ROLE 'manager';
```

**Grant privileges to a role:**
```sql
GRANT SELECT, INSERT, UPDATE ON database_name.* TO 'manager';
```

**Revoke privileges from a role:**
```sql
REVOKE UPDATE ON database_name.* FROM 'manager';
```

**Create index:**
```sql
CREATE INDEX idx_studentname ON STUDENT(StudentName);
```

---

Would you also like me to prepare a **one-page Quick Revision Sheet** summarizing the most important points from topics + viva tips?  
(It will help you revise in the last 30 minutes before viva!)  
Just say **"Make revision sheet"** if you want!
