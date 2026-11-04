# MeetNote — Project Work Report

This repository contains the final project-work report for **MeetNote**, a
desktop application that helps administrators turn recorded meetings into
reviewable and trackable action items.

The report is written in Markdown and published as a static website using
GitHub Pages and GitHub Actions.

## Project

**MeetNote** allows a user to:

- Upload an audio or video meeting recording.
- Transcribe the recording locally using faster-whisper.
- Extract candidate action items using Google Gemini.
- Generate a meeting summary.
- Review, approve, reject, and manage action items.
- Track approved action items on a Kanban board.

The application is implemented in Python with a Kivy-based desktop interface.
Its source code is available in the [MeetNote Artifact repository][artifact].

## Report website

The published report is available at:

<https://unibo-dtm-se-2526-meetnote.github.io/Report/>

The website is automatically rebuilt whenever changes are pushed to the
default branch of this repository.

## Repository structure

```text
.
├── _config.yml          # Jekyll and GitHub Pages configuration
├── index.md             # Report landing page
├── sections/            # Report chapters written in Markdown
├── pictures/            # Images and PlantUML diagram sources
├── Gemfile              # Ruby dependencies for local Jekyll preview
└── README.md            # This file
```

## Report sections

The report is divided into the following main sections:

1. **Concept** — project motivation, objectives, and scope.
2. **Requirements** — functional and non-functional requirements.
3. **Design** — architecture, domain model, behaviour, and data design.
4. **Development** — implementation choices, technologies, and project organization.
5. **Validation** — testing strategy and results.
6. **Release** — versioning, releases, and documentation.
7. **Deployment** — application deployment and runtime environment.
8. **CI/CD** — continuous integration, delivery, and automation.
9. **User guide** — instructions for using MeetNote.
10. **Developer guide** — instructions for setting up and extending the project.
11. **Self-evaluation** — reflection on the project work.
12. **Future work** — possible improvements and extensions.

## Diagrams

The report uses PlantUML diagrams instead of ASCII-art diagrams.

PlantUML source files are stored in the `pictures/` directory using the `.puml`
extension. Rendered SVG images are also stored in `pictures/` and embedded in
the Markdown report.

Example:

```markdown


*Figure 1: Layered architecture of MeetNote.*
```

## Editing the report

All report content is written in Markdown inside the `sections/` directory.

To edit a section:

1. Open the relevant Markdown file in `sections/`.
2. Update the text, tables, links, or images.
3. Commit and push the changes.
4. Wait for GitHub Actions to rebuild the website.

## Local preview

To preview the report locally, you need Ruby and Jekyll.

Clone this repository:

```bash
git clone [https://github.com/unibo-dtm-se-2526-MEETNOTE/Report.git](https://github.com/unibo-dtm-se-2526-MEETNOTE/Report.git)
cd Report
```

Install the dependencies:

```bash
bundle install
```

Start the local preview:

```bash
bundle exec jekyll serve
```

Then open the local URL shown in the terminal, usually:

<http://127.0.0.1:4000>

## Publishing changes

The report website is generated automatically by GitHub Actions.

To publish changes:

```bash
git add .
git commit -m "docs: update project report"
git push origin main
```

Then check the **Actions** tab in GitHub to ensure that the site build succeeds.

## Related repositories

- [MeetNote application source code][artifact]
- [Course project-work template][template]

## License

This report repository is based on the Software Engineering