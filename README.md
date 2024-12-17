# HRMS Payroll Management Module

This is a basic HRMS Payroll Management Module with functionalities for:
- Employee Management (Add, View, Update, Delete)
- Salary Slip Generation
- Integration with PostgreSQL database
- Frontend built using React
- Backend built using Java Spring Boot

- ## Prerequisites
- Java JDK 17 or higher
- Node.js v16 or higher
- PostgreSQL 13 or higher
- Maven (for backend)

Project Architecture Overview
Frontend: React.js
Backend: Java Spring Boot
Database: PostgreSQL

Implementation Steps

1. Database Schema
Create two tables: Employee and SalarySlip.
SQL Script:
-- Employee Table
CREATE TABLE employee (
    id SERIAL PRIMARY KEY,
    name VARCHAR(255) NOT NULL,
    email VARCHAR(255) UNIQUE NOT NULL,
    designation VARCHAR(100),
    basic_salary NUMERIC(10, 2),
    department VARCHAR(100)
);

-- SalarySlip Table
CREATE TABLE salary_slip (
    id SERIAL PRIMARY KEY,
    employee_id INT REFERENCES employee(id) ON DELETE CASCADE,
    pf_deduction NUMERIC(10, 2),
    professional_tax NUMERIC(10, 2),
    net_salary NUMERIC(10, 2)
);

2. Backend: Java Spring Boot
Project Setup:

Use Spring Initializr to create a project with the following dependencies:
Spring Web
Spring Data JPA
PostgreSQL Driver
Spring Boot DevTools
Backend Structure:

EmployeeController: REST endpoints for employee CRUD operations.
SalarySlipController: REST endpoint for generating and retrieving salary slips.
EmployeeService and SalarySlipService: Business logic.
EmployeeRepository and SalarySlipRepository: Database operations.
Employee Entity:
@Entity
public class Employee {
    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;

    private String name;
    private String email;
    private String designation;
    private Double basicSalary;
    private String department;

    // Getters and Setters
}
SalarySlip Entity:
@Entity
public class SalarySlip {
    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;

    @ManyToOne
    @JoinColumn(name = "employee_id", nullable = false)
    private Employee employee;

    private Double pfDeduction;
    private Double professionalTax = 200.0; // Fixed
    private Double netSalary;

    // Getters and Setters
}
EmployeeController:
@RestController
@RequestMapping("/api/employees")
public class EmployeeController {
    @Autowired
    private EmployeeService employeeService;

    @PostMapping
    public Employee addEmployee(@RequestBody Employee employee) {
        return employeeService.saveEmployee(employee);
    }

    @GetMapping
    public List<Employee> getAllEmployees() {
        return employeeService.getAllEmployees();
    }

    @PutMapping("/{id}")
    public Employee updateEmployee(@PathVariable Long id, @RequestBody Employee employee) {
        return employeeService.updateEmployee(id, employee);
    }

    @DeleteMapping("/{id}")
    public void deleteEmployee(@PathVariable Long id) {
        employeeService.deleteEmployee(id);
    }
}
SalarySlipController:
@RestController
@RequestMapping("/api/salary")
public class SalarySlipController {
    @Autowired
    private SalarySlipService salarySlipService;

    @PostMapping("/generate")
    public SalarySlip generateSalarySlip(@RequestBody Long employeeId) {
        return salarySlipService.generateSalarySlip(employeeId);
    }
}

3. Frontend: React
Setup:

Create a React project using create-react-app.
Install dependencies: axios, react-router-dom, and react-bootstrap.
Frontend Structure:

components/EmployeeManagement.js: Form and Table for Employee CRUD operations.
components/SalarySlipGeneration.js: Form to generate salary slip and view/download PDF.
EmployeeManagement.js:
import React, { useState, useEffect } from "react";
import axios from "axios";

const EmployeeManagement = () => {
    const [employees, setEmployees] = useState([]);
    const [form, setForm] = useState({ name: "", email: "", designation: "", basicSalary: "", department: "" });

    const fetchEmployees = async () => {
        const response = await axios.get("/api/employees");
        setEmployees(response.data);
    };

    const handleSubmit = async (e) => {
        e.preventDefault();
        await axios.post("/api/employees", form);
        fetchEmployees();
        setForm({ name: "", email: "", designation: "", basicSalary: "", department: "" });
    };

    const handleDelete = async (id) => {
        await axios.delete(`/api/employees/${id}`);
        fetchEmployees();
    };

    useEffect(() => {
        fetchEmployees();
    }, []);

    return (
        <div>
            <form onSubmit={handleSubmit}>
                <input
                    type="text"
                    placeholder="Name"
                    value={form.name}
                    onChange={(e) => setForm({ ...form, name: e.target.value })}
                />
                <input
                    type="email"
                    placeholder="Email"
                    value={form.email}
                    onChange={(e) => setForm({ ...form, email: e.target.value })}
                />
                <input
                    type="text"
                    placeholder="Designation"
                    value={form.designation}
                    onChange={(e) => setForm({ ...form, designation: e.target.value })}
                />
                <input
                    type="number"
                    placeholder="Basic Salary"
                    value={form.basicSalary}
                    onChange={(e) => setForm({ ...form, basicSalary: e.target.value })}
                />
                <input
                    type="text"
                    placeholder="Department"
                    value={form.department}
                    onChange={(e) => setForm({ ...form, department: e.target.value })}
                />
                <button type="submit">Add Employee</button>
            </form>

            <table>
                <thead>
                    <tr>
                        <th>Name</th>
                        <th>Email</th>
                        <th>Designation</th>
                        <th>Department</th>
                        <th>Actions</th>
                    </tr>
                </thead>
                <tbody>
                    {employees.map((emp) => (
                        <tr key={emp.id}>
                            <td>{emp.name}</td>
                            <td>{emp.email}</td>
                            <td>{emp.designation}</td>
                            <td>{emp.department}</td>
                            <td>
                                <button onClick={() => handleDelete(emp.id)}>Delete</button>
                            </td>
                        </tr>
                    ))}
                </tbody>
            </table>
        </div>
    );
};

export default EmployeeManagement;

4. Integration
Backend:

Run the Spring Boot application on port 8080.
Frontend:

Configure proxy in package.json to http://localhost:8080.


#### **d. API Documentation**
Document the APIs available in the backend. Use the following format:

**Employee Management APIs**:
```markdown
### Employee Management APIs

1. **Add Employee**
   - **Endpoint**: `POST /api/employees`
   - **Request Body**:
     ```json
     {
       "name": "John Doe",
       "email": "john.doe@example.com",
       "designation": "Software Engineer",
       "basicSalary": 50000,
       "department": "IT"
     }
     ```

2. **Get All Employees**
   - **Endpoint**: `GET /api/employees`

3. **Update Employee**
   - **Endpoint**: `PUT /api/employees/{id}`
   - **Request Body**:
     ```json
     {
       "name": "John Doe",
       "email": "john.doe@example.com",
       "designation": "Senior Software Engineer",
       "basicSalary": 60000,
       "department": "IT"
     }
     ```

4. **Delete Employee**
   - **Endpoint**: `DELETE /api/employees/{id}`

### Salary Slip APIs

1. **Generate Salary Slip**
   - **Endpoint**: `POST /api/salary/generate`
   - **Request Body**:
     ```json
     {
       "employeeId": 1
     }
     ```
   - **Response**:
     ```json
     {
       "id": 1,
       "employeeId": 1,
       "pfDeduction": 6000,
       "professionalTax": 200,
       "netSalary": 43800
     }
     ```
