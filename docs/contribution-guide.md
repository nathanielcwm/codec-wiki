---
title: Contribution Guide
sidebar_label: ✒️ Contribution Guide
sidebar_position: 13
---

# Contribution Guide

Codec Wiki - community-maintained wiki for all things encoding.

## Before You Contribute

1. By contributing to the Codec Wiki, you are communicating that you have read & agreed to our Terms & Conditions, Privacy Policy, & Code of Conduct.
2. Ensure your understanding of the material you're contributing is sufficient to a point where it is useful to the project. It is perfectly acceptable not to get everything right the first time, but always double-check your contributions for factual correctness.

**Our current priority is filling out the existing pages with content. Please assist in doing this, if possible, before considering adding new pages.**

If you're unsure the content in your entry is completely correct or you believe your entry needs review, please attach the following message at the top of your entry:

:::caution Pending Review
The content in this entry may not be entirely accurate, & is pending further review to assess the quality of the information.
:::

If you're aware your entry is too short or incomplete, please add the following message to the top of your entry:

:::info Under Maintenance
The content in this entry is incomplete & is in the process of being completed.
:::

If you've added a new page & you aren't sure what should go there (this isn't recommended while there are still so many empty pages to be filled), add the following message as your page entry:

:::warning Help Wanted
This section is in need of contributions. If you believe you can help, please see our [Contribution Guide](../docs/contribution-guide.md) to get started as a contributor!
:::

### Connect With Us

If you're interested in communicating with other passionate contributors helping this project, visit [rvlt.gg/emxNXv1x](https://rvlt.gg/emxNXv1x). You'll need a Revolt account to join. Revolt is an open-source Discord alternative, which you can read more about on [this page](https://github.com/revoltchat/legal/blob/master/About.md#communication-is-critical).

## Clone & Push Instructions
**Make sure to clone from & edit the** `main` **branch only, and push your final changes to the** `development` **branch according to the instructions below.**

*don't forget to add unimportant files to the .gitignore before making any commits*

1. Clone from the `main` branch to start to make a contribution:
```zsh
% git clone git@github.com:av1-community-contributors/av1-wiki.github.io.git -b main
```

2. Test your changes locally before making a commit:
```zsh
% yarn
% npx docusaurus start
```

3. Push changes to `main` branch:
```zsh
% git add .
% git commit -m "Commit Message"
% git push -u origin main
```

4. Deploy to `deployment` branch to make live on site:
```zsh
% GIT_USER=<username> DEPLOYMENT_BRANCH=deployment yarn deploy
```

*Docusaurus Info*
## Website

This website is built using [Docusaurus 2](https://docusaurus.io/), a modern static website generator.

### Installation

```
$ yarn
```

### Local Development

```
$ yarn start
```

This command starts a local development server and opens up a browser window. Most changes are reflected live without having to restart the server.

### Build

```
$ yarn build
```

This command generates static content into the `build` directory and can be served using any static contents hosting service.

### Deployment

See the initial instructions at the top.