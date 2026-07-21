# Personalized/Custom Agent Skills

This `skills` folder holds @ajhalili2006's custom-built agent skills whether he utilize AI agents for dev work and daily life ops, most of them are made/built collaboratively with Claude.

Note that while these skills are personalized for Andrei Jiroh's setup, you can fork the repo and tweak around as this repository being dual-licensed under both MIT-2.0 and CC-BY-SA-4.0.

**New here?** Visit <https://www.skills.sh> to learn more

## Usage

If you use AI agents on the command line, just summon the `skills` CLI to install them all (or just one).

```bash
# the whole ordeal
npx skills add andreijirohdev-labs/gen-ai-labs

# just one skill
npx skills add andreijirohdev-labs/gen-ai-labs --skill source-check
```

If you need to use them on Claude, it's a bit involved but you can clone the repository locally then `zip` each skill you need and then upload that zipped file to the webapp.
