# HiveFabric Documentation Repository

Welcome to the official documentation repository for **HiveFabric**, the distributed platform powering **Hive**, the autonomous agent app.  

At the core of HiveFabric is **AgentFabric**, the runtime that enables tasks to run securely, reliably, and at scale across mobile, desktop, and cloud devices.

This repository hosts all technical documentation, specifications, and guides for developers, contributors, and enterprise users.

---

## 🚀 Project Hierarchy

- **Hive** – User-facing app for managing autonomous agents, mobile-first, designed to be simple, viral, and intuitive.
- **HiveFabric** – Cloud-based platform that orchestrates agent execution, task scheduling, and resource allocation across heterogeneous nodes.
- **AgentFabric** – Runtime specification for autonomous agents, supporting fork-join DAG execution, sandboxed tasks, and cross-platform deployment.

---

## 📖 Documentation Contents

The documentation is organized as follows:

- **Overview** – High-level introduction to Hive, HiveFabric, and AgentFabric.
- **AgentFabric** – Runtime specification, SDK/API references, examples of agent tasks and DAG execution.
- **Executors** – Setup and guidance for mobile, Windows, Linux, and macOS executors; security and resource control.
- **Marketplace** – Marketplace design, agent economy, token / credit system.
- **Architecture** – System diagrams, workflow, and execution models.
- **FAQ** – Frequently asked questions, common challenges, and troubleshooting.

---

## 💻 Local Setup

To preview the documentation locally, make sure you have Python 3.x installed.

1. Clone the repository:

```bash
git clone https://github.com/YOUR_USERNAME/hive-docs.git
cd hive-docs
```

2. Install dependencies:
```python -m venv venv
source venv/bin/activate   # Linux/macOS
# venv\Scripts\activate    # Windows
pip install -r requirements.txt
```

3. Serve locally:
```mkdocs serve```

## 📦 Deployment

The documentation is automatically deployed to GitHub Pages via GitHub Actions.
After pushing updates to main, the site is rebuilt and served from the /site folder.

GitHub Pages URL (example): https://YOUR_USERNAME.github.io/hive-docs/

Custom domain support is available via the CNAME file.

## 🛠 Contributing

We welcome contributions! To contribute:

Fork the repository

Make your changes in a feature branch

Submit a pull request with a clear description

Ensure Markdown formatting and links are valid

Include images in /docs/assets/ if needed

## 📌 Notes

This documentation is versioned using MkDocs Material; future releases of HiveFabric / AgentFabric may have versioned docs.

All executable examples (YAML, DAGs, SDK snippets) are intended for reference and experimentation only.

Please follow security and sandbox guidelines when running agent executors.

## ⚡ License

This repository is licensed under the MIT License – see the LICENSE
 file for details.

## 🔗 Links

HiveFabric Website

Hive App (mobile)

AgentFabric Runtime Spec


---

This README:

- Clearly explains **what each component is** (Hive / HiveFabric / AgentFabric)  
- Gives **setup instructions** for local preview and GitHub Pages deployment  
- Outlines the **structure of the docs** for easy navigation  
- Includes contributing and licensing info
