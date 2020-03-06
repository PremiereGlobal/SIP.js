# SIP.js PGi Integration Branch
This branch is to be used for any integration of changes made to a forked version of SIP.js that end up being consumed by PGi projects.
This branch is based off the current stable release that is being consumed. (Check git history for the latest tag).
It is preferrable to use onsip/SIP.js directly, so if changes get merged in upstream, remove the relevant branches on this repo, and switch back to onsip/SIP.js.

## Modifying SIP.js

### Create the SIP.js PR
- Clone this repo (also pull in upstream changes to master to keep things in sync)
- If the change can be made on master, create the feature branch off master. Otherwise, create the feature branch off the latest release tag we are using.
- Make your changes and push your branch.
- Create PR on github and wait for feedback.

### Create the integration feature branch and internal PR
- Checkout the `integration` branch locally.
- Create a separate branch off `integration` with a name related to the original branch and backport your changes into the new branch (cherrypick, copy/paste, whatever).
- At this point you could optionally have an internal code review.

### Publish the internal pgi npm module
Note: You can also follow the publish steps on your feature branch, but doing it in the `integration` branch allows multiple changes to be applied at a time
- You'll likely need to set up your .npmrc to point at npm-local in artifactory
    - Save off your old .npmrc `mv ~/.npmrc ~/old.npmrc`
    - Follow the instructions on artifactory to login to the npm-local registry and setup the pgi scope
- Checkout the `integration` branch and merge in your feature branch
- Modify the `version` field in `package.json` to be <latest release tag>-<current commit hash> `echo "$(git describe --tags --abbrev=0)-$(git rev-parse HEAD)"`
- Follow an adapated version of RELEASE.md:
    - Remove all `dist` files `rm -rf dist`
    - Build and test `npm run buildAndTest`
    - Add and commit new dist/lib files
    - Push changes to PGi repo
    - Publish the npm module `npm publish --registry https://artifactory.pgi-tools.com/artifactory/api/npm/npm-local/`

### Use the pgi npm module
- Replace `sip.js` with `@pgi/sip.js` in `package.json` and update the version to the version you published
- Replace any other refernces to `sip.js` with `@pgi/sip.js`
