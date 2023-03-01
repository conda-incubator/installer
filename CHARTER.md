# Team charter

The team charter is dynamic following the conda governance policy, with the following caveats:

- 🧰 The team’s purpose is to develop and maintain code included in the conda/installer repository. That repository contains among other things tools and scripts to make it incredibly easy to create and maintain installers similar to the current “miniconda” or “miniforge” installers.

- 📨 The team acknowledges the need for better coordination between stakeholders that work on installers and bootstrappers in the conda community.

- 📢 To enable accountability and predictability towards end users, the team commits to open and transparent communication about the release process, whenever possible.

- ♻️ The team will abstract code shared between the various installers in the ecosystem in a central location to reduce diverging implementations.

## Areas of work

- Adopt various installer automation from Anaconda and the conda community to centralize maintenance

- Review overlap with the existing miniforge repo together with miniforge maintainers

- Apply sensible measures to prevent uncoordinated changes, e.g. branch protection, required code review by at least two maintainers via CODEOWNERS or 4, 6 or 8 eyes review principle

- Add reusable “composite GitHub Action” (like conda’s CLA action) to installer directory with parameters for main config variables, e.g. signing keys, to conda installer repo for use by 1st and 3rd parties

- Make use of created action in:
  - A new repo called conda/miniconda (?) that automates the build of miniconda (for handover to Anaconda/community QA)
  - A new Anaconda-internal (!) repo that automates the creation of the Anaconda Distribution Installer in a similar way

- Design decisions on how installer creation should split off between the various stakeholders involved

- Supply chain security topics such as reproducibilty and code signing

- Opportunities of tying together various “bundling” tools under a new `conda bundle` subcommand?

- ...

## Members

### conda & constructor maintainers

- @jezdez
- @chenghlee
- @jaimergp

### miniforge maintainers

- @xhochy
- @isuruf (also constructor maintainer)

### Anaconda packaging

- @AndrewVallette
- @marcoesters
- @psteyer
- @pseudoyim (also constructor maintainer)

### Anaconda infra

- @dbast

### Anaconda security

- @awwad
- @pkmooreanaconda

### Anaconda custom services

- @jlstevens

