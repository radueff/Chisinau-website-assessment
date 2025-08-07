# Chisinau-website-assessment
import express from "express";
import path from "path";
import { fileURLToPath } from "url";
import betterSqlite3 from "better-sqlite3";

const app = express();
const PORT = 5000;
const __filename = fileURLToPath(import.meta.url);
const __dirname = path.dirname(__filename);

// Database setup
const db = betterSqlite3(path.join(__dirname, "database", "community.db"));

// Middleware
app.use(express.static(path.join(__dirname, "public")));
app.use(express.urlencoded({ extended: true }));
app.set("view engine", "ejs");
app.set("views", path.join(__dirname, "views"));

// Routes
app.get("/", (req, res) => {
  const event = {
    name: "Chişinău Day Festival",
    date: "14th October 2025",
    description: "Join us for the biggest celebration of the year, filled with music, food, fireworks, and parades."
  };
  res.render("pages/home", { event });
});

app.get("/faq", (req, res) => {
  res.render("pages/faq");
});

app.get("/contact", (req, res) => {
  res.render("pages/contact");
});

app.post("/contact", (req, res) => {
  const { name, email, message } = req.body;
  const stmt = db.prepare("INSERT INTO messages (name, email, message) VALUES (?, ?, ?)");
  stmt.run(name, email, message);
  res.redirect("/contact");
});

// Dynamic route to display community areas and listings
app.get("/areas", (req, res) => {
  const areas = db.prepare("SELECT * FROM areas").all();
  const listings = db.prepare("SELECT * FROM listings").all();
  res.render("pages/areas", { areas, listings });
});

app.listen(PORT, () => {
  console.log(`Server running at http://localhost:${PORT}`);
});
