# Next.js Engineering Skills

A collection of reusable AI agent skills for building, reviewing, optimizing, and maintaining production-ready Next.js applications.

Designed for Claude Code and other compatible AI coding agents.

## Overview

Next.js Engineering Skills provides structured engineering guidelines, implementation standards, and reusable workflows for Next.js development.

The goal is to help AI agents produce consistent, maintainable, secure, accessible, and high-performance code across different projects.

These skills are framework-focused and project-agnostic. They are not limited to a specific business domain or application.

## Features

- Next.js 16 App Router engineering standards.
- React Server Components and Client Components.
- TypeScript and Tailwind CSS v4.
- Component architecture and code organization.
- Server Actions and data fetching.
- Performance optimization and Core Web Vitals.
- Accessibility and UI engineering.
- Security and error handling.
- Reusable implementation and code review workflows.

## Available Skills

| Skill                        | Description                                             |
| ---------------------------- | ------------------------------------------------------- |
| nextjs-component-engineering | Build and refactor pages, components, and forms.        |
| nextjs-performance-audit     | Diagnose and optimize Next.js performance.              |
| nextjs-security-review       | Review application security and common vulnerabilities. |

## Requirements

- Node.js and a Next.js project.
- Claude Code or a compatible agent supporting Agent Skills.
- TypeScript and Tailwind CSS v4 where applicable.

## Installation

Clone this repository:

```bash
git clone https://github.com/USERNAME/nextjs-engineering-skills.git
```

Copy the desired skill into your project's Claude skills directory:

```bash
mkdir -p .claude/skills

cp -r nextjs-engineering-skills/skills/nextjs-component-engineering \
.claude/skills/
```

## Usage

### Automatic Invocation

Claude can automatically discover and invoke a skill when the user's request matches its description.

Example:

```text
Create a reusable data table component using Next.js 16,
TypeScript, and Tailwind CSS v4.
```

### Manual Invocation

Invoke a skill directly using its slash command:

```text
/nextjs-component-engineering
```

You can then provide your implementation task.

## Project Structure

```text
nextjs-engineering-skills/
├── README.md
├── skills/
│   ├── nextjs-component-engineering/
│   │   ├── SKILL.md
│   │   ├── references/
│   │   └── examples/
│   └── nextjs-performance-audit/
│       ├── SKILL.md
│       ├── references/
│       └── examples/
├── LICENSE
└── CONTRIBUTING.md
```

### SKILL.md

Defines the skill's identity, invocation description, core engineering rules, and execution workflow.

### references/

Contains detailed technical documentation and supplementary engineering standards.

### examples/

Contains implementation examples and reusable code patterns.

## Best Practices

- Keep SKILL.md focused on essential instructions and workflows.
- Move detailed technical documentation into references/.
- Use examples to demonstrate recommended implementations.
- Avoid duplicating rules across multiple skills.
- Keep instructions framework-focused and project-independent.
- Validate technical guidance against the relevant framework versions.

## Contributing

Contributions are welcome.

Please ensure that new skills follow the repository's naming conventions, metadata requirements, documentation structure, and engineering standards.

## License

Specify your chosen license here.
