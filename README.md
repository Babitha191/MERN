//1. Login Page:
//Backend(Node.js/Express.js):
// server.js
const express = require('express');
const bodyParser = require('body-parser');
const mongoose = require('mongoose');

const app = express();
const port = process.env.PORT || 3000;

// Connect to MongoDB database
mongoose.connect('mongodb://localhost/mern_app', { useNewUrlParser: true, useUnifiedTopology: true })
  .then(() => console.log('MongoDB Connected'))
  .catch(err => console.error(err));

// Middleware
app.use(bodyParser.json());

// Employee Schema
const employeeSchema = new mongoose.Schema({
  name: String,
  email: String,
  mobile: String,
  designation: String,
  gender: String,
  course: String,
  createdDate: Date
});

const Employee = mongoose.model('Employee', employeeSchema);

// Routes
app.post('/login', (req, res) => {
  // Implement login logic here, including validation
  // ...
});

app.get('/employees', (req, res) => {
  Employee.find()
    .then(employees => res.json(employees))
    .catch(err => res.status(400).json('Error: ' + err));
});

app.post('/employees', (req, res) => {
  const newEmployee = new Employee({
    name: req.body.name,
    email: req.body.email,
    // ... other fields
  });

  newEmployee.save()
    .then(() => res.json('Employee added!'))
    .catch(err => res.status(400).json('Error: ' + err));
});

app.listen(port, () => {
  console.log(Server running on port ${port});
});
//Frontend(React):
// App.js
import React from 'react';
import { BrowserRouter as Router, Route, Routes } from 'react-router-dom';
import LoginPage from './components/LoginPage';
import Dashboard from './components/Dashboard';
import EmployeeList from './components/EmployeeList';

function App() {
  return (
    <Router>
      <Routes>
        <Route path="/" element={<LoginPage />} />
        <Route path="/dashboard" element={<Dashboard />} />
        <Route path="/employees" element={<EmployeeList />} />
      </Routes>
    </Router>
  );
}
//2.EmployeeForm.js:
//Frontend(React):
import React, { useState } from 'react';
import axios from 'axios';

const EmployeeForm = () => {
    const [name, setName] = useState('');
    const [email, setEmail] = useState('');
    const [mobile, setMobile] = useState('');
    const [designation, setDesignation] = useState('');
    const [gender, setGender] = useState('');
    const [course, setCourse] = useState([]);
    const [image, setImage] = useState(null);

    const handleSubmit = async (e) => {
        e.preventDefault();

        const formData = new FormData();
        formData.append('name', name);
        formData.append('email', email);
        formData.append('mobile', mobile);
        formData.append('designation', designation);
        formData.append('gender', gender);
        formData.append('course', course);
        formData.append('image', image);

        try {
            const response = await axios.post('/api/employees', formData, {
                headers: {
                    'Content-Type': 'multipart/form-data'
                }
            });
            console.log(response.data);
            // Handle success, e.g., clear form, display success message
        } catch (error) {
            console.error(error);
            // Handle error, e.g., display error message
        }
    };

    // ... (other form fields and validation logic)

    return (
        <form onSubmit={handleSubmit}>
            {/* ... (form fields and validation) */}
            <button type="submit">Submit</button>
        </form>
    );
};

export default EmployeeForm;

//Backend(Node.js/Express.js):
const express = require('express');
const multer = require('multer');
const mongoose = require('mongoose');

const app = express();
const port = process.env.PORT || 3000;

// Connect to MongoDB database
mongoose.connect('mongodb://localhost/mern_app', { useNewUrlParser: true, useUnifiedTopology: true })
    .then(() => console.log('MongoDB Connected'))
    .catch(err => console.error(err));

// Middleware
app.use(express.json());

// Multer storage configuration
const storage = multer.diskStorage({
    destination: (req, file, cb) => {
        cb(null, 'uploads/');
    },
    filename: (req, file, cb) => {
        cb(null, file.originalname);
    }
});

const upload = multer({ storage: storage });

// Employee Schema
const employeeSchema = new mongoose.Schema({
    name: String,
    email: String,
    mobile: String,
    designation: String,
    gender: String,
    course: [String],
    image: String
});

const Employee = mongoose.model('Employee', employeeSchema);

// Routes
app.post('/api/employees', upload.single('image'), (req, res) => {
    const newEmployee = new Employee({
        name: req.body.name,
        email: req.body.email,
        mobile: req.body.mobile,
        designation: req.body.designation,
        gender: req.body.gender,
        course: req.body.course,
        image: req.file.filename
    });

    newEmployee.save()
        .then(() => res.json('Employee added!'))
        .catch(err => res.status(400).json('Error: ' + err));
});

app.listen(port, () => {
    console.log(Server running on port ${port});
});

//3.Employee Data(Create):
//Backend(Node.js and Express):
const express = require('express');
const mongoose = require('mongoose');
const bodyParser = require('body-parser');
const multer = require('multer');
const path = require('path');

const app = express();
const port = process.env.PORT || 5000;

// Connect to MongoDB
mongoose.connect('mongodb://localhost/mern-app', { useNewUrlParser: true, useUnifiedTopology: true })
  .then(() => console.log('MongoDB connected'))
  .catch(err => console.error(err));

// Middleware
app.use(bodyParser.json());
app.use('/uploads', express.static('uploads'));

// Multer storage configuration
const storage = multer.diskStorage({
  destination: (req, file, cb) => {
    cb(null, 'uploads/');
  },
  filename: (req, file, cb) => {
    cb(null, file.originalname);
  }
});

const upload = multer({ storage: storage });

// Define an employee schema
const EmployeeSchema = new mongoose.Schema({
  name: String,
  email: String,
  mobile: String,
  designation: String,
  gender: String,
  course: String,
  image: String,
  isActive: Boolean
});

const Employee = mongoose.model('employee', EmployeeSchema);

// API endpoints
app.get('/api/employees', (req, res) => {
  Employee.find()
    .then(employees => res.json(employees))
    .catch(err => res.json(err));
});

app.post('/api/employees', upload.single('image'), (req, res) => {
  const newEmployee = new Employee({
    name: req.body.name,
    email: req.body.email,
    mobile: req.body.mobile,
    designation: req.body.designation,
    gender: req.body.gender,
    course: req.body.course,
    image: req.file.filename,
    isActive: req.body.isActive
  });

  newEmployee.save()
    .then(employee => res.json(employee))
    .catch(err => res.json(err));
});

app.put('/api/employees/:id', upload.single('image'), (req, res) => {
  Employee.findByIdAndUpdate(req.params.id, {
    name: req.body.name,
    email: req.body.email,
    mobile: req.body.mobile,
    designation: req.body.designation,
    gender: req.body.gender,
    course: req.body.course,
    image: req.file ? req.file.filename : req.body.image,
    isActive: req.body.isActive
  }, { new: true })
    .then(employee => res.json(employee))
    .catch(err => res.json(err));
});

app.delete('/api/employees/:id', (req, res) => {
  Employee.findByIdAndRemove(req.params.id)
    .then(() => res.json({ success: true }))
    .catch(err => res.json(err));
});

app.listen(port, () => console.log(Server started on port ${port}));

Frontend(React):
import React, { useState, useEffect } from 'react';
import axios from 'axios';

function App() {
  const [employees, setEmployees] = useState([]);
  const [employee, setEmployee] = useState({
    name: '',
    email: '',
    mobile: '',
    designation: '',
    gender: '',
    course: '',
    image: null,
    isActive: true
  });

  useEffect(() => {
    const fetchEmployees = async () => {
      const response = await axios.get('/api/employees');
      setEmployees(response.data);
    };

    fetchEmployees();
  }, []);

  const handleChange = (e) => {
    setEmployee({ ...employee, [e.target.name]: e.target.value });
  };

  const handleImageChange = (e) => {
    setEmployee({ ...employee, image: e.target.files[0] });
  };

  const handleSubmit = async (e) => {

//4.Update Employee:
//Backend(Node.js and Express):
const express = require('express');
const mongoose = require('mongoose');
const bodyParser = require('body-parser');
const multer = require('multer');
const path = require('path');

const app = express();
const port = process.env.PORT || 5000;

// Connect to MongoDB
mongoose.connect('mongodb://localhost/mern-app', { useNewUrlParser: true, useUnifiedTopology: true })
  .then(() => console.log('MongoDB connected'))
  .catch(err => console.error(err));

// Middleware
app.use(bodyParser.json());
app.use('/uploads', express.static('uploads'));

// Multer storage configuration
const storage = multer.diskStorage({
  destination: (req, file, cb) => {
    cb(null, 'uploads/');
  },
  filename: (req, file, cb) => {
    cb(null, file.originalname);
  }
});

const upload = multer({ storage: storage });

// Define an employee schema
const EmployeeSchema = new mongoose.Schema({
  name: String,
  email: String,
  mobile: String,
  designation: String,
  gender: String,
  course: String,
  image: String,
  isActive: Boolean
});

const Employee = mongoose.model('employee', EmployeeSchema);

// API endpoints
app.get('/api/employees', (req, res) => {
  Employee.find()
    .then(employees => res.json(employees))
    .catch(err => res.json(err));
});

app.post('/api/employees', upload.single('image'), (req, res) => {
  const newEmployee = new Employee({
    name: req.body.name,
    email: req.body.email,
    mobile: req.body.mobile,
    designation: req.body.designation,
    gender: req.body.gender,
    course: req.body.course,
    image: req.file.filename,
    isActive: req.body.isActive
  });

  newEmployee.save()
    .then(employee => res.json(employee))
    .catch(err => res.json(err));
});

app.put('/api/employees/:id', upload.single('image'), (req, res) => {
  Employee.findByIdAndUpdate(req.params.id, {
    name: req.body.name,
    email: req.body.email,
    mobile: req.body.mobile,
    designation: req.body.designation,
    gender: req.body.gender,
    course: req.body.course,
    image: req.file ? req.file.filename : req.body.image,
    isActive: req.body.isActive
  }, { new: true })
    .then(employee => res.json(employee))
    .catch(err => res.json(err));
});

app.delete('/api/employees/:id', (req, res) => {
  Employee.findByIdAndRemove(req.params.id)
    .then(() => res.json({ success: true }))
    .catch(err => res.json(err));
});

app.listen(port, () => console.log(Server started on port ${port}));
Frontend(React):
import React, { useState, useEffect } from 'react';
import axios from 'axios';

function App() {
  const [employees, setEmployees] = useState([]);
  const [employee, setEmployee] = useState({
    name: '',
    email: '',
    mobile: '',
    designation: '',
    gender: '',
    course: '',
    image: null,
    isActive: true
  });

  useEffect(() => {
    const fetchEmployees = async () => {
      const response = await axios.get('/api/employees');
      setEmployees(response.data);
    };

    fetchEmployees();
  }, []);

  const handleChange = (e) => {
    setEmployee({ ...employee, [e.target.name]: e.target.value });
  };

  const handleImageChange = (e) => {
    setEmployee({ ...employee, image: e.target.files[0] });
  };

  const handleSubmit = async (e) => {

