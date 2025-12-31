## Pelican Rides a Bicycle

> [!NOTE]
>This is what happens when you ask Claude-Code to generate an SVG of a pelican riding a bicycle!

![pelican-rides-a-bicycle](./images/pelican-bicycle.svg)

## Claude Code Skill

LLMs typically produce broken SVGs for complex spatial tasks—overlapping 
geometry, impossible proportions, physically implausible compositions. This 
happens because LLMs predict tokens but don't *compute* spatial relationships.

We can see this when we ask Claude Sonnet to generate this SVG directly:

![pelican-rides-a-bicycle](./images/claude-3-5-sonnet-20240620.svg)

### Underlying Idea

This repo combines simple ideas in a Claude-Code skill to accomplish an otherwise difficult LLM task:

1. Natural language understanding (Claude Code)
2. Image generation capability (Google Gemini)
3. Tool invocation to handle computational irreducibility (from this [2023 Stephen Wolfram essay](https://writings.stephenwolfram.com/2023/02/what-is-chatgpt-doing-and-why-does-it-work/#surely-a-network-thats-big-enough-can-do-anything))

This demonstrates that tool-augmented AI can tackle computationally irreducible problems like spatial intelligence via iterative computation (vectoring tracing), not just pattern matching (LLM/transformer).

Wolfram writes:

> Yes, we could memorize lots of specific examples of what happens in  some particular computational system. And maybe we could even see some  (“computationally reducible”) patterns that would allow us to do a  little generalization. But the point is that computational  irreducibility means that we can never guarantee that the unexpected  won’t happen—and it’s only by explicitly doing the computation that you  can tell what actually happens in any particular case.
>
> And in the end there’s just a fundamental tension between  learnability and computational irreducibility. Learning involves in  effect [compressing data by leveraging regularities](https://www.wolframscience.com/nks/chap-10--processes-of-perception-and-analysis/). But computational irreducibility implies that ultimately there’s a limit to what regularities there may be.

The essay, and especially that section, suggests that transformers are good language processors, for a generally intelligent machine it's not enough. Perhaps tools and agents are a partial way forward: if we combined them in clever ways we can make new gains on currently unsolvable problems.

## How to Use This Skill

To try the skill for yourself, you can simply clone this repo and run Claud Code in the cloned directory.

```bash
git clone https://github.com/0x6a77/pelican-rides-a-bicycle

cd pelican-rides-a-bicycle

claude --allowedTools mcp__container-use__environment_checkpoint,mcp__container-use__environment_create,mcp__container-use__environment_add_service,mcp__container-use__environment_file_delete,mcp__container-use__environment_file_list,mcp__container-use__environment_file_read,mcp__container-use__environment_file_write,mcp__container-use__environment_open,mcp__container-use__environment_run_cmd,mcp__container-use__environment_update
```

Then at the prompt, type `Generate an SVG of a pelican riding a bicycle`

## How to Build This Skill

These instruction show how we built the Claude Code skill from scratch.

The setup uses [Docker](https://formulae.brew.sh/formula/docker), [Container-Use](https://container-use.com/), Claude-Code [agent-skills](https://code.claude.com/docs/en/skills#agent-skills) and Claude-Code [sandboxing](https://code.claude.com/docs/en/sandboxing#sandboxing). Only the installation of Docker is platform dependent, this skill will work consistently on MacOS, Windows and Linux due to the use of `container-use` which not only sandboxes Claude Code from your local machine, it standardizes on Linux to run.

### Docker

The way we install Docker:

```
brew install docker
```

### Git

```
mkdir pelican-rides-a-bicycle
cd  pelican-rides-a-bicycle
git init
```

### Claude-Code

#### Sandboxing

To enable Claude-Code [sandboxing](https://code.claude.com/docs/en/sandboxing) we added `settings.json` with the following content:

```
{
	"env": {
    "INHERIT_FROM_SHELL": "true",
    "GEMINI_API_KEY": "${GEMINI_API_KEY}"
  },
  "sandbox": {
  "enabled": true,
  }
}
```

#### Agent-Skills

The `create-svg-from-prompt` already exists in the repo and is ready to use:

```
.claude/skills/create-svg-from-prompt/SKILL.md
```

### Container-Use

[Container-Use](https://container-use.com/quickstart), from Docker creator Solomon Hykes, allows you to isolate and sandbox your work so that gen/ai doesn't mess with your computer directly. It also allows you to run multiple experiments in parallel and merge back the results you want to keep. We set it up using the following instructions:

```
brew install dagger/tap/container-use
```

Here we add the Container-Use MCP server to Claude-Code.

```
claude mcp add  --scope project container-use -- container-use stdio
```

Here we update the Claude-Code context to include instructions that make Container-Use more effective.

```
curl https://raw.githubusercontent.com/dagger/container-use/main/rules/agent.md >> CLAUDE.md
```

Now we need to setup the Gemini API key:

```
container-use config env set GEMINI_API_KEY <the key value>
```

## Run It!

Invoke Claude-Code with Container-Use protections. (Container-Use will prompt you to verify each operation before execution,  preventing unintended actions by the AI agent.)

```
claude --allowedTools mcp__container-use__environment_checkpoint,mcp__container-use__environment_create,mcp__container-use__environment_add_service,mcp__container-use__environment_file_delete,mcp__container-use__environment_file_list,mcp__container-use__environment_file_read,mcp__container-use__environment_file_write,mcp__container-use__environment_open,mcp__container-use__environment_run_cmd,mcp__container-use__environment_update
```

At the prompt type:

> Generate an SVG of a pelican riding a bicycle.

## How It Works

When you prompt "Generate an SVG of a pelican riding a bicycle":

1. Claude Code interprets your natural language request
2. Gemini generates an initial image based on the prompt
3. Autotrace converts the bitmap to SVG through the iterative path fitting optimization process that fits Bezier curves to pixel data
4. Save the final SVG

The path fitting step is where computational irreducibility matters most:  there's no way to predict optimal SVG paths without actually running the  iterative curve-fitting algorithm. Gemini provides the spatial intelligence  (trained on spatially correct images to understand what a "pelican on a  bicycle" looks like), while autotrace handles the computational optimization  (fitting precise Bezier curves to the bitmap pixels).

This multi-step approach solves what single-model LLMs cannot: generating geometrically valid, visually accurate SVGs for complex spatial prompts.

## More Examples

### Near Pelican Point

> Generate an SVG of a pelican riding a bicycle with a basket of fish and no helmet during the day in half moon bay near pigeon point lighthouse. Make it photorealistic and accurate to the half moon bay area.

![pelican-rides-a-bicycle](./images/pelican-fish-basket.svg)

### GGNP Poster

> Generate an svg of a pelican riding a bicycle with a design similar to the golden gate national parks poster from the mid 90s. make sure the sky is blue, the bridge is red and pelican stands out.

![pelican-rides-a-bicycle](./images/pelican-golden-gate.svg)