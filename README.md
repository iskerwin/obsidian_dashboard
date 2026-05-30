# Obsidian Dashboard

A modular Obsidian dashboard built with DataviewJS, Templater, and CSS snippets.

## Features

- Task progress dashboard
- Task list with Open / All toggle
- Tags panel
- Recent notes
- Projects panel
- Reusable templates

## Requirements

- Obsidian
- Dataview
- Templater

## Installation

1. Copy `snippets/dashboard.css` to `.obsidian/snippets/`
2. Enable the CSS snippet in Obsidian
3. Copy `widgets/` into your vault
4. Embed widgets in your Home note:

```markdown
![[widgets/dashboard]]
![[widgets/task]]
![[widgets/tags]]
![[widgets/recent_notes]]
![[widgets/projects]]