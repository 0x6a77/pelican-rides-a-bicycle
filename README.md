## pelican rides a bicycle

spoiler alert! this is what happens when you ask claude-code to draw an svg of a pelican riding a bicycle:

![pelican-rides-a-bicycle](./pelican-rides-a-bicycle.png)

this repo combines two simple ideas in claude-code to accomplish an otherwise difficult llm task:

1. computational irreducibility from this [2023 stephen wolfram essay](https://writings.stephenwolfram.com/2023/02/what-is-chatgpt-doing-and-why-does-it-work/#surely-a-network-thats-big-enough-can-do-anything)
2. tool invocation

wolfram writes:

> So how is it, then, that something like ChatGPT can get as far as it  does with language? The basic answer, I think, is that language is at a  fundamental level somehow simpler than it seems. And this means that  ChatGPT—even with its ultimately straightforward neural net structure—is successfully able to “capture the essence” of human language and the  thinking behind it. And moreover, in its training, ChatGPT has somehow  “implicitly discovered” whatever regularities in language (and thinking) make this possible.

the essay, and especially that section, suggests that maybe llm's are a front-end language processor for what human(oid)s want to accomplish, but for a generally intelligent machine it's not enough.

the idea here is that tools and agents are a partial way forward: if we combined them in clever ways we can make new gains on currently unsolvable problems. (even agents are probably a partial solution. there is so much work ahead! e.g. if we solve spatial intelligence, will it be enough? it feels like intelligence is motivated by [physical reality](https://josevillegas.substack.com/p/plato-aristotle-and-chatgpt): even if we solve the [grasping problem](https://arxiv.org/abs/1806.09266) generally, will the machine know what every human child knows: don't grasp a cup covered in thorns?)

## do the thing!

start claude-code (using container-use mcp restrictions):

```
claude --allowedTools mcp__container-use__environment_checkpoint,mcp__container-use__environment_create,mcp__container-use__environment_add_service,mcp__container-use__environment_file_delete,mcp__container-use__environment_file_list,mcp__container-use__environment_file_read,mcp__container-use__environment_file_write,mcp__container-use__environment_open,mcp__container-use__environment_run_cmd,mcp__container-use__environment_update
```

at the prompt type:

```
generate an svg of a pelican riding a bicycle in half moon bay. make it photorealistic and accurate to the half moon bay area. e.g. place it near the pigeon point lighthouse. our pelican has no helmet or basket of fish. include joggers and other cyclists on the trail with our pelican.
```



## setup

this repo uses [docker](https://formulae.brew.sh/formula/docker),  [container-use](https://container-use.com/), claude-code [agent-skills](https://code.claude.com/docs/en/skills#agent-skills) and claude-code [sandboxing](https://code.claude.com/docs/en/sandboxing#sandboxing). these instructions are for macos because that is the only system i can verify. linux and windows installs will be similar and easy to figure out.

### docker

the way we install docker:

```
brew install docker
```

### git

```
mkdir pelican-rides-a-bicycle
cd  pelican-rides-a-bicycle
git init
```

### claude-code

#### sandboxing

to enable claude-code [sandboxing](https://code.claude.com/docs/en/sandboxing) we added `settings.json` with the following content:

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

#### agent-skills

this is how we setup or svg-drawing skill:

```
mkdir -p .claude/skills/draw-svg
```



### container-use

[container-use](https://container-use.com/quickstart), from docker creator solomon hykes, allows you to isolate and sandbox your work so that gen/ai doesn't mess with your computer directly. it also allows you to run multiple experiments in parallel and merge back the results you want to keep. we set it up using the following instructions:

```
brew install dagger/tap/container-use
```

here we add the container-use mcp server to claude-code.

```
claude mcp add  --scope project container-use -- container-use stdio
```

here we update the claude-code context to include instructions that make container-use more effective.

```
curl https://raw.githubusercontent.com/dagger/container-use/main/rules/agent.md >> CLAUDE.md
```

now we need to setup the gemini api key:

```
container-use config env set GEMINI_API_KEY <the key value>
```

this is how we invoke claude-code to prompt us before it executes certain container-use operations. (don't worry, it will ask you at the prompt how to handle these operations going forward if you find this too onerous. the general idea is that you should not let a gen/ai run wild when it has potential access to untrusted inputs.)

```
claude --allowedTools mcp__container-use__environment_checkpoint,mcp__container-use__environment_create,mcp__container-use__environment_add_service,mcp__container-use__environment_file_delete,mcp__container-use__environment_file_list,mcp__container-use__environment_file_read,mcp__container-use__environment_file_write,mcp__container-use__environment_open,mcp__container-use__environment_run_cmd,mcp__container-use__environment_update
```

