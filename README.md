# 🔐 Password Creator (HTML/CSS/JS)

This project is a simple Password Generator built with HTML, CSS, and JavaScript — not Python.

Purpose

A lightweight web page that generates random passwords with basic options such as length and character types.

Features

- Generate random passwords
- Choose password length
- Include/exclude uppercase letters, lowercase letters, numbers, and symbols
- Runs entirely in the browser (no installation required)

Key files

- `sait.html` — the main page and entry point. Open this file in a browser to run the app.
- `README.md` — this file
- `LICENSE` — MIT License

How to run

- Quick start:
  - Open `sait.html` in your browser (double-click or drag into a browser window).

- To run via a local HTTP server (useful for testing or avoiding local file restrictions):

```bash
# Using Python 3
python -m http.server 8000
# Then open http://localhost:8000/sait.html in your browser
```

Future ideas

- Split CSS and JS into separate files for better maintainability
- Add a "Copy to clipboard" button
- Show password strength estimation
- Optionally save a history of generated passwords (mindful of security concerns)

Security notice

This tool is intended for educational and convenience use. Generating and displaying passwords in a local browser may not be suitable for production or highly sensitive uses. For critical use cases, prefer well-reviewed libraries and follow security best practices.

License

This project is licensed under the MIT License. See the `LICENSE` file for details.

If you want additional changes to the README (example outputs, screenshots, or developer instructions), tell me what to add and I will update it.