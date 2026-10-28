# web-skills

Agent skills for web design and development work, usable with Claude Code and other agents that support the `SKILL.md` format.

## Skills

| Skill | Description |
|-------|-------------|
| [wireframe](skills/wireframe/SKILL.md) | Low-fidelity wireframes of UI screens as hand-written SVG, in light and dark mode. Supports multi-step interaction storyboards ("what happens when I click X") and annotated callouts with curved arrows. Optional PNG export via `rsvg-convert`. |

## Installation

With the [skills CLI](https://github.com/vercel-labs/skills):

```sh
npx skills add jackmorizo/web-skills
```

Or manually, by copying a skill folder into your skills directory:

```sh
git clone https://github.com/jackmorizo/web-skills.git
cp -r web-skills/skills/wireframe ~/.claude/skills/          # all projects
# or
cp -r web-skills/skills/wireframe .claude/skills/            # current project only
```

## Usage

Skills are picked up automatically when a request matches their description. For example:

> Make a wireframe of a settings page with a sidebar nav.

> Show me what happens when I click "Filter" on the orders table.

The wireframe skill writes `wireframes/<name>.light.svg` and `wireframes/<name>.dark.svg`.

### Optional dependency

PNG conversion uses [`rsvg-convert`](https://gitlab.gnome.org/GNOME/librsvg) (from librsvg). If it isn't installed, the skill keeps the SVGs and skips conversion.

```sh
# Debian/Ubuntu
sudo apt install librsvg2-bin
# macOS
brew install librsvg
```

## Repository layout

```
skills/
  <skill-name>/
    SKILL.md
```

Each skill lives in its own folder under `skills/` with a `SKILL.md` containing YAML frontmatter (`name`, `description`) and instructions.

## License

[MIT](LICENSE)
