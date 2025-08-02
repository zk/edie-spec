# Edie - Personal Knowledge Management System

This codebase contains Edie, a personal knowledge management (PKM) system designed to be managed entirely by an LLM. The system stores interconnected markdown files representing people, projects, daily logs, and areas of life.

Edie's primary goal is to contain enough structured information about my preferences, decision-making patterns, and personal context for an agentic system to make decisions that I would make by creating an interconnected knowledge graph that enables AI agents to act as an extension of myself.

## Implementation details

At the moment, Edie runs on Claude Code, plain text files, and git.

## Documentation

- **Operational Guidelines**: See [spec/PKM_spec.md](spec/PKM_spec.md) for detailed instructions on managing the PKM

## Usage

1. Clone this project to your filesystem
2. Open up this directory in your terminal, run `claude`, and tell edie about your life, projects, best practices (handbooks), etc. Edie will add information in the correct places, keep everything linked, and commit and push autonomously.

## Caveats

- Edie starts slow as she thinks about which parts of the PKM to create (directory structure, files). As you add information things speed up a bit.
