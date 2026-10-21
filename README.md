# Local Development Setup

## Prerequisites

- Python 3.13
- Git

## Setup

1. Clone the repository:

```bash
    git clone git@github.com:YOUR-USERNAME/rie-docs.git
    cd rie-docs
```

2. Create a virtual environment:

```bash
    python3 -m venv venv
```

3. Activate the virtual environment:

    === "macOS / Linux"

```bash
        source venv/bin/activate
```

    === "Windows (PowerShell)"

```powershell
        venv\Scripts\Activate.ps1
```

    === "Windows (CMD)"

```cmd
        venv\Scripts\activate.bat
```

4. Install dependencies:

```bash
    pip install mkdocs-material
```

5. Serve the site locally:

```bash
    mkdocs serve
```

6. Open [http://127.0.0.1:8000](http://127.0.0.1:8000) in your browser.

## Adding content

1. Create a new branch:

```bash
    git checkout -b my-changes
```

2. Add or edit `.md` files under `docs/`.

3. If adding a new page, add it to the `nav` section in `mkdocs.yml`.

4. Preview your changes locally with `mkdocs serve`.

5. Commit and push:

```bash
    git add .
    git commit -m "Description of changes"
    git push origin my-changes
```

6. Open a pull request on GitHub.