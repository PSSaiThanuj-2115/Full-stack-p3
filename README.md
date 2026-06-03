# Full-stack-p3
Decode labs
// app.js

const express = require("express");
const mongoose = require("mongoose");

const app = express();

app.use(express.json());

// DATABASE CONNECTION
mongoose.connect("mongodb://127.0.0.1:27017/project3db")
.then(() => console.log("Database Connected"))
.catch((err) => console.log(err));

// SCHEMA DESIGN
const studentSchema = new mongoose.Schema({
    name: {
        type: String,
        required: true
    },
    email: {
        type: String,
        required: true,
        unique: true
    },
    course: {
        type: String,
        required: true
    }
});

// MODEL
const Student = mongoose.model("Student", studentSchema);

// CREATE
app.post("/students", async (req, res) => {
    try {
        const student = new Student(req.body);
        await student.save();
        res.status(201).json(student);
    } catch (error) {
        res.status(500).json({ message: error.message });
    }
});

// READ ALL
app.get("/students", async (req, res) => {
    try {
        const students = await Student.find();
        res.json(students);
    } catch (error) {
        res.status(500).json({ message: error.message });
    }
});

// READ ONE
app.get("/students/:id", async (req, res) => {
    try {
        const student = await Student.findById(req.params.id);

        if (!student) {
            return res.status(404).json({
                message: "Student Not Found"
            });
        }

        res.json(student);

    } catch (error) {
        res.status(500).json({
            message: error.message
        });
    }
});

// UPDATE
app.put("/students/:id", async (req, res) => {
    try {

        const updatedStudent =
        await Student.findByIdAndUpdate(
            req.params.id,
            req.body,
            { new: true }
        );

        if (!updatedStudent) {
            return res.status(404).json({
                message: "Student Not Found"
            });
        }

        res.json(updatedStudent);

    } catch (error) {
        res.status(500).json({
            message: error.message
        });
    }
});

// DELETE
app.delete("/students/:id", async (req, res) => {
    try {

        const deletedStudent =
        await Student.findByIdAndDelete(req.params.id);

        if (!deletedStudent) {
            return res.status(404).json({
                message: "Student Not Found"
            });
        }

        res.json({
            message: "Student Deleted Successfully"
        });

    } catch (error) {
        res.status(500).json({
            message: error.message
        });
    }
});

// SERVER
app.listen(3000, () => {
    console.log("Server Running on Port 3000");
});
