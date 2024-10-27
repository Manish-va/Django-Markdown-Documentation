# Schema

## Department
- **department_name**: String (max length: 100)
- **department_id**: Auto-incremented Integer (Primary Key)
- **hod**: Foreign Key (Teacher, optional)
- **school_id**: Foreign Key (School, optional)

---



### Teacher
- **name**: String (max length: 50)
- **employee_id**: Auto-incremented Integer (Primary Key)
- **performance**: Float (optional)
- **department_id**: Foreign Key (Department)
- **created_on**: DateTime
- **updated_on**: DateTime

### Student
- **name**: String (max length: 50)
- **roll_no**: Auto-incremented Integer (Primary Key)
- **chemistry**: Integer (default: 0)
- **physics**: Integer (default: 0)
- **maths**: Integer (default: 0)
- **total**: Integer (default: 0)
- **percentage**: Float (default: 0.0)
- **employee_id**: Foreign Key (Teacher)
- **department_id**: Foreign Key (Department)
- **created_on**: DateTime
- **updated_on**: DateTime

### School
- **school_name**: String (max length: 100)
- **school_id**: Auto-incremented Integer (Primary Key)
- **location**: String (max length: 100)
