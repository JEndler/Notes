---
title: Nested Obsidian Vaults for Publishing Notes.
---
# Nested Vaults for publishing Obsidian Notes

I’ve got a little problem I’ve been trying to fix.

Simply put: I want to use obsidian to manage my published `.md` notes at [notes.endler.tech](https://notes.endler.tech) but i also want to keep using obsidian to write my daily to-do lists, as well as Work-related notes, which i don’t want to publish.

That leaves me with a couple of possibilities:

## Using Two Separate Vaults

This is the easiest approach, use two separate vaults, one for my published notes inside the `/content`  Section of my [notes repository](https://github.com/JEndler/Notes), and one for everything else.

That’s pretty easy, but makes the process of publishing notes quite cumbersome and deliberate, which i don’t want.

I want my [[digital gardening]] experience to be as **seamless** as possible.

## Using the /private Folder

Quartz, the software i use to publish my notes, allows [configuration](https://quartz.jzhao.xyz/notes/ignore-notes/) to make it so that specific folders aren’t rendered on the website.

Unfortunately this only prevents the notes from being rendered on the Website, but they are still visible inside the repo, which makes this not really a solution for confidential work-related notes.

## Nested Vaults

This is the solution I’ve been using.

Since an obsidian vault is really just a normal folder structure with some additional config files, its entirely possible to have a Vault nested inside another Vault. The Folder structure could then look like this:

- Notes (Outer Vault)
	- something.md
	- Published (Inner Vault)
		- someotherthing.md

There are some interesting caveats to this tho. Specifically, linking notes only works inside the respective Vaults, and from the Outer Vault into the Inner Vault.

So linking from `something.md` to `someotherthing.md` works.
But linking from `someotherthing.md` to `something.md` won’t.

Also, each vault should have its own attachment folder.

### Syncing the Vault with the repo

Using nested vaults solves the initial problem of publishing only a subset of notes, but editing the published notes alongside the private notes inside one Obsidian instance. But that leaves us with a problem of how to get the inner vault into the content folder of the published repository.

Cloning the files manually does work, but is not really seamless or nice.

Thus, I’ve gone with syncing the inner vault and the `/content` folder.

I use [unison](https://www.cis.upenn.edu/~bcpierce/unison/) to sync the Folders, and so far it’s been working great :)


