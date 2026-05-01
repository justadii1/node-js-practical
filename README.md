const express = require("express");
const app = express();

app.use(express.json());

let users = [];
let idCounter = 1;

app.use((req, res, next) => {
  const time = new Date().toLocaleString();
  console.log(`Request received at: ${time}`);
  console.log(`${req.method} ${req.url}`);
  next();
});

const response = (res, message, data = null) => {
  res.json({
    message,
    time: new Date().toLocaleString(),
    data
  });
};

app.get("/", (req, res) => {
  response(res, "Server Running");
});

app.get("/users", (req, res) => {
  response(res, "Users fetched", users);
});

app.get("/users/:id", (req, res) => {
  const user = users.find(u => u.id == req.params.id);
  if (!user) return response(res, "User not found");
  response(res, "User fetched", user);
});

app.post("/users", (req, res) => {
  const { name, email } = req.body;

  if (!name || !email) {
    return response(res, "Name and email required");
  }

  const exists = users.find(u => u.email === email);
  if (exists) {
    return response(res, "Email already exists");
  }

  const newUser = { id: idCounter++, name, email };
  users.push(newUser);

  response(res, "User added", newUser);
});

app.delete("/users/:id", (req, res) => {
  const index = users.findIndex(u => u.id == req.params.id);

  if (index === -1) {
    return response(res, "User not found");
  }

  users.splice(index, 1);
  response(res, "User deleted");
});

app.post("/login", (req, res) => {
  const { email, password } = req.body;

  if (!email || !password) {
    return response(res, "All fields required");
  }

  if (email === "admin@gmail.com" && password === "1234") {
    return response(res, "Login Success");
  }

  response(res, "Invalid Credentials");
});

app.listen(3000, () => {
  console.log("Server running on port 3000");
});
