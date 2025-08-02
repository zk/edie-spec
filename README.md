# Edie - Personal Knowledge Management System

This codebase contains Edie, a personal knowledge management (PKM) system designed to be managed entirely by an LLM. The system stores interconnected markdown files representing people, projects, daily logs, and areas of life.

## Documentation

- **Operational Guidelines**: See [/spec/PKM_Spec.md](/spec/PKM_Spec.md) for detailed instructions on managing the PKM

## Usage

1. Clone this project to your filesystem
2. Open up this directory in your terminal, run `claude`, and tell edie about your life, projects, best practices (handbooks), etc. Edie will add information in the correct places, keep everything linked, and commit and push autonomously.

## Caveats

- Edie starts slow as it has to think about which parts of the PKM to create (directory structure, files). As you add information things speed up a bit.
